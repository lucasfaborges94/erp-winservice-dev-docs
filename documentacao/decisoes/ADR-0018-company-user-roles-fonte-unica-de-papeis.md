# ADR-0018 — Fonte única de atribuição de papéis por vínculo usuário–empresa

**Identificador:** ADR-0018  
**Estado:** APROVADA  
**Data da proposta:** 2026-11-08  
**Data da aprovação:** 2026-11-08  
**Responsável pela decisão:** Responsável pelo projeto  
**ADR relacionado/substituído:** Complementa ADR-0017; não substitui nenhuma decisão.

## 1. Contexto

O modelo multiempresa permite que um usuário ERP possua vínculos independentes com várias empresas. Cada vínculo possui seu próprio contexto de autorização, incluindo papéis e permissões, conforme a ADR-0017.

A estrutura física existente contém duas representações concorrentes de papel:
- `company_users.role_identifier`, campo singular no vínculo;
- `company_user_roles`, tabela de relacionamento entre vínculo e papel, capaz de representar múltiplos papéis.

A inspeção somente leitura realizada em 2026-11-08 encontrou um vínculo em `company_users` e duas associações em `company_user_roles`. As associações não coincidem com o valor legado de `company_users.role_identifier`.

## 2. Problema ou necessidade

Manter duas fontes concorrentes para os papéis do mesmo vínculo cria ambiguidade de autorização, divergência de dados e risco de implementação inconsistente. É necessário estabelecer uma única fonte oficial.

## 3. Alternativas consideradas

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Manter `company_users.role_identifier` como fonte principal | Menor mudança imediata | Representa apenas um papel e conflita com a associação N:N existente |
| Manter as duas estruturas e definir precedência | Evita remoção imediata | Mantém duplicidade e ambiguidade |
| Adotar `company_user_roles` como fonte única | Representa múltiplos papéis e separa vínculo de atribuição | Exige retirar o campo legado do modelo e tratar dependências físicas |

## 4. Decisão

**`company_user_roles` é a única fonte oficial de atribuição de papéis aos vínculos usuário–empresa no ERP Winservice.**

Regras obrigatórias:

1. Cada papel atribuído a um usuário em uma empresa deverá ser representado por uma linha em `company_user_roles`, associada ao respectivo `company_users` e `roles`.
2. A associação deverá ser tenant-scoped por `company_identifier`, com FKs compostas que garantam que vínculo e papel pertencem à mesma empresa.
3. Um vínculo poderá possuir zero, um ou vários papéis, respeitadas as regras de provisionamento e acesso do produto.
4. `company_users.role_identifier` está fora do modelo do ERP e não poderá ser consultado, preenchido, sincronizado ou utilizado para autorizar operações.
5. O valor atualmente armazenado em `company_users.role_identifier` será ignorado. Não haverá reconciliação, migração ou conversão desse valor para `company_user_roles`.
6. A coluna legada deverá ser removida do schema físico em migration própria, após tratar dependências de constraints, funções, triggers, views, policies e outros objetos.
7. A remoção física da coluna e qualquer tratamento de dependências deverão ser implementados somente por migration revisada e autorizada. Esta ADR não autoriza execução direta no Supabase.

A decisão não altera o significado de `company_admin`, não cria papel automaticamente e não concede `superuser`. A semântica de união de permissões e exceções individuais permanece conforme o modelo de autorização vigente.

## 5. Justificativa

A tabela de relacionamento representa naturalmente a cardinalidade de múltiplos papéis por vínculo e está alinhada ao requisito da ADR-0017 de que cada vínculo usuário–empresa possua papéis próprios. Eliminar a representação singular concorrente evita que o backend, banco ou aplicações escolham fontes diferentes para a mesma autorização.

O responsável pelo projeto autorizou expressamente desconsiderar o campo legado e seus dados, eliminando a necessidade de reconciliação dos valores divergentes.

## 6. Consequências e impactos

### Benefícios esperados
- Uma única fonte consultável para atribuição de papéis.
- Suporte consistente a múltiplos papéis por vínculo.
- Eliminação de divergência semântica entre campo singular e tabela de relacionamento.
- FKs compostas poderão assegurar que papel e vínculo pertençam à mesma empresa.

### Custos, riscos e limitações
- Consultas, funções ou aplicações antigas que leiam `company_users.role_identifier` deverão ser removidas ou adaptadas.
- O valor legado será descartado; não poderá ser recuperado como atribuição oficial de papel.
- A coluna física não poderá ser removida antes de tratar dependências que a referenciem.

### Impactos operacionais e de manutenção
- Toda inclusão/remoção de papel deverá ocorrer em `company_user_roles`.
- Leituras de papéis deverão partir dessa tabela.
- A migração deverá manter as duas associações atualmente existentes em `company_user_roles`, após validar seus vínculos e papéis no mesmo tenant; não deve comparar ou importar o valor legado.

### Dependências e integrações
A auditoria somente leitura de 2026-11-08 identificou:
- constraint `company_users_role_identifier_fkey`;
- função `public.can_view_expense(uuid)`, cujo corpo contém referência a `company_users.role_identifier`.

A função `can_view_expense` está classificada no modelo de autorização como obsoleta e fora do escopo atual do MVP, com retomada futura no módulo Financeiro. Antes da remoção física da coluna, sua dependência deverá ser tratada explicitamente: retirar/substituir a função ou reescrevê-la sem depender do campo legado, conforme decisão e escopo do módulo Financeiro. Não deixar função inválida nem presumir que a referência possa permanecer.

## 7. Segurança, privacidade e isolamento multiempresa

- `company_user_roles.company_identifier` deverá ser obrigatório.
- O tenant deverá ser derivado do vínculo pai durante o backfill, não fornecido pelo cliente.
- FKs compostas deverão garantir que o vínculo e o papel pertencem à mesma empresa.
- A autorização deverá resolver os papéis exclusivamente pelo vínculo autenticado e pelo contexto de empresa validado no backend/RLS.
- Nenhuma identidade ou agente poderá obter privilégios por meio do campo legado descartado.
- Agentes de IA continuam impedidos de receber ou exercer `superuser`, conforme decisões vigentes.

## 8. Implementação e validação

### Etapas
1. Manter as linhas existentes em `company_user_roles`; validar vínculo pai, papel e empresa.
2. Adicionar/preencher `company_identifier` em `company_user_roles` a partir de `company_users.company_identifier`.
3. Criar/validar chaves únicas e FKs compostas para vínculo e papel.
4. Auditar todas as dependências de `company_users.role_identifier`.
5. Tratar a constraint `company_users_role_identifier_fkey`.
6. Tratar `public.can_view_expense(uuid)` e outras referências encontradas.
7. Remover fisicamente `company_users.role_identifier` em migration revisada, com rollback compatível com o descarte intencional do valor legado (não prometer restauração desse valor).
8. Testar atribuição de zero, um e múltiplos papéis, isolamento entre empresas, união de permissões, exceções individuais e ausência de consultas ao campo legado.

### Critérios mínimos de aceitação

| Cenário | Resultado esperado |
|---|---|
| Usuário possui vários papéis no mesmo vínculo | Todos são resolvidos por `company_user_roles` |
| Usuário não possui linha em `company_user_roles` | Nenhum papel é inferido do campo legado |
| Campo legado possui valor diferente das associações | Campo é ignorado; nenhuma reconciliação é feita |
| Papel pertence a outra empresa | FK composta rejeita a associação |
| Código tenta consultar `company_users.role_identifier` após a migration | Nenhuma dependência operacional deve existir; coluna não estará disponível |
| Função legada dependente do campo | Deve ser removida ou adaptada antes da remoção da coluna |

### Limite de autorização

A aprovação desta ADR formaliza a decisão arquitetural. Não autoriza execução de DDL/DML, migration, alteração de funções, triggers, policies ou grants no Supabase. A execução dependerá de plano técnico final e autorização operacional específica.

## 9. Referências e evidências

- `documentacao/decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md`
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md`
- Inspeção somente leitura do Supabase em 2026-11-08.

## 10. Revisão e substituição

- **Complementa:** ADR-0017.
- **Substitui:** Nenhuma.
- **Substituído por:** Nenhum.

## Histórico desta ficha

- 2026-11-08 — criação e aprovação explícita pelo responsável do projeto. Fonte única definida como `company_user_roles`; campo e dados de `company_users.role_identifier` desconsiderados, com remoção física condicionada ao tratamento das dependências.
