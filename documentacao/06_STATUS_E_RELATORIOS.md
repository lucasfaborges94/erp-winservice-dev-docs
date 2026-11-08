# Status do desenvolvimento e relatórios periódicos — ERP Winservice

**Versão:** 0.6.0  
**Data-base:** 2026-11-07

## 1. Resumo executivo

O projeto está em fase de definição arquitetural, organização de governança e consolidação da fundação de dados. Já existe implementação relevante no Supabase, mas seu estado precisa ser comparado com requisitos e auditado antes de declarar módulos concluídos.

A prioridade da primeira versão é Operação, com substituição gradual por módulos.

A documentação passou a incluir um protocolo de fonte da verdade e um procedimento de continuidade independente de conta, fornecedor de IA ou profissional. O repositório é a referência documental versionada; o estado efetivamente implementado deve ser verificado no código, nas migrações e nos ambientes correspondentes.

## 2. Estado conhecido

| Frente | Estado conhecido | Próxima verificação |
|---|---|---|
| Project Charter | Decisões centrais registradas; pacote precisa de revisão formal | Aprovar baseline e completar lacunas |
| Arquitetura SaaS | Direção multiempresa aprovada | Validar tenancy, isolamento e provisionamento |
| Supabase | Auditoria técnica somente leitura realizada em 2026-11-07; relatório versionado | Priorizar revisão de RLS, grants, função de ingestão, integridade multiempresa e trigger de visitas |
| Permissões | Catálogo global versionado e personalizações por empresa definidos | Auditar implementação e políticas |
| Isa | Limites de autonomia definidos | Testar autorização no backend/API |
| Operação | Prioridade V1 | Fechar processos, estados, requisitos e aceite |
| Atendimento | Chatwoot transitório | Mapear dependências e plano de substituição |
| WeWeb | Direção aprovada | Definir ambiente, autenticação e contratos de API |
| FlutterFlow | Direção aprovada | Especificar sync offline-first e GPS background |
| Jurídico | Módulo amplo aprovado; primeira funcionalidade documental | Elaborar arquitetura funcional e requisitos |
| Módulos do ERP | Regra geral de ativação em dois níveis definida na proposta documental v0.1.4; primeiro usuário humano como superadmin; agentes de IA proibidos de ocupar superadmin | Especificar enforcement transversal, interrupção segura de operações em andamento e recuperação do superadmin |
| ADRs | Índice e fichas individuais em consolidação documental | Verificar integridade de todas as fichas e referências |
| Continuidade | Protocolo universal e modelo de transferência registrados | Aplicar ao final de cada sessão relevante |

## 3. Relatório de auditoria Supabase — 2026-11-07\n\nFoi realizada auditoria técnica somente leitura no projeto Supabase Winservice (`voaovwphncwbrwtcqnsc`), schema `public`. O relatório completo está em [Auditoria técnica do Supabase — 2026-11-07](relatorios/2026-11-07-auditoria-tecnica-supabase.md).\n\nPrincipais achados registrados:\n- policies RLS sem restrição por empresa em entidades relevantes;\n- privilégios de tabela amplos para `anon` e `authenticated`, cujo impacto efetivo depende de RLS e do papel;\n- função `authorize_integration_sync_event` como `SECURITY DEFINER`, executável por papéis públicos e sem validação de autenticidade identificada no corpo inspecionado;\n- lacunas de integridade referencial entre entidades multiempresa;\n- incompatibilidade entre a função `set_visit_address_snapshot` e as colunas existentes em `addresses`;\n- alertas de search_path, índices duplicados e FKs sem índice de cobertura.\n\nNenhuma alteração foi realizada no banco. O relatório separa achados confirmados, riscos e itens não verificados. As recomendações ainda não foram executadas.\n\n## 4. Artefatos técnicos localizados

- inventario_supabase.json — criado em 2026-11-06.
- auditoria_estrutural_supabase.json — criado em 2026-11-06.

Esses artefatos devem acompanhar a base do Projeto como evidência técnica. Antes de gerar novas migrações, confirmar se representam o estado atual do ambiente.

## 5. Registro periódico — 2026-11-07

### Trabalho consolidado anteriormente
- Definição do ERP como SaaS multiempresa, inicialmente para Winservice.
- Consolidação das decisões de stack e arquitetura.
- Registro de disponibilidade-alvo de 99,5%, RPO 1h, RTO 4h e retenção de backups diários de 30 dias.
- Definição de Operação como prioridade da primeira versão e substituição gradual por módulos.
- Definição do catálogo de permissões global versionado com adesão voluntária e preservação de personalizações por empresa.
- Definição dos limites de autonomia da Isa.
- Definição do módulo Jurídico amplo, com Gestão de Documentos e Instrumentos Jurídicos como primeira funcionalidade.
- Definição de documentação oficial e rascunho separados.

### Entregáveis desta iniciativa de continuidade
- Protocolo de fonte da verdade independente de fornecedor, conta ou profissional.
- Procedimento de encerramento e retomada de sessões.
- Modelo de registro de sessão com commits, artefatos, testes, pendências e próximos passos.
- Integração dos procedimentos ao leia-me e à governança documental.

**Evidência Git:** a atualização documental foi publicada na branch main. O último commit desta iniciativa é cd5ac9d39d6d3600e10977387bcb4e24768fe389. Os commits individuais dos cinco arquivos iniciais estão relacionados em documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md.

### Pendências prioritárias
1. Conferir links relativos e presença de todos os 14 ADRs individuais.
2. Auditar consistência entre Charter, índice de decisões, fichas ADR, roadmap, escopo e governança.
3. Auditar o inventário e a auditoria Supabase de 2026-11-06.
4. Produzir matriz de conformidade do banco: requisito × objeto existente × situação × ação.
5. Consolidar mapa de processos operacionais e máquina de estados.
6. Fechar escopo da primeira entrega operacional.
7. Executar auditoria de consistência documental e registrar os achados.

### Riscos e observações
- A existência de tabelas ou políticas no banco não comprova, isoladamente, que o requisito esteja correto ou seguro.
- RLS habilitada não significa que as políticas estejam adequadas; testar cenários positivos e negativos.
- A documentação atual contém decisões e propostas; itens não confirmados estão marcados como pendentes.
- Não iniciar mudanças estruturais com base apenas em nomes de tabelas ou snippets: consultar inventário e definições completas.

## 6. Modelo para relatórios futuros

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
- Commits, PRs e evidências.

## 7. Status por módulo

Usar apenas estes estados:
- NOT_STARTED
- DISCOVERY
- SPECIFICATION
- IN_DEVELOPMENT
- IN_REVIEW
- BLOCKED
- DONE
- DEPRECATED

Não marcar DONE sem critérios de aceite e evidências.

## Histórico de alterações

- 2026-11-07 — v0.6.0: generaliza o controle de ativação em dois níveis para todos os módulos; registra primeiro usuário humano como superadmin e proíbe agentes de IA como superadmin.
- 2026-11-07 — v0.5.0: registra responsáveis pelos controles do Módulo de IA, mínimo de um superadmin por empresa e bloqueio de execuções quando o módulo não estiver ativo.
- 2026-11-07 — v0.4.0: registra a definição conceitual do Módulo de IA com disponibilidade global pela plataforma e ativação individual por empresa.
- 2026-11-07 — v0.3.0: inclusão do relatório de auditoria técnica somente leitura do Supabase e atualização do estado conhecido.\n- 2026-11-07 — v0.2.0: registro do protocolo de fonte da verdade e da continuidade entre ferramentas e profissionais.
- 2026-11-07 — v0.1.0: baseline inicial do status do projeto.
