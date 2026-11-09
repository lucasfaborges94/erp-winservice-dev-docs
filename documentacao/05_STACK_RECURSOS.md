# Stack, ferramentas e recursos — ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-07

## 1. Stack definida / direção aprovada

| Tecnologia | Papel no projeto | Estado |
|---|---|---|
| Supabase | Banco PostgreSQL e serviços de backend conforme arquitetura | Fonte de verdade |
| NestJS / TypeScript | API interna do ERP, monólito modular com REST versionada | Direção aprovada pela ADR-0019; ainda não implementada |
| n8n | Orquestração e automação de workflows | Utilizado / definido |
| WeWeb | Aplicação administrativa web | Direção aprovada; hospedagem na VPS |
| FlutterFlow | Aplicativo móvel técnico | Direção aprovada; offline-first |
| Chatwoot | Atendimento omnichannel transitório | Transição |
| Módulo Chat próprio | Atendimento omnichannel nativo no ERP | Direção futura |
| Auvo | Integração externa opcional | Não é dependência |
| Isa | Agente de IA para processos permitidos | Política de autonomia definida |
| VPS + Easypanel | Hospedagem de serviços e aplicações, incluindo inicialmente a API ERP | Ambiente existente; API aprovada para hospedagem inicial, ainda não implantada |
| Git / repositório documental | Governança, requisitos, ADRs e continuidade | `lucasfaborges94/erp-winservice-dev-docs` |
| Git / repositório de aplicação | Código-fonte, migrations, testes e CI/CD | Confirmado: `lucasfaborges94/erp-winservice-app`, privado, branch `main`, integração com permissão admin; inicializado com inventário estrutural Supabase e README, ainda sem migrations |

## 2. Recursos e práticas técnicas

- SQL e PostgreSQL para modelagem, constraints, funções, triggers e políticas RLS.
- Migrações de banco versionadas e revisadas.
- Controle de versão do código e documentação.
- Separação de ambientes de desenvolvimento, homologação e produção.
- Gestão de segredos por variáveis de ambiente/secret manager, sem credenciais em documentos.
- Backups, testes de restauração e monitoramento.
- Testes automatizados de regras, autorização, isolamento multiempresa e sincronização offline.
- Armazenamento de arquivos e evidências com política de acesso.
- Logs e trilhas de auditoria sem exposição indevida de dados pessoais.

## 3. Regras de arquitetura e desenvolvimento

- Não usar a interface como única camada de segurança.
- Validar permissões no backend e no banco.
- Aplicar RLS e testar acesso cruzado entre empresas.
- Manter integrações desacopladas por adaptadores/contratos.
- Não transformar sistemas externos em fonte de verdade sem decisão formal.
- Evitar dependência proprietária desnecessária e preservar exportabilidade.
- Documentar decisões técnicas relevantes como ADR.

## 4. Recursos ainda a inventariar

- Plano e região de hospedagem da VPS atual.
- Versões de Supabase, n8n, Chatwoot e demais serviços.
- Domínios, subdomínios e ambientes.
- Repositórios Git, branches e estratégia de release.
- Serviços de e-mail, mensageria, armazenamento e monitoramento.
- Serviços externos e respectivos limites/custos.
- Responsáveis e acessos administrativos (não inserir senhas ou tokens).
- Custos recorrentes e orçamento de infraestrutura.

## 5. Segurança documental

Nunca registrar neste documento senhas, tokens, chaves privadas, service-role keys, segredos de webhook ou dados pessoais sensíveis. Registrar apenas onde o segredo é gerenciado e quem é o responsável autorizado.

## 6. Verificação do repositório de aplicação — 2026-11-08

A integração GitHub confirmou metadados do repositório `lucasfaborges94/erp-winservice-app`: privado, branch padrão `main`, não arquivado e permissão `admin` para a integração. As APIs de conteúdo e commits confirmaram que o repositório está vazio. Nenhum código, migration ou pipeline foi criado. A estrutura inicial e a estratégia de migrations permanecem por definir; qualquer baseline deverá ser reconciliado com o estado real do Supabase antes de ser criado.


## 6.1 Delegação operacional e baseline Supabase — 2026-11-08

O responsável designou o agente atual como operador exclusivo para executar alterações no Supabase. A execução continuará subordinada aos gates de segurança, revisão, preflight, backup restaurável e autorização operacional específica já registrados; a delegação não será interpretada como autorização irrestrita.

O repositório de aplicação contém o inventário estrutural inicial em `supabase/baseline/2026-11-08-schema-inventory.json` e o relatório de advisors em `supabase/baseline/2026-11-08-advisors.json`. Ainda não há dump SQL restaurável nem baseline de migrations: o Supabase retornou zero migrations, a relação de histórico não existe e a integração disponível não fornece Supabase CLI/`pg_dump`.


## 6.2 Arquitetura da API interna — ADR-0019 aprovada em 2026-11-08

A API interna foi aprovada como monólito modular em TypeScript/NestJS, com REST versionada em `/api/v1`, hospedada inicialmente na VPS existente via Easypanel. O código ficará no repositório privado `lucasfaborges94/erp-winservice-app`. Supabase permanece como PostgreSQL/fonte de verdade e Supabase Auth como provedor de identidade. WeWeb e FlutterFlow consumirão a API para operações de negócio.

A decisão não comprova implementação nem autoriza deploy ou alteração no Supabase. Antes de implementar endpoints de negócio, devem ser definidos e testados autenticação, empresa ativa, vínculo e papéis, contexto transacional até RLS, privilégios de banco, reconciliação das policies, contratos REST e testes negativos multiempresa. Consultar a ADR-0019 para os gates completos.


## 6.3 Scaffold local da API — 2026-11-09

O responsável reportou a geração local do scaffold em `apps/api` com NestJS CLI `12.0.12`, Node.js `v26.13.0`, npm `11.20.0`, ESM e Vitest. A instalação foi concluída pelo CLI sem erro aparente; a integração GitHub ainda não vê esses arquivos porque não há commit/push confirmado. Inicialização, build e testes ainda não foram verificados. A opção NestJS Observe foi recusada para manter a fundação enxuta.
