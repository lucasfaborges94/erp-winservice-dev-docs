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
| CRED-2026-11-14-01 | Evolution API — chave global de autenticação | Valor visível na tela de variáveis de ambiente do Easypanel, no serviço Evolution API. O valor foi omitido deste registro. | 2026-11-14 | Pendente de rotação e validação | Responsável deve rotacionar a chave no Easypanel e atualizar os consumidores autorizados. Confirmar que a chave anterior foi invalidada. |
| CRED-2026-11-14-02 | Evolution API — chave literal em configuração de workflow n8n | Valor literal encontrado no nó Set do workflow “Testa conexao Evolution Api”. O valor foi omitido deste registro. O workflow não foi alterado nesta constatação. | 2026-11-14 | Pendente de confirmar relação com CRED-2026-11-14-01, remover a literal e validar rotação | Verificar se é a mesma credencial da chave global. Após confirmar, remover a literal do workflow, substituir por credencial gerenciada do n8n e invalidar a credencial antiga. Revisar o histórico do workflow se aplicável. |

## Histórico

- 2026-11-15 — criação do registro após constatações de 2026-11-14. As duas ocorrências Evolution permanecem separadas até que sua equivalência seja confirmada. Nenhum valor secreto foi copiado para este arquivo.
