# Modelo de autorização e isolamento multiempresa — ERP Winservice

**Documento:** Modelo de autorização e isolamento multiempresa  
**Versão:** 0.1.0  
**Data:** 2026-11-07  
**Estado:** PROPOSTA PARA DISCUSSÃO — não aprovada como decisão arquitetural  
**Projeto Supabase consultado:** Winservice (`voaovwphncwbrwtcqnsc`)  
**Schema consultado:** `public`  
**Base técnica:** estrutura e policies observadas em 2026-11-07; consultar novamente antes de implementar.

## 1. Objetivo

Definir o modelo conceitual e os requisitos de autorização que deverão orientar a identidade, o vínculo de usuários com empresas, o catálogo de permissões, a autorização de operações, o isolamento dos dados, as APIs, as políticas RLS e a atuação da Isa.

Este documento é uma proposta de especificação. Não altera ADRs aprovadas, não aprova por si só uma estratégia de tenancy e não autoriza alterações no banco.

## 2. Autoridade e decisões relacionadas

Este modelo deve respeitar:

- **ADR-0001:** produto preparado para SaaS multiempresa, inicialmente utilizado pela Winservice.
- **ADR-0002:** Supabase como fonte central de verdade.
- **ADR-0011:** catálogo padrão global versionado; cada empresa mantém catálogo próprio e escolhe quando aderir a novas versões; personalizações devem ser preservadas, rastreadas e restauráveis.
- **ADR-0012:** a Isa consulta somente informações do cliente identificado no atendimento atual; pode criar clientes, equipamentos, ordens de serviço e visitas; não pode excluir; só pode alterar registros criados por ela; enforcement no backend/API e autorização, não apenas em prompt ou interface.

O índice arquitetural registra como questões ainda abertas a estratégia exata de tenancy e a matriz definitiva de papéis e permissões. Este documento não as trata como decisões já aprovadas.

## 3. Evidências do estado atual consultado

A inspeção foi somente leitura. Nenhuma alteração foi feita no Supabase.

### 3.1 Estruturas relevantes encontradas

Foram identificadas, entre outras, as seguintes tabelas e campos:

| Estrutura | Evidência observada | Observação |
|---|---|---|
| `companies` | `identifier`, `name`, `is_active` | Cadastro de empresas |
| `users` | `identifier`, `auth_user_identifier`, `role`, `status` | Há uma distinção estrutural entre identificador interno e identificador de Auth |
| `company_users` | `company_identifier`, `user_identifier`, `role_identifier`, `company_admin` | Associação usuário–empresa |
| `company_user_roles` | `company_user_identifier`, `role_identifier` | Associação de múltiplos papéis ao vínculo |
| `roles` | `identifier`, `company_identifier` | Papel pode estar associado a uma empresa |
| `permissions` | `company_identifier`, `entity`, `field`, `action` | Permissões atuais são associadas a uma empresa |
| `role_permissions` | `company_identifier`, `role_identifier`, `permission_identifier` | Associação entre papel e permissão |
| `user_permissions` | `company_identifier`, `company_user_identifier`, `permission_identifier`, `allow` | Há estrutura para exceções por usuário; semântica completa ainda precisa ser definida |
| `modules`, `company_modules`, `role_modules`, `user_modules` | Catálogo e associações de módulos | Não comprovam, isoladamente, autorização sobre registros |
| `screens`, `role_screens`, `user_screens` | Catálogo e associações de telas | Controle de navegação não substitui autorização de dados |

Também foram observadas policies RLS permissivas em tabelas como `companies`, `customers`, `addresses`, `customer_contacts` e `visits`, com condições `true` para operações de usuários autenticados. A auditoria técnica de 2026-11-07 detalha os achados e suas limitações.

### 3.2 Pontos que exigem reconciliação

- `users` contém `auth_user_identifier`, mas a policy observada em `users` compara `auth.uid()` com `users.identifier`.
- A policy observada em `company_users` compara `user_identifier` com `auth.uid()`, enquanto a FK de `company_users.user_identifier` referencia `users.identifier`.
- A tabela `users` tem FK de `auth_user_identifier` para uma entidade externa ao schema `public`; a relação exata com `auth.users` e o fluxo de provisionamento precisam ser confirmados.
- `permissions.company_identifier` é obrigatório no esquema observado, o que não demonstra, por si só, um catálogo global independente como exige a ADR-0011.
- As FKs individuais não comprovam que todos os relacionamentos entre entidades pertencem à mesma empresa.
- O campo `company_admin` e os campos de papel em `company_users` coexistem com a tabela `company_user_roles`; precedência e semântica não estão documentadas.

Esses pontos são observações estruturais. Não se conclui, sem análise adicional, que sejam exploráveis ou que o fluxo de autenticação esteja necessariamente quebrado.

## 4. Vocabulário do modelo

- **Identidade autenticada:** identidade validada pelo provedor de autenticação, representada no contexto PostgreSQL por `auth.uid()`.
- **Usuário ERP:** perfil interno que representa uma pessoa ou identidade que utiliza o ERP.
- **Vínculo usuário–empresa:** associação ativa que autoriza o usuário a atuar em determinada empresa.
- **Empresa de contexto:** empresa em cujo escopo uma operação está sendo solicitada.
- **Papel (role):** conjunto nomeado de permissões atribuídas a um vínculo usuário–empresa.
- **Permissão:** autorização granular para executar uma ação sobre uma entidade e, quando aplicável, campo ou recurso.
- **Recurso tenant-scoped:** dado pertencente a uma empresa e isolado pelo identificador dessa empresa.
- **Recurso global:** dado mantido pela plataforma e compartilhado entre empresas segundo regras explícitas.
- **Autoria:** identidade/agente que criou um registro, distinta do usuário que o atualizou posteriormente.

## 5. Princípios propostos

Os princípios abaixo são propostos para aprovação e detalhamento:

1. **Negação por padrão:** ausência de uma autorização positiva resulta em acesso negado.
2. **Autenticação não é autorização:** estar em `authenticated` não concede acesso a uma empresa ou registro.
3. **Vínculo ativo obrigatório:** operações tenant-scoped exigem vínculo ativo do usuário autenticado com a empresa de contexto.
4. **Contexto validado no servidor:** o identificador da empresa enviado pelo cliente é apenas uma solicitação de contexto; nunca prova autorização.
5. **Isolamento em profundidade:** APIs/backend validam o contexto e o banco aplica RLS e constraints adequadas.
6. **Menor privilégio:** conceder apenas as ações e os dados necessários.
7. **Separação de responsabilidades:** permissões de interface, módulos e telas não substituem autorização no backend/banco.
8. **Integridade entre tenants:** relacionamentos entre registros tenant-scoped devem impedir associação entre empresas diferentes.
9. **Auditoria:** operações relevantes registram ator, empresa de contexto, ação, recurso e resultado, sem expor segredos.
10. **Sem bypass implícito:** funções privilegiadas e serviços internos devem ter finalidade, autenticação, autorização e escopo documentados.
11. **Consistência entre canais:** web, app móvel, n8n, Chatwoot, APIs e Isa devem obedecer ao mesmo modelo de autorização, com identidades técnicas próprias quando necessário.
12. **Catálogo versionado:** a evolução das permissões deve preservar a decisão da ADR-0011 e não sobrescrever customizações silenciosamente.

## 6. Fluxo de autorização proposto

Para uma operação de usuário, a sequência lógica proposta é:

1. Validar a sessão e obter a identidade autenticada confiável.
2. Resolver o usuário ERP associado à identidade autenticada.
3. Receber a empresa solicitada como contexto, sem confiar nela como autorização.
4. Confirmar que a empresa existe e está ativa.
5. Confirmar que o usuário possui vínculo ativo com essa empresa.
6. Resolver os papéis e exceções de permissão aplicáveis àquele vínculo.
7. Verificar a permissão exigida para a ação e o recurso.
8. Aplicar restrições adicionais de escopo, autoria, estado e relacionamento.
9. Executar a operação sob RLS e constraints coerentes com o mesmo contexto.
10. Registrar a operação auditável quando aplicável.

Qualquer falha de identidade, vínculo, contexto, permissão ou validação de escopo deve resultar em negação. A API não deve aceitar do cliente claims de papel, permissões, autoria ou empresa como fatos confiáveis sem validação no servidor.

## 7. Modelo conceitual de identidade e vínculo

### 7.1 Identidade e perfil ERP

A identidade do provedor de autenticação deve ser vinculada de forma inequívoca a um perfil ERP. A chave de autenticação e o identificador interno do usuário não devem ser confundidos.

**Proposta:** usar o identificador imutável do provedor como referência de autenticação e manter `users.identifier` como identificador interno do ERP, com relação explícita e única entre ambos. O campo `users.auth_user_identifier` parece destinado a esse vínculo, mas o fluxo real precisa ser confirmado antes de consolidar esta proposta.

### 7.2 Usuário em múltiplas empresas

O modelo deve permitir que uma identidade tenha vínculos independentes com uma ou mais empresas, cada qual com seus próprios papéis, permissões e estado de acesso.

A autorização em uma empresa não deve se propagar automaticamente a outra. Suspender ou remover um vínculo deve impedir novas operações nesse tenant sem exigir a exclusão do perfil global do usuário.

### 7.3 Empresa de contexto

A seleção da empresa ativa pode ocorrer na aplicação, mas sua validade deve ser confirmada em cada operação sensível no backend/banco. Não utilizar uma variável de sessão mutável pelo cliente como única prova de vínculo ou autorização.

A forma técnica de transportar e validar o contexto — JWT, parâmetro de API, contexto transacional ou outro mecanismo — permanece pendente de decisão.

## 8. Modelo conceitual de permissões

A autorização deve considerar, no mínimo:

- identidade autenticada;
- vínculo ativo com a empresa;
- papel ou conjunto de papéis atribuídos ao vínculo;
- permissões concedidas ao papel;
- exceções explícitas por usuário/vínculo, se aprovadas;
- módulo habilitado para a empresa, quando o recurso depender de módulo;
- restrições específicas do recurso, como autoria, cliente do atendimento, estado ou atribuição.

### 8.1 Catálogo global e customizações

A ADR-0011 exige distinguir:

1. catálogo padrão global mantido pela plataforma;
2. versões publicadas desse catálogo;
3. versão adotada por cada empresa;
4. personalizações locais da empresa;
5. histórico de alterações e possibilidade de restauração dos padrões.

A estrutura atual de `permissions` com `company_identifier` obrigatório não comprova que essa separação esteja implementada. A solução de dados deve ser especificada sem violar a preservação das personalizações.

### 8.2 Semântica de múltiplos papéis e exceções

Ainda não está decidido se permissões de múltiplos papéis serão agregadas por união, se haverá negações explícitas, como será resolvido conflito entre concessão e negação e qual será a precedência de exceções individuais.

**Recomendação para discussão:** definir uma regra determinística, documentada e testável, evitando precedências implícitas ou dependentes da ordem de consulta.

## 9. Isolamento e integridade dos dados

Para cada entidade, a especificação deverá classificá-la como:

- global da plataforma;
- pertencente diretamente a uma empresa;
- pertencente indiretamente a uma empresa por relacionamento;
- dado de sistema/infraestrutura com acesso restrito.

Para entidades tenant-scoped, a política deve assegurar que:

- leitura só alcance registros da empresa autorizada;
- criação só permita associar registros à empresa de contexto validada;
- atualização não permita mover um registro para outra empresa;
- exclusão dependa de permissão específica e regras de retenção;
- relacionamentos não permitam conectar registros de empresas diferentes;
- consultas indiretas e funções privilegiadas preservem o mesmo isolamento.

Quando um registro não possuir `company_identifier` próprio, o caminho de herança de tenant deve ser explícito, íntegro e verificável. Se necessário, constraints compostas ou outro mecanismo equivalente deverão impedir inconsistências entre tenant e referências.

A decisão sobre quais tabelas devem carregar `company_identifier` diretamente não está tomada neste documento.

## 10. Agentes de IA e identidades técnicas

### 10.1 Isa

A Isa não deve compartilhar uma identidade humana nem receber privilégios genéricos de serviço. Sua execução deve ter identidade técnica identificável e contexto de atendimento validado.

Os limites da ADR-0012 devem ser aplicados no backend/API e, quando aplicável, no banco:

- consulta limitada ao cliente identificado no atendimento atual;
- criação permitida apenas para clientes, equipamentos, ordens de serviço e visitas;
- exclusão proibida;
- atualização somente de registros criados pela própria Isa;
- operações fora do escopo negadas.

O identificador de cliente ou atendimento fornecido em texto pelo modelo não constitui prova de autorização. O backend deve resolver e validar o contexto.

### 10.2 Workflows e integrações

Cada serviço automatizado deve possuir identidade técnica própria, escopo de atuação mínimo, credenciais gerenciadas fora do código e trilha de auditoria. Não reutilizar indiscriminadamente a identidade de um administrador ou a chave `service_role`.

O uso de `service_role` ou de funções `SECURITY DEFINER` deve ser excepcional, restrito e justificado; essas vias não podem contornar silenciosamente as regras de negócio e de isolamento.

## 11. Estratégia de tenancy — alternativas em avaliação

O índice de ADRs registra que a estratégia exata ainda está aberta.

| Alternativa | Vantagens | Custos e riscos |
|---|---|---|
| Banco compartilhado, schema compartilhado, isolamento por linha (`company_identifier` + RLS) | Compatível com a estrutura atual; menor complexidade operacional inicial; facilita evolução incremental | Exige disciplina rigorosa em RLS, constraints, funções e testes; risco de falha de isolamento se houver policy incorreta |
| Schema por empresa | Separação lógica maior entre tenants | Migrações e manutenção mais complexas; compartilhamento de catálogo e evolução de módulos exigem coordenação |
| Banco por empresa | Maior isolamento operacional e possibilidade de configuração dedicada | Maior custo e complexidade de provisionamento, observabilidade, migrações, backup e operação SaaS |

**Recomendação preliminar, não aprovada:** manter banco e schema compartilhados com isolamento por linha, desde que a revisão de RLS, constraints, funções e testes demonstre isolamento robusto. A estrutura atual já concentra entidades em `public` e utiliza `company_identifier` em parte delas, mas isso é apenas evidência de compatibilidade inicial, não prova suficiente para decidir.

## 12. Matriz mínima de testes de autorização

Antes de produção, devem existir testes positivos e negativos para pelo menos os seguintes cenários:

| Cenário | Resultado esperado |
|---|---|
| Usuário sem sessão tenta acessar dado protegido | Negado |
| Usuário autenticado sem vínculo com a empresa tenta consultar dados | Negado |
| Usuário vinculado à empresa A consulta registro da empresa B | Negado |
| Usuário da empresa A cria registro informando empresa B | Negado |
| Usuário tenta atualizar registro para mudar seu tenant | Negado |
| Usuário tenta relacionar cliente da empresa A a endereço/OS/visita da empresa B | Negado |
| Usuário com papel sem permissão tenta executar ação | Negado |
| Usuário com permissão válida e vínculo ativo executa ação permitida | Permitido |
| Vínculo do usuário está suspenso | Novas operações negadas |
| Isa consulta cliente diferente do atendimento validado | Negado |
| Isa tenta excluir registro | Negado |
| Isa tenta alterar registro criado por outra identidade | Negado |
| Serviço técnico sem escopo tenta executar operação | Negado |
| Função privilegiada é chamada sem autenticação/autorização válida | Negado |

Os testes devem ser executados com identidades e empresas distintas em ambiente controlado, sem usar dados reais de clientes como fixtures.

## 13. Decisões pendentes para aprovação

Os itens abaixo precisam de resposta do responsável antes de transformar esta proposta em especificação normativa ou implementar mudanças estruturais:

1. **Tenancy:** adotar banco/schema compartilhado com isolamento por linha como arquitetura da V1?
2. **Usuários multiempresa:** permitir que um mesmo usuário tenha vínculos ativos com várias empresas, com papéis independentes em cada uma?
3. **Contexto da empresa:** a aplicação seleciona a empresa ativa, e o backend valida o vínculo em toda operação? Qual mecanismo técnico será usado para transportar o contexto?
4. **Múltiplos papéis:** as permissões dos papéis devem ser agregadas por união? Deve existir negação explícita com precedência sobre concessões?
5. **Exceções individuais:** `user_permissions.allow` deve representar concessão/negação explícita por vínculo, ou será adotado outro modelo?
6. **Papéis administrativos:** `company_admin` deve ser um atributo de conveniência ou uma função de autorização formal? Deve existir papel de proprietário/administrador da empresa separado de papéis operacionais?
7. **Dados globais:** quais catálogos e recursos serão globais da plataforma e quais serão sempre isolados por empresa?
8. **Identidade técnica:** Isa, n8n e demais integrações terão identidades de serviço distintas, cada uma com escopo próprio?
9. **Identidade Auth ↔ ERP:** confirmar que `users.auth_user_identifier` é a relação canônica com `auth.users.id` e que `users.identifier` é o ID interno do ERP.

## 14. Próximas atividades após aprovação

1. Resolver as decisões pendentes desta proposta.
2. Atualizar este documento para especificação aprovada.
3. Se houver decisão arquitetural nova, registrar ADR próprio antes de alterar o índice.
4. Fazer inventário de classificação tenant/global para todas as tabelas.
5. Desenhar matriz de autorização por entidade e ação.
6. Projetar RLS, constraints, funções e contratos de API.
7. Preparar migrations revisáveis e testes automatizados.
8. Aplicar mudanças somente após aprovação explícita e em ambiente apropriado.
9. Atualizar inventário, auditoria, status e continuidade com evidências reais.

## 15. Limites desta versão

- Documento em estado de proposta.
- Nenhuma decisão pendente foi considerada aprovada.
- Nenhuma alteração foi feita no banco Supabase.
- Nenhuma policy, função, trigger, grant ou constraint foi modificada.
- Não foram consultados dados pessoais de clientes.
- Não foram realizados testes de exploração nem testes de acesso cruzado.
- A análise se baseia em metadados e definições estruturais observadas em 2026-11-07; consultar novamente antes de implementar.

## Histórico de alterações

- 2026-11-07 — v0.1.0: criação da proposta inicial com base no Project Charter, ADR-0001, ADR-0011, ADR-0012 e inspeção somente leitura do schema e das policies do Supabase.
