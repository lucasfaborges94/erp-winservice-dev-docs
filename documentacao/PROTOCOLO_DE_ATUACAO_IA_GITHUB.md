# Protocolo de atuação da IA com o GitHub

**Status:** aprovado como procedimento operacional  
**Escopo:** tarefas relacionadas ao repositório do ERP Winservice  
**Repositório principal:** `lucasfaborges94/erp-winservice-dev-docs`  
**Branch padrão:** `main`
**Papel do repositório:** memória documental oficial e fonte persistente do projeto.

## 0. Inicialização de contexto: recuperar a memória do projeto

No início de toda nova conversa ou sessão de trabalho sobre o ERP Winservice, reconstruir o contexto a partir do repositório, e não presumir que o histórico da conversa ou a memória interna da IA esteja completo.

Sequência mínima obrigatória:

1. Confirmar acesso ao repositório e identificar branch padrão, HEAD/commit atual e árvore de arquivos.
2. Ler `00_FONTE_DA_VERDADE.md` na raiz.
3. Ler este protocolo e `documentacao/00_LEIA-ME.md`.
4. Ler `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`, localizar a sessão mais recente e seguir seu procedimento de retomada.
5. Comparar o commit citado no registro de continuidade com o HEAD atual; não presumir que o registro representa o estado remoto mais recente.
6. Ler `documentacao/06_STATUS_E_RELATORIOS.md` e `documentacao/03_ROADMAP_ETAPAS.md` para identificar situação, etapa e prioridades.
7. Consultar Project Charter, escopo, ADRs, regras, modelos e relatórios específicos da tarefa.
8. Separar fatos documentados, implementação verificada, decisões aprovadas, propostas, pendências e inferências não verificadas.
9. Se o repositório estiver inacessível, desatualizado ou incompleto, declarar a limitação. Não preencher lacunas com suposições nem afirmar que recuperou a memória integral.

A leitura deve ser proporcional: os documentos de entrada e a sessão mais recente são obrigatórios; os demais documentos devem ser selecionados conforme as dependências da tarefa. Não é necessário carregar todos os arquivos indiscriminadamente.

## 1. Regra obrigatória: verificar o GitHub antes de pedir ações locais

Sempre que o usuário solicitar continuidade, inspeção, auditoria, leitura, alteração ou documentação relacionada ao repositório:

1. Verificar se a integração do GitHub está disponível e autenticada.
2. Identificar o repositório correto. Para este projeto, começar por `lucasfaborges94/erp-winservice-dev-docs`.
3. Confirmar a branch padrão e consultar o estado remoto atual.
4. Ler diretamente no GitHub os arquivos, diretórios, commits ou metadados necessários à tarefa.
5. Preferir executar a inspeção ou alteração diretamente pela integração do GitHub, quando a operação estiver disponível e autorizada.
6. Não pedir ao usuário que execute PowerShell, Git ou comandos locais quando a mesma verificação puder ser feita diretamente no GitHub.
7. Solicitar uma ação local somente quando ela depender de estado que não está disponível remotamente, como arquivos não enviados, alterações locais não commitadas, credenciais locais, execução de testes na máquina do usuário ou operações que a integração não suporte.
8. Se a integração falhar ou não tiver permissão suficiente, informar precisamente a limitação antes de transferir a tarefa ao usuário.

## 2. Verificação de contexto e autoridade

Antes de alterar qualquer arquivo:

- Consultar este protocolo e a `00_FONTE_DA_VERDADE.md`.
- Consultar os documentos de governança, continuidade e escopo pertinentes.
- Confirmar se a decisão está aprovada e em qual etapa se aplica (MVP ou etapa futura).
- Distinguir proposta, decisão aprovada, documentação e implementação efetiva.
- Não presumir que uma decisão conceitualmente aprovada autoriza alterações no Supabase, código ou ambiente.
- Não reintroduzir tecnologias, integrações ou funcionalidades retiradas do escopo vigente.

## 3. Procedimento para leitura e auditoria

- Examinar os arquivos diretamente no GitHub e identificar a branch/ref consultada.
- Para auditorias de segurança, verificar o conteúdo atual e, quando o objetivo exigir, o histórico de commits.
- Procurar padrões específicos de credenciais e também atribuições genéricas de segredos, sem reproduzir valores sensíveis na conversa.
- Não classificar palavras como `secret`, `password` ou `service_role` isoladamente como vazamento: avaliar o contexto e se há valor efetivo.
- Registrar cobertura e limitações da auditoria: arquivos, commits, branches e padrões efetivamente examinados.
- Não declarar auditoria completa se houver histórico, branches, arquivos ou intervalos não examinados.
- Não afirmar que GitHub Secret Scanning, push protection ou outra proteção está habilitada sem consultar seu estado real.
- Nunca copiar credenciais para relatórios, comentários, commits ou mensagens. Se um segredo real for encontrado, informar apenas arquivo, localização aproximada, tipo e ação recomendada, sem revelar o valor.
- Se houver indício de segredo real publicado, recomendar revogação/rotação imediata. Remover o valor do arquivo atual não elimina sua presença no histórico Git.

## 4. Procedimento para alterações

- Ler a versão atual do arquivo antes de editar.
- Fazer alterações pequenas, específicas e revisáveis.
- Preservar conteúdo e decisões não relacionadas à tarefa.
- Usar a branch acordada e mensagens de commit descritivas.
- Não executar alterações destrutivas, reescrita de histórico, exclusão de arquivos ou mudanças de configuração de segurança sem autorização explícita.
- Após gravar, reler o arquivo no GitHub e conferir o commit resultante.
- Informar exatamente quais arquivos foram criados ou modificados e o SHA/link do commit.
- Não afirmar que algo foi salvo, commitado ou publicado sem confirmação da integração.

## 5. Entrega ao usuário

- Para um arquivo alterado, entregar somente esse arquivo; para vários arquivos, agrupar em ZIP quando houver geração de artefato local disponível, ou informar os caminhos individuais no GitHub.
- Identificar explicitamente os arquivos alterados.
- Separar resultados verificados de hipóteses e recomendações.
- Não solicitar que o usuário repita informações já disponíveis no repositório ou no contexto do projeto.
- Não pedir ao usuário para colar saídas de comandos que a integração do GitHub pode obter diretamente.

## 6. Limites da integração

A integração do GitHub permite consultar e modificar recursos remotos autorizados, mas não revela automaticamente:

- arquivos locais ainda não enviados;
- alterações locais não commitadas ou não rastreadas;
- segredos armazenados em ambientes externos;
- estado de serviços, banco de dados ou infraestrutura que não tenham integração própria;
- resultados de execução de testes que dependam da máquina ou ambiente do usuário.

Nesses casos, explicar o que não está acessível e solicitar somente a evidência mínima necessária.

## 7. Regra de manutenção da memória documental

Ao longo do trabalho, identificar o tipo de conhecimento produzido e registrá-lo no local correto:

- decisão arquitetural ou de governança aprovada: ADR individual e índice correspondente;
- requisito ou regra de negócio: especificação funcional/processual pertinente;
- proposta ainda não aprovada: documento de proposta ou rascunho, com estado explícito;
- mudança de implementação: código/migração e evidências de teste, sem confundir documentação com implementação;
- descoberta de auditoria: relatório datado, com escopo, evidências, limitações e ambiente;
- mudança de andamento, pendência ou próximo passo: status e registro de continuidade.

Não duplicar o mesmo conteúdo em vários documentos sem necessidade. Usar referências cruzadas e manter índices atualizados. Ao encerrar uma sessão relevante, verificar se outro profissional ou agente consegue retomar o trabalho sem consultar a conversa original.

## 8. Regra de continuidade

Este protocolo deve ser consultado no início de toda nova sessão de trabalho que envolva o repositório. Em caso de dúvida, a regra é verificar primeiro o GitHub e só depois solicitar intervenção local.

A existência deste documento não substitui a autorização humana para decisões de produto, arquitetura, segurança, alterações no banco ou operações irreversíveis.
