# Status do desenvolvimento e relatórios periódicos — ERP Winservice

**Versão:** 0.1.0  
**Data-base:** 2026-11-07

## 1. Resumo executivo

O projeto está em fase de definição arquitetural, organização de governança e consolidação da fundação de dados. Já existe implementação relevante no Supabase, mas seu estado precisa ser comparado com requisitos e auditado antes de declarar módulos concluídos.

A prioridade da primeira versão é Operação, com substituição gradual por módulos.

## 2. Estado conhecido

| Frente | Estado conhecido | Próxima verificação |
|---|---|---|
| Project Charter | Decisões centrais registradas; pacote precisa de revisão formal | Aprovar baseline e completar lacunas |
| Arquitetura SaaS | Direção multiempresa aprovada | Validar tenancy, isolamento e provisionamento |
| Supabase | Inventário e auditoria estrutural gerados em 2026-11-06 | Revisar tabelas, FKs, constraints, funções, triggers, RLS e gaps |
| Permissões | Catálogo global versionado e personalizações por empresa definidos | Auditar implementação e políticas |
| Isa | Limites de autonomia definidos | Testar autorização no backend/API |
| Operação | Prioridade V1 | Fechar processos, estados, requisitos e aceite |
| Atendimento | Chatwoot transitório | Mapear dependências e plano de substituição |
| WeWeb | Direção aprovada | Definir ambiente, autenticação e contratos de API |
| FlutterFlow | Direção aprovada | Especificar sync offline-first e GPS background |
| Jurídico | Módulo amplo aprovado; primeira funcionalidade documental | Elaborar arquitetura funcional e requisitos |
| Documentação | Estrutura persistente criada nesta versão | Revisar e anexar ao Projeto |

## 3. Artefatos técnicos localizados

- `inventario_supabase.json` — criado em 2026-11-06.
- `auditoria_estrutural_supabase.json` — criado em 2026-11-06.

Esses artefatos devem acompanhar a base do Projeto como evidência técnica. Antes de gerar novas migrações, confirmar se representam o estado atual do ambiente.

## 4. Registro periódico — 2026-11-07

### Trabalho consolidado
- Definição do ERP como SaaS multiempresa, inicialmente para Winservice.
- Consolidação das decisões de stack e arquitetura.
- Registro de disponibilidade-alvo de 99,5%, RPO 1h, RTO 4h e retenção de backups diários de 30 dias.
- Definição de Operação como prioridade da primeira versão e substituição gradual por módulos.
- Definição do catálogo de permissões global versionado com adesão voluntária e preservação de personalizações por empresa.
- Definição dos limites de autonomia da Isa.
- Definição do módulo Jurídico amplo, com Gestão de Documentos e Instrumentos Jurídicos como primeira funcionalidade.
- Decisão de manter documentação oficial e rascunho separados.

### Entregáveis desta consolidação
- Pacote inicial de documentação do ERP.
- Estrutura de governança documental.
- Registro de decisões conhecidas.
- Mapa macro de módulos e etapas.

### Pendências prioritárias
1. Revisar e aprovar o pacote documental.
2. Auditar o inventário e a auditoria Supabase de 2026-11-06.
3. Produzir matriz de conformidade do banco: requisito × objeto existente × situação × ação.
4. Consolidar mapa de processos operacionais e máquina de estados.
5. Fechar escopo da primeira entrega operacional.
6. Completar stack/infraestrutura e ambientes.

### Riscos e observações
- A existência de tabelas ou políticas no banco não comprova, isoladamente, que o requisito esteja correto ou seguro.
- RLS habilitada não significa que as políticas estejam adequadas; testar cenários positivos e negativos.
- A documentação atual contém decisões e propostas; itens não confirmados estão marcados como pendentes.
- Não iniciar mudanças estruturais com base apenas em nomes de tabelas ou snippets: consultar inventário e definições completas.

## 5. Modelo para relatórios futuros

Cada relatório deverá conter:
- Período e data.
- Objetivos planejados.
- Trabalho realizado.
- Decisões aprovadas (IDs ADR).
- Artefatos/código/migrações alterados.
- Testes executados e resultados.
- Progresso por módulo/etapa.
- Riscos, bloqueios e desvios.
- Pendências e responsáveis.
- Próximos passos.
- Documentos oficiais atualizados.

## 6. Status por módulo

Usar apenas estes estados:
- `NOT_STARTED`
- `DISCOVERY`
- `SPECIFICATION`
- `IN_DEVELOPMENT`
- `IN_REVIEW`
- `BLOCKED`
- `DONE`
- `DEPRECATED`

Não marcar `DONE` sem critérios de aceite e evidências.
