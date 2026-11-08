# Brainstorm — evolução de catálogos, sessões e administração da conta

**Data de registro:** 2026-11-08  
**Estado:** IDEIAS PARA EXPLORAÇÃO FUTURA  
**Natureza:** brainstorm; não são decisões tomadas, propostas formalmente aguardando aprovação ou autorização de implementação.

Este documento reúne possibilidades levantadas durante a evolução do ERP Winservice. As ideias podem ser alteradas, descartadas, divididas ou aprofundadas. Sua presença aqui não cria compromisso de implementação.

## 1. Catálogos personalizáveis
- Infraestrutura compartilhada para ciclo de vida de catálogos, sem impor estrutura idêntica a todos os domínios.
- Catálogo de permissões com versões globais e adoção voluntária por empresa, conforme ADR-0011.
- Catálogo de tipos de equipamentos.
- Catálogo de marcas de equipamentos.
- Catálogo de tipos de visita, com requisitos, checklists, parâmetros, configurações e regras operacionais próprias.
- Catálogos globais, por empresa e extensões locais.
- Histórico de alterações, autoria, publicação, adoção e restauração, quando fizer sentido.
- Dependências entre catálogos, por exemplo, tipos de equipamento associados a marcas e tipos de visita associados a requisitos.
- Evitar tabela universal de itens JSON quando atributos e integridade forem específicos do domínio.

## 2. Sessões e dispositivos
- Uma sessão operacional do app técnico por usuário e empresa.
- Múltiplas sessões do web app de atendimento, com limite configurável.
- Expiração configurável por aplicação, empresa e/ou perfil.
- Avaliar se novo login móvel substitui sessão anterior ou é rejeitado.
- Identificação de dispositivo, troca/perda de aparelho e recuperação de acesso.
- Operação offline do app técnico e revogação durante períodos sem conectividade.
- Política do futuro app de chat ainda não definida.
- Separar autenticação, sessão operacional, contexto de empresa e presença/conectividade.

## 3. Titularidade e administração da conta
- Usar account_owner para titularidade da conta SaaS.
- Manter titularidade independente dos papéis de autorização, especialmente superadmin.
- Explorar titularidade única ou múltipla, transferência, sucessão e recuperação.
- Trilha auditável para transferência e mudanças de titularidade.
- Avaliar atributo no vínculo usuário–empresa ou entidade própria.

## 4. Papéis iniciais a explorar
Lista mencionada: superadmin, gerente, supervisor, atendente, técnico, suporte e vendedor.
Questões: matriz por papel/módulo; papéis padrão versus criados pela empresa; múltiplos papéis por vínculo; papéis temporários/delegados; rótulos em português e identificadores técnicos em inglês.
A lista não estabelece hierarquia nem permissões.

## 5. Migração e preservação de dados
- Fotografar papéis, usuários, vínculos, administradores, permissões e associações antes da migração.
- Mapear company_users.role_identifier e company_users.company_admin antes de eventual descontinuação.
- Detectar órfãos, duplicidades, vínculos inconsistentes e dados sem correspondência.
- Mapear dependências de banco, backend, aplicações e automações.
- Criar relatório origem/destino e reconciliação de contagens.
- Planejar migração reversível e validar autorização antes de remover estruturas antigas.
- Manter inventário de estruturas candidatas à descontinuação/obsolescência, sem remoção automática.

## 6. Questões para exploração
- Quais políticas são globais e quais configuráveis por empresa?
- Como revogar sessões operacionais sem confundir isso com logout do provedor de autenticação?
- Como assegurar exclusividade móvel sob concorrência?
- Quais metadados do dispositivo são necessários e proporcionais?
- Quais catálogos exigem versionamento imutável e quais apenas histórico?
- Como representar requisitos de tipos de visita de modo validável pelo backend e utilizável offline?
- Como preservar customizações quando uma versão global evolui?

## 7. Governança
Ideias neste documento não alteram ADRs aprovadas. Quando selecionadas para decisão arquitetural, deverão ser avaliadas contra as ADRs vigentes, formalizadas no documento apropriado e submetidas à aprovação explícita. Implementação dependerá de especificação e autorização próprias.
