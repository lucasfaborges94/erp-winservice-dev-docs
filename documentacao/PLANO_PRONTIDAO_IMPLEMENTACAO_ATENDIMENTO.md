# Plano de prontidão para implementação — Atendimento SaaS

**Estado:** APROVADO pelo responsável em 2026-11-15 — integração documental à main pendente.  
**Data:** 2026-11-15  
**Base:** especificação aprovada do MVP de atendimento, ADR-0020 e gates G0–G8  
**Branch:** `plan/atendimento-saas-readiness`  
**Objetivo:** transformar os gates aprovados em uma sequência de trabalho verificável e segura.

> O responsável aprovou este plano em 2026-11-15. A aprovação valida a sequência e os critérios documentais; não aprova decisões que a especificação deixou pendentes nem autoriza implementação, alterações no Supabase/n8n/Evolution/Chatwoot, credenciais, deploy ou produção. Cada operação requer escopo e autorização próprios.

## 1. Estado inicial verificado

- A ADR-0020 e a especificação técnica do MVP foram aprovadas documentalmente; PR #2 foi integrado à `main`.
- A API ERP permanece documentada como scaffold NestJS, sem autenticação, tenancy, autorização ou endpoints de atendimento implementados, conforme a última evidência registrada.
- G0 e G1 estavam parciais na última revalidação registrada (2026-11-09); G2–G5 não iniciados. Revalidar antes de executar qualquer gate.
- A instância Evolution correta e o recebimento de entrada no Chatwoot foram verificados em um smoke test manual. Isso não conclui G6 nem comprova segurança, reconexão, idempotência ou processamento automatizado.
- Há duas ocorrências Evolution no [registro de credenciais a rotacionar](REGISTRO_CREDENCIAIS_A_ROTACIONAR.md). Não sabemos se são a mesma chave. Valores não foram armazenados.
- O produto não possui servidor MCP do ERP. REST é o caminho inicial previsto.
- Nenhum teste automatizado ou atividade operacional faz parte da criação deste plano.

## 2. Decisões mantidas e pontos que continuam pendentes

Este plano preserva as direções aprovadas sem expandi-las:

- Evolution API com Baileys/WhatsApp Web inicialmente; Cloud API exige nova avaliação.
- Chatwoot como inbox humano transitório.
- Workflows n8n e identidades/credenciais separados por perfil; perfil write-only não pode criar cadastro de cliente.
- Identidade de serviço n8n→API via credencial opaca aprovada para o desenho; emissor, validade máxima, escopos finais, tenant/canal e procedimento administrativo continuam pendentes.
- Ainda é necessário decidir retenção/privacidade antes de processar conversas reais, fonte autoritativa para histórico/status e contrato de handoff/retomada entre API, n8n, Evolution e Chatwoot.
- Não assumir que o smoke test de entrada prova fluxo de saída, pausa da automação ou sincronização de status.

## 3. Sequência proposta

| Ordem | Etapa | Pré-requisitos | Evidência de saída | Desbloqueia |
|---|---|---|---|---|
| 0 | Conter exposições de credenciais | Ação do responsável sobre a janela de rotação | Chaves antigas invalidadas; consumidor autorizado usa credencial gerenciada; registro atualizado sem valores secretos | Homologação segura das integrações |
| 1 | Fechar decisões pré-dados reais | Revisão do responsável | Política de retenção/anonimização/acesso; contrato de estado/handoff; escopos e ciclo de vida de credenciais aprovados | Testes com dados sintéticos e G6 |
| 2 | G0 — recuperação e baseline | Ambiente isolado e autorização operacional específica | Backup restaurado fora de produção; baseline reconciliada; pooler documentado; procedimento de restauração ensaiado | G1 e testes de banco |
| 3 | G1 — identidade de usuário | G0; ambiente de teste; autorização para código/testes | JWT válido/inválido testados; mapeamento Auth→ERP e provisionamento verificados | Autenticação do console |
| 4 | G2–G4 — contexto, tenancy e autorização | G0/G1; modelo de ameaças revisto | Role runtime sem BYPASSRLS; contexto transaction-local; testes cross-tenant negativos; matriz de escopos e autonomia testada | Endpoints de negócio |
| 5 | G5 — API do produto | G0–G4; contratos REST revisados | DTOs/erros/idempotência versionados; testes unitários e integração; revisão de segurança e logs tenant-safe | Homologação das integrações |
| 6 | G6 — n8n/Evolution/Chatwoot | G5; decisões da etapa 1; ambiente não produtivo | Autenticidade webhook, replay/idempotência, retry/limites, pausa/handoff/retomada, reconexão/ordenação e rotação testados | Piloto controlado |
| 7 | G7 — piloto Winservice | G0–G6; canal e regras de teste; suporte/pausa definidos | Aceite funcional e operacional documentado, com critérios de pausa e rollback | Decidir expansão |
| 8 | G8 — comercialização | Evidência do piloto; produto, privacidade e suporte revistos | Onboarding multi-tenant, limites, billing e suporte aprovados | Oferta a outros tenants |

## 4. Detalhamento e limites por etapa

### Etapa 0 — credenciais expostas

1. O responsável rotaciona a chave administrativa da Evolution na janela combinada e atualiza apenas consumidores autorizados.
2. Confirmar se a ocorrência no workflow “Testa conexao Evolution Api” é a mesma chave; não copiar o valor para conversa ou documento.
3. Remover a chave literal do workflow e substituí-la por credencial gerenciada antes de reutilizar esse workflow.
4. Confirmar que a chave antiga falha e atualizar o [registro](REGISTRO_CREDENCIAIS_A_ROTACIONAR.md), sem incluir a nova chave.

**Bloqueio:** não usar `GET /instance/fetchInstances` como forma de verificar o segredo; há alerta documentado para Evolution API 2.3.7. Esta etapa ainda não foi executada por este plano.

### Etapa 1 — decisões para proteger conversas reais

1. Definir retenção por categoria: conteúdo de mensagem, metadados, eventos de integração, logs e dados de contato.
2. Definir acesso humano, anonimização/exclusão e fluxo de solicitação do titular.
3. Definir qual componente é autoridade de estado/histórico e como sincronizar Chatwoot transitório sem duplicar mensagens ou respostas.
4. Aprovar validade máxima, emissão, rotação, revogação, escopos e vínculo tenant/canal da credencial de serviço.
5. Manter chamadas de homologação com dados sintéticos até as decisões de privacidade serem registradas.

### Etapas 2–5 — fundação e API

- Revalidar G0/G1 com evidência atual em vez de presumir que o snapshot de 2026-11-09 continua vigente.
- Elaborar matriz tabela×tenant×operação antes de alterações físicas; inventário estrutural sozinho não aprova DDL.
- Implementar em ambiente isolado depois de autorização específica, começando por validação JWT/contexto e testes negativos.
- Não iniciar endpoints de negócio se context isolation, RLS, escopos ou logs não passarem seus critérios de bloqueio.
- Perfil `atendimento_registrador_write`: sem leitura por ferramenta e sem `customer.minimal.create`; só qualificação/handoff para contexto validado pelo servidor.

### Etapa 6 — integrações

- Usar ambiente de homologação e credenciais próprias por perfil; manter segredos fora de exports, prompts, Git e logs.
- Confirmar a modalidade Baileys, imagem/versão e armazenamento seguro da sessão.
- Validar autenticidade do webhook, mapeamento canal→tenant, proteção contra replay, deduplicação, ordenação e limites.
- Confirmar que Chatwoot pausa a automação durante takeover e que retomada exige transição autorizada.
- Executar cenários de desconexão/reconexão, duplicidade, timeout, indisponibilidade e revogação de credencial.
- Não alterar o workflow de produção “Testa conexao Evolution Api” como parte de homologação; qualquer correção exige autorização operacional própria.

## 5. Critérios de parada

Interromper a etapa e registrar bloqueio se ocorrer qualquer um destes casos:

- Falta de backup restaurável, ambiente isolado ou evidência de restauração.
- Acesso cruzado entre tenants, contexto residual no pool de conexões ou ação fora do escopo do perfil.
- Retenção/privacidade não definida antes de usar conversa real.
- Credencial exposta, sem rotação/revogação demonstrada ou guardada em workflow exportável.
- Handoff sem pausa confiável, evento duplicado sem idempotência ou retomada automática indevida.
- Teste, deploy ou alteração operacional fora da autorização concedida para aquela etapa.

## 6. Aprovações que continuam separadas

A aprovação deste plano, se concedida, significa apenas aceite da ordem e dos critérios propostos. Ela não substitui:

- autorização para operações Supabase, mesmo que somente em branch/ambiente isolado;
- autorização para criar ou modificar workflows e credenciais n8n/Evolution/Chatwoot;
- autorização para editar código, abrir acesso a dados reais, ativar canal, fazer deploy ou operar produção;
- aceite explícito de políticas de retenção, consentimento, privacidade, validade/escopos de credenciais e estado autoritativo das conversas.

## 7. Próxima ação recomendada

O responsável aprovou esta sequência em 2026-11-15. Próximo passo documental: integrar o plano à main por PR, sem merge automático. O primeiro trabalho operacional continua sendo a rotação/revogação das credenciais Evolution pendentes pelo responsável; em paralelo, documentar retenção/privacidade e contrato de handoff antes de qualquer tráfego com conversas reais. G0/G1 só devem ser revalidados em escopo autorizado e ambiente apropriado.
