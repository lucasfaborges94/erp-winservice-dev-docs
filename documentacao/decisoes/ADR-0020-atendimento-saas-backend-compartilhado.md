# ADR-0020 — Produto SaaS de atendimento automatizado com núcleo compartilhado do ERP

**Identificador:** ADR-0020  
**Estado:** PROPOSTA  
**Data da proposta:** 2026-11-14  
**Data da aprovação/rejeição:** Não aplicável  
**Responsável pela decisão:** Responsável pelo projeto  
**ADR relacionado/substituído:** Complementa ADR-0001, ADR-0002, ADR-0005, ADR-0006, ADR-0007, ADR-0009, ADR-0011, ADR-0012, ADR-0017, ADR-0018 e ADR-0019; não substitui nenhuma enquanto não aprovada.

## 1. Contexto

O ERP Winservice é planejado como SaaS multiempresa, com a Winservice como primeira empresa usuária. O ERP já possui direção aprovada de API NestJS modular no repositório `lucasfaborges94/erp-winservice-app`, Supabase/PostgreSQL como fonte de verdade e autenticação Supabase Auth. O MVP operacional do ERP prioriza Operação; a ADR-0019 exclui integrações externas do MVP. O responsável deseja utilizar antecipadamente a qualificação automatizada de leads da Winservice e, posteriormente, comercializar o atendimento como SaaS, sem reconstruí-lo.

Esta ficha registra **uma alternativa proposta**, não uma alteração aprovada de prioridade, escopo, implementação ou produção.

## 2. Problema ou necessidade

Permitir a entrega independente do produto de atendimento, com contratação opcional em relação ao ERP, reaproveitando serviços centrais sem duplicar regras ou expor configurações e dados de uma empresa a outra.

## 3. Alternativas consideradas

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Atendimento apenas como módulo interno do ERP | Integração simples | Entrega antecipada condicionada ao ERP |
| SaaS de atendimento totalmente separado, inclusive backend e banco | Autonomia técnica | Duplicação de identidade, cadastros, regras e governança |
| Produto de atendimento independente com API NestJS e Supabase compartilhados | Lançamento incremental e reutilização de domínio | Exige contratos, isolamento tenant e governança de deploy compartilhado |

## 4. Direção proposta — NÃO APROVADA

1. Oferecer o atendimento automatizado como **produto SaaS independente comercialmente e na interface**, inicialmente utilizado pela Winservice como primeiro tenant.
2. Reutilizar a **mesma API NestJS modular** e o mesmo Supabase/PostgreSQL previstos para o ERP, sem duplicar o backend em outro repositório. Separar módulos e contratos de domínio.
3. Manter a API, migrations e regras compartilhadas em `erp-winservice-app`. **Propor**, sem criar ainda, o repositório privado `winservice-atendimento-ia` para interface própria, configuração versionada do agente, workflows n8n exportáveis e testes conversacionais. Credenciais e dados pessoais não devem ser versionados.
4. Permitir habilitar produtos/módulos por empresa, de forma que contratar atendimento não exija contratar o ERP operacional.
5. Aplicar configurações de negócio por empresa (equipamentos, marcas, regiões, condições comerciais, horários, disponibilidade, mensagens e transferência humana). Regras particulares da Winservice não serão defaults globais nem hardcoded no motor.
6. Oferecer onboarding estruturado para empresas sem documentação prévia; documentos internos e base de conhecimento serão opcionais. Quando uma informação essencial faltar, a IA deve solicitar confirmação ou transferir ao humano, sem inventar valores nem consultar informações de outro tenant.
7. Utilizar n8n como orquestrador de integração/automação e WhatsApp como canal inicial, com operações de negócio validadas pela API e controles da ADR-0012. Chatwoot permanece transitório conforme ADR-0007, sem criar dependência permanente.
8. MVP do atendimento: recepção, identificação/contexto do cliente, qualificação por equipamento, marca, defeito/solicitação e região, validação de regras configuradas, histórico e transferência para humano. Comercial completo e agendamento automático são fases posteriores.
9. Manter a prioridade do MVP operacional do ERP conforme ADR-0009/ADR-0019 **até aprovação explícita de eventual mudança de roadmap**. O atendimento terá plano de entrega próprio; esta proposta não autoriza alterar a ordem vigente do ERP.

## 5. Justificativa

O produto pode começar com um tenant real sem criar um protótipo descartável. O núcleo compartilhado evita divergência entre o atendimento e o ERP; a separação de interfaces e integrações preserva a possibilidade de comercialização e implantação gradual.

## 6. Consequências e impactos

### Benefícios esperados
- Qualificação utilizável pela Winservice antes de completar o ERP.
- Reutilização de identidade, clientes, regras e histórico mediante contratos autorizados.
- Possibilidade de contratação do atendimento sem módulos operacionais do ERP.

### Custos, riscos e limitações
- A API compartilhada vira dependência operacional de dois produtos; requer versionamento, disponibilidade, compatibilidade e deploy disciplinado.
- O compartilhamento de banco não significa que todos os tenants ou produtos podem acessar todas as entidades.
- Onboarding, planos, limites, billing, provisionamento e suporte SaaS ainda precisam de especificação.
- Integrações WhatsApp/n8n não passam automaticamente a integrar o MVP operacional do ERP.

### Impactos operacionais e dependências
- Documentação técnica e decisões continuam em `erp-winservice-dev-docs`; regras operacionais da Winservice podem continuar em repositório próprio, mas devem ser transformadas em configurações tenant-scoped antes do uso em produção.
- É necessário definir fonte autoritativa, publicação, atualização e auditoria dessas configurações; GitHub da Winservice não deve ser consultado como base global de respostas.
- Definir propriedade dos dados, tratamento de falhas, idempotência e observabilidade dos workflows.

## 7. Segurança, privacidade e isolamento multiempresa

- Respeitar ADR-0017, ADR-0018, ADR-0019 e especificação técnica de JWT, empresa ativa, contexto transacional e RLS.
- Toda leitura, escrita, consulta de documentos, cache, memória conversacional, índice vetorial, workflow, segredo e ferramenta da IA deve ser vinculada ao tenant autorizado e, quando aplicável, ao atendimento/cliente correto.
- O isolamento não é garantido apenas por repositórios separados ou por prompts; deve ser aplicado e testado na API, no banco e nas integrações.
- Nunca usar `service_role` como caminho normal de acesso nem contornar RLS implicitamente.
- Observar limites de autonomia da Isa (ADR-0012), retenção, consentimento/base legal aplicável, minimização de dados e auditoria.
- Segredos de canais e tokens devem ser segregados por empresa e ambiente, sem versionamento em Git.
- Testes negativos devem provar ausência de vazamento de regras, histórico, preços, documentos e mensagens entre empresas.

## 8. Implementação e validação — condicionadas a aprovação futura

1. Revisar esta ADR e decidir explicitamente seu estado; resolver a relação com a prioridade de Operação da ADR-0009 e o limite de integrações da ADR-0019, sem alterar silenciosamente essas decisões.
2. Especificar contratos entre produto de atendimento, API compartilhada, motor de regras, n8n e canal WhatsApp, incluindo tenancy, autenticação máquina-a-máquina, idempotência e tratamento de falhas.
3. Definir esquema e onboarding por tenant, catálogo configurável, regras comerciais, base de conhecimento opcional e habilitação de produtos.
4. Completar gates de backup restaurável, baseline de migrations, JWT, autorização e RLS antes de expor endpoints de negócio ou dados de produção.
5. Implementar e testar em ambiente isolado com pelo menos dois tenants fictícios, inclusive testes de vazamento de contexto, ausência de documentação, falta de regras, transferência humana e autorização negativa.
6. Implantar na Winservice somente após revisão e autorização operacional específica; validar resultados antes de comercializar para outros tenants.

**Esta ADR não autoriza criação de repositórios, código, migrações, DDL/DML, workflows, mudanças de segurança, provisionamento ou deploy.**

## 9. Referências e evidências

- `00_FONTE_DA_VERDADE.md`
- `documentacao/01_PROJECT_CHARTER.md`
- `documentacao/03_ROADMAP_ETAPAS.md`
- `documentacao/04_MODULOS_ESCOPO.md`
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`
- `documentacao/decisoes/ADR-0009-prioridade-v1-operacao-primeiro.md`
- `documentacao/decisoes/ADR-0012-autonomia-isa-operacoes-permitidas.md`
- `documentacao/decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md`
- `documentacao/decisoes/ADR-0019-arquitetura-api-interna.md`
- `documentacao/ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md`
- Conversa de planejamento de 2026-11-14; proposta solicitada pelo responsável, sem aprovação do conteúdo técnico.

## 10. Revisão e substituição

- **Substitui:** Nenhuma.
- **Substituído por:** Nenhuma.
- **Motivo de revisão:** A definir após análise do responsável.

## Histórico desta ficha

- 2026-11-14 — ficha criada em estado **PROPOSTA**, para revisão; nenhuma mudança operacional autorizada.
