# ERP Winservice — Contexto atual e índice operacional

**Documento:** índice operacional resumido  
**Data de referência:** 2026-11-08  
**Branch de referência:** `main`  
**Repositório:** [lucasfaborges94/erp-winservice-dev-docs](https://github.com/lucasfaborges94/erp-winservice-dev-docs)

> Este arquivo acelera a recuperação de contexto. Não é fonte normativa e não substitui a Fonte da Verdade, ADRs, especificações, código, banco, migrações ou evidências. Antes de cada resposta sobre o ERP, consulte novamente o repositório e valide as referências abaixo.

## 1. Protocolo obrigatório de consulta

1. Ler [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md).
2. Ler [PROTOCOLO_DE_ATUACAO_IA_GITHUB.md](PROTOCOLO_DE_ATUACAO_IA_GITHUB.md).
3. Consultar este índice e [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md).
4. Verificar HEAD/commits atuais e mudanças posteriores ao último registro.
5. Consultar documentos especializados e ADRs individuais relacionados à mensagem atual.
6. Responder somente após validar as fontes.
7. Registrar incrementalmente pontos-chave e sessões relevantes.

A consulta é obrigatória antes de cada resposta, não apenas no início de uma conversa. Se o GitHub estiver inacessível, informar a limitação e não usar a conversa como substituta da documentação vigente.

## 2. Estado documental conhecido

- O repositório é a memória documental oficial e persistente do projeto.
- A consulta por mensagem e o registro incremental de sessões foram aprovados em 2026-11-08.
- A etapa do projeto permanece em consolidação de arquitetura, fundação multiempresa, autorização e especificação operacional. Não presumir que a implementação esteja concluída.
- A prioridade registrada do produto é Operação primeiro, com evolução modular.
- O status detalhado e as limitações devem ser confirmados em [06_STATUS_E_RELATORIOS.md](06_STATUS_E_RELATORIOS.md).

## 3. Decisões e referências críticas

- [02_DECISOES_ARQUITETURA.md](02_DECISOES_ARQUITETURA.md) é somente o índice; consultar fichas individuais.
- [ADR-0017](decisoes/ADR-0017-*) — consultar a ficha individual para tenancy e autorização multiempresa.
- [ADR-0018](decisoes/ADR-0018-company-user-roles-fonte-unica-de-papeis.md) — aprovada: `company_user_roles` é a fonte única de atribuição de papéis por vínculo usuário–empresa; `company_users.role_identifier` e seu valor legado ficam fora do ERP, sem reconciliação ou migração; remoção física depende do tratamento de dependências.
- [MODELO_FUNCIONAL_OPERACIONAL_MVP.md](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) — modelo funcional da Operação; conferir versão atual.
- [MODELO_LOGICO_DADOS_OPERACAO.md](MODELO_LOGICO_DADOS_OPERACAO.md) — proposta lógica; não presumir aprovação como modelo físico.
- [BASE_CONHECIMENTO_ERP.md](BASE_CONHECIMENTO_ERP.md) — arquitetura conceitual aprovada da Base de Conhecimento; conferir escopo e pendências atuais.

## 4. Pendências técnicas documentadas na última continuidade consultada

A última parte da continuidade consultada registra como próxima frente preparar a especificação executável e o pacote de migração do catálogo de permissões, após:
- inventariar dependências de `permissions`, `role_permissions` e `user_permissions`;
- validar o registro legado;
- definir códigos estáveis;
- identificar repositório e baseline oficiais de migrations;
- resolver pontos técnicos ainda abertos na proposta física.

Também permanecem frentes registradas no histórico de continuidade, incluindo a fundação multiempresa e dependências da ADR-0018. Revalidar tudo contra o HEAD e os documentos específicos antes de agir; esta lista é um atalho, não uma declaração de status atual.

## 5. Decisão mais recente — códigos de permissão

Em 2026-11-08, foi aprovada a convenção semântica `entidade.ação` para `permission_code`, em minúsculas e independente dos nomes físicos das tabelas. O registro legado `customer/view` deverá ser mapeado para `customer.view`, sujeito à validação no plano de migração. A aprovação não define a matriz completa de permissões nem autoriza alterações no Supabase. Especificação: [proposta física do catálogo de permissões v0.4.0](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md).

## 6. Repositórios do projeto

- Repositório documental oficial: `lucasfaborges94/erp-winservice-dev-docs`.
- Em 2026-11-08, o responsável aprovou a criação de um repositório separado de código-fonte com o nome `lucasfaborges94/erp-winservice-app`, visibilidade privada e branch principal `main`.
- **Estado verificado em 2026-11-08:** repositório criado e acessível pela integração GitHub; privado, branch padrão `main`, proprietário `lucasfaborges94`, permissão da integração `admin`.
- O repositório está vazio: API de conteúdo retornou 404 com mensagem `This repository is empty` e a listagem de commits retornou HTTP 409 (`Git Repository is empty`). Não há código, arquivos, workflows ou baseline Git de migrations para auditar.
- Próximo passo: definir a estrutura inicial e o fluxo de migrations conforme arquitetura e stack aprovadas, sem presumir frameworks ou módulos; reconciliar o estado real do Supabase antes de criar baseline.

## 7. Índice por assunto

| Assunto | Fonte a consultar |
|---|---|
| Objetivo, escopo e limites | [01_PROJECT_CHARTER.md](01_PROJECT_CHARTER.md), [04_MODULOS_ESCOPO.md](04_MODULOS_ESCOPO.md) |
| Decisões arquiteturais | [02_DECISOES_ARQUITETURA.md](02_DECISOES_ARQUITETURA.md) e ADR individual pertinente |
| Roadmap e prioridades | [03_ROADMAP_ETAPAS.md](03_ROADMAP_ETAPAS.md) |
| Stack e ambientes | [05_STACK_RECURSOS.md](05_STACK_RECURSOS.md) |
| Status e relatórios | [06_STATUS_E_RELATORIOS.md](06_STATUS_E_RELATORIOS.md) |
| Aprovação, versionamento e governança | [07_GOVERNANCA_DOCUMENTAL.md](07_GOVERNANCA_DOCUMENTAL.md) |
| Sessões e retomada | [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md) |
| Autorização e isolamento multiempresa | [MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md](MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md) |
| Operação — requisitos | [MODELO_FUNCIONAL_OPERACIONAL_MVP.md](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) |
| Operação — dados | [MODELO_LOGICO_DADOS_OPERACAO.md](MODELO_LOGICO_DADOS_OPERACAO.md) |
| Base de Conhecimento | [BASE_CONHECIMENTO_ERP.md](BASE_CONHECIMENTO_ERP.md) |
| Migração multiempresa | [relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md](relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md) |
| Catálogo de permissões | [relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md) |

## 8. Registro incremental de sessões

- Registrar aprovações, decisões, requisitos, correções, descobertas, mudanças de estado, artefatos, testes, riscos, bloqueios e próximos passos assim que forem confirmados.
- Uma sessão pode abranger várias mensagens correlatas; atualizar seu registro em vez de criar um arquivo por mensagem.
- Para marcos independentes ou sessões extensas, usar `documentacao/sessoes/AAAA-MM-DD-titulo-curto.md` e manter o índice em [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md).
- Não registrar como implementado algo que foi apenas discutido, especificado ou aprovado.
- Não gerar commits artificiais quando não houver mudança relevante.

## 9. Última atualização

Atualizado em 2026-11-08 para registrar a aprovação do repositório privado de código-fonte `erp-winservice-app`. A criação permanece pendente de confirmação; o HEAD exato deve ser verificado no GitHub a cada consulta.


### Verificação de acesso ao repositório de aplicação — 2026-11-08

O responsável compartilhou a URL `https://github.com/lucasfaborges94/erp-winservice-app`. A consulta direta de metadados pela integração GitHub retornou HTTP 404; a listagem de repositórios acessíveis para o proprietário retornou somente `erp-winservice-dev-docs`. Portanto, o repositório de aplicação **não está visível/acessível à integração nesta verificação**. Isso não permite distinguir entre repositório inexistente e repositório privado sem acesso concedido à integração. Permanecem não verificados existência, visibilidade, branch padrão e permissões. Não iniciar estruturação nem versionamento de migrations até confirmar acesso e metadados.


### Confirmação de acesso ao repositório de aplicação — 2026-11-08

A integração GitHub passou a acessar `https://github.com/lucasfaborges94/erp-winservice-app`. Metadados confirmados: ID `1450814205`, proprietário `lucasfaborges94`, visibilidade `private`, branch padrão `main`, repositório não arquivado e permissão `admin` para a integração. O tamanho informado é zero. A consulta de conteúdo da branch `main` retornou HTTP 404 com a mensagem `This repository is empty`; a listagem de commits retornou HTTP 409 (`Git Repository is empty`). Portanto, a existência, privacidade, branch e permissão estão confirmadas, mas ainda não há arquivos, commits, código, workflow ou baseline de migrations. Nenhum arquivo foi criado e nenhuma alteração foi feita no Supabase.


### Delegação operacional do Supabase e baseline estrutural — 2026-11-08

O responsável determinou que o agente atual será o operador exclusivo para executar alterações no projeto Supabase do ERP. A delegação define quem executa as operações; não elimina os gates de segurança, revisão, preflight, backup restaurável, autorização operacional específica e verificação posterior já exigidos pelos documentos e ADRs. Não interpretar a delegação como autorização irrestrita para qualquer DDL/DML.

O repositório de aplicação `lucasfaborges94/erp-winservice-app` foi inicializado com:
- `supabase/README.md`;
- `supabase/baseline/2026-11-08-schema-inventory.json`;
- `supabase/baseline/2026-11-08-advisors.json`.

O inventário foi capturado em modo somente leitura no projeto `voaovwphncwbrwtcqnsc`, PostgreSQL `17.6.1.141`, região `sa-east-1`. Ele registra metadados do schema `public`, sem dados de linhas ou segredos: 38 tabelas, 461 colunas, 206 constraints, 110 índices, 11 funções, 18 triggers, 23 policies, 34 sequences, 8 extensões instaladas e 1.064 concessões de privilégios de tabela.

Contagens pontuais confirmadas por SQL somente leitura: 26 visitas, 103 clientes, 99 endereços, 1 empresa, 1 vínculo em `company_users`, 2 associações em `company_user_roles`, 3 papéis, 1 permissão, zero linhas em `role_permissions` e `user_permissions`. A coluna legada `company_users.role_identifier`, sua FK e a função `public.can_view_expense(uuid)` ainda existem; nenhuma foi alterada.

O Supabase retornou zero migrations e a relação `supabase_migrations.schema_migrations` não existe. Portanto, não há baseline executável de migrations. O arquivo JSON é um inventário estrutural datado, **não é um dump restaurável**. A integração disponível não oferece `pg_dump` nem Supabase CLI nesta sessão; gerar o baseline SQL completo continua pendente.

Os advisors foram registrados separadamente. Principais achados:
- Segurança: 26 tabelas com RLS habilitado sem policy; 7 funções com `search_path` mutável; 2 funções `SECURITY DEFINER` executáveis por `anon` e `authenticated`; proteção contra senhas vazadas desabilitada.
- Performance: 89 FKs sem índice de cobertura; 2 pares de índices duplicados; 12 índices sem uso observado; 1 policy com reavaliação desnecessária de função Auth por linha.

Nenhuma alteração foi feita no banco. O inventário é a referência estrutural conhecida desta captura; confirmar drift no Supabase real antes de cada alteração e atualizar o snapshot após mudanças controladas.


## Triagem de segurança Supabase — 2026-11-08

Foi realizada nova inspeção somente leitura dos Security Advisors, definições de funções e policies do schema public. O relatório detalhado está em [AUDITORIA_SEGURANCA_SUPABASE_2026-11-08.md](auditorias/AUDITORIA_SEGURANCA_SUPABASE_2026-11-08.md).

Achados prioritários: (1) `public.authorize_integration_sync_event(...)` é SECURITY DEFINER, executável por anon/authenticated, e o corpo consultado não apresentou validação de assinatura/segredo do webhook antes de inserir payload em `integration_sync_queue`; (2) policies permissivas com `USING (true)`/ `WITH CHECK (true)` em tabelas operacionais como customers, addresses, customer_contacts e visits não implementam o isolamento por empresa exigido pela ADR-0017; (3) `can_view_expense` permanece legado e fora do MVP conforme ADR-0018; (4) 26 tabelas com RLS sem policy exigem triagem individual, não sendo automaticamente exposição; (5) sete funções apresentam search_path mutável; (6) proteção contra senhas vazadas está desabilitada.

Nenhuma chamada RPC de negócio, teste de exploração ou leitura de payload/segredo foi executada. Nenhuma policy, função, grant ou configuração foi alterada. Próximo passo: revisar contrato de autenticação do webhook e produzir matriz de classificação/acesso por tabela, mantendo o dump restaurável como gate obrigatório antes de correções/migrations.


## Matriz inicial de RLS/grants/tenant — 2026-11-08

Foi consultado o catálogo PostgreSQL das 38 tabelas de public. RLS está habilitado em 38/38; FORCE RLS está desabilitado em 38/38; grants explícitos para anon e authenticated foram observados em 38/38; 26 tabelas não possuem policies; 8 tabelas têm policy com predicado literal true (em users, apenas INSERT). 21 tabelas não possuem company_identifier na captura, incluindo visits, work_orders, equipments, quotations e company_user_roles. A matriz por tabela e limitações está em [MATRIZ_RLS_GRANTS_TENANT_2026-11-08.md](auditorias/MATRIZ_RLS_GRANTS_TENANT_2026-11-08.md).

O grant amplo não ignora RLS por si só; tabelas sem policy tendem a negar acesso para papéis sujeitos a RLS. O risco mais direto está nas policies permissivas sem filtro de empresa e nas funções privilegiadas identificadas no relatório de segurança. Nenhuma alteração foi feita no banco.
