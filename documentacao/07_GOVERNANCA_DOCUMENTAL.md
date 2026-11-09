# Governança documental do ERP Winservice

**Versão:** 0.4.0  
**Data de atualização:** 2026-11-08

## 1. Objetivo

Estabelecer como a documentação do ERP será consultada, atualizada, aprovada, versionada e utilizada como base de continuidade entre conversas, ferramentas, contas e profissionais.

O protocolo universal de autoridade e trabalho está em [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md), na raiz do repositório. Este documento detalha a governança documental.

## 2. Fontes e níveis de autoridade

A fonte primária depende do tipo de informação. Aplicar a matriz e as regras de resolução de conflitos do documento Fonte da Verdade.

Em síntese:
- Charter e especificações oficiais: objetivos, requisitos e escopo aprovados.
- ADR individual vigente: decisão arquitetural aprovada.
- Código, migrações, banco e ambiente: estado efetivamente implementado.
- Testes e evidências: resultados verificados.
- Relatórios e registros de sessão: histórico de trabalho, pendências e continuidade.
- Rascunho e propostas: conteúdo ainda não aprovado.
- Conversas: meio de deliberação, não registro oficial durável por si só.

Se houver divergência, registrar e analisar; não escolher silenciosamente uma fonte nem ajustar código ou documentação para ocultar o conflito.

## 3. Uso obrigatório por qualquer agente ou profissional

Antes de trabalhar no ERP:
- Ler 00_FONTE_DA_VERDADE.md.
- Consultar 00_LEIA-ME.md e 08_CONTINUIDADE_E_TRANSFERENCIA.md.
- Consultar Charter, ADRs, roadmap, módulo e processo relevantes.
- Consultar inventários e evidências técnicas quando a tarefa envolver banco, RLS, funções, triggers, API ou infraestrutura.
- Confirmar o estado atual do repositório e dos ambientes aplicáveis.
- Identificar premissas, lacunas, divergências e informações não verificadas.

Durante e após o trabalho:
- Preservar decisões aprovadas.
- Não converter propostas em decisões sem aprovação explícita.
- Registrar mudanças, testes, evidências, pendências e próximo passo.
- Atualizar os documentos afetados e o registro de continuidade.
- Não alegar execução, teste ou publicação sem evidência.

### 3.1 Consulta por mensagem e atualização incremental

A consulta ao repositório é obrigatória antes de cada resposta relacionada ao ERP, inclusive dentro de uma conversa em andamento. A IA deve reler a última mensagem do usuário, consultar a versão atual de `00_FONTE_DA_VERDADE.md`, o protocolo de atuação, o contexto operacional, a continuidade e os documentos/ADRs pertinentes antes de responder.

Após a resposta, verificar se houve informação nova a persistir. Registrar progressivamente aprovações, decisões, requisitos, correções, descobertas, mudanças de estado, riscos, pendências e próximos passos. Não esperar o encerramento de uma conversa longa para registrar pontos-chave.

Uma sessão pode abranger várias mensagens correlatas. Atualizar o mesmo registro enquanto o trabalho prossegue; criar registros separados para sessões ou marcos independentes, não para cada mensagem. Mensagens sem avanço não exigem commit artificial, mas continuam exigindo consulta prévia.

O documento `CONTEXTO_ATUAL_ERP.md` serve como índice e resumo operacional, não como substituto das fontes normativas.

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

- Cada decisão arquitetural ou de produto que exija registro formal terá um arquivo Markdown individual em documentacao/decisoes/.
- O identificador é sequencial e estável, no formato ADR-0001, ADR-0002, ADR-0003 etc.
- Não reutilizar identificadores, inclusive para propostas rejeitadas ou superadas.
- O arquivo 02_DECISOES_ARQUITETURA.md é o índice e a visão consolidada; não substitui as fichas individuais.
- documentacao/decisoes/ADR-TEMPLATE.md é o modelo de referência e não consome número sequencial.
- O título do arquivo deve facilitar a identificação, por exemplo: ADR-0001-direcao-do-produto.md.

### 5.2 Ciclo de vida e aprovação

Estados permitidos:
- PROPOSTA: em análise, sem efeito como decisão vigente.
- APROVADA: aprovada explicitamente pelo responsável e vigente.
- REJEITADA: avaliada e rejeitada.
- SUPERADA: decisão anteriormente aprovada que foi substituída por outra.

Regras:
- A criação de um ADR ou sua inclusão no índice não significa aprovação.
- Registrar a aprovação e sua data somente após manifestação explícita do responsável.
- Até a aprovação, manter o estado PROPOSTA.
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
- Quando a substituição for aprovada, o ADR anterior passa a SUPERADA e deve apontar para o ADR sucessor.
- O novo ADR deve apontar para a decisão que substitui e explicar o motivo da mudança.
- Nunca reutilizar o número de um ADR superado ou rejeitado.

### 5.5 Decisões legadas

Decisões consolidadas em documentos anteriores podem ser migradas para fichas individuais quando seu conteúdo e estado de aprovação estiverem identificados. A migração documental não autoriza inventar contexto, alternativas, justificativas ou datas.

## 6. Versionamento e nomenclatura

- Usar Markdown (.md) como formato principal, por ser legível, portátil e adequado a Git.
- Versão semântica documental: MAJOR.MINOR.PATCH.
- MAJOR: alteração estrutural ou substituição relevante.
- MINOR: inclusão de seção, decisão ou escopo.
- PATCH: correção editorial ou esclarecimento sem mudança de sentido.
- Manter data de atualização no cabeçalho e histórico de alterações.
- Código, SQL e JSON devem ser anexados como artefatos específicos, não embutidos em relatórios extensos quando puderem ser versionados separadamente.

## 7. Registro de sessão e transferência

Ao final de cada sessão relevante, atualizar [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md) ou criar registro separado seguindo seu modelo.

O registro deve permitir retomada por pessoa ou ferramenta que não participou da sessão original. Incluir:
- data, responsável/agente, ferramenta/ambiente e branch;
- commits inicial e final, quando existentes;
- objetivo, contexto consultado e trabalho realizado;
- decisões aprovadas e propostas pendentes;
- arquivos, objetos e migrações alterados;
- testes realmente executados e evidências;
- estado concluído, parcial, não iniciado e não verificado;
- riscos, bloqueios, pendências e próximo passo acionável.

Não registrar atividade hipotética como realizada. Não criar relatórios artificiais quando não houve avanço relevante.

## 8. Relatórios periódicos

Produzir relatório ao final de cada marco ou sessão técnica significativa. Periodicidade de síntese executiva pode ser semanal, caso haja atividade contínua. Não criar relatório de progresso sem trabalho verificável.

## 9. Portabilidade e independência de ferramentas

- Documentação normativa em Markdown e texto simples sempre que possível.
- Não depender de memória privada, instruções ocultas ou recursos exclusivos de uma IA.
- Escrever requisitos e procedimentos de forma executável por profissionais humanos.
- Usar links relativos e identificadores estáveis.
- Definir siglas e termos de domínio.
- Ferramentas podem gerar ou revisar documentação, mas não aprovam decisões em nome do responsável.
- Acesso ao repositório não concede automaticamente acesso a ambientes, segredos ou dados.

## 10. Anexação a ferramentas de IA

Cópias anexadas a projetos, espaços de trabalho ou bases de conhecimento de ferramentas de IA são conveniências de consulta, não fontes oficiais independentes. O repositório Git é a referência versionada.

Quando uma ferramenta não puder acessar o repositório, o agente deve informar a limitação e solicitar acesso ou cópia atualizada; não presumir que anexos antigos representam a versão vigente.

## 11. Integridade e segurança

- Não incluir credenciais, tokens, chaves ou segredos.
- Minimizar dados pessoais em relatórios.
- Não registrar informações de clientes reais quando exemplos fictícios bastarem.
- Para evidências, referenciar IDs/arquivos e ambiente, evitando expor dados sensíveis.
- Exigir autorização e revisão para operações destrutivas ou irreversíveis.

## Histórico de alterações

- 2026-11-07 — v0.3.0: integração do protocolo independente de fornecedor e do processo formal de continuidade e transferência.
- 2026-11-07 — v0.2.0: inclusão da governança formal de ADRs individuais, estados, aprovação explícita, substituição e tratamento de decisões legadas.


## Histórico de alterações

- 2026-11-08 — v0.4.0: estabelece consulta obrigatória por mensagem, registro incremental de pontos-chave e uso de CONTEXTO_ATUAL_ERP.md como índice operacional.
