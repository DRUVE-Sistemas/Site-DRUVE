# Performance do hero 3D

O notebook da capa é uma cena Three.js construída em runtime (geometria
procedural, sem `.glb`), com 5 telas pintadas em canvas 2D e uma textura de
vídeo. Ele travava em celular e em PC sem aceleração gráfica. Este documento
registra o que foi medido, o que foi cortado e o que ficou de fora de propósito.

Tudo vive em `index.html`, no `<script type="text/x-dc">` do fim do arquivo.

## O diagnóstico

Em ordem de custo, do pior para o menor:

| # | Problema | Onde |
|---|---|---|
| 1 | `preserveDrawingBuffer: true` — força cópia do framebuffer todo frame; nada no site lia o canvas | criação do renderer |
| 2 | `pixelRatio` até 2 — celular tem dpr 3, então renderizava 2× a tela cheia. Custo de fragmento escala ao **quadrado** | criação do renderer |
| 3 | 5 canvas de 1536×960 (~28 MB de VRAM) + vídeo + PMREM — o que mata a aba em celular barato | `makeScreenCanvas` |
| 4 | 8 materiais `MeshPhysicalMaterial` com `clearcoat`/`anisotropy` + 8 luzes — shader de fragmento caro por pixel | bloco `mats` |
| 5 | 78 teclas, cada uma com `ExtrudeGeometry` + `smoothNormals` no boot — é o que congela a tela no carregamento | montagem do teclado |
| 6 | Loop renderizava sempre, inclusive com o canvas em `opacity: 0` e com a aba escondida | `loop()` |
| 7 | 23 `backdrop-filter` e 26 `filter: blur()` na página, dois deles em círculos de 48vw animados em loop infinito. **Sem GPU isso custa mais que o 3D inteiro** | estilos inline do HTML |

## Os três níveis

A detecção acontece uma vez, na criação do renderer.

```
memória apertada  →  coarse pointer  ou  hardwareConcurrency <= 4  ou  deviceMemory <= 4
sem GPU           →  UNMASKED_RENDERER_WEBGL casa com swiftshader|llvmpipe|basic render|paravirtual
```

São problemas **diferentes** e não devem compartilhar o mesmo interruptor: o
celular não tem VRAM para as texturas em tamanho cheio; o PC sem aceleração tem
VRAM de sobra, o que falta nele é taxa de preenchimento. Misturar os dois deixa
a tela do notebook borrada no PC sem necessidade.

| | GPU ativa | Sem GPU (PC) | Celular / pouca memória |
|---|---|---|---|
| `pixelRatio` | até 1.75 | 0.75 | 1.25 |
| MSAA | só se dpr < 2 | só se dpr < 2 | só se dpr < 2 (celular quase sempre fica sem) |
| FPS | livre | 30 | 30 |
| Telas do notebook | 1536×960 | 1536×960 | 768×480 |
| Serigrafia das teclas | 2048 | 2048 | 1024 |
| Tela do celular 3D | 540×1170 | 540×1170 | 270×585 |
| Vídeo do hero | toca | toca | toca |
| Blur CSS da página | ligado | **desligado** | ligado |
| Luzes | 8 | 4 | 4 |
| Materiais `Physical` | 6 | 0 (viram `Standard`) | 0 |
| `toneMappingExposure` | 1.18 | 1.32 | 1.32 |

A exposição maior compensa as luzes que o tier baixo não acende: sem ela a cena
ficava 17% mais escura; com ela, 4,7% — imperceptível lado a lado.

## Otimizações que valem para todos os níveis

- **`preserveDrawingBuffer: false`.** Se algum dia alguém precisar ler o canvas
  (`toDataURL`, captura de thumbnail), tem que ler **no mesmo frame** do
  `render()`, ou religar a flag.
- **Cache de geometria das teclas.** 78 teclas usam só ~16 tamanhos distintos:
  `ExtrudeGeometry` + `smoothNormals` caem de 78 para 16 no boot.
- **Render só quando visível.** `if (c.dim >= 0.02)` antes do `render()` — no
  fim da sequência o canvas dissolve no fundo e a GPU para.
- **Compensação de suavização a 30fps.** Menos frames = menos convergência por
  segundo, então o fator de interpolação sobe 1,7× quando há teto de fps
  (limitado a 0.95 para não ultrapassar o alvo).

## A escada de degradação

Quando a detecção estática não basta — GPU velha, driver ruim, navegador que
mascara a string do renderer (o Firefox mascara) — quem decide é a medição.

O watchdog mede o fps entregue a cada 1s e desce **um** degrau se ficar abaixo
de 62% do alvo. Só desce, nunca sobe: não oscila e não precisa adivinhar
hardware.

| Degrau | Ação |
|---|---|
| 0 | `pixelRatio` ≤ 1 |
| 1 | `pixelRatio` 0.75 + 30fps |
| 2 | `pixelRatio` 0.55 + 24fps |
| 3 | mata blur / backdrop-filter / animação CSS (classe `druve-sem-gpu`) |
| 4 | descarta o vídeo do hero (a tela cai no canvas pintado) |

Detectou modo software? Entra direto no degrau 1 **e** aplica o 3 (o blur é
ganho grande com custo visual pequeno), mas mantém o vídeo. O resto só acontece
se a medição cobrar.

Materiais e luzes ficam de fora da escada de propósito: trocá-los em runtime
recompila shader, que é exatamente o que trava.

**Janela longa demais é descartada.** Trocar de aba suspende o `rAF`; sem esse
cuidado o watchdog leria a pausa como lentidão e degradaria o site à toa.

## Vídeo do hero

Reinicia do começo ao voltar para a capa, **mas só depois de ter trocado de
seção** — rolar dentro do próprio hero não reinicia nada.

Não precisou de estado novo: o bloco que troca a textura da tela já roda
somente quando o índice muda, ou seja, quando a seção mudou de fato. A função
`restart()` do watchdog do vídeo (que já fazia `currentTime = 0` + play com
trava de 400ms contra rajada de seek) virou `this.restartVideo` e passou a ser
usada nos dois lugares.

Percurso medido:

| Etapa | `currentTime` | Estado |
|---|---|---|
| No hero | 2,59s | tocando |
| Rolou **dentro** do hero | 3,10s | tocando, não reiniciou |
| Foi para a seção 1 | 3,10s | pausado |
| **Voltou** ao hero | 0,61s | tocando, reiniciou |

Sair para a seção 5 (última, que também usa tela pintada) e voltar conta como
troca de seção e reinicia igual.

## Testar

```bash
python -m http.server 8000
```

| URL | Simula |
|---|---|
| `http://localhost:8000` | máquina normal |
| `http://localhost:8000/?low=1` | celular / pouca memória |
| `http://localhost:8000/?soft=1` | PC sem aceleração gráfica |

No console, o estado atual da cena:

```js
__druve.gpuName + ' | step ' + __druve.step + ' | ' + __druve.fps + 'fps | dpr ' + __druve.renderer.getPixelRatio()
```

`step` `undefined` ou `0` = nenhuma degradação aconteceu. `2` ou mais = o
watchdog (ou a detecção de software) agiu.

Para inspecionar sem depender do scroll, `window.__druvePreview` fixa a posição
na sequência (`0` = hero, `1` = seção 1, …); `null` devolve o controle ao
scroll.

## O que ficou de fora, e quando fazer

- **Instancing das teclas.** Continuam 78 draw calls. Juntar em ~16
  `InstancedMesh` custaria o nome individual de cada tecla, que o builder DC
  lista na árvore de camadas. Draw call de mesh pequeno é barato perto do que já
  foi cortado — só vale se a medição em aparelho real cobrar.
- **Trocar o 3D por vídeo scrubbado**, como o site do GTA VI. Lá é uma
  cinemática linear; aqui a cena tem parallax de mouse, clique que mergulha na
  tela para navegar, vídeo real rodando dentro do notebook e seções HTML
  sincronizadas. Virar vídeo mata tudo isso, exige um render por proporção de
  tela, e scrub de vídeo em Safari mobile trava do mesmo jeito. Só faz sentido
  como último recurso, e só no tier mais baixo.
- **Environment PMREM em resolução menor.** Ainda há folga ali se precisar de
  mais um degrau antes de mexer em resolução.
