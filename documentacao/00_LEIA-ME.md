# ERP Winservice — Base oficial de documentação

**Versão do pacote:** 1.1.0  
**Data de consolidação:** 2026-11-08  
**Estado:** baseline documental em evolução  
**Projeto:** ERP Winservice

## Objetivo

Esta documentação é a referência persistente para planejar, especificar, desenvolver, auditar e evoluir o ERP Winservice. Ela deve permitir continuidade por diferentes contas, ferramentas de IA, profissionais e equipes, sem dependência de históricos privados de conversa.

**Memória oficial do projeto:** o repositório Git é a memória documental persistente. Históricos de conversa, memórias internas de IA e cópias anexadas são auxiliares e não substituem os documentos versionados. Toda sessão relevante deve consultar a fonte da verdade e registrar decisões, mudanças e continuidade nos documentos especializados.

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
    ├── 08_CONTINUIDADE_E_TRANSFERENCIA.md\n    ├── IDENTIDADE_VISUAL_E_PALETA.md
    ├── CONTEXTO_ATUAL_ERP.md
    ├── MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md
    ├── MODELO_FUNCIONAL_OPERACIONAL_MVP.md
    ├── MODELO_LOGICO_DADOS_OPERACAO.md
    ├── BASE_CONHECIMENTO_ERP.md
    ├── ESPECIFICACAO_TECNICA_MVP_ATENDIMENTO_SAAS.md
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
10. [Continuidade e transferência](08_CONTINUIDADE_E_TRANSFERENCIA.md) — sessões, pontos-chave, encerramento e retomada entre pessoas e ferramentas.\n11. [Contexto atual do ERP](CONTEXTO_ATUAL_ERP.md) — índice operacional resumido para recuperação rápida; não substitui fontes normativas.
11. [Modelo de autorização e isolamento multiempresa](MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md) — proposta técnica para discussão; não aprovada.
12. [Modelo funcional operacional do MVP](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) — requisitos funcionais de Operação, incluindo requisitos, evidências e aceite digital; não substitui ADRs nem comprova implementação.
13. [Modelo lógico de dados e matriz de conformidade — Operação](MODELO_LOGICO_DADOS_OPERACAO.md) — proposta técnica para validação; não aprovada como modelo físico.
14. [Base de Conhecimento do ERP](BASE_CONHECIMENTO_ERP.md) — arquitetura aprovada conceitualmente para organizar conhecimento compartilhado por humanos e agentes de IA; contratos e conteúdos detalhados serão construídos progressivamente.
15. [Identidade visual e paleta semântica](IDENTIDADE_VISUAL_E_PALETA.md) — proposta de cores semânticas para textos, botões, fundos e cards; inclui regra explícita de contraste para o título do card secundário.
16. [Rascunho](RASCUNHO.md) — ideias e propostas ainda não aprovadas.

**Especificação aprovada, integração pendente:** [Especificação técnica do MVP de atendimento SaaS](ESPECIFICACAO_TECNICA_MVP_ATENDIMENTO_SAAS.md) — aprovada pelo responsável em 2026-11-15; aguarda integração à main. Não autoriza implementação ou alterações operacionais.

## Regras essenciais

- Documentos oficiais registram decisões, requisitos e especificações; código, migrações, banco e testes evidenciam o estado implementado.
- Não resolver conflitos silenciosamente entre documentação e implementação.
- Propostas não são decisões aprovadas.
- Não inventar decisões, requisitos, evidências, aprovações ou datas.
- Consultar ADRs individuais pertinentes; o índice não substitui suas fichas.
- Ao concluir trabalho relevante, atualizar os documentos afetados e o registro de continuidade.
- Não incluir segredos, credenciais ou dados pessoais desnecessários. Exposições e estado de rotação são acompanhados em [Registro de credenciais a rotacionar](REGISTRO_CREDENCIAIS_A_ROTACIONAR.md), sem armazenar valores secretos.
- Manter documentação em formatos portáveis e independente de fornecedor.

## Fontes de baseline já existentes

- inventario_supabase.json — inventário do banco Supabase, gerado em 2026-11-06.
- auditoria_estrutural_supabase.json — auditoria estrutural do Supabase, gerada em 2026-11-06.

Esses arquivos são fotografias técnicas daquela data, não garantia de que o ambiente continua idêntico.

## Histórico deste arquivo

- 2026-11-15 — registro da aprovação da especificação técnica e link para o controle de rotação de credenciais.

- 2026-11-14 — inclusão da especificação técnica proposta do MVP de atendimento SaaS no mapa documental; permanece pendente de aprovação.

- 2026-11-09 — inclusão do documento de identidade visual e paleta semântica no mapa documental.

- 2026-11-08 — atualização: inclui o índice operacional CONTEXTO_ATUAL_ERP.md e a regra de consulta por mensagem/registro incremental.\n- 2026-11-08 — v1.1.0: inclui a arquitetura da Base de Conhecimento do ERP e atualiza o mapa dos artefatos de Operação.
- 2026-11-08 — atualização do mapa documental para incluir a arquitetura da Base de Conhecimento do ERP e a consolidação do ciclo operacional único da visita.
- 2026-11-08 — v1.0.0: explicita o repositório como memória documental oficial do projeto e os históricos de conversa como contexto auxiliar.
- 2026-11-08 — v0.9.0: atualização do mapa documental para incluir o escopo consolidado de requisitos, evidências e aceite digital nos modelos funcional e lógico.
- 2026-11-08 — v0.8.0: consolidação das definições de GPS, histórico de posições, finalização de visita, histórico append-only aprovado e proposta de catálogo global versionado.
- 2026-11-08 — v0.8.0: consolidação das definições de GPS, histórico de posições, finalização de visita, histórico append-only aprovado e proposta de catálogo global versionado.
- 2026-11-08 — v0.7.0: atualização do modelo lógico com decisões funcionais sobre execução da visita, GPS, catálogo global editável por empresa e vínculo de orçamento.
- 2026-11-08 — v0.6.0: inclusão do modelo lógico de dados e matriz de conformidade da Operação.\n- 2026-11-08 — v0.5.0: inclusão do modelo funcional operacional do MVP no mapa documental.
- 2026-11-07 — v0.4.0: inclusão da proposta de modelo de autorização e isolamento multiempresa no mapa documental.

- 2026-11-07 — v0.3.0: inclusão do protocolo universal de fonte da verdade e do procedimento de continuidade e transferência entre contas, ferramentas e profissionais.
- 2026-11-07 — v0.2.0: inclusão da estrutura de ADRs individuais.
