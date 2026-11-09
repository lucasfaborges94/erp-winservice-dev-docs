# Procedimento de backup lógico e validação de baseline Supabase

**Estado:** PROCEDIMENTO PROPOSTO — ainda não executado  
**Data da pesquisa documental:** 2026-11-09  
**Projeto de referência:** Winservice (voaovwphncwbrwtcqnsc)  
**Finalidade:** apoiar o Gate G0 da especificação de autenticação, contexto e RLS.

## 1. Limites e princípio de segurança

Este procedimento não autoriza alterações no projeto remoto. A captura deve ser somente leitura. Não executar migrations, DDL, DML, alterações de roles/grants/policies, reset, restore ou deploy no projeto de produção.

Não compartilhar senha do banco, connection string completa, access token, service_role, dump com dados reais ou arquivos contendo dados pessoais na conversa ou no GitHub. Arquivos de dados devem permanecer em armazenamento local criptografado e com acesso restrito. O repositório deve receber apenas artefatos estruturais revisados e sanitizados.

O inventário JSON existente não é backup restaurável. Um dump lógico e um backup físico do provedor são artefatos distintos e complementares.

## 2. Opções oficiais identificadas

### Opção A — restauração física para um novo projeto Supabase

A documentação oficial descreve Restore to a new project (recurso beta), que cria uma cópia database-only. A cópia inclui schema, tabelas, views, procedures, dados, índices, roles/permissões/usuários e dados do Auth, incluindo contas e registros de autenticação. Não copia objetos/configurações do Storage, Edge Functions, configurações Auth/API keys, Realtime, configurações de extensões e réplicas. O recurso exige plano pago e backups físicos habilitados; o novo projeto gera cobrança adicional. Extensões com efeitos externos podem iniciar jobs assim que a restauração terminar, portanto devem ser avaliadas antes de escolher essa rota.

É a opção mais abrangente para provar restauração do banco e Auth, mas não deve ser iniciada sem estimar e aprovar o custo e revisar o risco das extensões.

### Opção B — dump lógico via Supabase CLI

A documentação oficial orienta instalar Supabase CLI e Docker, obter a connection string em Dashboard → Connect e executar três dumps separados: roles, schema e data. A conexão direta é preferível; usar Session Pooler se a rede não suportar IPv6 e não houver IPv4 add-on.

Exemplo oficial (substituir o placeholder somente no terminal local; nunca colar credenciais no chat):

```bash
supabase db dump --remote="$DATABASE_URL" -f roles.sql --role-only
supabase db dump --remote="$DATABASE_URL" -f schema.sql
supabase db dump --remote="$DATABASE_URL" -f data.sql --use-copy --data-only -x "storage.buckets_vectors" -x "storage.vector_indexes"
```

A CLI executa pg_dump com exclusões de schemas gerenciados pelo Supabase, incluindo auth, storage e schemas de extensões. Portanto, esse conjunto não é uma cópia integral do projeto Supabase e não substitui a opção A para comprovar restauração completa do Auth. Customizações próprias em auth/storage (por exemplo triggers ou policies próprias) exigem captura/diff separado, conforme documentação oficial.

O arquivo data.sql pode conter dados pessoais e operacionais. Não enviar esse arquivo para a conversa nem versioná-lo no Git. Mantê-lo localmente, criptografado e com acesso restrito.

## 3. Sequência recomendada para o Gate G0

1. Confirmar no Dashboard qual método de backup está habilitado e quais pontos de restauração existem. Não clicar em Restore no projeto de produção.
2. Para o baseline lógico inicial, executar a Opção B em máquina controlada, com connection string mantida apenas em variável de ambiente local (DATABASE_URL).
3. Guardar roles.sql, schema.sql e data.sql em pasta local protegida. Gerar hashes SHA-256 e registrar data/hora UTC, versão da CLI, versão PostgreSQL e origem do projeto em um manifesto sem credenciais.
4. Revisar roles.sql e schema.sql antes de qualquer versionamento. Remover/mascarar valores sensíveis, verificar funções, grants, owners, policies e customizações. Nunca presumir que um dump é seguro para publicar só porque não contém linhas de tabelas.
5. Manter data.sql fora do repositório. Para teste de restauração, usar ambiente isolado e dados protegidos; não restaurar sobre produção.
6. Restaurar o conjunto lógico em banco local/ambiente descartável compatível, registrar comandos, versão, logs e resultado. A restauração deve falhar imediatamente diante de erro (ON_ERROR_STOP).
7. Documentar explicitamente o escopo restaurado e o que ficou fora: schemas gerenciados, Auth, Storage objects, configurações de projeto, extensões e outros componentes.
8. Se for necessário provar recuperação completa de Auth e banco, planejar a Opção A em projeto novo, depois de avaliar extensão, plano e custo. Não usar restore_project contra o projeto de produção para esse ensaio.
9. Só considerar G0 concluído após revisão do baseline, restauração de ensaio bem-sucedida e evidência de cobertura/limitações. Até lá, G0 permanece parcial.

## 4. Arquivos e evidências esperados

- roles.sql: definições de roles e memberships, revisadas e sem segredos.
- schema.sql: schema lógico, objetos, policies e grants abrangidos pelo dump, revisados.
- data.sql: dados reais; armazenamento local criptografado, nunca Git/chat.
- manifesto-backup.md: data/hora UTC, projeto/ref, versão PostgreSQL, versão CLI, escopo, hashes, local seguro dos arquivos (sem caminho sensível), operador e resultado da restauração.
- relatorio-restauracao.md: ambiente de destino, versão, comandos, logs relevantes sem segredos, verificações pós-restore e limitações.

## 5. Próxima ação pendente

O operador local deve executar a captura lógica da Opção B em ambiente controlado e disponibilizar para revisão apenas schema.sql e roles.sql depois de verificar e remover qualquer segredo ou dado pessoal. data.sql permanece privado. A execução do dump e a restauração ainda não foram realizadas por este documento.

## 6. Referências oficiais consultadas

- Supabase — [Backup and Restore using the CLI](https://supabase.com/docs/guides/platform/migrating-within-supabase/backup-restore) (atualizado em 2026-11-02).
- Supabase — [Database Backups](https://supabase.com/docs/guides/platform/backups) (atualizado em 2026-11-05).
- Supabase — [Restore to a new project](https://supabase.com/docs/guides/platform/clone-project) (recurso beta; atualizado em 2026-10-29).
- Supabase — [CLI db dump reference](https://supabase.com/docs/reference/cli/supabase-db-dump).

## Histórico

- 2026-11-09 — criação como procedimento proposto, baseado na documentação oficial atual; nenhuma operação Supabase foi executada.

## 7. Classificação dos dados de teste — declaração do responsável (2026-11-09)

O responsável informou que os registros atualmente existentes no banco são dados de mockup inseridos para fins de teste, mantidos intencionalmente como pistas para compreender a lógica e o funcionamento do modelo de dados em construção. Para fins de análise funcional e estrutural deste projeto, os dados de linhas podem ser considerados dados de teste conforme essa declaração.

Essa classificação não comprova que os dumps estejam livres de credenciais, hashes de autenticação, tokens, chaves, configurações sensíveis ou dados pessoais eventualmente inseridos em campos de teste. Antes de compartilhar ou versionar qualquer arquivo, inspecionar e remover segredos e credenciais. O arquivo data.sql pode ser fornecido ao agente para análise privada após essa triagem; não deve ser publicado no GitHub por padrão. Se contiver apenas dados fictícios e nenhum segredo, o responsável poderá autorizar explicitamente sua inclusão em artefato privado, mas isso não é necessário para o baseline estrutural.

A declaração não altera a necessidade de backup restaurável, ensaio isolado, nem os gates de segurança e autorização.
