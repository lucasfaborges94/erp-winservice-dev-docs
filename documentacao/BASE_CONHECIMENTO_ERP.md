# Arquitetura da Base de Conhecimento do ERP Winservice

**Versão:** 0.1.0  
**Data:** 2026-11-08  
**Estado:** arquitetura conceitual aprovada; conteúdos e contratos em especificação  
**Escopo:** conhecimento compartilhado do domínio e da operação do ERP para uso por pessoas e agentes de IA.

> Este documento define a organização e a governança da Base de Conhecimento. Não concede permissões a agentes, não substitui ADRs, modelos funcionais, contratos técnicos nem código, e não declara funcionalidades implementadas.

## 1. Objetivo

Construir uma base de conhecimento operacional e semântica que permita a humanos e agentes de IA compreenderem profundamente o ERP Winservice, seus conceitos, entidades, processos, regras, exceções e operações.

A base deverá apoiar:
- operação diária do ERP;
- assistência contextual por IA;
- treinamento e onboarding de atendentes, técnicos, gestores e administradores;
- elaboração de manuais, cartilhas e materiais temáticos;
- integração controlada de profissionais e prestadores externos, respeitando escopo e permissões.

## 2. Princípios fundamentais

1. **Conhecimento não é autorização.** Conhecer uma operação ou ter acesso à sua descrição não autoriza sua execução.
2. **Autorização uniforme.** As permissões atribuídas aos papéis devem ser aplicadas a humanos e agentes conforme identidade, empresa, contexto e escopo.
3. **Enforcement técnico.** Backend, API e RLS são responsáveis por impor autorização. Prompts, instruções e documentos não substituem controles técnicos.
4. **Fonte normativa única.** Uma regra de negócio deve possuir uma fonte normativa identificável. Guias e materiais de treinamento referenciam essa fonte, sem criar cópias concorrentes.
5. **Rastreabilidade.** Conteúdos devem indicar origem, versão, vigência e autoridade.
6. **Separação entre definido e proposto.** Requisitos aprovados, decisões arquiteturais, propostas técnicas e informações implementadas devem ser claramente distinguidos.
7. **Acesso mínimo necessário.** Usuários e agentes externos consultam apenas informações compatíveis com suas permissões e finalidade.

## 3. Arquitetura em quatro camadas

### Camada 1 — Conhecimento do domínio

Descreve a semântica estável do negócio e do ERP:
- glossário e linguagem do domínio;
- entidades, atributos e identificadores;
- relacionamentos e cardinalidades;
- conceitos, classificações e invariantes;
- origem e significado dos dados.

Artefatos previstos:
- glossário corporativo;
- catálogo semântico de entidades;
- relacionamentos e regras de integridade conceitual;
- mapa de domínios e módulos.

### Camada 2 — Conhecimento operacional

Descreve como o trabalho ocorre:
- processos ponta a ponta;
- regras de negócio e critérios de decisão;
- estados e transições;
- pré-condições, pós-condições e exceções;
- responsabilidades humanas e automatizadas;
- tratamento de falhas, pendências e escalonamentos.

Artefatos previstos:
- processos por módulo;
- especificações de ciclos de vida;
- regras e políticas operacionais;
- procedimentos e cenários excepcionais.

### Camada 3 — Contratos de interação

Descreve de forma precisa como sistemas, módulos, pessoas e agentes interagem com o ERP:
- operações e APIs disponíveis;
- parâmetros, tipos e validações;
- identidade, autenticação e autorização exigidas;
- escopo de empresa e contexto;
- respostas, erros e códigos de resultado;
- idempotência, concorrência e efeitos colaterais;
- requisitos de auditoria e eventos emitidos.

Artefatos previstos:
- contratos OpenAPI e/ou JSON Schema;
- catálogo de operações/ferramentas para agentes;
- catálogo de erros e resultados;
- políticas de autorização vinculadas às operações.

Os contratos técnicos deverão refletir a implementação aprovada e testada. Não criar ferramentas executáveis apenas com base em uma descrição conceitual.

### Camada 4 — Guias por público

Transforma as três camadas normativas em materiais de uso:
- técnico de campo;
- atendente;
- gestor;
- administrador;
- agente de IA;
- profissional externo autorizado, quando aplicável.

Os guias são derivados. Não podem introduzir regra nova, divergir de ADR, requisito funcional ou contrato, nem substituir a fonte normativa. Divergências devem ser corrigidas na fonte normativa e então propagadas aos guias.

## 4. Hierarquia e autoridade das fontes

A Base de Conhecimento não substitui a hierarquia oficial do projeto:
1. ADRs aprovadas — decisões arquiteturais vigentes.
2. Requisitos e modelos funcionais aprovados — comportamento esperado do produto.
3. Contratos técnicos aprovados — interfaces e garantias de interação.
4. Código, migrações, banco e testes — evidência do comportamento realmente implementado.
5. Guias — explicações derivadas para públicos específicos.

Em caso de divergência, não resolver silenciosamente. Registrar o conflito, identificar a autoridade aplicável e encaminhar a correção do artefato divergente. Estado implementado e comportamento esperado devem permanecer distinguíveis.

## 5. Metadados obrigatórios de conteúdo

Cada artefato deverá manter, conforme aplicável:
- identificador estável;
- título e domínio/módulo de origem;
- versão;
- estado: rascunho, proposta, aprovado, vigente, superado ou arquivado;
- data de criação e atualização;
- vigência inicial/final, quando aplicável;
- nível de autoridade;
- responsável pela aprovação;
- referências às ADRs, requisitos, contratos ou evidências relacionadas;
- público e classificação de acesso.

A classificação de acesso não substitui autorização técnica no sistema que hospeda ou entrega o conteúdo.

## 6. Governança de alterações

- Alterações de decisão arquitetural exigem ADR e aprovação conforme governança documental.
- Alterações de requisito funcional devem atualizar o modelo funcional e preservar histórico.
- Alterações de interface exigem atualização/versionamento do contrato correspondente.
- Mudanças de implementação devem ser verificadas no código, banco e testes antes de atualizar a descrição do estado implementado.
- Guias devem ser revisados quando suas fontes normativas mudarem.
- Não duplicar regras em documentos independentes; usar referências para conectar conteúdo.
- Conteúdo superado deve permanecer rastreável, sem ser apresentado como vigente.

## 7. Estrutura documental inicial sugerida

A organização física poderá evoluir, mantendo a separação das quatro camadas:
- documentacao/dominio/: glossário, entidades, relacionamentos e processos;
- documentacao/operacao/: regras gerais e processos por módulo;
- documentacao/contratos/: APIs, JSON Schema, operações de agentes e catálogo de erros;
- documentacao/manuais/: guias derivados para técnico, atendente, gestor, administrador e agentes.

A estrutura acima é um ponto de partida aprovado conceitualmente, não significa que todos os diretórios ou artefatos já existam. A criação dos conteúdos deverá ocorrer progressivamente, priorizando domínios e módulos efetivamente especificados.

## 8. Limites de autonomia de agentes

- Um agente pode receber contexto amplo para interpretar o ERP, mas suas operações devem ser limitadas por permissões técnicas explícitas.
- A descrição de uma ferramenta não concede acesso à ferramenta.
- Toda operação deve validar identidade do agente, empresa, contexto do atendimento, escopo do recurso e permissão correspondente.
- A autorização deve ocorrer no backend/API e, quando aplicável, ser reforçada por RLS.
- Operações relevantes devem gerar autoria e eventos auditáveis.
- A política específica da Isa permanece subordinada às ADRs-0006 e ADR-0012; este documento não amplia sua autonomia.

## 9. Pendências para evolução

1. Construir glossário e catálogo semântico do domínio.
2. Mapear entidades e relacionamentos por módulo.
3. Documentar processos operacionais com regras, exceções e responsabilidades.
4. Definir padrão de contratos de APIs e operações de agentes.
5. Definir catálogo padronizado de erros, idempotência e correlação.
6. Definir versionamento e publicação dos conteúdos e sua recuperação por contexto.
7. Definir classificação de acesso e estratégia de exposição a prestadores externos.
8. Criar guias derivados após estabilização das respectivas fontes normativas.

## Histórico de alterações

- 2026-11-08 — v0.1.0: criação da arquitetura conceitual da Base de Conhecimento em quatro camadas, aprovada pelo responsável; estabelece separação entre conhecimento e autorização, hierarquia de fontes, metadados, governança e guias derivados.
