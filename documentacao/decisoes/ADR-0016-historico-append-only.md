# ADR-0016 — Histórico imutável de alterações (append-only)

**Identificador:** ADR-0016  
**Estado:** APROVADA  
**Data da proposta:** 2026-11-08  
**Data da aprovação:** 2026-11-08  
**Responsável pela decisão:** Responsável pelo projeto Winservice  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP Winservice precisa preservar a história operacional e administrativa de registros que podem ser corrigidos ou atualizados ao longo do tempo. O responsável pelo projeto aprovou explicitamente a adoção de histórico imutável baseado em append-only.

## 2. Problema ou necessidade

Uma atualização destrutiva pode eliminar o conteúdo que existia no momento de um atendimento, orçamento, visita ou outra operação. É necessário preservar a evidência histórica e identificar quem realizou cada alteração e quando.

## 3. Alternativas consideradas

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Sobrescrever registros e manter apenas updated_at/updated_by | Simplicidade operacional | Perde valores anteriores e não permite reconstruir a sequência histórica |
| Histórico append-only com eventos e revisões/snapshots | Preserva sequência, autoria, conteúdo anterior e correções | Exige política de retenção, controles de integridade, volume e consultas históricas |
| Histórico apenas em logs externos | Pode reduzir acoplamento ao modelo transacional | Integridade e consulta histórica podem ficar separadas da fonte de verdade |

## 4. Decisão

Adotar histórico append-only para alterações relevantes do ERP.

- Eventos históricos não devem ser sobrescritos nem excluídos no fluxo operacional.
- Correções devem gerar novos eventos, revisões ou snapshots versionados, mantendo referência ao registro corrigido.
- O histórico deve preservar entidade, identificador, tipo de operação, autor/ator, temporalidade, origem e valores anteriores/novos ou snapshot correspondente.
- Devem ser distinguidos o instante em que a alteração ocorreu e o instante em que foi registrada, quando necessário.
- A diretriz se aplica aos registros operacionais e administrativos relevantes, conforme matriz de auditoria a ser especificada.

Esta ADR aprova a diretriz arquitetural, não o desenho físico definitivo nem sua implementação.

## 5. Justificativa

O histórico é evidência operacional e pode ser necessário para auditoria, atendimento, garantia, análise de divergências e reconstrução de decisões. Eventos append-only reduzem o risco de perda silenciosa de evidências. O responsável aprovou explicitamente esta diretriz em 2026-11-08.

## 6. Consequências e impactos

### Benefícios esperados
- Preservação de versões e sequência temporal.
- Rastreabilidade de autoria e origem.
- Correções sem destruição da evidência anterior.

### Custos, riscos e limitações
- Crescimento de armazenamento e complexidade de consulta.
- Necessidade de distinguir eventos de negócio, revisões e projeções de estado atual.
- Histórico imutável não substitui backups nem controles de acesso.

### Impactos operacionais e de manutenção
- Telas e APIs precisarão consultar estado atual e histórico conforme autorização.
- Correções deverão criar novas revisões/eventos.
- A política de retenção e eventual anonimização deverá respeitar requisitos legais e operacionais, sem apagar indevidamente evidências sujeitas a preservação.

### Dependências e integrações
- Modelo de identidade de atores humanos, agentes e sistema.
- Estratégia de tenancy e RLS.
- Mecanismo de escrita transacional e integridade entre registros de negócio e eventos históricos.

## 7. Segurança, privacidade e isolamento multiempresa

- Cada evento deverá pertencer à empresa correta e respeitar isolamento multiempresa.
- A leitura do histórico será autorizada por backend/API e RLS.
- Papéis operacionais não devem possuir permissão direta de UPDATE/DELETE sobre eventos históricos.
- A imutabilidade deve ser reforçada no banco e no backend; não basta ocultar ações na interface.
- Snapshots podem conter dados pessoais e devem seguir minimização, controle de acesso e retenção apropriada.
- A decisão não declara que os controles atuais do Supabase estejam seguros ou implementados.

## 8. Implementação e validação

A implementação futura deverá definir entidades/eventos auditáveis, estratégia de integridade, autoria, ordenação, retenção, particionamento e consultas históricas; impedir UPDATE/DELETE por políticas, privilégios e mecanismos do banco/backend; e testar transações, concorrência, correções, isolamento multiempresa e tentativas de adulteração.

Critério de aceitação: uma alteração preserva o conteúdo anterior, registra o novo conteúdo e identifica ator, momento e origem, sem permitir modificação silenciosa do histórico.

Nenhuma implementação é declarada concluída por esta ADR.

## 9. Referências e evidências

- documentacao/MODELO_FUNCIONAL_OPERACIONAL_MVP.md
- documentacao/MODELO_LOGICO_DADOS_OPERACAO.md
- Registro de continuidade da sessão de 2026-11-08.
- Aprovação explícita do responsável pelo projeto em 2026-11-08.

## 10. Revisão e substituição

- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

## Histórico desta ficha

- 2026-11-08 — criação e aprovação explícita da ADR-0016.