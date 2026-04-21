# CLAUDE.md

Este arquivo dá contexto a assistentes IA (Claude, Claude Code, Cursor, Copilot) que forem expandir, modificar ou debugar este projeto. Leia antes de escrever código.

---

## 📋 O que é este projeto

**THE/GARAGE Avatar & Banner Generator** — gerador web single-file que aplica efeito duotone halftone em fotos (avatares) e gera banners de texto com a identidade visual do evento THE/GARAGE (Superteam Brasil). Processamento 100% no browser via Canvas API.

**Arquivo único:** `index.html` com CSS + JS inline. Sem build step.

---

## ⚡ 30-second architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        index.html                                │
│                                                                  │
│  ┌──── HTML ────┐      ┌──── CSS ────┐      ┌──── JS ────┐      │
│  │ Hero (canvas)│      │ :root vars  │      │ 20 funções │      │
│  │ Tabs AV/BN   │◄────►│ .ctrl-box   │◄────►│ Canvas API │      │
│  │ Controles    │      │ .previews   │      │ Eventos    │      │
│  │ Previews     │      │ @font-face  │      │ Renderers  │      │
│  └──────────────┘      └─────────────┘      └────────────┘      │
│                                                                  │
│  Estado global (top-level vars):                                 │
│    _img     — foto do avatar (Image)                             │
│    _bnImg   — foto opcional do banner (Image)                    │
│    VARS     — array das 2 variantes de cor (limão, roxo)        │
└──────────────────────────────────────────────────────────────────┘
```

**Fluxo típico de render:**

1. Usuário interage com controle → handler dispara `renderAvatar()` ou `renderBanner()`
2. Renderer cria 2 cards (limão + roxo), cada um com seu `<canvas>`
3. Para cada canvas: pinta fundo → desenha foto em halftone (se houver) → desenha texto → desenha logos
4. Botão "Baixar PNG" usa `canvas.toDataURL()` e dispara download

---

## 🗺 Mapa de funções

### Utilitários
| Função | O que faz |
|---|---|
| `setMode(m)` | Troca entre tab Avatar e Banner |
| `h2r(hex)` | Converte `#RRGGBB` pra `[r,g,b]` |
| `makeCard(bg,fg,label,name)` | Cria card de preview com canvas + botão download |

### Desenho de foto
| Função | O que faz |
|---|---|
| `drawPhotoHT(img,ctx,sz,bg,fg,inv,circ,gs)` | Halftone duotone **quadrado** (avatar). `gs` = grid size, `inv` = inverte luminância |
| `drawPhotoDT(img,ctx,sz,bg,fg,circ)` | Duotone plano sem halftone (legacy, não usado no UI atual) |
| `drawBannerPhoto(img,ctx,sz,szH,fg,inv,gs,pos)` | Halftone retangular pra banner. `pos` = `'left'/'right'/'full'`. Usa **cover-fit** (preserva aspect ratio) |

### Desenho de fundo (halftone)
| Função | O que faz |
|---|---|
| `drawWaveHT(ctx,w,h,bg,fg,gs)` | Padrão ondulado orgânico (seno + polar) |
| `drawUniformDots(ctx,w,h,bg,fg,gs)` | Grid uniforme de pontos (**default** do banner) |
| `drawGradientDots(ctx,w,h,bg,fg,gs)` | Pontos que crescem da esquerda pra direita |

### Desenho de logos
| Função | O que faz |
|---|---|
| `drawLogos(ctx,sz,fg,bg,circ,ht)` | Barra de logos horizontal no rodapé. `ht` = altura total do canvas (necessário pro banner com `szH≠sz`) |
| `drawLogosArcSegments(ctx,sz,fg,bg)` | 4 logos em arco pro avatar circular |

### Desenho de texto
| Função | O que faz |
|---|---|
| `arcText(ctx,text,cx,cy,r,fg,bg,fs,arcCenter,arcSpan,fontFam,strokeW,skipBg)` | Texto seguindo um arco (usado pro handle circular) |
| `drawHandle(ctx,sz,txt,fg,bg,hasLg,circ)` | Handle/nome embaixo do avatar |
| `drawBannerTxt(ctx,sz,szH,t1,t2,t3,fg,bg,hasLg,pos)` | Título + subtítulo + tag do banner. **Função mais complexa do projeto.** |

### Renderers principais
| Função | O que faz |
|---|---|
| `renderAvatar()` | Entry point do avatar — lê controles, itera `VARS`, chama draw funcs |
| `renderBanner()` | Entry point do banner — idem. IIFE pra isolar variáveis |

### Auxiliares
| Função | O que faz |
|---|---|
| `loadFile(f)` / `onDrop(e)` | File upload do avatar |
| `bnAuto()` / `bnAutoD()` | Auto-render do banner (debounced pra text inputs) |
| `clearBnImg()` | Remove foto opcional do banner |

---

## 🎨 Constantes e convenções

### Cores oficiais (nunca mude sem atualizar tudo)

```js
#EAFD7C  // limão — cor principal do evento
#C299EE  // roxo — cor secundária
#1a1a1a  // dark — texto, fundo do app
```

### Estrutura `VARS`

```js
var VARS = [
  { id:'a', bg:'#EAFD7C', fg:'#C299EE', label:'FUNDO LIMÃO', inv:false },
  { id:'b', bg:'#C299EE', fg:'#EAFD7C', label:'FUNDO ROXO',  inv:true  }
];
```

- `bg` / `fg` são invertidos entre as variantes (nunca a mesma cor em ambas)
- `inv` controla inversão de luminância no halftone da foto
- Qualquer nova variante deve ser adicionada mantendo esse contraste

### Convenções de naming

- `sz` — tamanho quadrado (avatar) ou largura (banner, quando `szH` é fornecido separado)
- `szW` / `szH` — largura e altura do banner
- `szMin` — `Math.min(szW, szH)` com boost especial pra landscape
- `gs` — grid size do halftone
- `fg` — foreground (cor dos pontos/texto)
- `bg` — background (cor do fundo)
- `ht` — altura (parâmetro opcional em `drawLogos` pra não-quadrados)
- `circ` — flag booleana "é circular?"

---

## 🎯 Pontos de entrada (IDs importantes)

### Avatar
| ID | Elemento | Handler |
|---|---|---|
| `fi` | input file da foto | `onchange` → `loadFile` |
| `dz` | drop zone | `ondrop` → `onDrop` |
| `circ` | checkbox circular | `onchange` → `renderAvatar` |
| `avGS` | slider halftone | `oninput` → `renderAvatar` |
| `avTXT` | input handle | `oninput` → `renderAvatar` |
| `avLG` | checkbox logos | `onchange` → `renderAvatar` |
| `avPV` | container dos previews | populado por `renderAvatar` |

### Banner
| ID | Grupo/tipo | Handler |
|---|---|---|
| `bnT1` / `bnT2` / `bnT3` | inputs de texto | `oninput` → `bnAutoD` (debounced) |
| `bnLG` | checkbox logos | `onchange` → `bnAuto` |
| `bnGS` | slider halftone | `oninput` → `bnAutoD` |
| `szSq` / `szLand` / `szPort` | radios de tamanho | `onchange` → `bnAuto` |
| `patWave` / `patUniform` / `patGrad` | radios de estilo | `onchange` → `bnAuto` |
| `posRight` / `posLeft` / `posFull` | radios posição foto | `onchange` → `bnAuto` |
| `fntMono` / `fntImpact` | radios de fonte | `onchange` → `bnAuto` |
| `tpTL` ... `tpBR` | radios posição texto (9) | `onchange` → `bnAuto` |
| `bnFI` | input file da foto opcional | custom handler |
| `bnPosRow` | row de "Posição" | `display:none` até carregar foto |
| `bnPV` | container dos previews | populado por `renderBanner` |

**Default do banner:** Quadrado 1:1 + Uniforme + Fundo inteiro + Montserrat + Esquerda cima.

---

## ⚠️ Landmines conhecidas (bugs que já morderam)

Este projeto tem um histórico rico de bugs sutis. Não reintroduza estes:

### 1. `var` hoisting em `drawBannerTxt`

```js
// ❌ BUG: szMin é undefined aqui, areaW vira NaN silencioso
var areaW = sz - Math.round(szMin * 0.1);
// ...
var szMin = Math.min(sz, szH);  // declarado DEPOIS
```

Resultado: `areaW = NaN`, então `measureText.width > NaN` é sempre `false`, word wrap e shrink-to-fit nunca executam. Texto transborda.

**Regra:** sempre calcular `szMin` **antes** de qualquer expressão que o use.

### 2. `isCtr` ≠ `isVCtr`

```js
var isCtr  = tpos==='cc'||tpos==='cb'||tpos==='ct';  // horizontal center (3 posições)
var isVCtr = tpos==='cc'||tpos==='cl'||tpos==='cr';  // vertical middle (3 posições)
```

Não faça `isVCtr = isCtr || ...` — `ct` é horizontally-centered mas verticalmente **top**, não middle.

### 3. Canvas temp no tamanho errado em `drawBannerPhoto`

```js
// ❌ BUG clássico: cria tmp quadrado mas banner é retangular
var tmp = document.createElement('canvas');
tmp.width = sz; tmp.height = sz;  // ← deveria ser szH, não sz
```

E o loop interno:

```js
for(var dy=0; dy<gs && y+dy<sz; dy++) {  // ← deveria ser szH
```

### 4. Crop quadrado forçado (sem cover-fit)

```js
// ❌ BUG: usa o menor lado, crop quadrado, estica pra retângulo
var s = Math.min(img.width, img.height);
tc.drawImage(img, (img.width-s)/2, (img.height-s)/2, s, s, 0, 0, photoW, szH);
```

Pra foto 1500×500 em banner 1500×500, pegava só 500×500 do centro e esticava 3× → distorção. **Sempre use cover-fit calculando `srcR` vs `destR` e preservando aspect ratio.**

### 5. Canvas não aceita `;` no fim da font string

```js
ctx.font = 'bold 40px Montserrat;'  // ❌ não aplica!
ctx.font = 'bold 40px Montserrat'   // ✅
```

### 6. Arcos sem `closePath()` geram elipses

Quando desenha muitos círculos em um batch path:

```js
ctx.beginPath();
for (...) {
  ctx.moveTo(cx+r, cy);
  ctx.arc(cx, cy, r, 0, Math.PI*2);
  ctx.closePath();  // ← CRÍTICO, sem isso os arcos se conectam em elipses
}
ctx.fill();
```

### 7. Banner só renderiza após primeiro clique em "Gerar"

`bnAuto()` checa se `bnPV.display !== 'none'` antes de re-renderizar. Isso é intencional — evita render antes do usuário estar pronto. Se adicionar novo controle do banner, garanta que ele chame `bnAuto` (ou `bnAutoD` pra inputs de texto) e respeite esse comportamento.

### 8. Variable shadowing por nome curto

```js
function drawWaveHT(ctx, w, h, ...) {
  for (...) {
    var w = Math.sin(...);  // ❌ sombra o parâmetro w, JS hoista
  }
}
```

Use nomes internos diferentes (`wv`, `val`, etc.).

---

## 🧪 Checklist de testes (antes de commitar)

Qualquer mudança em render deve passar por:

**Avatar:**
- [ ] Foto colorida com pouco contraste (teste luminância)
- [ ] Foto PB (sem cor)
- [ ] Handle curto (3 chars)
- [ ] Handle longo (20+ chars)
- [ ] Handle vazio
- [ ] Circular com logos ON e OFF
- [ ] Quadrado com logos ON e OFF

**Banner:**
- [ ] 3 tamanhos (quadrado, paisagem 3:1, retrato 4:5)
- [ ] 3 estilos de halftone (wave, uniforme, gradiente)
- [ ] 9 posições de texto (grid 3×3)
- [ ] Com foto: direita, esquerda, fundo inteiro
- [ ] Sem foto
- [ ] Textos longos (título de 40+ chars)
- [ ] Tag vazia, só título
- [ ] Só tag, sem subtítulo nem título
- [ ] Mudar tamanho DURANTE edição (auto-render deve reagir)
- [ ] Logos ON e OFF

**Ambas variantes (limão + roxo)** devem renderizar corretamente em todos os casos.

---

## 📐 Como adicionar features comuns

### Adicionar novo tamanho de banner

1. Em `renderBanner()`, adicionar o novo dimensionamento:
   ```js
   var szW = document.getElementById('szLand').checked ? 1500 :
             document.getElementById('szPort').checked ? 1080 :
             document.getElementById('szStory').checked ? 1080 : 800;
   ```
2. Adicionar o radio no HTML:
   ```html
   <label class="ck"><input type="radio" name="bnSz" id="szStory"> 📱 Story 9:16</label>
   ```
3. Adicionar o ID na lista de wire-up (fim do script):
   ```js
   [..., 'szStory', ...].forEach(...)
   ```
4. Testar aspect ratio em `drawBannerTxt` — se for muito extremo, pode precisar novo cálculo de `szMin`.

### Adicionar novo estilo de halftone

1. Criar função `drawNewStyle(ctx, w, h, bg, fg, gs)` seguindo o contrato das existentes (fillStyle → beginPath → loop → fill)
2. Adicionar radio `patNew` no HTML
3. Adicionar branch em `renderBanner`:
   ```js
   if (pat==='new') drawNewStyle(...)
   ```
4. Adicionar ID no wire-up

### Adicionar nova variante de cor

Modificar `VARS`:
```js
{ id:'c', bg:'#NEW_BG', fg:'#NEW_FG', label:'FUNDO NOVO', inv:false }
```

Renderers já iteram `VARS.forEach`, então a nova variante vira preview automaticamente. Apenas **cuide do layout dos previews** — 3+ variantes podem precisar ajustar o `grid-template-columns` em `.previews`.

### Adicionar novo campo de texto no banner

1. HTML: adicionar `<input id="bnT4">` e registrar em `bnAutoD` wire-up
2. Em `renderBanner()`: `var t4 = document.getElementById('bnT4').value`
3. Passar pro `drawBannerTxt`: adicionar parâmetro e lógica de desenho
4. Recalcular `totalH` (inclui o novo elemento)

---

## 🔍 Debugging dicas

### "Texto não aparece" ou "texto transborda"
1. Verificar se `areaW` não é `NaN` (problema de hoisting)
2. Verificar se `szMin` foi calculado antes do uso
3. Inspecionar com `console.log(areaW, fs1, t1lines)` em `drawBannerTxt`

### "Logos não renderizam"
1. Verificar ordem de parâmetros em `drawLogos(ctx, sz, fg, bg, circ, ht)` — facil trocar `fg` e `bg`
2. Verificar se `circ` está correto pro contexto (banner sempre `false`, avatar depende)
3. No landscape: verificar se `ht` foi passado

### "Halftone fica quadriculado (elipses) em vez de pontos"
- Falta `closePath()` após cada `arc()` no batch path

### "Banner não atualiza quando mudo controle"
1. Verificar se o ID do controle está no array de wire-up (fim do script)
2. Verificar se já clicou em "Gerar banner" pelo menos uma vez (auto-render é ativado depois disso)
3. Para inputs de texto: deve usar `bnAutoD` (debounced), não `bnAuto` direto

### "Canvas font não aplica"
- Remover `;` do fim da font string
- Garantir que a fonte está carregada (Montserrat usa `@font-face` local)

---

## 🚫 O que NÃO fazer

- ❌ Adicionar frameworks (React, Vue, etc.) — o projeto é intencionalmente zero-dep
- ❌ Adicionar build step — deve continuar sendo `index.html` direto
- ❌ Enviar a foto do usuário pra servidor — processamento é local, sempre
- ❌ Adicionar tracking, analytics, cookies
- ❌ Usar syntax ES2020+ sem testar em browsers antigos (o app é ES5-friendly hoje)
- ❌ Quebrar a compatibilidade das funções existentes sem atualizar todos os call sites
- ❌ Mudar as cores oficiais `#EAFD7C` / `#C299EE` sem concordância do mantenedor

---

## 📚 Referências

- [Canvas API — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- [Halftone basics](https://en.wikipedia.org/wiki/Halftone) — a técnica original que inspirou o visual
- [THE/GARAGE — Superteam Brasil](https://x.com/SuperteamBR)
- [Colosseum Hackathon](https://www.colosseum.org/)

---

## 💬 Pedindo ajuda ao Claude

Se você for um assistente IA lendo isso, dicas pra ser útil:

1. **Leia este arquivo inteiro antes de sugerir mudanças grandes.** As landmines documentadas aqui vão te economizar horas.
2. **Teste mentalmente nos 3 formatos de banner** antes de dizer "pronto" — muita coisa funciona no quadrado e quebra no paisagem.
3. **Respeite o single-file constraint.** Não sugira "vamos separar em módulos" — isso quebra o modelo mental de deploy.
4. **Proponha mudanças cirúrgicas.** Refatorar grandes blocos aumenta risco de reintroduzir bugs já corrigidos.
5. **Valide com `new Function(scriptBody)`** ao menos pra pegar erros de sintaxe antes de responder.

Se algo contradiz este documento, o documento está desatualizado — pergunte antes de agir.

---

<sub>Criado como parte da iteração inicial do projeto, para facilitar manutenção e expansão. Atualize este arquivo quando adicionar features ou descobrir novas landmines.</sub>
