# DRUVE — Design System

Sistema visual da **DRUVE — Desenvolvimento de Software** (Contagem/MG, druve.com.br).
Direção: **azul-noite + metálico prata**, sóbrio e minimalista.

Fontes deste sistema (materiais entregues pelo usuário):
- `assets/druve-wordmark.svg` — **wordmark vetorial oficial** (viewBox 1600×328,
  `fill: currentColor`). Fonte da verdade para a marca. Aplique o metal por
  máscara CSS sobre um gradiente, nunca recolorindo o path.
- `assets/druve-lockup.jpg`, `assets/logo-druve.jpg` — lockup raster (marca +
  tagline) para onde SVG não serve.
- `assets/assinatura-email.jpg` — assinatura de e-mail do diretor executivo: a peça
  de marca mais completa que existe (hierarquia, tracking, ícones em capsula, faixa
  metálica escovada no rodapé).
- `assets/ref-metal-blue-marble.jpeg`, `assets/ref-pantone-mood.jpeg`,
  `assets/ref-silver-palette.jpg` — referências de moodboard (Pantone 19-3940 Blue
  Depths, Pantone 20-0002 Ice Palace, rampa de prata).
- Projeto de origem: site institucional com notebook 3D (`DRUVE Sistemas.dc.html`).

Produtos representados: **iGenda** (agendamento + PDV), **MesaFlow** (food service),
**IPorte** (financeiro, web + app). São produtos independentes entre si — a marca
DRUVE é o guarda-chuva, e no novo sistema **nenhum produto tem cor própria**: a
diferenciação é feita por numeração, tipografia e ordem, não por matiz.

---

## VISUAL FOUNDATIONS

**Cor.** Duas famílias, ponto. Azul-noite (`--ink-950` a `--navy-300`) para tudo que
é fundo ou superfície; prata (`--steel-050` a `--steel-800`) para tudo que é texto,
hairline ou metal. O azul royal (`--blue-500/400`) é **acento**, não cor de fundo:
foco, link, estado ativo, um número em destaque. Nunca mais de ~5% da tela.
Zero roxo, laranja, ciano, verde-menta. Gradiente colorido de fundo é proibido — o
único "gradiente" permitido é metal (prata) ou o `--marble-blue` mascarado a baixa
opacidade em fundos de seção grandes.

**Metal.** Metal não é uma cor, é uma receita: gradiente prata em ângulo (~168°) +
hairline de 1px + realce interno no topo (`--bevel-metal`) + sombra curta. Aplicado
em: botão primário, faixas divisórias, chips de dado. Superfície escura elevada usa
`--metal-dark` (branco a 7% no topo, preto no pé) — parece aço anodizado, não vidro
colorido.

**Tipografia.** Space Grotesk em 300/400/500/600 — nunca 700+ (a marca é sóbria).
Display sempre com tracking negativo (-0.02em a -0.04em). O motivo mais forte da
marca é o **label mono em caixa alta com tracking 0.22em** (`--font-mono`,
`--text-label`): vem direto de `< DESENVOLVIMENTO DE SOFTWARE />` e de
`D I R E T O R  E X E C U T I V O`. Use-o em eyebrows, contadores, metadados e
rótulos de eixo. Corpo de texto em `--steel-400`, nunca branco puro.

**Espaço e layout.** Grade de 4px, container 1240px, gutter fluido. Respiro generoso:
seções com `--section-y` (72–140px). Minimalismo aqui significa **menos elementos e
mais ar**, não densidade.

**Cantos.** 2/4/6/10px. Cards em 6px, painéis grandes em 10px. Pílula só em badge e
em botão de ação comprimido. Nada de blob arredondado.

**Bordas e divisores.** Hairline de 1px em `rgba(255,255,255,0.09)` como padrão;
`--line-strong` em elemento interativo; `--metal-edge` (gradiente que morre à
direita) para divisores de destaque. Régua fina horizontal separando bloco de
identidade de bloco de contato — motivo tirado da assinatura.

**Fundos.** Página em `--ink-950` quase preto. Sem imagem full-bleed decorativa. A
única imagem de fundo aceitável é o mármore azul-prata em opacidade ≤ 18% com
máscara radial, ou a faixa metálica escovada como remate inferior de seção (motivo
da assinatura). Ruído/grão: opcional, ≤ 3%.

**Transparência e blur.** Só em elemento fixo sobre conteúdo em rolagem (header,
painel flutuante): `background: rgba(7,10,20,0.6)` + `--blur-glass`. Card estático
não usa blur.

**Sombras.** Curtas e escuras, sem cor: `--shadow-1/2/3`. Metal usa
`--shadow-metal`. Nunca sombra colorida ou glow.

**Movimento.** Mecânico e breve. `--dur-fast` para hover, `--dur-base` para
entrada/saída, `--dur-slow` para revelações de seção. `--ease-out` padrão; sem
bounce, sem elástico. Revelação: opacidade 0→1 + translateY(10px→0). A animação do
notebook 3D é a única peça expressiva do site e permanece como está.

**Hover.** Superfície clareia (`--bg-surface` → `--bg-surface-hover`), hairline sobe
para `--line-strong`, texto secundário vira primário. Botão metálico clareia o
gradiente ~6%. Nada de escala em hover além de 1.01 em card clicável.

**Press.** `transform: translateY(1px)` + sombra reduzida. Sem mudança de matiz.

**Foco.** `outline: 1px solid var(--focus-ring); outline-offset: 2px`. Sempre visível.

**Imagens.** Frias, alto contraste, quase monocromáticas — azul/prata. Sem foto
calorosa, sem gente sorrindo em escritório. Se não houver imagem real, deixe o
espaço vazio ou use placeholder declarado.

---

## CONTENT FUNDAMENTALS

- **Português do Brasil, tratamento direto na 2ª pessoa** ("seu financeiro",
  "você fecha o mês"), sem "nós entregamos soluções".
- **Frases curtas, concretas, sem jargão de agência.** Diga o que o software faz e
  para quem. Bom: "Agenda cheia e o caixa fechado no fim do dia." Ruim:
  "Soluções inovadoras para potencializar seu negócio."
- **Números sem inflar.** Se não há métrica real, não invente — descreva a função.
- **Caixa.** Títulos em caixa de frase. Caixa alta só em label mono curto
  (1–3 palavras). Nunca caixa alta em frase inteira.
- **Sem emoji.** Sem exclamação. Sem "!" em CTA.
- **CTA em verbo direto:** "Acessar o sistema", "Ver detalhes", "Falar com a gente".
- **Tom:** técnico, calmo, confiante. A empresa é pequena e faz software sob medida —
  soa como engenheiro sério, não como startup em rodada de captação.

---

## ICONOGRAPHY

Os materiais entregues **não contêm um set de ícones** — a assinatura usa três
glifos (telefone, pin de mapa, globo) desenhados em traço fino dentro de cápsula
quadrada de canto arredondado, em prata sobre azul-noite.

Decisão: usar **Lucide** via CDN (`https://unpkg.com/lucide-static`), traço 1.5px,
tamanho 16/20/24, cor `--steel-400`. É o match mais próximo do traço fino da
assinatura. **Substituição sinalizada — se existir um set oficial da DRUVE, envie.**

Padrão de uso: ícone dentro de cápsula 28×28 com `--radius-sm`, hairline 1px e
fundo `--bg-surface` (replica a assinatura). Nunca ícone colorido, nunca emoji,
nunca ícone preenchido. Setas: `→` unicode é aceito em CTA inline.

Marca: use `assets/druve-wordmark.svg` com máscara CSS. **Não redesenhar o
lettering.** A tagline `< DESENVOLVIMENTO DE SOFTWARE />` é composta em
`--font-mono` a `--text-micro` com tracking 0.24em, entre `<` e `/>` em
`--steel-700`.

---

## Adições intencionais

Não havia inventário de componentes nos materiais (só marca, assinatura e
moodboard), então os primitivos abaixo foram autorados a partir das fundações:
Button, Badge, Card, MetalRule, Label, Input, StatRow.

---

## Índice

- `styles.css` — entrada única de CSS (só `@import`).
- `tokens/` — `typography.css`, `colors.css`, `space.css`, `effects.css`.
- `assets/` — marca, assinatura e referências de moodboard.
- `SKILL.md` — invólucro para uso como Agent Skill.
- `../DRUVE Design System.dc.html` — guia visual navegável (cores, tipo,
  componentes, motivos, movimento).

## Pendências para o usuário

1. **Fonte real do lettering** — Space Grotesk é aproximação do wordmark.
2. **Set de ícones oficial**, se houver.
3. Fotos/imagens reais dos três sistemas (hoje o site usa notebook 3D + mocks).
