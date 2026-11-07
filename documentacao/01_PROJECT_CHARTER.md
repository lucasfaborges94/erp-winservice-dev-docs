# Project Charter — ERP Winservice

**Documento:** Charter do Projeto  
**Versão:** 0.1.0  
**Data:** 2026-11-07  
**Estado:** consolidação inicial das decisões já tomadas; itens em aberto identificados

## 1. Visão

Construir um ERP próprio, modular e preparado para SaaS multiempresa, inicialmente utilizado pela Winservice, com arquitetura que permita evolução gradual, integração com serviços externos e futura comercialização para outras empresas.

## 2. Objetivos

- Unificar os processos administrativos, comerciais, operacionais e de atendimento.
- Ter o Supabase como fonte central de verdade dos dados.
- Reduzir dependência de fornecedores e permitir portabilidade dos dados e componentes.
- Construir uma arquitetura multiempresa segura, com isolamento de dados.
- Automatizar fluxos de trabalho e permitir participação controlada de agentes de IA.
- Substituir gradualmente as ferramentas administrativas atuais por módulos próprios.
- Manter trilha de auditoria e rastreabilidade das operações relevantes.

## 3. Escopo macro

O ERP deverá contemplar, em fases, núcleo da plataforma, identidade e acesso, cadastros, operação, comercial, atendimento omnichannel, inteligência/automação, gestão administrativa, integrações e aplicações móveis.

A lista detalhada de módulos e o escopo por funcionalidade constam em `04_MODULOS_ESCOPO.md`. A inclusão no mapa macro não significa que todos os requisitos estejam especificados ou aprovados.

## 4. Público e implantação

- Primeira empresa usuária: Winservice.
- Direção de produto: preparado para SaaS multiempresa desde a arquitetura inicial.
- Estratégia de implantação: evolução incremental e substituição gradual por módulos.
- Prioridade da primeira versão: operação primeiro.

## 5. Parâmetros de serviço aprovados

| Parâmetro | Decisão |
|---|---|
| Disponibilidade-alvo SaaS | 99,5% |
| RPO | 1 hora |
| RTO | 4 horas |
| Backups diários — retenção | 30 dias |
| Prioridade da primeira versão | Operação primeiro |
| Estratégia de substituição | Substituição gradual por módulos |

Os parâmetros são objetivos de projeto. A implementação, os contratos dos fornecedores, a arquitetura de backup/DR e os testes deverão demonstrar como serão atingidos.

## 6. Princípios

- Supabase como fonte de verdade.
- SaaS multiempresa e isolamento por empresa desde a concepção.
- Segurança aplicada no backend e no banco, não apenas na interface.
- Modularidade e baixo acoplamento.
- Auditoria de ações e rastreabilidade.
- Integrações desacopladas, substituíveis e opcionais quando possível.
- Portabilidade e documentação versionada.
- Desenvolvimento orientado a processos e modelagem antes da interface.
- Decisões explícitas e mudanças rastreáveis.

## 7. Limites e exclusões vigentes

- O ERP não deve depender do Auvo para operar.
- Auvo poderá ser suportado como integração opcional, não como núcleo ou fonte de verdade.
- Chatwoot é solução transitória para atendimento; a direção de produto inclui um módulo Chat omnichannel próprio.
- WeWeb é a direção para a aplicação administrativa web, hospedada na VPS.
- FlutterFlow é a direção para o aplicativo móvel técnico offline-first.
- A substituição das ferramentas atuais será gradual, não um corte único.

## 8. Critérios gerais de sucesso

- Processos prioritários de operação executados pelo ERP.
- Dados centralizados e íntegros no Supabase.
- Isolamento multiempresa validado por testes de segurança.
- Permissões aplicadas no backend/RLS.
- Rotinas críticas auditáveis.
- Backup e recuperação testados contra RPO/RTO definidos.
- Documentação atualizada e suficiente para continuidade do desenvolvimento.
- Migração gradual sem perda de continuidade operacional.

## 9. Itens ainda a detalhar

- Metas de desempenho e capacidade por operação.
- Plano de continuidade, arquitetura de backup e evidências de testes.
- Política de observabilidade, alertas e resposta a incidentes.
- Estratégia de ambientes (desenvolvimento, homologação e produção).
- Critérios de aceite por módulo e por release.
- Modelo comercial e limites de planos SaaS.
- Cronograma e estimativas por etapa.

## 10. Aprovação e controle

Este documento consolida decisões comunicadas pelo responsável do projeto em conversas anteriores. A versão 0.1.0 deve ser revisada pelo responsável e complementada antes de ser considerada Charter integralmente aprovado.
