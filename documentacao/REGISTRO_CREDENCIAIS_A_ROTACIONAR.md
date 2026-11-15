# Registro de credenciais a rotacionar

**Objetivo:** acompanhar credenciais que possam ter sido expostas e a respectiva rotação, sem armazenar os segredos.

> Este arquivo não é um cofre e não torna uma credencial segura. Trate toda credencial registrada como comprometida até que seja revogada ou rotacionada e a substituição seja validada. Faça a rotação no sistema que emitiu a credencial e atualize os consumidores autorizados.

## Regras do registro

- Registrar somente serviço, tipo de credencial, local/contexto, data, estado e ação pendente.
- Nunca registrar valor, prefixo, sufixo, hash, fingerprint, QR code, captura que revele o segredo ou qualquer representação recuperável da credencial.
- Não colar segredos em conversas, tickets, commits ou arquivos do repositório.
- Após a rotação, validar que a credencial antiga não autentica mais e que os consumidores autorizados usam a nova; então atualizar o estado aqui, sem registrar a nova credencial.
- Se a rotação depender de alteração em workflow, ambiente ou produção, planejar a mudança com o responsável e registrar a evidência sem incluir o valor.

## Itens pendentes

| ID | Serviço / credencial | Local/contexto observado | Data da constatação | Estado | Próxima ação |
|---|---|---|---|---|---|
| CRED-2026-11-14-01 | Evolution API — chave global de autenticação | Valor visível na tela de variáveis de ambiente do Easypanel, no serviço Evolution API. O valor foi omitido deste registro. | 2026-11-14 | Chave rotacionada pelo responsável em 2026-11-15 e validada na credencial `Evolution API` do n8n | A consulta da instância 51981459630 retornou `open`. A validação dos demais consumidores segue pendente. |
| CRED-2026-11-14-02 | Evolution API — chave literal em configuração de workflow n8n | Valor literal encontrado no nó Set do workflow “Testa conexao Evolution Api”. O valor foi omitido deste registro. A instância correspondente está desconectada, foi preservada somente como histórico e não voltará à produção por falta de acesso ao número. Não utilizar o workflow legado para produção. | 2026-11-14 | Histórico; fora da produção. A chave exposta permanece registrada; confirmar se o acesso de leitura ao histórico precisa ser preservado antes de revogar. | Não usar para atendimento nem novos workflows. Decidir revogação após confirmar necessidade de consultar o histórico. |

## Histórico

- 2026-11-15 — criação do registro após constatações de 2026-11-14. As duas ocorrências Evolution permanecem separadas; nenhum valor secreto foi copiado para este arquivo.
- 2026-11-15 — responsável confirmou a rotação de CRED-2026-11-14-01; a consulta read-only da instância 51981459630 no n8n retornou 401 Authorization failed, confirmando que a credencial guardada no n8n ainda precisa ser atualizada. Invalidação global da chave anterior e demais consumidores aguardam validação. Responsável esclareceu que CRED-2026-11-14-02 pertence a uma instância desconectada, preservada somente como histórico e fora da produção.
- 2026-11-15 — após a atualização da credencial no n8n, a execução 9942409 consultou a instância 51981459630 com sucesso e recebeu o estado `open`.
