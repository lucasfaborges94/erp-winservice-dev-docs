# Governança documental do ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-07

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
- Consultar o Charter, decisões, roadmap, módulo relevante e último relatório.
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

## 5. Versionamento e nomenclatura

- Usar Markdown (`.md`) como formato principal, por ser legível, portátil e adequado a Git.
- Versão semântica documental: `MAJOR.MINOR.PATCH`.
- `MAJOR`: alteração estrutural ou substituição relevante.
- `MINOR`: inclusão de seção, decisão ou escopo.
- `PATCH`: correção editorial ou esclarecimento sem mudança de sentido.
- Manter data de atualização no cabeçalho e histórico de alterações.
- Código, SQL e JSON devem ser anexados como artefatos específicos, não embutidos em relatórios extensos quando puderem ser versionados separadamente.

## 6. Registro de sessão

Ao final de cada sessão relevante, criar entrada no relatório com:
- data;
- objetivo;
- decisões;
- arquivos e objetos alterados;
- comandos/migrações relevantes;
- testes e evidências;
- riscos e pendências;
- próximo passo recomendado.

## 7. Relatórios periódicos

Produzir relatório ao final de cada marco ou sessão técnica significativa. Periodicidade de síntese executiva pode ser semanal, caso haja atividade contínua. Não criar relatório de progresso sem trabalho verificável.

## 8. Anexação ao Projeto ChatGPT

A pasta documental deverá ser anexada como base de conhecimento do Projeto na interface do ChatGPT. A Biblioteca de arquivos pode funcionar como repositório persistente complementar, mas a cópia anexada ao Projeto precisa ser atualizada quando houver nova versão.

Manter uma cópia versionada fora do ChatGPT (preferencialmente repositório Git sob controle do responsável) para garantir histórico e portabilidade.

## 9. Integridade e segurança

- Não incluir credenciais, tokens, chaves ou segredos.
- Minimizar dados pessoais em relatórios.
- Não registrar informações de clientes reais quando exemplos fictícios bastarem.
- Para evidências, referenciar IDs/arquivos e ambiente, evitando expor dados sensíveis.
