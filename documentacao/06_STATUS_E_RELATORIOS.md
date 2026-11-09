

## 30. Revisão dos arquivos de orientação e manifesto da API — 2026-11-09

A saída local compartilhada pelo responsável confirma que `@nestjs/mau` já não consta nas dependências de `apps/api/package.json`. Entretanto, o script `deploy: nest deploy` permanece no manifesto e deve ser retirado, pois não corresponde ao fluxo aprovado de hospedagem VPS/Easypanel.

O `AGENTS.md` ainda contém instruções específicas do NestJS Observe, ferramenta não adotada na fundação. O `README.md` permanece como template do NestJS, incluindo referências a Mau/AWS, Observe e licença MIT do framework. Ambos devem ser ajustados/substituídos por conteúdo próprio e alinhado à ADR-0019. O `package-lock.json` não foi apresentado nesta interação e sua consistência permanece não verificada.

**Estado:** revisão dos arquivos concluída; nenhum ajuste local, commit ou push foi executado pelo assistente ou confirmado pelo responsável nesta mensagem. Build, testes unitários, e2e e lint precisam ser repetidos após os ajustes. O scaffold ainda não implementa módulos ou endpoints de negócio e continua sujeito aos gates de autenticação, tenancy, autorização, contexto transacional/RLS e privilégios definidos na ADR-0019. Nenhuma operação Supabase foi realizada.

**Próximos passos:** remover o script deploy; substituir a seção Observe de AGENTS.md por orientações neutras; substituir README pelo guia da API ERP; conferir package-lock.json; repetir build/testes/lint e revisar diff/status antes do primeiro commit.


## 31. Remoção do script deploy e validação automatizada da API — 2026-11-09

O responsável reportou a execução de `npm.cmd pkg delete scripts.deploy` no diretório `apps/api`, seguida de quatro verificações locais:

- `npm.cmd run build`: aprovado, sem erro;
- `npm.cmd test`: 1 arquivo e 1 teste aprovados;
- `npm.cmd run test:e2e`: 1 arquivo e 1 teste aprovados;
- `npm.cmd run lint`: 0 warnings e 0 errors.

**Estado:** validação automatizada aprovada após a remoção reportada de Mau e do script deploy. A execução do comando de remoção não substitui a inspeção do conteúdo final do package.json; package-lock.json, AGENTS.md e README.md atualizados ainda precisam ser revisados. Nenhum commit/push do scaffold foi confirmado. O scaffold permanece sem módulos/endpoints de negócio e sujeito aos gates da ADR-0019. Nenhuma operação Supabase foi realizada.

**Próximo passo:** revisar os quatro arquivos atualizados e o diff/status local antes de preparar o primeiro commit.