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
3. O papel `superuser` não deve ser renomeado para `superadmin` sem aprovação de equivalência semântica.
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

### 12.1 Função dependente do campo legado

Foi localizada a função `public.can_view_expense(p_expense_identifier uuid)`, definida como `SECURITY DEFINER`, que:
- consulta `company_users.role_identifier`;
- considera `superuser` e `financial` como autorizados na regra de visualização;
- não consulta `company_user_roles`;
- pode ser executada por `anon`, `authenticated` e `service_role`, conforme `has_function_privilege`.

Consequência: `company_users.role_identifier` possui dependência funcional concreta no banco e não pode ser removido ou descontinuado sem substituir e validar essa função. A exposição de execução a `anon` deve ser revisada como achado de segurança; esta auditoria não alterou grants nem a função.

### 12.2 Policies RLS nas tabelas de autorização

Todas as sete tabelas examinadas têm RLS habilitado. Foram encontradas policies somente em:
- `users`: INSERT para authenticated e SELECT do próprio registro;
- `company_users`: SELECT do vínculo do próprio usuário.

Não foram encontradas policies em `company_user_roles`, `roles`, `permissions`, `role_permissions` e `user_permissions`. Isso precisa ser avaliado com os privilégios SQL e os caminhos de acesso das aplicações. RLS habilitado sem policy tende a negar acesso aos papéis sujeitos a RLS, mas não protege contra funções privilegiadas ou papéis com bypass RLS.

### 12.3 Identidade e triggers

Os cinco registros de `users` têm `identifier = auth_user_identifier`, alinhamento observado que torna coerente a comparação atual da policy de `company_users` com `auth.uid()` para os registros existentes. Não foram encontrados triggers nas sete tabelas examinadas.

### 12.4 Limitações

A busca de dependências por catálogo não identificou dependência relacional registrada para a função SQL; a dependência foi confirmada pela leitura do corpo da função. Ainda falta examinar grants de tabela, demais funções e views, código das aplicações e workflows.
