

### Revisão dos arquivos do scaffold após remoção de Mau — 2026-11-09

O responsável compartilhou a leitura local de `apps/api/AGENTS.md`, `README.md` e `package.json`. A inspeção confirma que `@nestjs/mau` não aparece mais nas dependências do `package.json`, mas o script `"deploy": "nest deploy"` permanece e deve ser removido por não corresponder ao destino aprovado VPS/Easypanel. O `package-lock.json` não foi compartilhado, portanto sua consistência ainda não foi verificada.

O `AGENTS.md` mantém uma seção de telemetria e instruções operacionais do NestJS Observe, incompatíveis com a decisão de não adotar essa ferramenta na fundação. O `README.md` continua sendo o template padrão do NestJS, com conteúdo de Mau/AWS, Observe e licença MIT do framework; deve ser substituído por documentação própria da API, sem confundir a licença do framework com a licença `UNLICENSED` da aplicação.

**Estado:** revisão dos três arquivos concluída; ainda não houve alteração local confirmada, commit ou push. Permanecem pendentes a adequação de AGENTS.md e README.md, remoção do script deploy, inspeção do package-lock.json e repetição de build, testes unitários, e2e e lint após os ajustes. O scaffold continua sem módulos de negócio; os gates da ADR-0019 permanecem aplicáveis.


### Validação automatizada após remoção de Mau e do script deploy — 2026-11-09

O responsável reportou a execução local, dentro de `apps/api`, de `npm.cmd pkg delete scripts.deploy`, seguida de build, testes unitários, testes e2e e lint. Todos os comandos terminaram sem erro: build NestJS aprovado; Vitest unitário com 1 arquivo/1 teste aprovado; Vitest e2e com 1 arquivo/1 teste aprovado; Oxlint com 0 warnings e 0 errors. A remoção do script foi executada, mas o conteúdo atualizado do manifesto ainda não foi inspecionado diretamente. O lockfile também permanece não verificado.

**Próximo passo:** receber/revisar AGENTS.md, README.md, package.json e package-lock.json atualizados; conferir consistência e diff/status local antes de qualquer commit. Nenhum commit/push do scaffold foi confirmado.