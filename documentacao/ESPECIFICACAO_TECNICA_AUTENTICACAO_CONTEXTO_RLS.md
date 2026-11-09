# Especificação técnica — autenticação, empresa ativa e contexto RLS

**Estado:** PROPOSTA TÉCNICA PARA APROVAÇÃO — não autoriza implementação  
**Data:** 2026-11-09  
**Arquitetura-base:** ADR-0019 (APROVADA)  
**Decisões relacionadas:** ADR-0011, ADR-0012, ADR-0017 e ADR-0018  
**Escopo:** caminho de requisição autenticada da API NestJS ao PostgreSQL/Supabase, com isolamento multiempresa.

> Esta especificação propõe um contrato técnico para revisão. Nenhum endpoint, role PostgreSQL, função, policy, migration ou configuração do Supabase foi criado ou alterado por este documento.

## 1. Objetivo e limites

Definir como a API autentica o usuário, resolve o perfil ERP, seleciona e valida a empresa ativa, calcula autorização e executa cada operação sob contexto transacional confiável e RLS.

Aplica-se ao tráfego de negócio da API. Não define a matriz completa de permissões por entidade, os contratos funcionais dos módulos, o provisionamento inicial de usuários, MFA, política de sessões por aplicação nem a estratégia completa de sincronização offline.

A empresa enviada pelo cliente é apenas uma solicitação de contexto. Não é prova de vínculo ou autorização.

## 2. Requisitos normativos herdados

1. Banco e schema compartilhados, com isolamento lógico por empresa (ADR-0017).
2. Usuário com um vínculo ativo pode ter a empresa selecionada automaticamente; com múltiplos vínculos ativos deve escolher explicitamente; sem vínculo ativo, acesso tenant-scoped é negado.
3. Backend/API e RLS são barreiras complementares. Constraints devem impedir relacionamentos entre empresas diferentes.
4. Papéis são resolvidos exclusivamente por `company_user_roles`; `company_users.role_identifier` não pode ser consultado nem usado como fallback (ADR-0018).
5. A autorização deve respeitar o catálogo e a evolução definidos na ADR-0011.
6. Os limites da Isa da ADR-0012 são aplicados no backend e, quando pertinente, no banco: consulta restrita ao cliente do atendimento atual; criação apenas dos tipos autorizados; sem exclusão; alteração somente de registros criados pela própria Isa.
7. A API não pode confiar em tenant, papéis, permissões, autoria ou escopo enviados pelo cliente.
8. Não usar `service_role` como caminho normal de acesso. Nenhuma operação pode contornar RLS implicitamente.

## 3. Evidências e lacunas que bloqueiam implementação

A fotografia estrutural do Supabase de 2026-11-08 e a auditoria documental registraram:

- `users.identifier` e `users.auth_user_identifier` existem como identificadores distintos.
- Foi observada divergência entre a comparação de `auth.uid()` e `users.identifier` em policy de `users`, e a relação de `company_users.user_identifier` com `users.identifier`.
- A relação física exata entre `users.auth_user_identifier` e `auth.users.id`, incluindo tipo, unicidade, FK e fluxo de provisionamento, precisa ser confirmada no banco atual.
- O schema possui grants e policies permissivas que não demonstram isolamento tenant completo; a matriz atual não pode ser considerada pronta para produção.
- A fotografia não é dump restaurável e não substitui nova inspeção do banco antes de qualquer migration.

**Gate 0:** antes de implementar, confirmar no Supabase real os tipos, constraints, FKs, índices, policies, grants, funções e roles envolvidos; reconciliar o resultado com um baseline SQL restaurável e ensaio não produtivo. Não inferir o estado atual apenas do inventário JSON.

## 4. Identidade e validação do token

### 4.1 Token aceito

- A API aceita exclusivamente access tokens emitidos pelo projeto Supabase configurado para o ambiente.
- Validar assinatura criptográfica, algoritmo permitido conforme a configuração real de signing keys, issuer, audience, expiração e not-before.
- Resolver o identificador imutável do usuário autenticado a partir do claim `sub`.
- Rejeitar tokens sem `sub` válido, tokens de outro projeto/ambiente, tokens expirados, tokens com algoritmo inesperado e qualquer token cuja validação falhe.
- Não aceitar como identidade uma UUID enviada em body, query, header customizado ou claim arbitrário não assinado pelo emissor confiável.
- Não registrar access tokens, refresh tokens ou claims sensíveis em logs.

A implementação deverá usar biblioteca JWT mantida e configuração explícita por ambiente. A estratégia concreta de chave (JWKS ou segredo legado) será definida após inspecionar a configuração real do projeto; não presumir o algoritmo atual.

### 4.2 Mapeamento para usuário ERP

Após validar o JWT, a API resolve `auth.users.id = token.sub` para exatamente um perfil ERP por meio de `users.auth_user_identifier`.

Requisitos:
- `users.auth_user_identifier` deve ser único e referenciar a identidade Auth correta.
- `users.identifier` permanece como chave interna do ERP; nunca deve ser comparado diretamente com `auth.uid()` sem evidência de que representam o mesmo identificador.
- Perfil ausente, duplicado, inativo ou inconsistente resulta em negação.
- A API não cria automaticamente um perfil nem vínculo durante uma requisição comum.
- A consulta de vínculo usa o identificador interno resolvido, não o `sub` diretamente, salvo quando a FK e o contrato físico forem explicitamente redesenhados.

A confirmação da FK/constraint e o fluxo de provisionamento são pré-condições, não fatos já comprovados.

## 5. Seleção e validação da empresa ativa

### 5.1 Início de sessão/contexto

A API obtém os vínculos ativos do usuário ERP autenticado no servidor.

- Zero vínculos ativos: negar acesso a recursos tenant-scoped e encaminhar ao fluxo de provisionamento/recuperação definido pelo produto.
- Um vínculo ativo: pode selecionar essa empresa automaticamente.
- Mais de um vínculo ativo: exigir seleção explícita da empresa antes de acessar recursos tenant-scoped.
- Empresa inativa, vínculo suspenso/removido ou associação inconsistente: negar.
- A seleção do cliente identifica o contexto desejado; a API sempre revalida o vínculo no servidor.

### 5.2 Troca de empresa

A troca de empresa é uma operação explícita da API. Ela não altera papéis nem cria vínculo. Cada requisição subsequente deve carregar a empresa pretendida e a API deve revalidar vínculo e estado no banco. Não reutilizar cache de autorização da empresa anterior.

A empresa ativa não será tratada como atributo confiável de armazenamento local do cliente. O backend pode emitir um identificador opaco de contexto para conveniência, mas esse identificador não substitui revalidação no banco.

## 6. Estratégia proposta de acesso ao PostgreSQL e RLS

### 6.1 Alternativas

| Alternativa | Avaliação |
|---|---|
| API usar `service_role` para consultas comuns | Rejeitada: risco de bypass de RLS e concentração excessiva de privilégio |
| API usar Supabase Data API com JWT original do usuário | Mantém RLS e reduz acesso SQL direto, mas exige solução confiável para propagar a empresa ativa a cada transação e pode dispersar controle transacional entre chamadas |
| API usar conexão PostgreSQL dedicada, role de runtime sem BYPASSRLS e contexto transacional local | **Recomendada para validação técnica:** permite agrupar validação de vínculo, contexto e operação na mesma transação; exige auditoria de roles/grants e disciplina rigorosa de pool/transactions |

### 6.2 Direção recomendada para prova técnica

Usar conexão PostgreSQL da API (direta ou via pooler em modo compatível com transação) com um login dedicado, por exemplo `erp_api_login`, sem superuser, sem `BYPASSRLS`, sem propriedade das tabelas e sem credenciais expostas aos clientes.

A role de login deverá receber apenas a capacidade estritamente necessária de assumir a role não privilegiada de aplicação/RLS (`authenticated` ou role dedicada equivalente), após confirmação dos grants e da compatibilidade com o Supabase. A role efetiva usada nas consultas deverá estar sujeita a RLS. Não conceder associação que permita assumir `service_role`, `postgres` ou roles administrativas.

**A existência, os atributos e a possibilidade segura dessa associação ainda precisam ser verificados no projeto real.** Se não for possível provar que a role efetiva está sujeita a RLS e não possui bypass, esta alternativa não poderá ser adotada.

### 6.3 Envelope transacional obrigatório

Cada requisição autenticada que acesse dados tenant-scoped executará todo o trabalho de banco dentro de uma única transação:

1. Obter uma conexão do pool.
2. Iniciar `BEGIN`.
3. Assumir a role efetiva não privilegiada definida no Gate 0.
4. Definir, exclusivamente em escopo local à transação, as claims mínimas que as funções Auth/RLS utilizadas realmente leem (identidade Auth validada e role de aplicação).
5. Definir `app.current_company_id` por `set_config(..., true)` ou mecanismo equivalente estritamente transaction-local.
6. Resolver novamente o perfil ERP e confirmar o vínculo ativo para a empresa solicitada dentro dessa mesma transação.
7. Resolver papéis em `company_user_roles` e as permissões aplicáveis.
8. Executar o caso de uso e suas leituras/escritas sob o mesmo contexto.
9. Registrar auditoria transacional quando aplicável.
10. Fazer `COMMIT`; em qualquer erro, `ROLLBACK`.
11. Devolver a conexão ao pool somente após término confirmado da transação.

O terceiro argumento `true` de `set_config` é essencial: o valor deve equivaler a `SET LOCAL` e desaparecer no fim da transação. É proibido usar `SET`/configuração persistente de sessão para identidade ou tenant.

Todas as queries de negócio daquela requisição devem usar a mesma conexão/transação. Não executar consultas tenant-scoped fora do envelope. Não manter transação aberta durante chamadas HTTP externas, processamento demorado ou espera do usuário.

### 6.4 Pool e isolamento entre requisições

- Nunca confiar em estado de sessão deixado por requisição anterior.
- Inicializar explicitamente todos os valores de contexto em cada transação.
- Em falha de `COMMIT`, `ROLLBACK` ou limpeza, descartar a conexão se o estado transacional for incerto.
- Testar reutilização sequencial e concorrente da mesma conexão entre usuários e empresas diferentes.
- Se for utilizado pooler em modo transaction pooling, comprovar que todo o contexto é transaction-local e que nenhuma operação depende de estado de sessão.
- Prepared statements, timeouts e cancelamento devem ser compatíveis com o pooler escolhido.
- Usar TLS e armazenar credenciais apenas no gerenciador de segredos do ambiente de execução.

### 6.5 RLS

As policies devem verificar cumulativamente:
- identidade Auth válida representada no contexto transacional;
- empresa da linha igual à empresa de contexto;
- vínculo ativo entre a identidade e a empresa;
- autorização de ação e escopo aplicáveis, quando a granularidade da policy exigir.

O GUC de empresa não é prova autônoma de autorização. Policies/helpers devem verificar o vínculo e negar por padrão. A API também valida o vínculo antes do caso de uso. A classificação de tabelas globais, tenant-scoped e associadas indiretamente deve anteceder a criação de policies.

Não usar policies com predicado universal `true` como substituto do contrato tenant. Revisar grants e policies em conjunto. Avaliar FORCE RLS, ownership e privilégios efetivos por tabela; habilitar FORCE RLS somente após análise de impacto e testes, não por aplicação indiscriminada.

## 7. Autorização na API

A autorização é composta por:
1. identidade Auth validada;
2. perfil ERP ativo;
3. vínculo ativo na empresa de contexto;
4. papéis do vínculo em `company_user_roles`;
5. permissões efetivas conforme catálogo/versão e exceções definidas;
6. restrições do caso de uso: propriedade/autoria, cliente do atendimento, estado, relacionamento e módulo ativo.

A semântica final de união de múltiplos papéis e exceções deve seguir o modelo de autorização aprovado/consolidado e ser transformada em algoritmo testável antes de implementação. Não inferir autorização a partir de telas, módulos visíveis ou `company_admin`.

O backend deve negar por padrão e verificar autorização em cada operação. Cache de autorização, se futuramente necessário, terá TTL curto, chave incluindo usuário+empresa+versão e invalidação após alteração de vínculo/papel/permissão; não é necessário para a primeira versão.

## 8. Tratamento de operações privilegiadas

Operações administrativas que precisem ultrapassar RLS não serão misturadas ao fluxo normal. Para cada operação excepcional será exigido:
- finalidade e caso de uso documentados;
- identidade humana/técnica e autorização específica;
- função/role mínima, isolada e sem endpoint genérico de SQL;
- validação explícita de empresa e escopo;
- auditoria imutável de ator, justificativa, recurso e resultado;
- testes negativos e revisão antes da liberação.

Nenhuma operação privilegiada é definida ou autorizada por esta proposta.

## 9. Contrato mínimo da API

- O cliente envia access token Supabase no mecanismo padrão `Authorization: Bearer`.
- A empresa pretendida é indicada por campo/rota de contexto documentado, por exemplo `X-Company-Context`; o nome final do contrato HTTP permanece pendente.
- A API nunca repassa esse valor diretamente como autorização nem o usa em SQL sem validação.
- Respostas distinguem não autenticado (401), não autorizado/sem vínculo (403) e recurso inexistente conforme política de não divulgação (404).
- Não retornar detalhes internos de policy, SQL, claims ou segredos.
- Toda operação de escrita offline deverá futuramente definir idempotency key e escopo empresa+usuário; o desenho detalhado pertence à especificação de sincronização.

## 10. Testes obrigatórios

### 10.1 Identidade
- JWT válido do projeto e usuário com perfil único: permitido.
- Token expirado, assinatura inválida, issuer/audience incorretos ou algoritmo inesperado: 401.
- Perfil ERP ausente, duplicado, inativo ou mapeamento Auth divergente: negado.
- UUID de outro usuário enviado no body/header: ignorado como identidade ou rejeitado.

### 10.2 Empresa e autorização
- Zero vínculo ativo: negado.
- Um vínculo ativo: seleção automática.
- Múltiplos vínculos: seleção explícita obrigatória.
- Empresa solicitada sem vínculo ou inativa: 403.
- Vínculo suspenso entre requisições: próxima operação negada.
- Usuário A/empresa A tentando acessar empresa B: negado.
- Papéis resolvidos apenas de `company_user_roles`; valor legado em `company_users.role_identifier` não altera resultado.
- Permissão ausente, exceção de negação ou módulo inativo: negado conforme semântica aprovada.
- Isa fora do cliente/autoria permitidos ou tentando excluir: negado.

### 10.3 RLS e pool
- Consultar linha de outra empresa por ID conhecido: nenhuma linha retornada/negado.
- Inserir ou alterar linha com tenant diferente do contexto: negado.
- Relacionar entidades de empresas distintas: constraint/policy rejeita.
- Fazer requisição empresa A e depois empresa B na mesma conexão reutilizada: nenhum contexto residual.
- Executar requisições concorrentes de usuários/empresas diferentes: sem cruzamento de contexto.
- Forçar erro/rollback e reutilizar conexão: sem contexto residual.
- Conectar com role de runtime: confirmar ausência de superuser, BYPASSRLS, ownership e acesso a `service_role`.
- Acessar banco diretamente sem contexto: negado para tabelas tenant-scoped.

Todos os testes devem usar fixtures sintéticas em ambiente não produtivo.

## 11. Gates e sequência de execução

| Gate | Entrega | Critério para avançar |
|---|---|---|
| G0 — Reconciliação | Inventário atual de Auth, users, vínculos, roles, grants, RLS e pooler | Evidências reais e baseline restaurável revisado |
| G1 — Prova de identidade | JWT → `auth.users.id` → `users.auth_user_identifier` → `users.identifier` | Mapeamento único e fluxo de provisionamento comprovados |
| G2 — Prova de conexão | Role de runtime e transação local | Sem bypass; RLS aplicado; contexto isolado em pool |
| G3 — Contrato tenant | Helpers e policies por classificação de tabela | Matriz por tabela revisada e testes negativos aprovados |
| G4 — Autorização | Resolução de papéis/permissões e limites da Isa | Algoritmo especificado e testes aprovados |
| G5 — Implementação | Guard/interceptor e camada transacional NestJS | Revisão, pipeline e evidências automatizadas |

Nenhum gate autoriza automaticamente DDL/DML no Supabase. Alterações no banco exigem backup restaurável, ensaio não produtivo, migration revisada, autorização operacional específica e verificação posterior. A API não deve ser conectada a produção antes da aprovação dos gates relevantes.

## 12. Decisões solicitadas ao responsável

Esta proposta recomenda aprovar a direção de conexão direta da API ao PostgreSQL por role dedicada sem BYPASSRLS, com identidade Auth e empresa ativa propagadas exclusivamente por contexto local à transação, após prova técnica no ambiente não produtivo.

Solicita-se validar:
1. JWT Supabase validado na API e mapeamento para perfil ERP via `users.auth_user_identifier`.
2. Empresa ativa enviada como contexto solicitado, mas sempre revalidada pelo servidor.
3. Conexão PostgreSQL com role dedicada sem BYPASSRLS, sem service_role no caminho normal.
4. Contexto de identidade e empresa configurado somente transaction-local e validado no mesmo envelope transacional.
5. RLS como enforcement no banco, com backend como segunda barreira e sem acesso direto dos clientes ao banco.
6. Manter todos os gates de auditoria, baseline restaurável, ensaio e autorização específica antes de alterações reais.

A aprovação desta proposta não autoriza implementar nem alterar o Supabase. Se aprovada, o próximo trabalho será produzir a prova de conceito e o plano de reconciliação, começando por G0 e G1, sem alterar produção.
