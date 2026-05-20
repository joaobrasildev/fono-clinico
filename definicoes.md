# Definições do Protótipo — FonoClínico

> Documento de referência para qualquer agente/desenvolvedor que for evoluir o protótipo.
> **Regra de ouro:** novas funcionalidades podem ser adicionadas, mas **nenhuma das funcionalidades, telas, fluxos, textos ou estilos atuais pode ser removida ou quebrada**.

---

## 1. Visão geral

- **Nome do produto (mock):** FonoClínico — Raciocínio Clínico em Fonoaudiologia
- **Inspirado em:** https://monumental-fono-flow-lab.base44.app/
- **Objetivo do protótipo:** demonstração navegável para reunião com cliente. Nada precisa ser funcional de verdade (sem backend, sem autenticação real, sem persistência).
- **Arquivo único:** `prototipo/index.html` (HTML + CSS + JS inline, sem dependências externas além de Google Fonts).
- **Idioma:** Português do Brasil (pt-BR).
- **Servidor local em uso:** `python3 -m http.server 8765` rodando em `prototipo/`. Acesso em http://localhost:8765.

---

## 2. Identidade visual (não alterar)

### Tipografia
- Família única: **Inter** (Google Fonts), pesos `300;400;500;600;700;800;900`.
- Carregada via `<link>` no `<head>`.

### Paleta de cores (CSS variables em `:root`)
| Token | Valor | Uso |
|---|---|---|
| `--primary` | `#1f5fa8` | Azul principal (botões, links, gradientes) |
| `--primary-dark` | `#184c87` | Hover/títulos fortes |
| `--primary-light` | `#eaf2fb` | Fundos suaves, badges, tabs |
| `--accent` | `#26a4b8` | Ciano de destaque (gradientes) |
| `--accent-dark` | `#1b8295` | — |
| `--bg` | `#f5f8fc` | Fundo da página |
| `--card` | `#ffffff` | Cards |
| `--foreground` | `#1e293b` | Texto principal |
| `--muted` | `#64748b` | Texto secundário |
| `--muted-bg` | `#eef2f7` | Tags |
| `--border` | `#dbe3ee` | Bordas |
| `--success` | `#16a34a` | Confirmação |
| `--warning` | `#d97706` | Aviso |
| `--danger` | `#dc2626` | Erro |
| `--shadow` / `--shadow-lg` | sombras azuladas | Elevação |

> A paleta foi extraída do CSS real do site do cliente. **Não trocar para outras famílias de cor (ex: laranja).**

### Componentes visuais reutilizáveis (classes CSS já existentes — preferir reuso)
- `.btn`, `.btn-primary`, `.btn-outline`, `.btn-ghost`, `.btn-block`
- `.badge` (pílula com ponto colorido antes do texto)
- `.logo` + `.logo-icon` (quadrado gradiente com "F")
- `.module` (card de módulo), `.tag` (pílula cinza)
- `.tip` (card com borda esquerda accent)
- `.modal-overlay` + `.modal`
- `.scale-btn` (botão de escala 1–10)
- `.result-block`

### Classes CSS — Coluna esquerda da tela de Login

| Classe | Uso |
|---|---|
| `.login-side` | Coluna esquerda com fundo `var(--bg)`, `border-right: 1px solid var(--border)` |
| `.login-hero-content` | Flex column centralizado verticalmente; ocupa o espaço restante entre o logo e o copyright |
| `.login-hero-title` | `<h2>` com tamanho clamp(28px–42px), `font-weight: 800`, cor `var(--foreground)` |
| `.login-hero-title span` | Gradiente linear primary→accent via `background-clip: text` (mesma técnica do `h1 span` da Home) |
| `.login-hero-lead` | Parágrafo descritivo, `font-size: 16px`, `color: var(--muted)` |
| `.login-features` | Lista sem marcadores com bullets ✓ em `var(--primary-light)` / `var(--primary)`, `font-size: 15px` |

---

## 3. Estrutura de telas (SPA via show/hide de divs)

O protótipo é uma única página com telas controladas por `.hidden`. A função utilitária `hideAll()` oculta todas de uma vez antes de mostrar a desejada.

| ID | Função | Como mostrar |
|---|---|---|
| `#login-screen` | Tela de login/cadastro (inicial) | `logout()` |
| `#home-screen` | Landing/home com conteúdo | `goToHome()` |
| `#diag-type-screen` | Seleção do tipo de diagnóstico | `startDiagnostic(plano)` |
| `#diag-screen` | Diagnóstico (perguntas e resultado) | `selectDiagType(type)` |
| `#plans-modal` | Modal de planos (overlay) | `openPlans()` / `closePlans()` |
| `#guide-anamnese` | Guia educativo — Anamnese Clínica | `showGuide('anamnese')` |
| `#guide-linguagem` | Guia educativo — Linguagem Infantil | `showGuide('linguagem')` |
| `#guide-fala` | Guia educativo — Fala e Fonologia | `showGuide('fala')` |
| `#guide-motricidade` | Guia educativo — Motricidade Orofacial | `showGuide('motricidade')` |
| `#guide-raciocinio` | Guia educativo — Raciocínio Clínico Integrado | `showGuide('raciocinio')` |
| `#admin-guias` | Painel admin — Gestão de Guias | `showAdminGuias()` |
| `#admin-perguntas` | Painel admin — Gestão de Perguntas | `showAdminPerguntas()` |
| `#diag-list-screen` | Listagem de diagnósticos | `showDiagList()` |
| `#profile-screen` | Perfil do usuário logado | `showProfile()` |

**Estado inicial ao abrir a página:** `#login-screen` visível, demais ocultos.

A constante `ALL_SCREENS` no topo do script lista todos os IDs gerenciados pelo `hideAll()`. Ao adicionar uma nova tela, incluí-la nessa array.

---

## 4. Fluxo de navegação (NÃO QUEBRAR)

```
[Login] --(qualquer submit/social/link)--> [Home]
[Home] --(botão "Fazer Diagnóstico" ou CTA)--> [Modal Planos]
[Modal Planos] --(Selecionar Plano)--> [Seleção de Tipo de Diagnóstico]
[Seleção de Tipo] --(card de tipo clicado)--> [Diagnóstico - Pergunta 1]
[Diagnóstico] --(N perguntas por tipo)--> [Resultado]
[Resultado] --("Refazer")--> [Diagnóstico Pergunta 1 (mesmo tipo)]
[Resultado] --("Avaliar outra área clínica")--> [Seleção de Tipo]
[Resultado] --("Voltar ao Início")--> [Home]
[Home] --(dropdown "Conta" → "Sair")--> [Login]
[Home] --(dropdown "Guias técnicos")--> [Guia X]
[Guia X] --("← Voltar ao início")--> [Home]
[Guia X] --(dropdown "Guias técnicos")--> [Guia Y]
[Diagnóstico] --("← Tipos")--> [Seleção de Tipo]
[Home/Qualquer tela] --(dropdown Admin → "Gestão de guias")--> [Admin Guias]
[Home/Qualquer tela] --(dropdown Admin → "Gestão de perguntas")--> [Admin Perguntas]
[Admin Guias] --(nav "Início")--> [Home]
[Admin Perguntas] --(nav "Início")--> [Home]
[Home/Qualquer tela] --(dropdown "Conta" → "Perfil")--> [Perfil]
[Perfil] --(nav "Início")--> [Home]
[Perfil] --(botão "Gerenciar plano")--> [Modal Planos]
```

Regras invioláveis:
- **Login é puramente visual.** Qualquer ação (Entrar, Criar conta, login social Google/LinkedIn/Apple, "Esqueceu a senha") deve levar para a Home (ou exibir alert mock, no caso de "Esqueceu a senha"). Não validar nada de verdade.
- O botão **"Fazer Diagnóstico"** sempre abre o **modal de planos**, nunca pula direto para o diagnóstico.
- Após selecionar o plano, o usuário vai para a **tela de seleção de tipo** — nunca direto para as perguntas.
- O usuário **não pode avançar** sem responder a pergunta atual (botão "Próxima" desabilitado). Isso vale para os três tipos de pergunta (escala, sim/não, texto).
- A primeira pergunta tem o botão "Anterior" desabilitado.
- A última pergunta troca "Próxima →" por "Ver Diagnóstico →".

---

## 5. Tela de Login (`#login-screen`)

Layout duas colunas:

**Coluna esquerda (`.login-side`)** — fundo claro (`var(--bg)`) com borda direita (`var(--border)`), sem gradiente. Contém:
- Logo "FonoClínico" + subtítulo.
- **Bloco hero (`.login-hero-content`)** espelhando o conteúdo da seção Hero da Home:
  - Badge "Material educativo · baseado em evidências" (reusa classe `.badge`).
  - `<h2 class="login-hero-title">` — "Raciocínio Clínico em **Fonoaudiologia Infantil**" com `<span>` em gradiente primary→accent.
  - `<p class="login-hero-lead">` — parágrafo descritivo da plataforma.
  - `<ul class="login-features">` — 4 bullets com ícone ✓ em `var(--primary-light)` (módulos, roteiros, indicadores de risco, evidências).
- Copyright no rodapé.

**Coluna direita (`.login-form-wrap`)** — contém:
- Título dinâmico (`#auth-title`) e subtítulo (`#auth-sub`).
- **Tabs "Entrar" / "Criar conta"** (`switchTab('login'|'register')`). No modo register, mostrar campo Nome.
- Form: E-mail, Senha (+ Nome no register).
- Linha "Lembrar de mim" + "Esqueceu a senha?" (visível só no modo login).
- Botão principal (submit) — texto também muda conforme tab.
- Divider "ou continue com".
- **3 botões sociais:** Google, LinkedIn, Apple (com SVGs inline coloridos). Todos chamam `goToHome()`. *(LinkedIn substituiu o Facebook em 16/05/2026)*
- Linha "Não tem uma conta? Cadastre-se" / "Já tem conta? Faça login" (`#switch-mode`).

**Não remover nenhum desses elementos.**

---

## 6. Tela Home (`#home-screen`)

Seções na ordem (manter ordem):

1. **Header sticky** (`header.nav`)
   - Logo
   - Nav links na ordem: **Início → Guias técnicos → Diagnóstico → Conta → Admin**
   - **Link "Início"** (`goToHome()`): leva de volta à home.
   - **Dropdown "Guias técnicos"** (`.nav-trigger` + `.dropdown`): 5 links que chamam `showGuide('anamnese'|'linguagem'|'fala'|'motricidade'|'raciocinio')`
   - **Dropdown "Diagnóstico"**: links "Novo" (`openPlans()`) e "Listagem" (`showDiagList()`).
   - **Dropdown "Conta"**: submenus "Perfil" (chama `showProfile()`) e "Sair" (chama `logout()`).
   - **Dropdown "Admin"**: links "Gestão de guias" (`showAdminGuias()`) e "Gestão de perguntas" (`showAdminPerguntas()`).
   - Botão primário "Fazer Diagnóstico" (chama `openPlans()`)

   > O header idêntico (com os mesmos dropdowns funcionais) é replicado em **todas** as telas: guias (`#guide-*`), admin (`#admin-guias`, `#admin-perguntas`), listagem de diagnósticos e perfil. As telas de guia antes tinham nav reduzido — agora exibem o nav completo.

2. **Hero** — badge, h1 com `<span>` no gradiente, lead, 2 CTAs (Fazer Diagnóstico + Ver Módulos), ilustração SVG circular com 2 floating cards.

3. **Stats** — 4 indicadores (9 módulos, +50 roteiros, +850 profissionais, 100% evidências).

4. **`#modulos`** — grid de **9 módulos clínicos** (não diminuir): Anamnese Clínica, Histórico de Desenvolvimento, Avaliação Compreensiva, Análise do Discurso, Sistema Fonológico, Processos Fonológicos, Avaliação Articulatória, Funções Estomatognáticas, Deglutição e Mastigação. Cada um com ícone, título, descrição e tags.

5. **`#integrado`** — bloco "Raciocínio Clínico Integrado" com texto + lista de **5 passos numerados** (Coleta → Correlação → Análise crítica → Hipótese → Plano).

6. **`#dicas`** — grid 2 colunas com **6 dicas clínicas** (empatia, perguntas abertas, fatores de risco, registrar desconhecido, evitar conclusões precipitadas, EME).

7. **CTA block** — banner gradiente com botão "Fazer Diagnóstico Agora".

8. **Footer `#contato`** — 4 colunas (marca, Módulos, Plataforma, Suporte) + linha de copyright.

---

## 7. Modal de Planos (`#plans-modal`)

- Aberto via `openPlans()`, fechado via `closePlans()` (ou clicando fora do `.modal`).
- 3 planos lado a lado — **manter os 3 e os limites diários**:

| Plano | Preço | Diagnósticos/dia | Destaque |
|---|---|---|---|
| Essencial | R$29/mês | 1 | — |
| Profissional | R$79/mês | 3 | `.featured` + tag "MAIS POPULAR" |
| Instituição | R$199/mês | 10 | — |

- Cada botão "Selecionar Plano" chama `startDiagnostic('1 por dia' | '3 por dia' | '10 por dia')`.

---

## 8. Diagnóstico — Seleção de Tipo (`#diag-type-screen`)

Tela intermediária exibida após o usuário selecionar um plano no modal. Apresenta quatro cards clicáveis, um para cada tipo de avaliação disponível.

### Layout
- Header com logo + botão "← Voltar" (`goToHome()`).
- Badge de introdução + título + subtítulo explicativo.
- Badge com o plano selecionado (`#diag-type-plan-info`), preenchido dinamicamente.
- Grid 2×2 (`.diag-types-grid`) com os quatro cards (`.diag-type-card`).

### Tipos de diagnóstico disponíveis

| Chave | Rótulo | Ícone |
|---|---|---|
| `anamnese` | Anamnese Clínica | AN |
| `linguagem` | Linguagem Infantil | LI |
| `fala` | Fala e Fonologia | FA |
| `motricidade` | Motricidade Orofacial | MO |

Cada card tem: ícone, título, descrição e texto "Iniciar avaliação →". Ao clicar, chama `selectDiagType(type)`.

### Classes CSS específicas

| Classe | Uso |
|---|---|
| `.diag-type-screen` | Container full-page da tela |
| `.diag-type-wrap` | Wrapper centralizado (max-width 860px) |
| `.diag-type-header` | Linha logo + botão voltar |
| `.diag-type-intro` | Bloco de título e subtítulo da tela |
| `.diag-type-plan-badge` | Container do badge de plano ativo |
| `.diag-types-grid` | Grid 2×2 dos cards |
| `.diag-type-card` | Card clicável de tipo de avaliação |
| `.diag-type-icon` | Ícone emoji do card (56×56px, bg primary-light) |
| `.diag-type-action` | Texto "Iniciar avaliação →" no rodapé do card |

---

## 9. Diagnóstico — Perguntas e Resultado (`#diag-screen`)

Estado global (variáveis JS): `currentQ`, `answers`, `currentPlan`, `currentType`.

### Conjuntos de perguntas (`QUESTION_SETS`)

Objeto JS com 4 chaves (`anamnese`, `linguagem`, `fala`, `motricidade`). Cada chave contém:
```js
{
  label: string,       // ex: 'Fala e Fonologia'
  icon:  string,       // emoji
  questions: [ ... ]   // array de 5 perguntas
}
```

Cada pergunta tem o formato:
```js
{
  type:    'scale' | 'yesno' | 'text',
  q:       string,           // enunciado
  help:    string,           // texto de ajuda abaixo do enunciado
  positive: boolean          // apenas em type:'yesno' — ver abaixo
}
```

### Tipos de pergunta

| Tipo | Interface | Como salva | Pontuação numérica |
|---|---|---|---|
| `scale` | Botões `.scale-btn` 1–10 | Número inteiro 1–10 | Valor direto |
| `yesno` | Dois botões `.yn-btn` (Sim / Não) | String `'sim'` ou `'nao'` | Se `positive:true` → sim=9, não=2; se `positive:false` → sim=2, não=9 |
| `text` | `<textarea>` livre | String com o texto digitado | 7 se preenchido, 3 se vazio |

> **`positive`**: indica se "Sim" é clinicamente favorável (`true`) ou um indicador de risco (`false`). Usado na função `getNumericScore()` para calcular a média.

### Composição atual das 5 perguntas por tipo

| Tipo | Q1 | Q2 | Q3 | Q4 | Q5 |
|---|---|---|---|---|---|
| Anamnese Clínica | scale | yesno (risco) | scale | **text** | scale |
| Linguagem Infantil | scale | yesno (positivo) | scale | scale | **text** |
| Fala e Fonologia | scale | scale | yesno (risco) | scale | **text** |
| Motricidade Orofacial | scale | yesno (risco) | scale | scale | **text** |

### Render das perguntas (`renderQuestion()`)

- Barra de progresso com `icon + label + "Pergunta N de N"` e plano.
- Tag de tipo de pergunta (`.q-type-tag`) acima do enunciado.
- Interface específica conforme o `type`.
- Navegação: "← Anterior" (desabilitado na Q1) + "Próxima →" / "Ver Diagnóstico →" (desabilitado sem resposta).

### Resultado (`renderResult()`)

Calcula `avg = média de getNumericScore(answer, q)` sobre todas as respostas. As mesmas três faixas do diagnóstico original:
- `avg ≤ 4` → "Alto nível de alerta clínico"
- `avg ≤ 7` → "Atenção moderada — sinais de alerta"
- `avg > 7` → "Indicadores dentro da normalidade"

Blocos exibidos: Área avaliada → Nível identificado → Hipótese clínica → Encaminhamentos → Detalhamento (total/max, área, plano).

Botões de ação:
- **"↻ Refazer"** → `selectDiagType(currentType)` (reinicia o mesmo tipo)
- **"↩ Avaliar outra área clínica"** → `goBackToTypes()` (volta à tela de seleção)
- **"Voltar ao Início"** → `goToHome()`

Disclaimer obrigatório no final: **"Este diagnóstico é apenas indicativo e não substitui avaliação fonoaudiológica presencial completa."**

### Classes CSS específicas das perguntas

| Classe | Uso |
|---|---|
| `.q-type-tag` | Pílula de tipo de pergunta (ex: "Avaliação 1–10") |
| `.yn-row` | Grid 2 colunas para os botões Sim/Não |
| `.yn-btn` | Botão grande de Sim ou Não |
| `.yn-btn.selected-sim` | Estado selecionado — gradiente verde |
| `.yn-btn.selected-nao` | Estado selecionado — gradiente azul primary |
| `.text-field-wrap` | Container do campo de texto livre |
| `.text-field-wrap textarea` | Área de texto com foco primary |
| `.text-field-wrap .field-hint` | Texto de instrução abaixo do textarea |

---

## 10. Funções JavaScript públicas (não renomear)

| Função | Responsabilidade |
|---|---|
| `switchTab(mode)` | Alterna entre login/register no form |
| `goToHome()` | Usa `hideAll()`, mostra home, fecha modal, rola para o topo |
| `logout()` | Usa `hideAll()`, mostra login |
| `openPlans()` / `closePlans()` | Modal de planos |
| `startDiagnostic(plan)` | Salva `currentPlan`, fecha modal, exibe `#diag-type-screen`, preenche badge de plano |
| `selectDiagType(type)` | Salva `currentType`, inicializa `answers`, exibe `#diag-screen`, chama `renderQuestion()` |
| `goBackToTypes()` | Oculta `#diag-screen`, exibe `#diag-type-screen` |
| `renderQuestion()` | Desenha barra de progresso, tag de tipo, enunciado, interface de resposta e navegação |
| `selectScore(n)` | Salva resposta numérica (scale) e re-renderiza |
| `selectYN(val)` | Salva resposta `'sim'`/`'nao'` (yesno) e re-renderiza |
| `saveText(val)` | Salva texto livre e atualiza estado do botão "Próxima" em tempo real |
| `prevQ()` / `nextQ()` | Navegação entre perguntas; `nextQ()` chama `renderResult()` na última |
| `getNumericScore(answer, q)` | Converte qualquer tipo de resposta em pontuação 1–10 para o cálculo da média |
| `renderResult()` | Calcula média, desenha resultado completo com 5 blocos e 3 botões de ação |
| `showGuide(name)` | Usa `hideAll()`, exibe `#guide-{name}`, rola para o topo. Valores válidos: `'anamnese'`, `'linguagem'`, `'fala'`, `'motricidade'`, `'raciocinio'` |
| `hideAll()` | Utilitário interno: oculta todos os IDs listados em `ALL_SCREENS` |
| `showAdminGuias()` | Usa `hideAll()`, exibe `#admin-guias`, chama `renderAdminGuides()`, rola para o topo |
| `showAdminPerguntas()` | Usa `hideAll()`, exibe `#admin-perguntas`, chama `renderAdminQuestions()`, rola para o topo |
| `showDiagList()` | Usa `hideAll()`, exibe `#diag-list-screen`, rola para o topo |
| `showProfile()` | Usa `hideAll()`, exibe `#profile-screen`, rola para o topo |
| `renderAdminGuides()` | Renderiza a tabela de guias no `#admin-guides-tbody` a partir de `adminGuides[]` |
| `openGuideForm(idx?)` | Abre o modal de formulário de guia; `idx` undefined = novo, número = edição |
| `closeGuideForm()` | Fecha o modal de formulário de guia |
| `saveGuide(e)` | Cria ou atualiza um guia em `adminGuides[]` e exibe toast |
| `openGuideDelete(idx)` | Abre o modal de confirmação de exclusão de guia |
| `closeGuideDelete()` | Fecha o modal de confirmação de exclusão de guia |
| `confirmDeleteGuide()` | Remove o guia de `adminGuides[]`, fecha modal e exibe toast |
| `renderAdminQuestions()` | Renderiza tabs de área e tabela de perguntas do `_currentAdminQType` |
| `switchAdminQType(type)` | Atualiza `_currentAdminQType` e re-renderiza a tabela de perguntas |
| `togglePositiveField(type)` | Exibe/oculta o campo "Sentido do Sim" conforme o tipo ser `yesno` ou não |
| `openQuestionForm(idx?)` | Abre o modal de formulário de pergunta; `idx` undefined = nova, número = edição |
| `closeQuestionForm()` | Fecha o modal de formulário de pergunta |
| `saveQuestion(e)` | Cria ou atualiza uma pergunta em `QUESTION_SETS[area].questions` e exibe toast |
| `openQuestionDelete(idx)` | Abre o modal de confirmação de exclusão de pergunta |
| `closeQuestionDelete()` | Fecha o modal de confirmação de exclusão de pergunta |
| `confirmDeleteQuestion()` | Remove a pergunta de `QUESTION_SETS[_currentAdminQType].questions`, fecha modal e exibe toast |
| `escHtml(str)` | Utilitário interno: escapa `&`, `<`, `>`, `"` para uso seguro em `innerHTML` |
| `showToast(msg, variant)` | Exibe o toast `#admin-toast`; `variant` aceita `'success'` (padrão) ou `'warning'` |

Variáveis globais: `currentQ`, `answers[]`, `currentPlan`, `currentType`.
Variáveis globais (admin): `adminGuides[]`, `_guideNextId`, `_editingGuideIdx`, `_deletingGuideIdx`, `_currentAdminQType`, `_editingQIdx`, `_deletingQIdx`, `_toastTimer`.
Constantes: `ALL_SCREENS` (array de todos os IDs de tela), `QUESTION_SETS` (objeto com os 4 conjuntos de perguntas).

---

## 11. Guias de Conteúdo Educativo (`#guide-*`)

Cinco telas de leitura adicionadas ao SPA, acessíveis pelo dropdown "Guias técnicos" no header. Cada tela compartilha a mesma estrutura visual e padrão de CSS.

### Estrutura comum de cada guia

```
#guide-{nome}
  header.nav              ← idêntico ao da home (com dropdown funcional)
  .guide-header           ← faixa gradiente com botão voltar, ícone e título
    .guide-header-inner
      button.guide-back   ← chama goToHome()
      .guide-title-wrap
        .guide-icon       ← emoji representativo
        div  h1 + p       ← título e subtítulo do guia
  .guide-body
    .guide-module-label   ← box "Sobre este módulo"
    .edu-section × N      ← uma por subtítulo
```

### Classes CSS dos componentes educativos

| Classe | Uso |
|---|---|
| `.edu-section` | Contêiner de seção com separador visual |
| `.edu-section-title` | `<h2>` com linha decorativa |
| `.edu-lead` | Parágrafo introdutório em destaque |
| `.edu-card` | Card branco com `h4`, `p`, `ul/li` |
| `.edu-highlight` | Callout azul (borda esquerda `--primary`) |
| `.edu-alert` | Callout laranja (borda esquerda `--warning`) |
| `.edu-grid-2` | Grid responsivo 2 colunas |
| `.edu-grid-3` | Grid responsivo 3 colunas |
| `.edu-mini-card` | Card compacto para listas de itens |
| `.edu-mini-card.ok-card` | Variante verde (padrão adequado) |
| `.edu-mini-card.warn-card` | Variante amarela (padrão alterado) |
| `.edu-table` | Tabela com `<thead>`, `.status-ok` e `.status-warn` |
| `.edu-steps` / `.edu-step` | Lista numerada com `.edu-step-num` + `.edu-step-body` |
| `.smart-grid` / `.smart-item` | Grid de metas SMART (`.smart-letter`, `h5`, `p`) |
| `.edu-case` / `.edu-case-label` | Bloco de caso clínico com label em destaque |

---

### Guia 1 — Anamnese Clínica (`#guide-anamnese`)
Ícone: AN · 4 seções

| Seção | Conteúdo |
|---|---|
| O que é a Anamnese Clínica? | Definição, papel clínico e dica de condução empática (`.edu-highlight`) |
| Estrutura da Anamnese | 4 cards (`.edu-grid-2`): Identificação, Queixa principal, Histórico de saúde, Desenvolvimento |
| Como Interpretar os Dados | Fatores de risco (`.edu-alert`), correlação de dados (`.edu-highlight`), 5 perguntas norteadoras |
| Erros Comuns na Anamnese | 3 cards (`.edu-grid-3`): perguntas fechadas, não registrar o desconhecido, conclusões precipitadas |

---

### Guia 2 — Linguagem Infantil (`#guide-linguagem`)
Ícone: LI · 4 seções

| Seção | Conteúdo |
|---|---|
| Marcos de Desenvolvimento | Tabela 8 faixas etárias × Expressão / Compreensão + alerta de sinal de risco |
| Avaliação da Linguagem | Grid 2×2 (`.edu-grid-2`): aspectos expressivos, compreensivos, pragmáticos, formais |
| Coleta e Análise de Amostras | 6 passos (`.edu-steps`) + dica de cálculo da EME (`.edu-highlight`) |
| Indicadores de Risco e Transtornos | Lista de indicadores + distinção atraso × transtorno (`.edu-grid-2`) |

---

### Guia 3 — Fala e Fonologia (`#guide-fala`)
Ícone: FA · 4 seções

| Seção | Conteúdo |
|---|---|
| Sistema Fonológico do PB | Grid 6 itens (`.edu-grid-3`): classes de fonemas + referência de aquisição até 5–6 anos |
| Processos Fonológicos | Tabela 8 processos com `.status-ok` (esperado) e `.status-warn` (desvio) |
| Avaliação Articulatória | Grid 2 colunas: o que avaliar / tipos de erros + erros consistentes × inconsistentes |
| Inteligibilidade de Fala | Grid 4 faixas etárias com percentuais + alerta urgente para < 4 anos |

---

### Guia 4 — Motricidade Orofacial (`#guide-motricidade`)
Ícone: MO · 4 seções

| Seção | Conteúdo |
|---|---|
| Avaliação das Estruturas Orofaciais | Grid 2×2 (`.edu-grid-2`): lábios, língua, bochechas, palato e oclusão |
| Avaliação das Funções Estomatognáticas | Grid 3 colunas (`.edu-grid-3`) com pares `.ok-card` / `.warn-card`: respiração, mastigação, deglutição |
| Tonicidade e Mobilidade Muscular | 3 cards de tônus (hipo/normo/hiper) + checklist de mobilidade + nota contextual |
| Hábitos Orais e seu Impacto | Tabela: hábito / consequências / faixa de risco + nota sobre abordagem multidisciplinar |

---

### Guia 5 — Raciocínio Clínico Integrado (`#guide-raciocinio`)
Ícone: RC · 4 seções

| Seção | Conteúdo |
|---|---|
| O que é Raciocínio Clínico? | Definição + processo em 6 etapas (`.edu-steps`) |
| Integração dos Dados da Avaliação | 6 perguntas integradoras (`.edu-steps`) + callout "hipóteses abertas" |
| Formulando Hipóteses Diagnósticas | 3 cards (`.edu-grid-3`): baseada em evidências, provisória, funcional + caso clínico (`.edu-case`) |
| Do Diagnóstico ao Planejamento | Grid SMART 6 itens (`.smart-grid`) + priorização + documentação clínica |

---

---

## 12. Painel Administrativo

Duas telas SPA adicionadas ao fluxo, acessíveis via dropdown **Admin** no header de qualquer tela. Toda a persistência é **em memória** (sem backend) — os dados são perdidos ao recarregar a página, o que é intencional para este protótipo.

---

### Tela: Gestão de Guias (`#admin-guias`)

Estrutura:
```
#admin-guias (.admin-screen)
  header.nav              ← mesmo padrão dos guias (com todos os dropdowns)
  .admin-wrap
    .admin-page-header    ← título h1 + botão "+ Novo Guia"
    .admin-card
      table.admin-table   ← tbody populado por renderAdminGuides()
  #guide-form-modal (.admin-modal-overlay)    ← modal criar/editar
  #guide-delete-modal (.admin-modal-overlay)  ← modal confirmação exclusão
```

**Dados em memória (`adminGuides[]`):**
```js
[
  { id: string, icon: string, label: string, description: string, active: boolean },
  ...
]
```
Pré-populado com os 5 guias existentes (anamnese, linguagem, fala, motricidade, raciocinio). Novos guias recebem `id = 'custom-N'` com `_guideNextId` auto-incrementado.

**Colunas da tabela:** Ícone · Nome · Descrição · Status (badge) · Ações (Editar / Excluir)

**Formulário (modal `#guide-form-modal`):**
| Campo | Input | Validação |
|---|---|---|
| Ícone (emoji) | `text`, `maxlength=4` | `required` |
| Nome do guia | `text` | `required` |
| Descrição | `textarea` | `required` |
| Status | `select` Ativo / Inativo | — |

**Exclusão:** modal `#guide-delete-modal` exibe o nome do guia e requer confirmação antes de remover.

---

### Tela: Gestão de Perguntas (`#admin-perguntas`)

Estrutura:
```
#admin-perguntas (.admin-screen)
  header.nav
  .admin-wrap
    .admin-page-header    ← título h1 + botão "+ Nova Pergunta"
    #admin-q-tabs         ← tabs de filtro por área (renderizadas via JS)
    .admin-card
      table.admin-table   ← tbody populado por renderAdminQuestions()
  #q-form-modal (.admin-modal-overlay)    ← modal criar/editar
  #q-delete-modal (.admin-modal-overlay)  ← modal confirmação exclusão
```

**Fonte de dados:** As perguntas são lidas e escritas diretamente no objeto `QUESTION_SETS` (já existente). Edições feitas aqui refletem imediatamente nos diagnósticos realizados na sessão.

**Tabs de área:** um botão por chave de `QUESTION_SETS`; a ativa tem classe `.admin-tab.active`. Clicar chama `switchAdminQType(type)`.

**Colunas da tabela:** # · Tipo (badge colorido) · Enunciado + Ajuda clínica · Ações (Editar / Excluir)

**Formulário (modal `#q-form-modal`):**
| Campo | Input | Observação |
|---|---|---|
| Área de diagnóstico | `select` (gerado via JS com as chaves de `QUESTION_SETS`) | Pré-seleciona a tab ativa |
| Tipo de pergunta | `select` scale / yesno / text | Ao mudar para `yesno`, exibe o campo abaixo |
| Sentido da resposta "Sim" | `select` positivo / negativo | Visível **apenas** quando tipo = `yesno` |
| Enunciado | `textarea` | `required` |
| Ajuda clínica | `textarea` | `required` |

**Exclusão:** modal `#q-delete-modal` identifica a pergunta pelo número e área.

---

### Sistema de Toast (`#admin-toast`)

Elemento único compartilhado entre as duas telas admin, posicionado `fixed` no rodapé centralizado.

```js
showToast('Mensagem.', 'success' | 'warning')
```

- `'success'` → fundo `var(--success)` (verde)
- `'warning'` → fundo `var(--warning)` (laranja)
- Auto-oculto após 3,5 s via `_toastTimer`.
- Usa `role="status"` + `aria-live="polite"` para acessibilidade.

---

### Classes CSS do Admin

#### Layout
| Classe | Uso |
|---|---|
| `.admin-screen` | Container full-page das telas admin (`min-height: 100vh`) |
| `.admin-wrap` | Wrapper centralizado (max-width 1100px, padding 40px 24px) |
| `.admin-page-header` | Flex row: título+subtítulo à esquerda, botão CTA à direita |
| `.admin-card` | Card branco com borda, border-radius 14px e sombra |

#### Tabela
| Classe | Uso |
|---|---|
| `.admin-table` | Tabela full-width com collapse |
| `.admin-td-icon` | Célula de ícone centralizada (52px) |
| `.admin-td-desc` | Célula de descrição com `max-width: 320px` |
| `.admin-td-num` | Célula de número (#) centralizada |
| `.admin-td-q` | Célula de enunciado com `max-width: 400px` |
| `.admin-td-actions` | Célula de ações em flex row com gap |
| `.admin-q-text` | Parágrafo de enunciado dentro da célula |
| `.admin-q-help` | Parágrafo de ajuda clínica (menor, cor muted) |
| `.admin-empty` | Linha de estado vazio centralizada |

#### Badges
| Classe | Uso |
|---|---|
| `.admin-badge` | Pílula genérica com ponto colorido antes do texto |
| `.badge-active` | Verde — guia ativo |
| `.badge-inactive` | Vermelho — guia inativo |
| `.admin-type-badge` | Pílula de tipo de pergunta |
| `.type-scale` | Roxo — escala 1–10 |
| `.type-yesno` | Verde — sim/não |
| `.type-text` | Amarelo — texto livre |

#### Botões
| Classe | Uso |
|---|---|
| `.btn-sm` | Versão compacta de `.btn` (padding e font-size reduzidos) |
| `.btn-danger-outline` | Botão com borda e texto vermelhos; hover inverte para fundo vermelho |

#### Modais admin
| Classe | Uso |
|---|---|
| `.admin-modal-overlay` | Overlay fixo fullscreen com blur |
| `.admin-modal` | Container do modal (max-width 520px, `max-height: 90vh`) |
| `.admin-modal-sm` | Variante menor (max-width 420px) para confirmações |
| `.admin-modal-header` | Flex row: título h3 + botão fechar (✕) |
| `.admin-modal-footer` | Flex row justify-end: botões Cancelar + ação principal |
| `.admin-modal-confirm-body` | Área de texto de confirmação |

#### Formulários
| Classe | Uso |
|---|---|
| `.form-group` | Grupo label + input/select/textarea com margin-bottom 18px |

#### Tabs e Toast
| Classe | Uso |
|---|---|
| `.admin-tabs` | Container flex das tabs de área |
| `.admin-tab` | Botão de tab individual |
| `.admin-tab.active` | Tab selecionada (fundo primary) |
| `.admin-toast` | Toast fixo no rodapé; classe `.show` o torna visível |
| `.toast-success` | Variante verde |
| `.toast-warning` | Variante laranja |

---

## 13. Diagnóstico — Listagem (`#diag-list-screen`)

Tela SPA adicionada ao fluxo, acessível via dropdown **Diagnóstico → Listagem** no header da Home (e de qualquer outra tela que replique o header). O link anteriormente apontava para `#diagnostico/listagem` (âncora morta) e foi corrigido para chamar `showDiagList()`.

### Estrutura

```
#diag-list-screen (.admin-screen)
  header.nav              ← mesmo padrão das telas admin (com todos os dropdowns funcionais)
  .admin-wrap
    .admin-page-header    ← título h1 "Listagem de Diagnósticos" + botão "+ Novo Diagnóstico"
    .admin-card
      table.admin-table   ← 8 linhas de dados mockados
```

O botão "+ Novo Diagnóstico" chama `openPlans()`, respeitando o fluxo obrigatório: Modal de Planos → Seleção de Tipo → Perguntas.

### Dados mockados (8 registros)

| Paciente | Data | Área Avaliada | Resultado | Plano |
|---|---|---|---|---|
| Ana Luísa Ferreira | 14/05/2026 | Anamnese Clínica | Normalidade | Profissional |
| Carlos Eduardo Nunes | 13/05/2026 | Linguagem Infantil | Atenção moderada | Essencial |
| Mariana Costa Lima | 12/05/2026 | Fala e Fonologia | Alto alerta clínico | Profissional |
| Rafael Souza Andrade | 10/05/2026 | Motricidade Orofacial | Atenção moderada | Instituição |
| Beatriz Helena Rocha | 08/05/2026 | Linguagem Infantil | Normalidade | Profissional |
| Thiago Melo Barbosa | 07/05/2026 | Anamnese Clínica | Alto alerta clínico | Essencial |
| Fernanda Oliveira Dias | 05/05/2026 | Fala e Fonologia | Normalidade | Instituição |
| Lucas Pereira Santos | 03/05/2026 | Motricidade Orofacial | Atenção moderada | Profissional |

### Colunas da tabela

| Coluna | Conteúdo |
|---|---|
| Paciente | Nome em `<strong>` |
| Data | Cor `var(--muted)`, `white-space: nowrap` |
| Área Avaliada | Tag `.diag-list-type-tag` com abreviação e nome da área |
| Resultado | Badge `.diag-list-badge` com variante de cor conforme nível |
| Plano | Texto simples, cor `var(--muted)` |
| Ação | Botão "PDF" (`.btn.btn-outline.diag-list-pdf-btn`) — ver abaixo |

### Botão de download PDF

Exibe `alert('Funcionalidade de download em breve.')` ao ser clicado. **Não implementa download real** — placeholder visual para demonstração.

### Classes CSS adicionadas

| Classe | Uso |
|---|---|
| `.diag-list-type-tag` | Pílula `var(--primary-light)` / `var(--primary-dark)` para área avaliada |
| `.diag-list-badge` | Pílula base para resultado do diagnóstico |
| `.diag-list-badge--ok` | Variante verde (`#dcfce7` / `var(--success)`) — Normalidade |
| `.diag-list-badge--warn` | Variante amarela (`#fef3c7` / `var(--warning)`) — Atenção moderada |
| `.diag-list-badge--danger` | Variante vermelha (`#fee2e2` / `var(--danger)`) — Alto alerta clínico |
| `.diag-list-pdf-btn` | Botão compacto de ação (`font-size: 12px`, `padding: 5px 12px`) |

### Funções JS adicionadas / alteradas

| Função | Responsabilidade |
|---|---|
| `showDiagList()` | Usa `hideAll()`, exibe `#diag-list-screen`, rola para o topo |

`'diag-list-screen'` foi adicionado ao array `ALL_SCREENS`.

---

## 14. Tela de Perfil (`#profile-screen`)

Tela SPA acessível via **dropdown Conta → Perfil** no header (presente em todas as telas que replicam o header). Exibe informações mockadas do profissional logado e, em destaque, os dados de uso do plano atual.

### Estrutura

```
#profile-screen (.admin-screen)
  header.nav              ← mesmo padrão das demais telas (com todos os dropdowns funcionais)
  .profile-screen-wrap   ← max-width 860px, centrado
    .profile-hero-card   ← cartão gradiente com avatar, nome e CRFa
    .plan-usage-card     ← seção de destaque: plano, uso e renovação
    .profile-info-grid   ← grid 2×2 com dados complementares
    .profile-actions     ← botões "Editar perfil" e "Gerenciar plano"
```

### Dados mockados

| Campo | Valor |
|---|---|
| Nome | Dra. Mariana Fonseca |
| Iniciais (avatar) | MF |
| Especialidade | Fonoaudiologia Clínica Infantil |
| Cidade | São Paulo, SP |
| CRFa | 2‑12345 |
| E-mail | mariana.fonseca@clinicainfantil.com.br |
| Local de atuação | Clínica Infantil Bem Viver — São Paulo/SP |
| Membro desde | Janeiro de 2025 (16 meses) |
| Plano ativo | Profissional |
| Diagnósticos realizados | 2 de 3 (este mês) |
| Diagnósticos disponíveis | 1 |
| Próxima renovação | 01 de junho de 2026 |

### Seção de uso do plano (`.plan-usage-card`)

Element de maior destaque visual da tela. Possui:
- Faixa lateral esquerda em gradiente (`var(--primary)` → `var(--accent)`).
- Badge do plano com gradiente: **★ Profissional**.
- Data de renovação alinhada à direita.
- Contador tipográfico grande: **2 / 3** (diagnósticos realizados / total do plano).
- Barra de progresso (`66.67%` preenchida).
- Box lateral **"1 disponível"** em `var(--primary-light)`.

### Ações disponíveis

| Botão | Comportamento |
|---|---|
| Editar perfil | `alert('Funcionalidade em breve.')` — placeholder visual |
| Gerenciar plano | `openPlans()` — abre o modal de planos |

### Classes CSS adicionadas

#### Layout
| Classe | Uso |
|---|---|
| `.profile-screen-wrap` | Wrapper centralizado (max-width 860px, padding 40px 24px 80px) |

#### Cartão de boas-vindas
| Classe | Uso |
|---|---|
| `.profile-hero-card` | Card gradiente primary→accent com flex row; avatar + info |
| `.profile-avatar` | Círculo 84×84px com iniciais, fundo semi-transparente branco |
| `.profile-hero-info h2` | Nome do profissional em branco |
| `.profile-role` | Especialidade + cidade em branco com opacidade |
| `.profile-crfa` | Pílula com borda branca semi-transparente para o número CRFa |

#### Seção de plano
| Classe | Uso |
|---|---|
| `.plan-usage-card` | Card branco com borda azul e faixa lateral gradiente |
| `.plan-usage-header` | Flex row: plano ativo (esquerda) + renovação (direita) |
| `.plan-usage-section-label` | Label uppercase minúsculo (cor muted) |
| `.plan-name-badge` | Pílula gradiente com ícone ★ e nome do plano |
| `.plan-renew-info` | Bloco de renovação alinhado à direita |
| `.plan-renew-label` | Label uppercase "Próxima renovação" |
| `.plan-renew-date` | Data em destaque (18px, font-weight 700) |
| `.plan-stats-row` | Grid 2 colunas: contador à esquerda, "disponível" à direita |
| `.plan-usage-numbers` | Flex com `.plan-usage-used` / `.plan-usage-sep` / `.plan-usage-total` |
| `.plan-usage-used` | Número grande (52px, 900) em `var(--primary)` |
| `.plan-usage-sep` | Separador `/` em `var(--border)` |
| `.plan-usage-total` | Total (30px, 700) em `var(--muted)` |
| `.plan-usage-label` | Label descritiva abaixo dos números |
| `.plan-usage-bar` | Container da barra de progresso |
| `.plan-usage-fill` | Preenchimento da barra (gradiente, `width` inline em %) |
| `.plan-usage-remaining` | Box "N disponível" em `var(--primary-light)` com borda sutil |
| `.rem-count` | Número do saldo disponível (40px, 900, `var(--primary)`) |
| `.rem-label` | Label "disponível" uppercase |

#### Informações e ações
| Classe | Uso |
|---|---|
| `.profile-info-grid` | Grid 2×2 com cards de informações complementares |
| `.profile-info-card` | Card branco com borda e sombra; `h4` uppercase + `p` de valor |
| `.profile-actions` | Flex row com gap de 12px para os botões de ação |

### Responsividade

Ao `max-width: 640px`: hero-card vira coluna centralizada, plan-stats-row vira 1 coluna, renovação alinha à esquerda, info-grid vira 1 coluna.

### Funções JS adicionadas

| Função | Responsabilidade |
|---|---|
| `showProfile()` | Usa `hideAll()`, exibe `#profile-screen`, rola para o topo |

`'profile-screen'` foi adicionado ao array `ALL_SCREENS`.

O link `<a href="#" onclick="return false">Perfil</a>` no dropdown **Conta** (presente no header da Home e em todas as telas replicadas) foi atualizado para `onclick="showProfile();return false"`.

---

## 15. Responsividade

Breakpoint único: `@media (max-width: 900px)`. Tudo em uma coluna, nav-links escondidos, escala vira 2 linhas de 5 botões, grid de tipos de diagnóstico vira 1 coluna, botões Sim/Não reduzem gap.

Na tela de login em mobile: `.login-side` perde `border-right` e recebe `border-bottom: 1px solid var(--border)`. `.login-hero-content` reduz padding.

Manter o protótipo funcional em mobile.

---

## 16. Regras para o próximo agente

✅ **Pode (encorajado):**
- Adicionar novas seções na Home (ex: depoimentos, FAQ, blog) **abaixo** das existentes.
- Adicionar novos módulos extras na grid (preservando os 9 atuais).
- Adicionar novas dicas clínicas (preservando as 6 atuais).
- Incluir animações sutis, micro-interações, ícones adicionais.
- Adicionar um menu mobile (hamburguer) — atualmente os nav-links somem em mobile.
- Adicionar mais campos opcionais ao registro.
- Adicionar mais provedores sociais.
- Adicionar telas novas (ex: histórico detalhado por paciente, relatórios) **sem remover as existentes**.
- Adicionar novos tipos de diagnóstico ao `QUESTION_SETS` e incluir um novo card em `#diag-type-screen`.
- Adicionar mais perguntas por tipo (alterar o array `questions` de cada chave do `QUESTION_SETS`).
- Estender o painel admin com novas funcionalidades (ex: reordenação de perguntas por drag-and-drop, exportação de dados, histórico de diagnósticos).

**Não pode:**
- Remover ou renomear qualquer função JS listada na §10.
- Remover qualquer das telas, do modal de planos, ou de qualquer seção da Home.
- Remover qualquer dos 4 tipos de diagnóstico existentes (`anamnese`, `linguagem`, `fala`, `motricidade`).
- Reduzir o número de planos (deve permanecer 3) ou alterar a equivalência 1/3/10 diagnósticos por dia.
- Trocar a paleta de cores ou a fonte Inter.
- Tornar o login funcional/restritivo — ele **precisa** permitir entrar com qualquer (ou nenhum) dado.
- Adicionar dependências externas pesadas (frameworks, build steps). Manter HTML único.
- Quebrar o fluxo: Login → Home → Modal → Seleção de Tipo → Perguntas → Resultado → Home.
- Remover o disclaimer "não substitui avaliação presencial" do resultado.

⚠️ **Atenção:**
- Todos os textos clínicos foram baseados no conteúdo do site do cliente. Ao adicionar novos, manter tom técnico/educativo em pt-BR.
- Conteúdo do diagnóstico é **mockado** — não conectar a APIs reais sem aviso prévio.
- Antes de qualquer edição grande, **ler o `index.html` inteiro** para conhecer o estado atual.
- A constante `ALL_SCREENS` deve ser atualizada sempre que uma nova tela (`div` de nível raiz) for adicionada.
- Ao adicionar novas funções JS públicas, registrá-las na tabela da §10.
- Ao adicionar novas classes CSS admin, registrá-las na §12 (subseção "Classes CSS do Admin") ou na §13 se forem específicas da Listagem.
- As alterações feitas via painel admin são **em memória** — recarregar a página descarta tudo. Isso é intencional; não implementar persistência (localStorage, API) sem acordo com o cliente.
- Nunca usar `innerHTML` com dados externos sem passar por `escHtml()`. Todos os dados de `adminGuides[]` e `QUESTION_SETS` devem ser escapados antes de inserir no DOM.

---

## 18. Histórico de alterações

| Data | Alteração | Detalhes |
|---|---|---|
| 2026-05-16 | Dropdown "Conta" no header da Home | O link estático `<a href="#conta">Conta</a>` foi substituído por um `.nav-item` com `.nav-trigger` e `.dropdown`, contendo dois submenus: **Perfil** (placeholder, `onclick="return false"`) e **Sair** (chama `logout()`, redireciona para `#login-screen`). Mesma implementação de dropdown já usada pelos menus Guias técnicos, Diagnóstico e Admin. |
| 2026-05-16 | Tela de Perfil (`#profile-screen`) | Nova tela SPA acessível via **Conta → Perfil**. Exibe cartão de boas-vindas com gradiente, seção em destaque com dados do plano ativo (nome, diagnósticos realizados/disponíveis, data de renovação, barra de progresso), grid de informações pessoais e botões de ação. Dados 100% mockados. Adicionada `showProfile()` ao JS, `'profile-screen'` ao `ALL_SCREENS`, e link do dropdown atualizado de `onclick="return false"` para `onclick="showProfile();return false"`. Ver §14 para especificação completa. |
| 2026-05-16 | Reordenação dos itens do nav e nav completo nas telas de guia | **Ordem dos menus alterada** em todos os headers (home, guias, admin, diagnóstico, perfil) para: **Início → Guias técnicos → Diagnóstico → Conta → Admin**. Nota: o cliente solicitou a ordem "Início, Guias técnicos, Diagnóstico, Conta" — e optou por manter **Conta antes de Admin** (Admin por último). As telas de guia (`#guide-anamnese`, `#guide-linguagem`, `#guide-fala`, `#guide-motricidade`, `#guide-raciocinio`) que antes exibiam apenas os dropdowns "Guias técnicos" e o link "Início" passaram a ter o **nav completo** (todos os dropdowns), evitando que o menu sumisse ao navegar por um guia. |

---

## 17. Como rodar

```bash
cd prototipo
python3 -m http.server 8765
# abrir http://localhost:8765
```

Não há build, não há `npm install`, não há transpilação. Apenas abrir no navegador.
