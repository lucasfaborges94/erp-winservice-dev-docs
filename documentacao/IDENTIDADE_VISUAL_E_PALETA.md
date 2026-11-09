# Winservice — identidade visual e paleta semântica

**Versão:** 0.1.0  
**Data:** 2026-11-09  
**Estado:** proposta de padronização visual registrada para validação  
**Escopo:** identidade visual institucional e aplicações digitais/documentais

> Este documento organiza derivações semânticas da paleta institucional. A paleta derivada é uma proposta de uso; não substitui nem altera as cores institucionais originais. A adoção definitiva como padrão transversal depende de validação do responsável pelo projeto.

## 1. Objetivo

Definir uma linguagem cromática consistente para páginas públicas, ERP, documentos, relatórios e futuras aplicações Winservice. As cores são organizadas por função semântica, e não apenas por aparência, para facilitar manutenção e implementação em CSS, WeWeb e FlutterFlow.

## 2. Paleta institucional de referência

| Nome | HEX | Papel |
|---|---|---|
| Azul-marinho | `#0A223A` | Cor institucional primária |
| Amarelo-ouro | `#F0A500` | Cor institucional de destaque |
| Branco | `#FFFFFF` | Neutro claro |
| Cinza-claro | `#F3F4F6` | Neutro de superfície |
| Cinza-escuro | `#111827` | Neutro de texto |

## 3. Cores semânticas para textos

| Token | HEX | Uso |
|---|---|---|
| `--color-text-title` | `#0A223A` | Títulos principais, H1 e cabeçalhos |
| `--color-text-subtitle` | `#25445F` | Subtítulos, H2 e H3 |
| `--color-text-body` | `#263445` | Corpo de texto, parágrafos e conteúdo de documentos |
| `--color-text-superscript` | `#64748B` | Sobrescritos, referências e informação auxiliar |
| `--color-text-caption` | `#667085` | Legendas de imagens, tabelas e gráficos |
| `--color-text-label` | `#344054` | Rótulos de campos, identificação e metadados |
| `--color-text-link` | `#1769AA` | Links e referências clicáveis |
| `--color-button-primary-text` | `#FFFFFF` | Texto de botão primário azul |
| `--color-button-accent-text` | `#0A223A` | Texto de botão de destaque amarelo |

### 3.1 Padrões de botões

- **Primário:** fundo `#0A223A`, texto `#FFFFFF`.
- **Destaque:** fundo `#F0A500`, texto `#0A223A`.

Não utilizar o amarelo-ouro institucional como texto pequeno sobre fundo branco. Para links, utilizar o azul de navegação `#1769AA`.

## 4. Cores semânticas para fundos e superfícies

| Token | HEX | Uso |
|---|---|---|
| `--color-bg-page` | `#F5F7FA` | Fundo geral de páginas e do ERP |
| `--color-bg-primary` | `#FFFFFF` | Fundo principal de conteúdo e documentos |
| `--color-bg-secondary` | `#F3F4F6` | Áreas secundárias e seções alternadas |
| `--color-card-primary` | `#FFFFFF` | Cards principais, indicadores e formulários |
| `--color-card-secondary` | `#F8FAFC` | Cards internos e agrupamentos auxiliares |
| `--color-card-highlight` | `#FFF8E8` | Cards com informação que merece atenção |
| `--color-bg-info` | `#FFF1CC` | Avisos informativos e destaques suaves |
| `--color-bg-brand` | `#0A223A` | Cabeçalhos, rodapés e áreas institucionais |

## 5. Regra de contraste e hierarquia

O texto de cada superfície deve declarar explicitamente sua cor semântica. Não depender da cor padrão herdada do navegador, do framework ou do componente.

**Correção obrigatória da visualização de referência:** no card secundário, o título deve usar azul institucional `#0A223A`, o corpo deve usar grafite azulado `#263445` e o fundo deve permanecer `#F8FAFC`. O título não deve herdar uma cor implícita ou pouco contrastante.

Exemplo de composição:

| Elemento | Cor |
|---|---|
| Fundo do card secundário | `#F8FAFC` |
| Título do card secundário | `#0A223A` |
| Texto do conteúdo | `#263445` |
| Informação auxiliar | `#667085` |

A combinação deve ser validada nos componentes reais, inclusive em estados hover, focus, disabled e em temas claros/escuros, quando existirem.

## 6. Tokens CSS de referência

```css
:root {
  /* Textos */
  --color-text-title: #0A223A;
  --color-text-subtitle: #25445F;
  --color-text-body: #263445;
  --color-text-superscript: #64748B;
  --color-text-caption: #667085;
  --color-text-label: #344054;
  --color-text-link: #1769AA;

  /* Botões */
  --color-button-primary-bg: #0A223A;
  --color-button-primary-text: #FFFFFF;
  --color-button-accent-bg: #F0A500;
  --color-button-accent-text: #0A223A;

  /* Fundos */
  --color-bg-page: #F5F7FA;
  --color-bg-primary: #FFFFFF;
  --color-bg-secondary: #F3F4F6;

  /* Cards */
  --color-card-primary: #FFFFFF;
  --color-card-secondary: #F8FAFC;
  --color-card-highlight: #FFF8E8;

  /* Destaques institucionais */
  --color-bg-info: #FFF1CC;
  --color-bg-brand: #0A223A;
}
```

## 7. Diretrizes de aplicação

1. Azul-marinho e amarelo-ouro permanecem como cores de reconhecimento institucional.
2. Azul-marinho deve predominar em títulos, cabeçalhos e ações primárias.
3. Amarelo-ouro deve ser usado em destaques e ações secundárias de alta visibilidade, com texto azul-marinho.
4. Textos corridos devem priorizar tons escuros neutros, evitando o uso de cores institucionais saturadas em parágrafos longos.
5. Fundos claros e neutros devem estruturar páginas e cards; superfícies coloridas devem ser pontuais e intencionais.
6. Aplicações devem consumir tokens semânticos, evitando repetir valores HEX diretamente em componentes.
7. A paleta deve ser validada quanto ao contraste WCAG 2.2 antes de ser adotada como padrão definitivo, incluindo texto normal, texto grande, componentes e estados interativos.

## 8. Histórico

- 2026-11-09 — v0.1.0: registro inicial da proposta de paleta semântica; inclusão explícita da correção de contraste do título do card secundário.
