

## 30. Revisão dos arquivos de orientação e manifesto da API — 2026-11-09

A saída local compartilhada pelo responsável confirma que `@nestjs/mau` já não consta nas dependências de `apps/api/package.json`. Entretanto, o script `deploy: nest deploy` permanece no manifesto e deve ser retirado, pois não corresponde ao fluxo aprovado de hospedagem VPS/Easypanel.

O `AGENTS.md` ainda contém instruções específicas do NestJS Observe, ferramenta não adotada na fundação. O `README.md` permanece como template do NestJS, incluindo referências a Mau/AWS, Observe e licença MIT do framework. Ambos devem ser ajustados/substituídos por conteúdo próprio e alinhado à ADR-0019. O `package-lock.json` não foi apresentado nesta interação e sua consistência permanece não verificada.

**Estado:** revisão dos arquivos concluída; nenhum ajuste local, commit ou push foi executado pelo assistente ou confirmado pelo responsável nesta mensagem. Build, testes unitários, e2e e lint precisam ser repetidos após os ajustes. O scaffold ainda não implementa módulos ou endpoints de negócio e continua sujeito aos gates de autenticação, tenancy, autorização, contexto transacional/RLS e privilégios definidos na ADR-0019. Nenhuma operação Supabase foi realizada.

**Próximos passos:** remover o script deploy; substituir a seção Observe de AGENTS.md por orientações neutras; substituir README pelo guia da API ERP; conferir package-lock.json; repetir build/testes/lint e revisar diff/status antes do primeiro commit.
