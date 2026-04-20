# THE/GARAGE — Gerador de Avatar & Banner

> Feito pela comunidade do **Superteam Brasil** para amplificar o lançamento do **THE/GARAGE**, a imersão de 3 semanas rumo ao **Colosseum** — o Hackathon Global da Solana.

![roxo + limão](https://img.shields.io/badge/cores-roxo+lim%C3%A3o-C299EE?style=flat-square)
![sem backend](https://img.shields.io/badge/backend-nenhum-EAFD7C?style=flat&labelColor=1a1a1a&color=EAFD7C)
![puro HTML](https://img.shields.io/badge/stack-HTML%2FCSS%2FJS-black?style=flat-square)

---

## O que é isso?

O **Shimas** (Superteam Brasil) pediu pra galera trocar a foto de perfil no Twitter pelas cores do THE/GARAGE pra amplificar o alcance do evento no dia do lançamento. A ideia: quanto mais gente com a pfp nas cores roxo + limão, maior a onda.

Esse gerador surgiu pra facilitar — qualquer pessoa (membro ou não) pode subir a própria foto, aplicar o efeito duotone halftone e baixar o avatar pronto em segundos.

---

## Como usar

Acessa o link, arrasta sua foto, ajusta os controles e clica em **Baixar PNG**.

Ou abre o `index.html` direto no navegador — funciona 100% offline, sem servidor, sem login, sem instalar nada.

---

## Funcionalidades

### Avatar (foto)
- Upload por drag & drop ou clique
- Efeito **duotone halftone** com pontilhado ajustável (3–12px)
- Opção de recorte **circular** (pronto pra pfp) ou quadrado
- Duotone suave (sem halftone) também disponível
- Campo de **handle / nome** no rodapé da imagem
- Toggle de **logos** (Solana · Superteam Brasil · THE/GARAGE · Colosseum)
- Exporta em 800×800px PNG

### Banner de texto
- Campo de **título** (grande, estilo pixel/monospace)
- Campo de **subtítulo** (data, local, nome do palestrante)
- Campo de **tag/evento** em caixa de destaque
- Fundo com **halftone espiral** gerado matematicamente — mesmo estilo dos banners oficiais
- Toggle de logos no rodapé
- Exporta em 800×800px PNG

### Duas variantes de cor
| Variante | Fundo | Elementos |
|----------|-------|-----------|
| Limão | `#EAFD7C` | `#C299EE` |
| Roxo | `#C299EE` | `#EAFD7C` |

---

## Stack

Puro HTML + CSS + JavaScript vanilla. Sem frameworks, sem dependências, sem build step. Um único arquivo `.html`.

O processamento de imagem acontece 100% no browser via [Canvas API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) — a foto nunca sai do dispositivo do usuário.

---

## Deploy

### Netlify Drop (mais rápido)
1. Acessa [app.netlify.com/drop](https://app.netlify.com/drop)
2. Arrasta a pasta com o `index.html`
3. Link gerado na hora

### GitHub Pages
1. Cria um repositório com o arquivo renomeado para `index.html`
2. Settings → Pages → Branch: main → Save
3. Link: `https://seuuser.github.io/nome-do-repo`

---

## Contexto

**THE/GARAGE** é a imersão de 3 semanas do Superteam Brasil na Solana House, de 20 de abril a 9 de maio de 2025, rumo ao Colosseum — o hackathon global da Solana. Cohort.1.

Apoiado por: **Solana · Superteam Brasil · Colosseum**

---

## Licença

MIT — usa, adapta e manda ver.
