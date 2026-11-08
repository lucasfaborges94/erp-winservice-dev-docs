# Auditoria de dados atuais de autorização — 2026-11-08

**Projeto Supabase:** `voaovwphncwbrwtcqnsc`  
**Schema auditado:** `public`  
**Tipo:** somente leitura  
**Estado:** fotografia inicial concluída; não representa aprovação de migração.

## 1. Escopo

Foram consultadas as estruturas `companies`, `users`, `company_users`, `roles`, `company_user_roles`, `permissions`, `role_permissions` e `user_permissions`. Também foram consultadas as colunas relevantes e verificadas referências órfãs e atribuições entre empresas.

Nenhum DDL, INSERT, UPDATE ou DELETE foi executado.

## 2. Contagem de registros

| Estrutura | Registros |
|---|---:|
| `companies` | 1 |
| `users` | 5 |
| `company_users` | 1 |
| `roles` | 3 |
| `company_user_roles` | 2 |
| `permissions` | 1 |
| `role_permissions` | 0 |
| `user_permissions` | 0 |

A única empresa cadastrada é Winservice.

## 3. Papéis cadastrados

| Identificador funcional atual | Escopo (`company_identifier`) | Vínculos atribuídos em `company_user_roles` |
|---|---|---:|
| `financial` | Global (`NULL`) | 1 |
| `superuser` | Global (`NULL`) | 0 |
| `technician` | Global (`NULL`) | 1 |

Os três registros de papel possuem `company_identifier = NULL`.

## 4. Vínculos e divergências

Existe um único registro em `company_users`, associado à empresa Winservice e a um usuário ativo.

- `company_admin = true`.
- `role_identifier` legado referencia o papel `superuser`.
- `company_user_roles` associa o mesmo vínculo aos papéis `financial` e `technician`.

Portanto, os mecanismos de papel divergem: o campo legado aponta para `superuser`, enquanto a tabela de múltiplos papéis atribui `financial` e `technician`. Não se deve migrar ou converter automaticamente essa divergência sem decisão e mapeamento aprovados.

## 5. Usuários

Existem cinco registros em `users`; todos possuem `auth_user_identifier` preenchido.

- 1 usuário com status `active`.
- 4 usuários com status `inactive`.
- Apenas o usuário ativo possui vínculo em `company_users`.
- O campo textual legado `users.role` contém `superuser` no usuário ativo e `Atendente` em um usuário inativo; os outros três registros não possuem valor nesse campo.

A tabela `company_users` não possui coluna de status no esquema consultado. O status de `users` não deve ser tratado automaticamente como status do vínculo empresarial. A natureza humana/agente dos registros não foi determinada por esta consulta.

## 6. Permissões

Existe uma permissão registrada para a empresa Winservice:

- entidade: `customer`;
- campo: `NULL`;
- ação: `view`;
- descrição: visualizar todos os dados de clientes.

Não há registros em `role_permissions` nem em `user_permissions`. Assim, a base atual ainda não contém associações de permissões por papel ou exceções individuais nessas estruturas.

## 7. Integridade verificada

As consultas retornaram:

| Verificação | Resultado |
|---|---:|
| Vínculos empresariais sem usuário correspondente | 0 |
| Atribuições sem vínculo empresarial correspondente | 0 |
| Atribuições com papel inexistente | 0 |
| Referências legadas a papel inexistente | 0 |
| Atribuições com papel empresarial de outra empresa | 0 |

Essas verificações cobrem somente as relações consultadas. Não comprovam correção integral das policies RLS, funções, APIs ou comportamento das aplicações.

## 8. Colunas relevantes observadas

- `company_users.role_identifier`: nullable; aponta para papel legado.
- `company_users.company_admin`: boolean, NOT NULL, default false.
- `company_users`: não possui coluna de status.
- `company_user_roles.company_user_identifier`: NOT NULL.
- `company_user_roles.role_identifier`: NOT NULL.
- `roles.company_identifier`: nullable.
- `permissions.company_identifier`: NOT NULL.
- `user_permissions.allow`: nullable.

## 9. Implicações para o planejamento

1. A migração deve preservar a atribuição legada `superuser` e as duas atribuições atuais `financial` e `technician` até que o responsável aprove o mapeamento de destino.
2. `company_admin = true` é o único indicador atual de administração/titularidade no vínculo encontrado; não converter para `account_owner` antes de aprovar regra e destino.
3. A nomenclatura conceitual vigente é `superuser`; o registro legado já contém esse identificador, mas sua equivalência e migração devem ser avaliadas no mapeamento de papéis, sem conversão automática de dados.
4. Os quatro usuários inativos sem vínculo exigem análise individual antes de qualquer migração; não devem ser apagados nem receber vínculo automaticamente.
5. A ausência de linhas em `role_permissions` e `user_permissions` significa que não há dados de concessão/negação nessas estruturas a migrar nesta fotografia, mas a permissão isolada existente deve ser preservada e avaliada.
6. Como `company_users` não tem status próprio, a futura especificação deve distinguir estado da identidade global e estado do vínculo com cada empresa.

## 10. Limitações e próximos levantamentos

Esta auditoria não examinou integralmente:
- políticas RLS por tabela;
- funções, triggers, views e dependências que leem os campos legados;
- metadados e sessões do Supabase Auth;
- código das aplicações e workflows;
- semântica histórica dos papéis `financial`, `superuser` e `technician`.

Esses pontos devem ser verificados antes da especificação final de migração. A presente auditoria não autoriza alterações no banco.

## 11. Consultas de referência

As consultas executadas foram SELECTs sobre `information_schema.columns` e agregações/leitura nas tabelas citadas. Os resultados completos permanecem no registro de execução da auditoria desta conversa; este documento consolida os achados relevantes sem expor e-mails, telefones ou identificadores pessoais.

## 12. Dependências técnicas e segurança — complemento da auditoria

### 12.1 Função legada obsoleta — fora do escopo atual

A função `public.can_view_expense(p_expense_identifier uuid)` foi identificada durante a auditoria, mas o responsável pelo projeto determinou que ela seja tratada como obsoleta e ignorada nesta etapa, pois será reavaliada no futuro módulo Financeiro. Portanto, ela não deve orientar decisões do modelo geral de autorização nem bloquear o avanço da especificação atual. Sua dependência do campo legado `company_users.role_identifier` e seus privilégios de execução permanecem registrados apenas como contexto histórico para a futura retomada do módulo Financeiro. Nenhuma alteração na função, em seus grants ou no campo foi executada ou autorizada por esta classificação.

### 12.1.1 Evidência técnica registrada na auditoria

Foi localizada a função `public.can_view_expense(p_expense_identifier uuid)`, definida como `SECURITY DEFINER`, que:
- consulta `company_users.role_identifier`;
- considera `superuser` e `financial` como autorizados na regra de visualização;
- não consulta `company_user_roles`;
- pode ser executada por `anon`, `authenticated` e `service_role`, conforme `has_function_privilege`.

A dependência foi registrada apenas para contexto histórico do futuro módulo Financeiro. Como a função está obsoleta e fora do escopo atual, esse achado não deve bloquear a especificação geral de autorização. A função, seus grants e o campo legado não foram alterados; eventual revisão ocorrerá quando o módulo Financeiro for especificado.

### 12.2 Policies RLS nas tabelas de autorização

Todas as sete tabelas examinadas têm RLS habilitado. Foram encontradas policies somente em:
- `users`: INSERT para authenticated e SELECT do próprio registro;
- `company_users`: SELECT do vínculo do próprio usuário.

Não foram encontradas policies em `company_user_roles`, `roles`, `permissions`, `role_permissions` e `user_permissions`. Isso precisa ser avaliado com os privilégios SQL e os caminhos de acesso das aplicações. RLS habilitado sem policy tende a negar acesso aos papéis sujeitos a RLS, mas não protege contra funções privilegiadas ou papéis com bypass RLS.

### 12.3 Identidade e triggers

Os cinco registros de `users` têm `identifier = auth_user_identifier`, alinhamento observado que torna coerente a comparação atual da policy de `company_users` com `auth.uid()` para os registros existentes. Não foram encontrados triggers nas sete tabelas examinadas.

### 12.4 Limitações

A busca de dependências por catálogo não identificou dependência relacional registrada para a função SQL; a dependência foi confirmada pela leitura do corpo da função. Ainda falta examinar grants de tabela, demais funções e views, código das aplicações e workflows.

## 13. Privilégios SQL e advisors de segurança — complemento

### 13.1 Privilégios de tabela

A consulta a `information_schema.role_table_grants` encontrou concessões de privilégios amplas para `anon`, `authenticated` e `service_role` nas sete tabelas examinadas (`users`, `company_users`, `company_user_roles`, `roles`, `permissions`, `role_permissions`, `user_permissions`), incluindo operações de leitura e escrita e privilégios estruturais.

Essas concessões não significam, isoladamente, que todas as operações estejam acessíveis: RLS continua sendo uma camada de filtragem para papéis sujeitos a RLS. Contudo, a combinação de grants amplos, ausência de policies em cinco tabelas e funções `SECURITY DEFINER` executáveis por `anon`/ `authenticated` exige revisão coordenada. Nenhum grant foi alterado.

### 13.2 Views e rotinas

A busca em views públicas não encontrou definições que mencionem diretamente `company_users`, `company_user_roles`, `company_admin` ou `role_identifier`. A busca de rotinas confirmou `public.can_view_expense(uuid)` como função que consulta `company_users.role_identifier`; esse achado fica registrado para retomada no futuro módulo Financeiro, não como bloqueio da especificação atual.

### 13.3 Supabase Security Advisors

A consulta aos Security Advisors em 2026-11-08 retornou:
- 26 tabelas com RLS habilitado e sem policies;
- 7 funções com `search_path` mutável;
- 2 funções `SECURITY DEFINER` executáveis por `anon`;
- 2 funções `SECURITY DEFINER` executáveis por `authenticated`;
- proteção contra senhas vazadas desabilitada.

Entre as funções destacadas estão `public.can_view_expense` (obsoleta e fora do escopo atual; futura retomada no módulo Financeiro) e `public.authorize_integration_sync_event`. A lista completa de tabelas sem policies e as referências de remediação estão no resultado dos advisors da execução desta auditoria. Estes achados são para avaliação e não foram corrigidos nesta etapa.

## 14. Migrations e Edge Functions

- A listagem de migrations do projeto Supabase retornou vazia.
- A listagem de Edge Functions retornou vazia.

A ausência de migrations registradas no Supabase dificulta reconstruir a evolução do schema exclusivamente pelo histórico nativo de migrations. Antes de alterações futuras, será necessário estabelecer uma linha de base documental do schema real e reconciliá-la com os arquivos versionados de migrations, se existirem em outro repositório/ambiente. A ausência de Edge Functions no projeto consultado significa que não foi identificada essa camada de backend neste levantamento; não exclui APIs ou serviços hospedados externamente.

## 15. Deliberação posterior — nomenclatura e escopo da função de despesas

Em 2026-11-08, foi adotado `superuser` como nomenclatura conceitual no lugar de `superadmin`. A função SQL `public.can_view_expense(uuid)` foi classificada como obsoleta e fora do escopo atual, devendo ser reavaliada no futuro módulo Financeiro. A classificação não alterou o banco e não autoriza remover a função, seus grants ou o campo legado consultado por ela.


## 16. Auditoria RLS ampliada — fotografia de 2026-11-08

Esta seção registra os achados da inspeção ampliada do schema `public`, para que sessões futuras consultem este documento em vez de repetir a descoberta e voltar a apresentar os mesmos problemas como novos.

### 16.1 Escopo e método

Inspeção somente leitura das policies registradas em `pg_policies`, das tabelas do schema `public` e de colunas selecionadas de tabelas de domínio. Nenhuma policy, grant, função, tabela ou dado foi alterado. Esta fotografia complementa as observações anteriores dos Security Advisors; não substitui testes de acesso efetivo via PostgREST/API e diferentes papéis de banco.

### 16.2 Cobertura de policies

Na fotografia consultada:
- 38 tabelas no schema `public`, todas com RLS habilitado segundo a listagem de tabelas;
- 12 tabelas com ao menos uma policy;
- 26 tabelas sem policy registrada.

Ter RLS habilitado não comprova isolamento correto. Para tabelas sem policy, o comportamento efetivo depende também do papel de execução, grants, funções privilegiadas e caminhos de acesso.

### 16.3 Policies permissivas sem isolamento por empresa

Foram observadas policies com predicado `true` ou `WITH CHECK (true)` nas seguintes tabelas:

| Tabela | Operação | Predicado observado |
|---|---|---|
| `addresses` | SELECT | `true` |
| `addresses` | INSERT | `WITH CHECK (true)` |
| `addresses` | UPDATE | `USING (true)` |
| `companies` | ALL | `USING (true)` |
| `customer_contacts` | SELECT | `true` |
| `customer_contacts` | INSERT | `WITH CHECK (true)` |
| `customers` | SELECT | `true` |
| `customers` | INSERT | `WITH CHECK (true)` |
| `entity_status` | SELECT | `true` |
| `entity_status` | INSERT | `WITH CHECK (true)` |
| `entity_status` | UPDATE | `USING (true)` |
| `entity_types` | SELECT | `true` |
| `entity_types` | INSERT | `WITH CHECK (true)` |
| `entity_types` | UPDATE | `USING (true)` |
| `visits` | SELECT | `true` |
| `visits` | INSERT | `WITH CHECK (true)` |
| `visits` | UPDATE | `USING (true)` |

A policy de `company_users` limita SELECT ao próprio usuário; a policy de SELECT em `users` limita a identidade ao próprio registro. A policy de `expenses` chama a função legada `can_view_expense`, já classificada fora do MVP e reservada à retomada do Financeiro.

**Impacto:** as policies permissivas acima não expressam o isolamento por `company_identifier` exigido pela ADR-0017. Em especial, operações de leitura/escrita em dados de clientes, endereços, contatos e visitas não devem ser consideradas protegidas por tenancy apenas porque RLS está habilitado. O risco precisa ser tratado como pendência de segurança já conhecida e bloqueadora para confiar no isolamento multiempresa dessas rotas.

### 16.4 Estado e tratamento

- Status: **PENDÊNCIA DE SEGURANÇA CONHECIDA — NÃO CORRIGIDA**.
- Referência normativa: ADR-0017 e modelo de autorização multiempresa.
- Não criar policies corretivas isoladamente sem revisar grants, funções `SECURITY DEFINER`, backend/API, contexto de empresa ativa e testes positivos/negativos.
- A correção de tenancy, integridade multiempresa e RLS permanece pré-condição para implementar `visit_events`, conforme o registro de continuidade e a especificação operacional.
- Nenhuma alteração foi autorizada ou executada nesta auditoria.

### 16.5 Regra para sessões futuras — evitar redescoberta

Antes de relatar problemas de RLS, tenancy ou autorização:
1. Ler esta seção e a seção 13 deste documento.
2. Tratar os achados acima como **já conhecidos**, não como novas descobertas.
3. Revalidar somente se a tarefa exigir confirmar o estado atual, houver evidência de alteração posterior ou for necessário testar comportamento efetivo.
4. Se revalidar, comparar o resultado com esta fotografia e registrar apenas diferenças, correções, regressões ou novas evidências.
5. Atualizar este documento com data, escopo, resultados e referências de commit antes de considerar o achado encerrado.

A lista de policies é uma fotografia do momento da consulta. Não afirmar que o estado continua igual sem revalidação.
