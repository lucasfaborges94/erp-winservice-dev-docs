# ERP Winservice — Base oficial de documentação

**Versão do pacote:** 0.8.0  
**Data de consolidação:** 2026-11-08  
**Estado:** baseline documental em evolução  
**Projeto:** ERP Winservice

## Objetivo

Esta documentação é a referência persistente para planejar, especificar, desenvolver, auditar e evoluir o ERP Winservice. Ela deve permitir continuidade por diferentes contas, ferramentas de IA, profissionais e equipes, sem dependência de históricos privados de conversa.

**Ponto de entrada obrigatório:** [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md), na raiz do repositório. Ele define a autoridade das fontes, o protocolo de trabalho, aprovação, segurança e transferência.

## Estrutura documental

```text
/
├── 00_FONTE_DA_VERDADE.md
└── documentacao/
    ├── 00_LEIA-ME.md
    ├── 01_PROJECT_CHARTER.md
    ├── 02_DECISOES_ARQUITETURA.md
    ├── 03_ROADMAP_ETAPAS.md
    ├── 04_MODULOS_ESCOPO.md
    ├── 05_STACK_RECURSOS.md
    ├── 06_STATUS_E_RELATORIOS.md
    ├── 07_GOVERNANCA_DOCUMENTAL.md
    ├── 08_CONTINUIDADE_E_TRANSFERENCIA.md
    ├── MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md
    ├── MODELO_FUNCIONAL_OPERACIONAL_MVP.md
    ├── RASCUNHO.md
    └── decisoes/
        ├── ADR-TEMPLATE.md
        └── ADR-NNNN-titulo-da-decisao.md
```

## Hierarquia e finalidade dos documentos

1. [Fonte da verdade](../00_FONTE_DA_VERDADE.md) — protocolo universal de consulta, autoridade, execução e transferência.
2. [Project Charter](01_PROJECT_CHARTER.md) — propósito, objetivos, limites e parâmetros aprovados.
3. [Decisões arquiteturais](02_DECISOES_ARQUITETURA.md) — índice; as decisões são detalhadas nas fichas individuais.
4. [ADRs individuais](decisoes/) — registro, justificativa, consequências e ciclo de vida de cada decisão.
5. [Roadmap](03_ROADMAP_ETAPAS.md) — etapas, dependências e critérios de conclusão.
6. [Módulos e escopo](04_MODULOS_ESCOPO.md) — mapa de módulos, funcionalidades, escopos e exclusões.
7. [Stack e recursos](05_STACK_RECURSOS.md) — tecnologias, ferramentas, ambientes e recursos.
8. [Status e relatórios](06_STATUS_E_RELATORIOS.md) — situação consolidada, entregas, riscos, pendências e relatórios.
9. [Governança documental](07_GOVERNANCA_DOCUMENTAL.md) — regras de aprovação, atualização e versionamento.
10. [Continuidade e transferência](08_CONTINUIDADE_E_TRANSFERENCIA.md) — encerramento e retomada de trabalho entre pessoas e ferramentas.
11. [Modelo de autorização e isolamento multiempresa](MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md) — proposta técnica para discussão; não aprovada.
12. [Modelo funcional operacional do MVP](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) — requisitos funcionais de Operação e confronto inicial com o schema Supabase; não substitui ADRs nem comprova implementação.
13. [Modelo lógico de dados e matriz de conformidade — Operação](MODELO_LOGICO_DADOS_OPERACAO.md) — proposta técnica para validação; não aprovada como modelo físico.\n14. [Rascunho](RASCUNHO.md) — ideias e propostas ainda não aprovadas.

## Regras essenciais

- Documentos oficiais registram decisões, requisitos e especificações; código, migrações, banco e testes evidenciam o estado implementado.
- Não resolver conflitos silenciosamente entre documentação e implementação.
- Propostas não são decisões aprovadas.
- Não inventar decisões, requisitos, evidências, aprovações ou datas.
- Consultar ADRs individuais pertinentes; o índice não substitui suas fichas.
- Ao concluir trabalho relevante, atualizar os documentos afetados e o registro de continuidade.
- Não incluir segredos, credenciais ou dados pessoais desnecessários.
- Manter documentação em formatos portáveis e independente de fornecedor.

## Fontes de baseline já existentes

- inventario_supabase.json — inventário do banco Supabase, gerado em 2026-11-06.
- auditoria_estrutural_supabase.json — auditoria estrutural do Supabase, gerada em 2026-11-06.

Esses arquivos são fotografias técnicas daquela data, não garantia de que o ambiente continua idêntico.

## Histórico deste arquivo

- 2026-11-08 — v0.8.0: consolidação das definições de GPS, histórico de posições, finalização de visita, histórico append-only aprovado e proposta de catálogo global versionado.
- 2026-11-08 — v0.8.0: consolidação das definições de GPS, histórico de posições, finalização de visita, histórico append-only aprovado e proposta de catálogo global versionado.
- 2026-11-08 — v0.7.0: atualização do modelo lógico com decisões funcionais sobre execução da visita, GPS, catálogo global editável por empresa e vínculo de orçamento.
- 2026-11-08 — v0.6.0: inclusão do modelo lógico de dados e matriz de conformidade da Operação.\n- 2026-11-08 — v0.5.0: inclusão do modelo funcional operacional do MVP no mapa documental.
- 2026-11-07 — v0.4.0: inclusão da proposta de modelo de autorização e isolamento multiempresa no mapa documental.

- 2026-11-07 — v0.3.0: inclusão do protocolo universal de fonte da verdade e do procedimento de continuidade e transferência entre contas, ferramentas e profissionais.
- 2026-11-07 — v0.2.0: inclusão da estrutura de ADRs individuais.
