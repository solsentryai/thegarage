# THE/GARAGE — Gerador de Avatar & Banner

> Ferramenta web gratuita para a comunidade **Superteam Brasil** amplificar o lançamento do **THE/GARAGE**, a imersão de 3 semanas rumo ao **Colosseum** — o Hackathon Global da Solana.

![stack](https://img.shields.io/badge/stack-HTML%2FCSS%2FJS-black?style=flat-square)
![sem backend](https://img.shields.io/badge/backend-nenhum-EAFD7C?style=flat-square&labelColor=1a1a1a&color=EAFD7C)
![sem deps](https://img.shields.io/badge/dependencias-zero-C299EE?style=flat-square&labelColor=1a1a1a)
![single file](https://img.shields.io/badge/arquivo-single--file-1a1a1a?style=flat-square&labelColor=EAFD7C&color=1a1a1a)

🔗 **Live:** [solsentryai.github.io/thegarage](https://solsentryai.github.io/thegarage/)

---

## Sumário

- [O que é](#-o-que-é)
- [Demo rápida](#-demo-rápida)
- [Funcionalidades](#-funcionalidades)
- [Brand kit](#-brand-kit)
- [Rodando localmente](#-rodando-localmente)
- [Deploy](#-deploy)
- [Arquitetura](#%EF%B8%8F-arquitetura)
- [Personalizando para sua comunidade](#-personalizando-para-sua-comunidade)
- [Contribuindo](#-contribuindo)
- [Créditos](#-créditos)

---

## 🎯 O que é

O **@SuperteamBR** pediu que membros da comunidade trocassem suas fotos de perfil pelas cores do THE/GARAGE para amplificar o alcance do evento. A ideia é simples: quanto mais gente com a pfp em roxo + limão, maior a onda visual até o dia 9 de maio.

Este gerador nasceu pra facilitar isso — qualquer pessoa (membro ou não) pode:

- Subir a própria foto e transformar em avatar duotone halftone em segundos
- Criar banners de eventos (lives, workshops, meetups) mantendo a identidade visual
- Baixar tudo em PNG de alta resolução

**Sem login. Sem servidor. Sem instalar nada.** A foto nunca sai do seu dispositivo — todo o processamento é feito no browser via Canvas API.

---

## ⚡ Demo rápida

1. Acessa o [link](https://solsentryai.github.io/thegarage/)
2. Arrasta sua foto (ou clica pra selecionar)
3. Ajusta os controles — tamanho dos pontos, handle, formato
4. Clica em **Baixar PNG** na cor que preferir (limão ou roxo)

Pronto. Troca a pfp e compartilha com a hashtag do evento.

---

## 🛠 Funcionalidades

### Avatar (foto)

| Feature | Descrição |
|---|---|
| **Upload** | Drag & drop ou clique para escolher |
| **Halftone** | Efeito pontilhado duotone com densidade ajustável (3–12px) |
| **Formato** | Circular (pronto pra pfp) ou quadrado |
| **Handle/nome** | Campo opcional que aparece em arco (circular) ou barra (quadrado) |
| **Logos** | Rodapé com Superteam Brasil · THE/GARAGE · Solana · Colosseum |
| **Auto-render** | Qualquer mudança nos controles atualiza o preview instantaneamente |
| **Output** | PNG 800×800 nas duas variantes de cor |

### Banner de texto

| Feature | Descrição |
|---|---|
| **Textos** | Título, Subtítulo e Tag (pílula de destaque) |
| **Tamanhos** | Quadrado 1:1 (800×800), Paisagem (1500×500), Retrato 4:5 (1080×1350) |
| **3 estilos de halftone** | Wave (orgânico), Uniforme (grid), Gradiente (fade) |
| **Fonte opcional** | Foto com halftone que pode ir à esquerda, direita ou fundo inteiro |
| **9 posições de texto** | Grid 3×3 (canto superior esquerdo, centro, canto inferior direito, etc.) |
| **2 famílias de fonte** | Montserrat (default) ou Cascadia Code |
| **Output** | PNG nas proporções escolhidas, duas variantes de cor |

### Duas variantes de cor, sempre

Todo avatar/banner é gerado em **duas versões simultaneamente**:

| Variante | Fundo | Elementos |
|---|---|---|
| 🟡 Fundo Limão | `#EAFD7C` | `#C299EE` |
| 🟣 Fundo Roxo | `#C299EE` | `#EAFD7C` |

Assim a pessoa escolhe a que prefere sem precisar regenerar.

---

## 🎨 Brand kit

```css
/* Cores */
--lime:   #EAFD7C;
--purple: #C299EE;
--dark:   #1a1a1a;

/* Tipografia */
--font-primary:   'Montserrat', sans-serif;     /* variable font, local */
--font-secondary: 'Cascadia Code', monospace;   /* via fontsource CDN */
--font-ui:        'Courier New', Courier, monospace;
```

**Ordem oficial dos logos:** Superteam Brasil · THE/GARAGE · Solana · Colosseum

**Ícones (desenhados em canvas, sem dependência externa):**
- Superteam Brasil → quadrado preenchido com "ST" invertido
- THE/GARAGE → casinha com porta recortada
- Solana → 3 barras horizontais
- Colosseum → 3 colunas com capitel

---

## 💻 Rodando localmente

```bash
# Clone o repo
git clone https://github.com/solsentryai/thegarage.git
cd thegarage

# Abre no navegador — não precisa de servidor
open index.html       # macOS
xdg-open index.html   # Linux
start index.html      # Windows
```

Como é single-file HTML com as fontes locais, basta abrir o arquivo. Não precisa de `npm install`, build step ou servidor.

> 💡 Se quiser rodar com um servidor estático local (para testar drag & drop de forma mais realista), rode `python3 -m http.server 8080` e acesse `localhost:8080`.

---

## 🚀 Deploy

### Opção 1 — GitHub Pages (gratuito, permanente)

```bash
git push origin main
```

No GitHub: Settings → Pages → Branch: `main` → Save. Link fica `https://seuuser.github.io/thegarage`.

### Opção 2 — Netlify Drop (mais rápido)

1. Acessa [app.netlify.com/drop](https://app.netlify.com/drop)
2. Arrasta a pasta do projeto
3. Link gerado na hora

### Opção 3 — Vercel

```bash
npx vercel
```

Qualquer uma funciona — o projeto é 100% estático.

---

## 🏗️ Arquitetura

```
thegarage/
├── index.html                               # App completo single-file
├── Montserrat-VariableFont_wght.ttf         # Fonte local (variable)
├── Montserrat-Italic-VariableFont_wght.ttf  # Fonte local itálico
├── README.md                                # Este arquivo
└── CLAUDE.md                                # Guia para LLMs expandirem o projeto
```

**Stack completa:**
- HTML5 + CSS3 + JavaScript vanilla (ES5 compatível)
- Canvas API para geração de imagens
- `@font-face` para Montserrat local
- CDN fontsource para Cascadia Code (única dependência externa)

**Sem:** React, Vue, Webpack, Vite, TypeScript, npm, build step, backend, banco de dados, analytics, tracking, cookies.

O app é carregado em **um único request HTML** (fora as fontes). Funciona offline depois do primeiro load.

---

## 🎨 Personalizando para sua comunidade

Quer adaptar pro seu evento / comunidade? Os pontos de customização mais óbvios:

### 1. Trocar as cores (linhas ~25 no CSS)

```css
:root {
  --lime:   #EAFD7C;  /* sua cor A */
  --purple: #C299EE;  /* sua cor B */
  --dark:   #111;
}
```

E atualizar o array `VARS` no JavaScript:

```js
var VARS = [
  {id:'a', bg:'#EAFD7C', fg:'#C299EE', label:'FUNDO LIMÃO', inv:false},
  {id:'b', bg:'#C299EE', fg:'#EAFD7C', label:'FUNDO ROXO',  inv:true}
];
```

### 2. Trocar os logos

Os ícones são desenhados em canvas nas funções `drawLogos` e `drawLogosArcSegments`. Cada logo é uma combinação de `fillRect` / `beginPath` / `arc`. Ver `CLAUDE.md` para guia completo.

### 3. Trocar as fontes

Substituir o `.ttf` da Montserrat e ajustar o `@font-face` no CSS.

### 4. Ajustar tamanhos/proporções

As dimensões dos banners estão em `renderBanner()`:

```js
var szW = document.getElementById('szLand').checked ? 1500 :
          document.getElementById('szPort').checked ? 1080 : 800;
var szH = document.getElementById('szLand').checked ? 500 :
          document.getElementById('szPort').checked ? 1350 : 800;
```

Ver `CLAUDE.md` para entender como o resto do código reage a essas mudanças.

---

## 🤝 Contribuindo

PRs bem-vindos! Algumas áreas interessantes pra expandir:

- [ ] **Paisagem (1500×500)** — proporções de texto ainda em ajuste fino
- [ ] Mais proporções de banner (LinkedIn 1200×627, Instagram Story 1080×1920)
- [ ] Pré-visualização em tempo real enquanto ajusta sliders (hoje: avatar tem, banner tem após primeiro "Gerar")
- [ ] Modo "dark mode" do app (atualmente só o preview pode ser claro)
- [ ] Export em SVG além de PNG
- [ ] Exportar múltiplos formatos em ZIP de uma vez só
- [ ] Templates de banner salvos (hackathons, lives, meetups, thread)
- [ ] Paleta expandida (accent colors neon opcionais)

**Antes de abrir PR:**

1. Testa nos 3 tamanhos de banner (quadrado, paisagem, retrato)
2. Testa com e sem foto, com e sem cada texto
3. Testa as 9 posições de texto do grid 3×3
4. Testa com handle curto, longo e vazio no avatar
5. Testa circular e quadrado no avatar

Se for mexer em lógica de render, lê o `CLAUDE.md` primeiro — tem um histórico de bugs sutis (hoisting de `var`, coordenadas cover-fit) que é fácil reintroduzir.

---

## 📜 Licença

MIT. Use, copie, modifique, distribua. Sem garantias.

Se usar para outra comunidade / evento, **um crédito nos logos ou no footer é apreciado** mas não obrigatório.

---

## 🙏 Créditos

**THE/GARAGE** é a imersão do Superteam Brasil na Solana House, rumo ao Colosseum — o hackathon global da Solana.

Apoiado por: **Solana · Superteam Brasil · Colosseum**

Ferramenta criada por **[Crash](https://x.com/crashdiniz)**, builder de **[Solsentry](https://x.com/solsentryai)**, durante o THE/GARAGE Cohort.1 — for hackathon frontier 🟡🟣

---

<sub>github.com/solsentryai/thegarage · THE/GARAGE Cohort.1 · SuperTeam Brasil</sub>
