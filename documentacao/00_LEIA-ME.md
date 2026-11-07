# ERP Winservice — Base oficial de documentação

**Versão do pacote:** 0.2.0
**Data de consolidação:** 2026-11-07  
**Estado:** baseline inicial — requer validação e complementação  
**Projeto:** ERP Winservice

## Objetivo

Esta documentação é a referência persistente para planejar, desenvolver, auditar e evoluir o ERP Winservice. Ela reduz a dependência do histórico de conversas e deve ser mantida como base de conhecimento do Projeto no ChatGPT.

## Estrutura documental

```text
documentacao/
├── 00_LEIA-ME.md
├── 01_PROJECT_CHARTER.md
├── 02_DECISOES_ARQUITETURA.md
├── 03_ROADMAP_ETAPAS.md
├── 04_MODULOS_ESCOPO.md
├── 05_STACK_RECURSOS.md
├── 06_STATUS_E_RELATORIOS.md
├── 07_GOVERNANCA_DOCUMENTAL.md
├── RASCUNHO.md
└── decisoes/
    ├── ADR-TEMPLATE.md
    └── ADR-NNNN-titulo-da-decisao.md
```

## Hierarquia documental

1. `01_PROJECT_CHARTER.md` — propósito, objetivos, limites e parâmetros aprovados do projeto.
2. `02_DECISOES_ARQUITETURA.md` — índice e visão consolidada das decisões; as fichas individuais ficam em `decisoes/`.
3. `decisoes/ADR-NNNN-*.md` — registro individual, histórico e estado de cada decisão arquitetural.
4. `03_ROADMAP_ETAPAS.md` — fases, etapas, dependências e critérios de conclusão.
5. `04_MODULOS_ESCOPO.md` — mapa de módulos, funcionalidades, escopos e exclusões.
6. `05_STACK_RECURSOS.md` — tecnologias, ferramentas, ambientes e recursos.
7. `06_STATUS_E_RELATORIOS.md` — estado consolidado, entregas, riscos, pendências e relatórios periódicos.
8. `07_GOVERNANCA_DOCUMENTAL.md` — regras para atualizar, aprovar e versionar a documentação.
9. `RASCUNHO.md` — ideias e propostas ainda não aprovadas.

## Regras de autoridade

- Os documentos oficiais representam decisões aprovadas ou fatos verificados.
- Propostas e hipóteses devem permanecer identificadas como `PROPOSTA`, `PENDENTE` ou no `RASCUNHO.md`.
- Uma conversa, sugestão do assistente ou trecho de código não altera por si só uma decisão oficial.
- Em caso de conflito, registrar o conflito no relatório de status e solicitar resolução; não escolher silenciosamente.
- O estado real do banco de dados e do código deve ser verificado em inventários/auditorias recentes. A documentação não substitui inspeção técnica.
- O pacote é uma consolidação inicial; lacunas são explícitas e não devem ser preenchidas por suposição.

## Rotina obrigatória ao trabalhar no ERP

1. Consultar esta base antes de propor ou executar mudanças.
2. Identificar quais documentos oficiais e decisões são afetados.
3. Trabalhar com uma unidade clara: requisito, decisão, etapa, módulo ou correção.
4. Ao concluir, atualizar o status, registrar decisões e incluir evidências/artefatos.
5. Se surgir uma ideia não aprovada, registrá-la no `RASCUNHO.md`.
6. Só promover uma ideia ao documento oficial após decisão explícita do responsável pelo projeto.
7. Ao encerrar uma sessão relevante, gerar um registro de desenvolvimento com data, escopo, alterações, testes, pendências e próximos passos.

## Fontes de baseline já existentes

Na Biblioteca do ChatGPT foram localizados estes artefatos, que devem ser mantidos como evidências técnicas anexas ao Projeto:
- `inventario_supabase.json` — inventário do banco Supabase, gerado em 2026-11-06.
- `auditoria_estrutural_supabase.json` — auditoria estrutural do Supabase, gerada em 2026-11-06.

Esses arquivos são uma fotografia técnica daquela data, não uma garantia de que o ambiente continua idêntico.

## Histórico deste arquivo

- 2026-11-07 — atualização para v0.2.0: inclusão da estrutura de ADRs individuais e referência ao índice de decisões.
