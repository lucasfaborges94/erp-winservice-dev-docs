# ADR-0019 — Arquitetura da API interna do ERP

**Identificador:** ADR-0019  
**Estado:** APROVADA  
**Data da proposta:** 2026-11-08  
**Data da aprovação:** 2026-11-08  
**Responsável pela decisão:** Responsável pelo projeto  
**ADR relacionado/substituído:** Complementa ADR-0002, ADR-0003, ADR-0004, ADR-0011, ADR-0012, ADR-0017 e ADR-0018; não substitui nenhuma decisão.

## 1. Contexto

O ERP utiliza Supabase/PostgreSQL como fonte de verdade, Supabase Auth para identidade, WeWeb como direção aprovada para o administrativo web e FlutterFlow para o aplicativo técnico offline-first. As ADRs de tenancy e autorização exigem validação no backend e no banco, com RLS como barreira complementar. A existência de serviços nativos do Supabase não elimina a necessidade de uma camada que concentre os casos de uso e regras de negócio do ERP.

## 2. Problema ou necessidade

Definir a arquitetura da API interna que atenderá os clientes do ERP, centralizar regras de negócio e autorização e permitir evolução modular sem introduzir complexidade operacional desnecessária no MVP.

## 3. Alternativas consideradas

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Acesso direto dos clientes ao Supabase como arquitetura principal | Menos componentes próprios no início | Dispersa regras entre clientes, dificulta consistência de autorização e evolução dos casos de uso |
| Monólito modular com API própria | Regras centralizadas, fronteiras de domínio e operação simples | Exige desenvolvimento e operação de backend; requer contrato seguro com Supabase/RLS |
| Microsserviços | Escala e implantação independentes por domínio | Complexidade de rede, observabilidade, consistência e operação desproporcional ao MVP |

## 4. Decisão

Adotar um **monólito modular com API própria em TypeScript usando NestJS**, inicialmente hospedado na VPS existente por meio do Easypanel.

A API exporá uma interface HTTP REST versionada, inicialmente sob `/api/v1`. WeWeb e FlutterFlow utilizarão a API ERP para operações de negócio. O Supabase permanece como PostgreSQL e fonte de verdade, e Supabase Auth permanece como provedor de identidade.

Não adotar microsserviços no MVP. A estrutura de código deverá permanecer no repositório privado `lucasfaborges94/erp-winservice-app`; não criar um terceiro repositório para a API.

### 4.1 Responsabilidades

- **API:** casos de uso, regras de negócio, validação de JWT, seleção e validação da empresa ativa, vínculo e papéis em `company_user_roles`, autorização, auditoria, idempotência e coordenação da sincronização offline do app técnico.
- **Supabase/PostgreSQL:** persistência, integridade referencial, constraints, RLS e funções de banco estritamente necessárias.
- **Supabase Auth:** identidade e ciclo de autenticação.
- **WeWeb e FlutterFlow:** apresentação, captura de dados e experiência de uso; não são autoridade para conceder acesso, selecionar arbitrariamente tenant ou impor regras de negócio.
- **Aplicativo técnico:** armazenamento local e fila de operações necessárias ao funcionamento offline, com sincronização posterior pela API. Essa sincronização interna não é integração externa.

### 4.2 Organização modular

A API será organizada por domínios, com fronteiras explícitas. A estrutura inicial poderá contemplar auth, companies, users, permissions, customers, equipments, work-orders, visits, tasks e audit, ajustada às especificações funcionais vigentes. Não criar módulos sem escopo documentado.

### 4.3 Limites do MVP

Integrações externas, conectores, Auvo, webhooks, sincronizações com terceiros, filas e infraestrutura dedicada de integração permanecem fora do MVP. Não podem ser dependências dos fluxos operacionais. Não remover automaticamente objetos legados existentes no Supabase.

## 5. Justificativa

O monólito modular centraliza a lógica de negócio e os controles de autorização, mantém separação lógica por domínio e reduz a complexidade operacional em comparação com microsserviços. NestJS fornece estrutura explícita para módulos, validação e organização do backend em TypeScript. A VPS e o Easypanel já existentes permitem iniciar a hospedagem sem introduzir uma plataforma adicional.

## 6. Consequências, riscos e limitações

- A API passa a ser componente crítico para as operações de negócio; disponibilidade, logs, monitoramento, backup de configuração e estratégia de deploy deverão ser definidos.
- O backend não poderá confiar em `company_identifier`, papel ou permissão enviados pelo cliente sem validação.
- A escolha de NestJS e REST não comprova implementação, capacidade ou desempenho; estes serão verificados por testes.
- Não estão definidos nesta ADR todos os contratos de endpoints, payloads, versionamento de DTOs, limites de requisição, política de retry ou estratégia de conflitos offline.
- Não adicionar Redis, broker, gateway separado ou microsserviços como dependências sem decisão específica.

## 7. Segurança, identidade, tenancy e RLS — gates obrigatórios

Antes de implementar endpoints de negócio ou conectar clientes, deverá existir especificação técnica revisada que defina e teste:

1. Validação de JWT do Supabase Auth e mapeamento seguro entre `auth.users.id` e o usuário ERP.
2. Seleção da empresa ativa conforme ADR-0017: seleção automática quando houver um único vínculo ativo; seleção explícita quando houver múltiplos; negação quando não houver vínculo ativo.
3. Validação no servidor do vínculo ativo, dos papéis em `company_user_roles` e das permissões aplicáveis. Não consultar nem utilizar `company_users.role_identifier`, conforme ADR-0018.
4. Contrato confiável para propagar o contexto da empresa ativa até o PostgreSQL/RLS, com escopo transacional, limpeza garantida entre operações e proteção contra reutilização de contexto entre requisições.
5. Estratégia de conexão e privilégios da API ao Supabase/PostgreSQL, sem uso indiscriminado de `service_role` e sem contornar RLS. Operações privilegiadas excepcionais deverão ser isoladas, justificadas e auditadas.
6. Reconciliação das policies atuais com ADR-0017; policies permissivas existentes não podem ser consideradas proteção suficiente.
7. Testes negativos de acesso cruzado entre empresas, troca de empresa, ausência/revogação de vínculo, permissões insuficientes, concorrência e vazamento de contexto entre requisições.
8. Aplicação dos limites da Isa definidos na ADR-0012 no backend e no banco quando aplicável.

A arquitetura aprovada não autoriza, por si só, alterações no Supabase, execução de migrations, publicação de serviços ou acesso a produção. Essas atividades continuam sujeitas aos gates de backup restaurável, revisão, preflight, autorização operacional específica e verificação posterior.

## 8. Implementação e validação

Sequência obrigatória:

1. Elaborar especificação técnica de autenticação, empresa ativa, autorização, contexto transacional e acesso ao banco.
2. Definir contratos REST, fronteiras dos módulos e política de erros/idempotência, incluindo sincronização offline interna.
3. Reconciliar o schema e as policies reais do Supabase com as ADRs vigentes; não presumir que o inventário estrutural seja um dump restaurável.
4. Estabelecer migrations versionadas e estratégia de ambientes, com backup restaurável e ensaio não produtivo antes de mudanças no banco.
5. Criar a estrutura NestJS no repositório de aplicação, testes automatizados e pipeline.
6. Implementar primeiro os fluxos do MVP de Operação, com testes unitários, integração e segurança multiempresa.
7. Implantar na VPS/Easypanel somente após critérios de aceite, revisão e validação do ambiente.

Nenhuma dessas etapas é declarada implementada por esta aprovação.

## 9. Histórico

- 2026-11-08 — proposta apresentada ao responsável.
- 2026-11-08 — aprovada explicitamente pelo responsável; direção arquitetural formalizada. Gates de segurança e implementação permanecem obrigatórios.
