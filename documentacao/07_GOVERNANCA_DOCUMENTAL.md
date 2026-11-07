# Governança documental do ERP Winservice

**Versão:** 0.2.0
**Data de atualização:** 2026-11-07

## 1. Objetivo

Estabelecer como a documentação do ERP será consultada, atualizada, aprovada, versionada e utilizada como base de continuidade entre conversas e sessões de desenvolvimento.

## 2. Fontes e níveis de autoridade

1. **Documentos oficiais do Projeto:** referência persistente para decisões e escopo aprovados.
2. **Código, migrações e ambiente:** evidência do que está efetivamente implementado.
3. **Relatórios de desenvolvimento:** registro do que foi feito, testado e ficou pendente.
4. **Rascunho:** ideias não aprovadas.
5. **Conversas:** meio de trabalho e deliberação; não são, por si só, registro oficial durável.

Se houver divergência entre documento e implementação, registrar a divergência. Não alterar o documento para refletir código possivelmente incorreto sem análise e decisão.

## 3. Uso obrigatório pelo assistente e pelo responsável

Sempre que trabalhar no ERP:
- Consultar o Charter, o índice de decisões e os ADRs relevantes, roadmap, módulo relevante e último relatório.
- Consultar o inventário técnico quando a tarefa envolver banco, RLS, funções, triggers ou API.
- Identificar premissas não confirmadas.
- Não reabrir decisões aprovadas sem motivo técnico ou pedido explícito.
- Não transformar proposta em decisão sem confirmação.
- Ao concluir, atualizar os documentos afetados e produzir resumo de sessão.

## 4. Separação oficial × rascunho

### Rascunho

Usado para:
- Ideias novas.
- Hipóteses técnicas.
- Alternativas de arquitetura.
- Melhorias futuras.
- Experimentos e perguntas em aberto.

Cada item deve ter data, problema/objetivo, descrição, benefícios, impactos, riscos, dependências e estado.

### Promoção para documentação oficial

Uma proposta só pode ser promovida após:
1. Análise de impacto.
2. Identificação dos documentos afetados.
3. Decisão explícita do responsável.
4. Registro de ADR quando alterar arquitetura, segurança, escopo ou padrão.
5. Atualização do documento oficial e do histórico.

Após promoção, manter no rascunho uma referência ao ADR/documento de destino; não apagar silenciosamente o histórico da ideia.

## 5. Governança das decisões arquiteturais (ADRs)

### 5.1 Estrutura e identificação

- Cada decisão arquitetural ou de produto que exija registro formal terá um arquivo Markdown individual em `documentacao/decisoes/`.
- O identificador é sequencial e estável, no formato `ADR-0001`, `ADR-0002`, `ADR-0003` etc.
- Não reutilizar identificadores, inclusive para propostas rejeitadas ou superadas.
- O arquivo `02_DECISOES_ARQUITETURA.md` é o índice e a visão consolidada; não substitui as fichas individuais.
- `documentacao/decisoes/ADR-TEMPLATE.md` é o modelo de referência e não consome número sequencial.
- O título do arquivo deve facilitar a identificação, por exemplo: `ADR-0001-direcao-do-produto.md`.

### 5.2 Ciclo de vida e aprovação

Estados permitidos:
- `PROPOSTA`: em análise, sem efeito como decisão vigente.
- `APROVADA`: aprovada explicitamente pelo responsável e vigente.
- `REJEITADA`: avaliada e rejeitada.
- `SUPERADA`: decisão anteriormente aprovada que foi substituída por outra.

Regras:
- A criação de um ADR ou sua inclusão no índice não significa aprovação.
- Registrar a aprovação e sua data somente após manifestação explícita do responsável.
- Até a aprovação, manter o estado `PROPOSTA`.
- Decisões rejeitadas permanecem no histórico e não são apagadas.
- O índice deve refletir o estado de cada ficha individual.

### 5.3 Conteúdo mínimo

Cada ficha deve registrar, na medida em que houver informação disponível:
- identificador, título, estado e datas;
- contexto e problema;
- alternativas consideradas;
- decisão e justificativa;
- consequências, riscos e limitações;
- impactos de segurança, privacidade e isolamento multiempresa;
- dependências, implementação e validação;
- referências e evidências;
- relações com ADRs anteriores ou sucessores.

Não preencher lacunas históricas por suposição. Quando a informação não estiver disponível, declarar isso expressamente.

### 5.4 Alterações, reversões e substituições

- Uma decisão aprovada não deve ser apagada nem ter seu significado alterado silenciosamente.
- Correções editoriais que não alterem o sentido podem ser feitas, com registro no histórico da ficha e no Git.
- Mudanças substantivas, reversões ou retirada de uma decisão aprovada exigem novo ADR.
- Quando a substituição for aprovada, o ADR anterior passa a `SUPERADA` e deve apontar para o ADR sucessor.
- O novo ADR deve apontar para a decisão que substitui e explicar o motivo da mudança.
- Nunca reutilizar o número de um ADR superado ou rejeitado.

### 5.5 Decisões legadas

Decisões consolidadas em documentos anteriores podem ser migradas para fichas individuais quando seu conteúdo e estado de aprovação estiverem identificados. A migração documental não autoriza inventar contexto, alternativas, justificativas ou datas. Enquanto a ficha individual não existir, o índice deve identificá-la como registro legado com ficha pendente.

## 6. Versionamento e nomenclatura

- Usar Markdown (`.md`) como formato principal, por ser legível, portátil e adequado a Git.
- Versão semântica documental: `MAJOR.MINOR.PATCH`.
- `MAJOR`: alteração estrutural ou substituição relevante.
- `MINOR`: inclusão de seção, decisão ou escopo.
- `PATCH`: correção editorial ou esclarecimento sem mudança de sentido.
- Manter data de atualização no cabeçalho e histórico de alterações.
- Código, SQL e JSON devem ser anexados como artefatos específicos, não embutidos em relatórios extensos quando puderem ser versionados separadamente.

## 7. Registro de sessão

Ao final de cada sessão relevante, criar entrada no relatório com:
- data;
- objetivo;
- decisões;
- arquivos e objetos alterados;
- comandos/migrações relevantes;
- testes e evidências;
- riscos e pendências;
- próximo passo recomendado.

## 8. Relatórios periódicos

Produzir relatório ao final de cada marco ou sessão técnica significativa. Periodicidade de síntese executiva pode ser semanal, caso haja atividade contínua. Não criar relatório de progresso sem trabalho verificável.

## 9. Anexação ao Projeto ChatGPT

A pasta documental deverá ser anexada como base de conhecimento do Projeto na interface do ChatGPT. A Biblioteca de arquivos pode funcionar como repositório persistente complementar, mas a cópia anexada ao Projeto precisa ser atualizada quando houver nova versão.

Manter uma cópia versionada fora do ChatGPT (preferencialmente repositório Git sob controle do responsável) para garantir histórico e portabilidade.

## 10. Integridade e segurança

- Não incluir credenciais, tokens, chaves ou segredos.
- Minimizar dados pessoais em relatórios.
- Não registrar informações de clientes reais quando exemplos fictícios bastarem.
- Para evidências, referenciar IDs/arquivos e ambiente, evitando expor dados sensíveis.

## Histórico de alterações

- 2026-11-07 — v0.2.0: inclusão da governança formal de ADRs individuais, estados, aprovação explícita, substituição e tratamento de decisões legadas.
