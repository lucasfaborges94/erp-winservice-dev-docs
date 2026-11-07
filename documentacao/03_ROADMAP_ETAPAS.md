# Roadmap e etapas de desenvolvimento — ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-07  
**Estado:** estrutura inicial; sequência e critérios devem ser refinados

## 1. Método

O desenvolvimento será incremental, orientado por dependências e com prioridade para operação. Cada etapa deverá possuir objetivo, escopo, dependências, entregáveis, critérios de aceite, riscos e evidências.

## 2. Etapas macro propostas para organização

| Etapa | Nome | Resultado esperado | Estado |
|---|---|---|---|
| 0 | Governança e documentação | Charter, decisões, escopo, roadmap, padrões e relatórios persistentes | Em consolidação |
| 1 | Auditoria e fundação de dados | Inventário do Supabase, avaliação de tabelas, relações, RLS, funções e gaps | Em andamento / baseline existente |
| 2 | Núcleo da plataforma | Empresas, usuários, vínculos, papéis, permissões, módulos e auditoria | Parcial; validar pelo banco |
| 3 | Cadastros essenciais | Clientes, contatos, endereços e equipamentos | Parcial; validar pelo banco |
| 4 | Operação | Ordens de serviço, visitas, estados, execução e histórico | Prioridade da V1; escopo detalhado pendente |
| 5 | Comercial e financeiro | Orçamentos, aprovações, preços, despesas, cobranças e pagamentos | Escopo e dependências a detalhar |
| 6 | Atendimento omnichannel | Atendimento atual e evolução para Chat próprio | Chatwoot transitório |
| 7 | Inteligência e automação | Isa, workflows n8n, regras e automações | Parcial; autonomia da Isa já delimitada |
| 8 | Aplicação web | Administração e operação via WeWeb na VPS | Planejada |
| 9 | Aplicativo móvel | FlutterFlow offline-first, GPS background, sincronização | Planejada |
| 10 | Jurídico e conformidade | Módulo Jurídico amplo; primeira funcionalidade: documentos e instrumentos jurídicos | Planejamento funcional iniciado |
| 11 | Integrações e migração | Integrações desacopladas e substituição gradual de ferramentas | Contínua |
| 12 | SaaS e escala | Onboarding multiempresa, planos, limites, billing, suporte e operação SaaS | Futuro |

A tabela é um mapa de trabalho, não uma declaração de que cada módulo já está implementado ou aprovado em detalhe.

## 3. Estrutura obrigatória de cada etapa

- Objetivo e problema resolvido.
- Escopo incluído e explicitamente excluído.
- Requisitos funcionais e não funcionais.
- Entidades e modelo de dados.
- Regras de negócio e máquina de estados, quando aplicável.
- Segurança, RLS, papéis e permissões.
- Integrações e contratos de API.
- Interface e experiência.
- Testes unitários, integração, segurança e aceite.
- Migração e rollback.
- Documentação e evidências.
- Critério de conclusão e responsável pela aprovação.

## 4. Critérios de passagem entre etapas

Uma etapa só será considerada concluída quando:
1. Escopo e critérios de aceite estiverem definidos.
2. Implementação estiver identificada por versão/commit ou artefato.
3. Testes previstos tiverem evidência.
4. Segurança e isolamento tiverem sido verificados quando aplicável.
5. Migrações e rollback estiverem documentados.
6. Documentos oficiais afetados estiverem atualizados.
7. Pendências residuais estiverem registradas e aceitas.

## 5. Próxima sequência recomendada

1. Revisar e aprovar este pacote documental inicial.
2. Consolidar o Project Charter completo e as decisões ADR.
3. Analisar inventário e auditoria estrutural Supabase de 2026-11-06.
4. Produzir relatório de estado real: existente, parcial, ausente, inconsistente e não verificado.
5. Fechar mapa de módulos e dependências.
6. Definir backlog e critérios de aceite da primeira entrega operacional.
