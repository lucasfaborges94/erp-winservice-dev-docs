# Mapa de módulos e escopo — ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-07  
**Estado:** mapa macro inicial; detalhamento por módulo pendente

## 1. Princípios de organização

- Módulos representam domínios de negócio e responsabilidades.
- Funcionalidades podem compartilhar serviços de plataforma.
- Um módulo não deve acessar diretamente detalhes internos de outro; preferir contratos e serviços bem definidos.
- A inclusão no mapa não significa que o módulo esteja implementado.
- O escopo deve ser refinado em requisitos, histórias, regras e critérios de aceite.

## 2. Mapa macro

| Domínio / módulo | Responsabilidade macro | Observação |
|---|---|---|
| Núcleo da plataforma | Empresas, usuários, autenticação, autorização, módulos, parâmetros e auditoria | Fundação transversal |
| Cadastros | Clientes, contatos, endereços, equipamentos, fornecedores e outros cadastros | Dados mestres |
| Operação | OS, visitas, execução técnica, estados, histórico e encerramento | Prioridade da V1 |
| Comercial | Orçamentos, propostas, aprovação, catálogo/preços e condições comerciais | Integrado à operação |
| Financeiro | Despesas, contas, recebimentos, pagamentos, conciliação e indicadores | Escopo a detalhar |
| Atendimento | Conversas, filas, canais, histórico e atendimento omnichannel | Chatwoot transitório; Chat próprio futuro |
| Inteligência e automação | Isa, workflows, regras, classificações e automações | n8n na automação |
| Gestão | Relatórios, indicadores, administração e gestão interna | Transversal |
| Integrações | Conectores, webhooks, credenciais, sincronização e filas | Desacopladas e opcionais |
| Aplicação web | Interface administrativa e operacional | WeWeb hospedado na VPS |
| Aplicativo móvel | Aplicativo técnico offline-first e GPS em background | FlutterFlow |
| Jurídico | Gestão jurídica empresarial, contratos, documentos, ocorrências e prazos | Módulo amplo |
| SaaS / administração da plataforma | Onboarding, planos, limites, faturamento SaaS e suporte multiempresa | Futuro |

## 3. Módulo Jurídico — direção aprovada

O módulo Jurídico será amplo e não se limitará a documentos legais.

Funcionalidades candidatas a detalhar:
- Gestão de documentos e instrumentos jurídicos.
- Controle de versões, vigência, aprovação e publicação.
- Contratos com clientes, fornecedores e parceiros.
- Aceites, assinaturas e evidências.
- Ocorrências jurídicas, reclamações, notificações e disputas.
- Prazos, obrigações e compromissos jurídicos.
- Procurações e autorizações.
- Conformidade e controles internos.

**Primeira funcionalidade a especificar:** Gestão de Documentos e Instrumentos Jurídicos.

Decisões já registradas para documentos:
- Conteúdo e versões armazenados no banco.
- Páginas públicas consultam versão vigente.
- Histórico publicado preservado, sem sobrescrita.
- Deve ser possível registrar qual versão foi apresentada e aceita em cada contratação.
- Não tratar apresentação, ciência, aceite contratual e consentimento como eventos juridicamente equivalentes.

Formato de conteúdo, workflow de aprovação, publicação programada e modelo de eventos ainda precisam de decisão formal.

## 4. Fronteiras e integrações

- A camada de plataforma deve fornecer identidade, autorização, auditoria, arquivos, notificações e serviços compartilhados.
- A integração com Auvo será opcional e não pode ser requisito para operação do ERP.
- Chatwoot é uma solução transitória; a arquitetura deve evitar dependência estrutural que impeça o Chat próprio.
- A Isa deve atuar por APIs e permissões de backend, respeitando sua política de autonomia.
