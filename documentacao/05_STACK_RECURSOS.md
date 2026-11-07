# Stack, ferramentas e recursos — ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-07

## 1. Stack definida / direção aprovada

| Tecnologia | Papel no projeto | Estado |
|---|---|---|
| Supabase | Banco PostgreSQL, autenticação/serviços de backend conforme arquitetura | Fonte de verdade |
| n8n | Orquestração e automação de workflows | Utilizado / definido |
| WeWeb | Aplicação administrativa web | Direção aprovada; hospedagem na VPS |
| FlutterFlow | Aplicativo móvel técnico | Direção aprovada; offline-first |
| Chatwoot | Atendimento omnichannel transitório | Transição |
| Módulo Chat próprio | Atendimento omnichannel nativo no ERP | Direção futura |
| Auvo | Integração externa opcional | Não é dependência |
| Isa | Agente de IA para processos permitidos | Política de autonomia definida |
| VPS + Easypanel | Hospedagem de serviços e aplicações | Ambiente existente; topologia final a documentar |
| Git / repositório | Controle de versão e histórico de alterações | Estratégia e URL do repositório a registrar |

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
