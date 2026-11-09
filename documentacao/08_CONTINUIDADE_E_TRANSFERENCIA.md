

### Continuação — revisão de AGENTS.md, README.md e package.json (2026-11-09)

O responsável compartilhou a saída local de `Get-Content apps\api\AGENTS.md`, `README.md` e `package.json`.

**Constatações verificadas na saída:**
- `package.json` não contém mais `@nestjs/mau` nas dependências, confirmando a remoção refletida no manifesto.
- O script `"deploy": "nest deploy"` ainda está presente e deve ser removido, pois o destino aprovado pela ADR-0019 é a VPS existente via Easypanel, não o fluxo Mau/AWS.
- `AGENTS.md` ainda contém a seção “Production data: NestJS Observe”, com instruções de instalação/link e uso da ferramenta. Remover essa seção ou substituí-la por orientações neutras de diagnóstico, sem recomendar a adoção do Observe.
- `README.md` ainda é o template padrão NestJS e contém referências a Mau/AWS, NestJS Observe e licença MIT do framework. Substituir por documentação própria da API ERP; manter clara a licença `UNLICENSED` da aplicação.
- O conteúdo de `package-lock.json` não foi compartilhado nesta interação. A remoção pelo npm foi reportada anteriormente, mas a consistência do lockfile ainda não foi inspecionada diretamente.

**Estado e limites:** a revisão foi concluída, mas nenhuma alteração local foi executada pelo assistente. Não há commit/push confirmado e o scaffold segue sem módulos de negócio. A remoção de Mau no manifesto está confirmada; o script deploy e os documentos ainda precisam de ajuste. Após as alterações, executar novamente build, testes unitários, e2e e lint, revisar manifests/lockfile e diff. Não criar commit sem revisão e autorização explícita. Nenhuma operação no Supabase foi realizada.

**Próxima atividade:** alinhar os três arquivos à ADR-0019, inspecionar o lockfile atualizado e repetir as verificações automatizadas antes de preparar o primeiro commit.


### Continuação — remoção do script deploy e validação automatizada (2026-11-09)

O responsável informou que executou em `apps/api`:
- `npm.cmd pkg delete scripts.deploy`;
- `npm.cmd run build` — concluído sem erro;
- `npm.cmd test` — 1 arquivo e 1 teste aprovados;
- `npm.cmd run test:e2e` — 1 arquivo e 1 teste aprovados;
- `npm.cmd run lint` — 0 warnings e 0 errors.

**Estado verificado por evidência compartilhada:** os quatro gates automatizados (build, unitário, e2e e lint) passaram após a limpeza reportada. O comando de remoção do script deploy foi executado, mas ainda não foi feita leitura do package.json atualizado para confirmar o estado final do manifesto. O package-lock.json permanece sem inspeção direta. AGENTS.md e README.md ainda aguardam revisão das versões atualizadas.

**Limites:** execução local reportada pelo responsável; não houve acesso à máquina local, commit ou push confirmado. Nenhuma operação no Supabase foi realizada.

**Próxima atividade:** revisar as versões atualizadas de AGENTS.md, README.md, package.json e package-lock.json; verificar diff e status local antes de preparar o primeiro commit. Não criar commit sem autorização explícita.