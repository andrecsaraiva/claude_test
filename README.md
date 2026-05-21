# AR Try-On de Parede — protótipo

Try-on em AR para projetar quadros/posters na parede, com tracking de
superfície vertical e escala real, via **WebXR**. Funciona em **Android + Chrome**.

## Estrutura dos arquivos

Coloque os 4 arquivos **todos juntos na raiz** do repositório, sem subpastas:

    ar-tryon/
    ├── index.html      <- página com o QR code
    ├── ar.html         <- a experiência de AR (WebXR)
    ├── poster.glb      <- seu modelo 3D (56 × 86 cm)
    └── vercel.json     <- faz o Vercel servir o .glb corretamente

Não crie pasta para o GLB. O `ar.html` procura o modelo em `./poster.glb`
(mesma pasta). Se você mover o GLB para uma subpasta, atualize a constante
`MODEL_URL` no topo do `ar.html` (ex.: `'./assets/poster.glb'`).

## Deploy com GitHub Desktop + Vercel

1. No GitHub Desktop: **File > New Repository**, escolha uma pasta.
2. Copie os 4 arquivos para dentro dessa pasta (na raiz).
3. **Commit** ("primeira versão") e **Publish repository**.
4. Em vercel.com: **Add New > Project**, importe esse repositório.
5. Framework Preset: **Other**. Não precisa de build. Clique **Deploy**.
6. O Vercel te dá uma URL `https://...vercel.app` — já é HTTPS, pronto.

Abra essa URL no desktop: o `index.html` mostra o QR code. O QR aponta
sozinho para o `ar.html` no mesmo domínio — não precisa editar nada.

## Como testar

1. No Android, abra a câmera e aponte para o QR (ou abra a URL no Chrome).
2. Toque para abrir no **Google Chrome** (não funciona em outro navegador).
3. Toque em "Iniciar câmera" e permita o acesso à câmera.
4. Aponte para a **parede** e mova o celular devagar.
5. Um contorno verde do tamanho real do quadro aparece grudado na parede.
6. Toque na tela para fixar. Toque de novo em outro ponto para reposicionar.

## Configuração do modelo (já ajustada)

No topo do `ar.html`, o bloco CONFIG já está configurado para o seu GLB:

    MODEL_URL       = './poster.glb'
    MODEL_SCALE     = 1.0      // GLB já veio em metros (Blender exporta em m)
    MODEL_REAL_SIZE = null     // usa o tamanho nativo: 0.56 × 0.86 m
    MODEL_AUTOCENTER = true

Observação: mesmo com a Unit do Blender em cm, o exportador glTF grava
sempre em metros. Seu GLB foi medido em 0.56 × 0.86 m — por isso a escala
correta é 1.0, e NÃO 0.01. Se quiser travar uma medida exata diferente,
use `MODEL_REAL_SIZE = { width: 0.56, height: 0.86 }` (valores em metros).

## Ajuste de tracking (se necessário)

Teste no aparelho primeiro. Se precisar afinar, no `ar.html`:

- `VERTICAL_DOT_MIN` (0.82): se ele recusar paredes válidas, baixe p/ ~0.70.
  Se aceitar o chão por engano, suba p/ ~0.90.
- `WALL_OFFSET` (0.012 m): distância do quadro para fora da parede.

Para depurar no Android, conecte o cabo USB e abra `chrome://inspect` no
PC — o `console.log` do `ar.html` imprime as dimensões detectadas do GLB.

## Limitações conhecidas

- **iPhone não funciona**: o Safari não suporta WebXR AR. Fase iOS exigirá
  um caminho separado (USDZ + Quick Look).
- Paredes lisas e sem textura dão menos pontos de tracking ao ARCore —
  por isso o fluxo é "mira + toque", mais estável que detecção automática.
- A textura atual do GLB é um pôster licenciado (Star Wars). Ok para teste
  interno; troque por arte licenciada ao cliente antes de qualquer uso público.
