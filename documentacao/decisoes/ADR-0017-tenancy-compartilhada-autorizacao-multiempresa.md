# ADR-0017 — Tenancy compartilhada e autorização multiempresa

**Identificador:** ADR-0017  
**Estado:** APROVADA  
**Data da proposta:** 2026-11-08  
**Data da aprovação:** 2026-11-08  
**Responsável pela decisão:** Responsável pelo projeto  
**ADR relacionado/substituído:** ADR-0001, ADR-0002, ADR-0011 e ADR-0012. Não substitui nenhuma decisão.

## 1. Contexto

O ERP Winservice é um produto SaaS multiempresa (ADR-0001) e utiliza o Supabase como fonte central de verdade (ADR-0002). A estrutura existente contém entidades de usuários, empresas, vínculos, papéis e permissões, mas sua existência não comprova isolamento e autorização corretos.

É necessário formalizar a estratégia de tenancy, a identidade autenticada, os vínculos multiempresa, a seleção da empresa ativa e as barreiras de autorização e integridade.

## 2. Problema ou necessidade

Definir uma arquitetura que permita a uma mesma identidade operar em várias empresas sem misturar seus contextos nem permitir acesso ou alteração de dados fora do escopo autorizado. A autorização não pode depender exclusivamente da interface, de parâmetros enviados pelo cliente ou de identificadores fornecidos pelo usuário.

## 3. Alternativas consideradas

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Banco e schema compartilhados, com isolamento por empresa | Menor complexidade operacional inicial; compatibilidade com a estrutura existente; evolução incremental | Exige rigor em RLS, constraints, funções, APIs e testes |
| Schema separado por empresa | Maior separação lógica | Aumenta a complexidade de migrações, manutenção e compartilhamento de recursos |
| Banco separado por empresa | Maior isolamento operacional | Aumenta custos e complexidade de provisionamento, observabilidade, backup e manutenção |

A alternativa de banco e schema compartilhados foi aprovada.

## 4. Decisão

### 4.1. Estratégia de tenancy

O ERP utilizará um banco PostgreSQL compartilhado e um schema compartilhado para os dados do ERP. O isolamento lógico será realizado por empresa, utilizando `company_identifier` nas entidades aplicáveis.

A arquitetura não utilizará, como padrão da V1, banco ou schema independente para cada empresa.

A utilização de infraestrutura compartilhada não concede a uma empresa acesso aos dados de outra.

### 4.2. Identidade autenticada e usuário ERP

O modelo distinguirá:

- `auth.users.id`: identificador da identidade autenticada.
- `users.identifier`: identificador interno do usuário no ERP.
- `users.auth_user_identifier`: relacionamento entre o usuário ERP e a identidade autenticada.

O vínculo entre identidade autenticada e perfil interno deverá ser validado pelo backend e pelos mecanismos de autorização. O identificador interno do ERP não deverá ser confundido com o identificador de autenticação.

### 4.3. Vínculo de usuários com empresas

Um mesmo usuário ERP poderá possuir vínculos com várias empresas. Cada vínculo será independente e terá seu próprio contexto de autorização, incluindo empresa associada, situação, papéis, permissões e eventuais exceções individuais autorizadas.

O acesso dependerá cumulativamente de:

1. Identidade autenticada válida.
2. Vínculo ativo com a empresa.
3. Permissão para executar a operação solicitada.
4. Disponibilidade e ativação dos módulos envolvidos, quando aplicável.
5. Cumprimento das demais regras de negócio e segurança.

O simples conhecimento ou envio de um `company_identifier` não concede acesso à empresa.

### 4.4. Seleção da empresa ativa na sessão

A empresa em cujo contexto o usuário está operando deverá ser identificada na sessão autenticada.

- **Nenhum vínculo ativo:** o usuário não poderá acessar o ambiente operacional de nenhuma empresa.
- **Um único vínculo ativo:** a empresa poderá ser selecionada automaticamente como contexto da sessão.
- **Mais de um vínculo ativo:** o usuário deverá selecionar explicitamente qual empresa deseja utilizar antes de acessar o ambiente operacional.

A seleção será associada à sessão/dispositivo em uso. A aplicação deverá apresentar claramente qual empresa está ativa.

A troca de empresa deverá ser uma ação explícita. As operações seguintes utilizarão o novo contexto, sem reaproveitar autorização ou dados de contexto da empresa anterior.

A seleção feita pelo usuário não é uma concessão de acesso. O backend deverá validar se a identidade autenticada possui vínculo ativo e permissões na empresa selecionada. O identificador enviado pelo aplicativo ou armazenado no dispositivo não constitui, isoladamente, prova de autorização.

O mecanismo técnico definitivo para transportar e validar o contexto entre aplicação, autenticação, backend e banco será detalhado na especificação de implementação.

### 4.5. RLS e backend como barreiras complementares

**Banco de dados — RLS**

- Tabelas com dados protegidos deverão possuir políticas RLS compatíveis com o modelo de tenancy.
- As políticas deverão validar identidade autenticada e contexto de empresa autorizado.
- Consultas e operações não poderão ultrapassar os limites do vínculo ativo.
- A integridade entre registros de empresas diferentes deverá ser protegida também por constraints e relacionamentos adequados.

**Backend/API**

- Validará identidade, empresa ativa, vínculo, permissões e regras de negócio.
- Impedirá operações fora do contexto autorizado.
- Não confiará exclusivamente em identificadores ou parâmetros enviados pelo cliente.
- Controlará operações privilegiadas e impedirá que funções ou serviços contornem as regras de autorização.

RLS e backend são mecanismos complementares e ambos deverão participar da proteção.

### 4.6. Integridade referencial entre empresas

As relações entre entidades pertencentes a empresas deverão preservar a consistência do tenant. Quando aplicável, serão utilizadas chaves estrangeiras compostas envolvendo `company_identifier` e o identificador da entidade relacionada.

Uma visita da empresa A, por exemplo, não poderá estar relacionada a uma ordem de serviço da empresa B, mesmo que ambos os identificadores existam.

A integridade não deverá depender exclusivamente de validações da aplicação.

### 4.7. Classificação dos dados

O modelo distinguirá:

| Categoria | Regra |
|---|---|
| Dados globais da plataforma | Compartilhados conforme sua natureza e autorização |
| Dados pertencentes a uma empresa | Isolados pelo respectivo `company_identifier` |
| Dados associados indiretamente a uma empresa | Devem preservar o isolamento por relacionamentos e validações |
| Dados de infraestrutura | Utilizam identidade técnica e escopo próprio, sem acesso indiscriminado aos dados operacionais |

A classificação detalhada de todas as tabelas deverá ocorrer antes da implementação física.

### 4.8. Agentes e identidades técnicas

Agentes de IA e serviços de infraestrutura deverão possuir identidades e escopos próprios.

- Um agente não poderá ampliar permissões por meio de prompts ou parâmetros fornecidos pelo modelo.
- Agentes respeitarão os limites globais da plataforma e as permissões concedidas pela empresa.
- Workflows e integrações técnicas não serão confundidos com usuários humanos ou agentes de negócio.
- Serviços técnicos utilizarão identidades próprias e privilégios mínimos.
- A chave `service_role` não poderá ser disponibilizada em aplicações cliente.
- Funções privilegiadas deverão possuir controles explícitos de autenticação, autorização e escopo.

Permanecem integralmente aplicáveis as restrições da ADR-0012 à Isa.

### 4.9. Catálogo de permissões

A autorização respeitará a ADR-0011: catálogo padrão global versionado; catálogo próprio por empresa; adesão voluntária às versões globais; preservação das customizações locais; histórico e possibilidade de restauração dos padrões.

Esta ADR não define a matriz definitiva de papéis, a precedência entre múltiplos papéis ou a semântica completa de concessões e negações individuais.

## 5. Justificativa

A estratégia compartilhada atende à direção SaaS multiempresa e permite evolução incremental da estrutura existente. A separação lógica por empresa reduz a complexidade operacional inicial, mas exige controles rigorosos no banco e no backend.

A seleção explícita da empresa ativa permite que um usuário opere em diferentes empresas com a mesma identidade autenticada, mantendo um contexto operacional inequívoco por sessão. RLS, backend e integridade referencial reduzem a dependência de uma única camada de proteção.

## 6. Consequências e impactos

### Benefícios esperados

- Uma identidade poderá operar em diferentes empresas sem contas independentes.
- O contexto operacional ficará explícito na sessão.
- Os dados serão isolados logicamente por empresa.
- As aplicações compartilharão infraestrutura sem compartilhar autorização.
- O modelo será compatível com a evolução SaaS do ERP.

### Custos, riscos e limitações

- Uma falha de RLS poderá comprometer o isolamento se não houver validação complementar.
- A seleção da empresa não poderá ser tratada como autorização.
- Será necessária revisão das FKs, constraints e políticas atuais.
- Serviços privilegiados exigirão controles específicos.
- A migração poderá exigir correções de dados e relacionamentos.

### Impactos operacionais

- A aplicação deverá identificar e apresentar a empresa ativa.
- Usuários com múltiplos vínculos ativos deverão selecionar uma empresa.
- A troca de empresa deverá alterar o contexto de autorização da sessão.
- A revogação ou suspensão de um vínculo deverá impedir novas operações naquele contexto.

### Dependências

Supabase Auth; PostgreSQL e RLS; backend/API; estruturas de usuários e vínculos; catálogo de permissões da ADR-0011; regras de autonomia da ADR-0012.

## 7. Segurança, privacidade e isolamento multiempresa

São requisitos obrigatórios:

1. Usuário sem autenticação não poderá acessar dados protegidos.
2. Usuário sem vínculo ativo não poderá acessar uma empresa.
3. Usuário vinculado à empresa A não poderá acessar dados da empresa B sem vínculo e autorização válidos.
4. O cliente não poderá trocar o tenant de um registro por meio de UPDATE direto.
5. Relacionamentos entre registros de empresas diferentes deverão ser rejeitados.
6. A suspensão do vínculo deverá bloquear novas operações.
7. Funções privilegiadas deverão validar identidade, autorização e contexto.
8. A troca de empresa deverá impedir o reaproveitamento indevido do contexto anterior.
9. O isolamento deverá ser testado com identidades e empresas distintas.
10. Operações relevantes deverão manter trilha de auditoria.

A aprovação desta ADR não representa certificação de segurança nem comprova implementação.

## 8. Implementação e validação

### 8.1. Etapas previstas

1. Auditar novamente o schema atual.
2. Classificar entidades em globais, pertencentes a empresas ou indiretamente associadas.
3. Revisar o relacionamento entre `auth.users` e `users`.
4. Revisar os vínculos em `company_users`.
5. Definir o contrato de identificação da empresa ativa na sessão.
6. Projetar e revisar as políticas RLS.
7. Corrigir e complementar FKs e constraints compostas.
8. Revisar funções, triggers e operações privilegiadas.
9. Implementar validação de autorização no backend/API.
10. Criar testes automatizados de isolamento e autorização.
11. Executar os testes antes de liberar o modelo para produção.

### 8.2. Critérios mínimos de aceitação

| Cenário | Resultado esperado |
|---|---|
| Usuário sem sessão acessa dado protegido | Negado |
| Usuário sem vínculo tenta acessar empresa | Negado |
| Usuário com um vínculo ativo entra no sistema | Empresa selecionada como contexto |
| Usuário com múltiplos vínculos ativos entra no sistema | Seleção obrigatória da empresa |
| Usuário seleciona empresa sem vínculo ativo | Negado |
| Usuário da empresa A consulta dados da empresa B | Negado |
| Usuário cria registro em empresa não autorizada | Negado |
| Usuário troca contexto para outra empresa à qual pertence | Permitido após validação |
| Usuário reutiliza autorização da empresa anterior | Negado |
| Usuário relaciona registros de empresas diferentes | Negado |
| Vínculo é suspenso durante a utilização | Novas operações no vínculo suspenso são negadas |
| Serviço técnico sem escopo tenta executar operação | Negado |

### 8.3. Estado da implementação

A aprovação desta ADR estabelece a decisão arquitetural, mas não significa que a implementação esteja concluída. A auditoria estrutural anterior identificou inconsistências relevantes em RLS, relacionamentos e isolamento multiempresa. O estado real deverá ser novamente verificado antes de qualquer implementação.

### 8.4. Limite de autorização

Esta ADR não autoriza alterações no Supabase, criação ou alteração de tabelas, políticas RLS, funções, triggers, execução de migrations ou alteração de dados. Essas atividades dependerão de especificação técnica, revisão e aprovação próprias.

## 9. Referências e evidências

- `documentacao/01_PROJECT_CHARTER.md`
- `documentacao/02_DECISOES_ARQUITETURA.md`
- `documentacao/decisoes/ADR-0001-direcao-do-produto.md`
- `documentacao/decisoes/ADR-0002-fonte-de-verdade.md`
- `documentacao/decisoes/ADR-0011-catalogo-permissoes-global-versionado.md`
- `documentacao/decisoes/ADR-0012-autonomia-isa-operacoes-permitidas.md`
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`
- Auditoria estrutural do Supabase realizada em 2026-11-08, somente leitura; deverá ser atualizada antes da implementação.

## 10. Revisão e substituição

- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

Mudanças substantivas futuras deverão ser registradas em novo ADR.

## Histórico desta ficha

- 2026-11-08 — criação da ficha como APROVADA após aprovação explícita do responsável. Incorpora a seleção obrigatória da empresa quando o usuário possuir múltiplos vínculos ativos.
- 2026-11-08 — proposta revisada após deliberação dos três pontos arquiteturais.
