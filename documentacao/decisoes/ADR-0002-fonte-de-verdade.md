# ADR-0002 — Supabase como fonte central de verdade

**Identificador:** ADR-0002  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável:** Responsável pelo projeto  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP Winservice utiliza o Supabase como plataforma central de dados, baseado em PostgreSQL e serviços de backend.

O Project Charter estabelece o Supabase como fonte central de verdade dos dados. A documentação arquitetural também registra que os demais componentes devem operar de maneira integrada a essa fonte central.

A stack contempla WeWeb para a aplicação administrativa, FlutterFlow para o aplicativo técnico, n8n para automações e integrações com serviços externos.

O contexto detalhado da deliberação original não está documentado.

## 2. Problema ou necessidade

Definir qual componente será responsável por manter a referência oficial dos dados do ERP.

É necessário evitar que diferentes aplicações, integrações ou serviços mantenham versões independentes e conflitantes das informações operacionais.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Supabase como fonte central | Centralização e consistência dos dados | Dependência operacional da plataforma e necessidade de disponibilidade |
| Bancos independentes por aplicação | Autonomia local dos componentes | Complexidade de sincronização e risco de divergência |
| Não documentado | Não documentado | Não documentado |

*Observação: a tabela apresenta possibilidades conceituais para contextualização, não alternativas comprovadamente avaliadas na decisão original.*

## 4. Decisão

O **Supabase será a fonte central de verdade dos dados do ERP Winservice**.

Isso estabelece que:

- O banco PostgreSQL do Supabase será a referência oficial dos dados persistentes do ERP.
- As aplicações administrativas e os serviços de automação deverão consultar e persistir dados por meio da arquitetura definida para o Supabase.
- Os demais componentes não deverão estabelecer fontes alternativas independentes para os mesmos dados operacionais.
- Sistemas externos integrados não serão considerados fontes de verdade do ERP apenas por fornecerem informações ou eventos.

A documentação também estabelece uma diretriz específica para o WeWeb: não manter cache operacional local como fonte alternativa.

No FlutterFlow, a operação offline-first permanece prevista. O armazenamento local necessário à operação sem conexão deverá ser tratado como mecanismo de operação e sincronização, não como substituição da fonte central de verdade.

## 5. Justificativa

A justificativa histórica detalhada não está documentada.

O Project Charter registra objetivos compatíveis com essa decisão:

- Centralização e integridade dos dados.
- Redução da dependência de fornecedores.
- Portabilidade dos dados e componentes.
- Arquitetura modular.
- Rastreabilidade das operações.
- Integração entre aplicações e serviços.

Esses objetivos contextualizam a decisão, mas não comprovam quais critérios foram utilizados na aprovação original.

## 6. Consequências e impactos

### Benefícios esperados

- Centralização dos dados persistentes.
- Redução de divergências entre aplicações.
- Padronização dos mecanismos de acesso aos dados.
- Maior facilidade para auditoria e rastreabilidade.
- Menor acoplamento entre aplicações e armazenamento.
- Possibilidade de substituição gradual das interfaces sem migração da fonte central de dados.

### Custos, riscos e limitações

- Dependência da disponibilidade do Supabase.
- Necessidade de planejamento de backups e recuperação.
- Necessidade de controle de concorrência e integridade transacional.
- Necessidade de gerenciamento de capacidade e desempenho.
- Necessidade de mecanismos de sincronização para aplicações offline.

### Impactos operacionais e de manutenção

A modelagem, as regras de integridade e as permissões deverão ser centralizadas na camada de dados e backend.

Mudanças estruturais deverão ser versionadas e revisadas.

O banco deverá ser auditado periodicamente para verificar conformidade com os requisitos e decisões arquiteturais.

### Dependências e integrações

A decisão impacta diretamente:

- WeWeb.
- FlutterFlow.
- n8n.
- APIs e serviços internos.
- Integrações externas.
- Processos de sincronização.
- Rotinas de backup e recuperação.

## 7. Segurança, privacidade e isolamento multiempresa

A centralização dos dados exige controles de segurança consistentes.

A documentação estabelece:

- Aplicação de segurança no backend e no banco, não apenas na interface.
- Uso de RLS para controle de acesso.
- Isolamento multiempresa.
- Uso de `company_identifier` nas entidades aplicáveis.
- Identificadores UUID (`identifier`) nas entidades relevantes.
- Auditoria das operações importantes.

A existência dessas diretrizes não comprova que todos os mecanismos estejam implementados ou validados.

Permanecem pendentes de definição ou auditoria:

- Estratégia exata de tenancy.
- Matriz definitiva de papéis e permissões.
- Cobertura e validação das políticas RLS.
- Estratégia completa de autenticação e MFA.
- Gestão de segredos.
- Testes de acesso cruzado entre empresas.

## 8. Implementação e validação

### Componentes afetados

- Banco PostgreSQL do Supabase.
- Estrutura de tabelas e relacionamentos.
- Constraints, funções e triggers.
- Políticas RLS.
- APIs e serviços de backend.
- Integrações e workflows.
- Aplicações administrativas e móveis.

### Diretrizes de implementação

A documentação estabelece que a modelagem de dados, relacionamentos, constraints, triggers, RLS e funções deve preceder a construção das telas.

As alterações estruturais devem ser versionadas por migrações.

### Testes e critérios de aceitação

A validação deverá contemplar:

- Integridade dos dados.
- Consistência entre aplicações.
- Controle de acesso.
- Isolamento multiempresa.
- Comportamento das integrações.
- Recuperação de dados.
- Sincronização da aplicação offline.

Os resultados desses testes não estão documentados nesta ficha.

### Estado da implementação

O relatório de status registra implementação relevante no Supabase e a existência de inventário e auditoria estrutural gerados em 2026-11-06.

Entretanto, a documentação exige revisão desses artefatos antes de considerar a estrutura validada.

A aprovação desta decisão não significa que toda a arquitetura esteja concluída.

### Plano de reversão

Não documentado.

## 9. Referências e evidências

- `documentacao/01_PROJECT_CHARTER.md` — objetivos e princípios do projeto.
- `documentacao/02_DECISOES_ARQUITETURA.md` — decisão legada ADR-0002.
- `documentacao/05_STACK_RECURSOS.md` — stack e práticas técnicas.
- `documentacao/06_STATUS_E_RELATORIOS.md` — situação conhecida do Supabase e auditoria estrutural.
- `documentacao/07_GOVERNANCA_DOCUMENTAL.md` — governança das decisões.
- Registro primário da aprovação original: não documentado.

## 10. Revisão e substituição

- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

Uma mudança na fonte central de verdade deverá ser registrada por novo ADR, conforme a governança documental.

## Histórico desta ficha

- 2026-11-07 — minuta de individualização da decisão legada ADR-0002. O estado aprovado foi preservado do baseline documental. Data e registro primário da aprovação original não documentados.
