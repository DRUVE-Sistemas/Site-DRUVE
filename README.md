# Site DRUVE

Site estático (HTML puro + runtime `support.js`). Sem build, sem npm — o que está
no repositório é exatamente o que vai pro ar.

## Estrutura

```
index.html            página principal (hero 3D + seções + contato)
igenda.html           página de detalhe do iGenda
mesaflow.html         página de detalhe do MesaFlow
iporte.html           página de detalhe do IPorte
design-system.html    documentação visual da marca
support.js            runtime que interpreta os arquivos (não editar — é gerado)
assets/               mídia usada pelo site
  logo-druve.jpg      textura do logo no notebook 3D
  druve-hero.mp4      vídeo que roda na tela do notebook
design-system/
  tokens/*.css        cores, tipografia, espaçamento, efeitos
  assets/             wordmark SVG, lockup, referências visuais
  readme.md           guia da marca
uploads/              originais do editor (ignorado pelo git — só cópias)
```

### De onde veio cada asset

Tudo em `uploads/` é o arquivo original que o editor salvou. Os que o site usa já
foram copiados com nome limpo — o `uploads/` fica só como backup local:

| original em `uploads/` | usado como | onde |
|---|---|---|
| `DRUVE Animação (Estúdio horizontal).mp4` | `assets/druve-hero.mp4` | tela do notebook 3D |
| `DRUVE-logo (upscaled).jpg` | `assets/logo-druve.jpg` | textura do logo no 3D |
| `DRUVE-logo (upscaled).jpg` | `design-system/assets/druve-lockup.jpg` | `og:image` + guia da marca |
| `druve-logo-fiel.svg` | `design-system/assets/druve-wordmark.svg` | logo do header (todas as páginas) |
| `Assinatura DRUVE (pedro).jpg` | `design-system/assets/assinatura-email.jpg` | assinatura de e-mail (design system) |
| `WhatsApp ... 12.15.53.jpeg` | `design-system/assets/ref-metal-blue-marble.jpeg` | referência visual (design system) |
| `WhatsApp ... 12.17.12.jpeg` | `design-system/assets/ref-pantone-mood.jpeg` | referência visual (design system) |
| `bce46fce...jpg` | `design-system/assets/ref-silver-palette.jpg` | referência visual (design system) |

## Como editar

Cada página tem duas partes:

1. **`<x-dc> ... </x-dc>`** — o conteúdo. HTML normal com estilos inline.
   Textos, títulos, preços, links e seções ficam aqui. É o que você mexe 95% do tempo.
2. **`<script type="text/x-dc" data-dc-script>`** (no fim do arquivo) — a lógica:
   cena 3D em Three.js, animação de scroll, parallax.

### Trocar links e textos rápidos

No atributo `data-props` do script (fim do `index.html`) ficam os valores centrais:

| prop | o que é |
|---|---|
| `accentColor` | azul da marca (`#2F62C4`) |
| `igendaUrl` / `mesaflowUrl` / `iporteUrl` | link "acessar o sistema" |
| `igendaDetailUrl` / `mesaflowDetailUrl` / `iporteDetailUrl` | páginas de detalhe |
| `notebookFinish` | acabamento do notebook 3D: `grafite`, `prata`, `preto` |
| `motionAmount` | intensidade da animação (0.4 a 1.6) |
| `pointerParallax` | parallax do mouse liga/desliga |

Dentro do HTML esses valores aparecem como `{{ props.igendaUrl }}`.

### Rodar local

```bash
python -m http.server 8000
```

Depois abrir http://localhost:8000. **Não abra por `file://`** — o runtime busca a
própria página via `fetch` e o navegador bloqueia em `file://`.

## Publicar no GitHub Pages

```bash
git init && git add . && git commit -m "site druve"
```

```bash
git remote add origin git@github.com:USUARIO/REPO.git && git push -u origin main
```

No GitHub: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.

O `.nojekyll` já está no repo (impede o Jekyll de comer arquivos). Os nomes de
arquivo são todos minúsculos e sem acento/espaço, que é o que o Pages espera.

### Domínio próprio

As tags canônicas apontam para `https://druve.com.br/`. Para usar o domínio:
criar um arquivo `CNAME` na raiz com `druve.com.br` dentro e apontar o DNS
(`A` para os IPs do GitHub Pages, ou `CNAME` do `www` para `USUARIO.github.io`).

## Dependências externas (carregadas em runtime, via CDN)

- React 18 + Babel standalone — `unpkg.com` (o `support.js` puxa sozinho)
- Three.js 0.184 — `unpkg.com`, declarado no `importmap` do `<head>`
- Fontes Space Grotesk + JetBrains Mono — Google Fonts

Sem internet, a página não renderiza.
