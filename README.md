# AR Try-On de Parede — protótipo

Try-on em AR para projetar quadros/posters na parede, com tracking de
superfície vertical e escala real, via **WebXR**. Funciona em **Android + Chrome**.

## Estrutura dos arquivos

Coloque os 4 arquivos **todos juntos na raiz** do repositório, sem subpastas:

    ar-tryon/
    ├── index.html      <- página com o QR code (gerador embutido, sem CDN)
    ├── ar.html         <- a experiência de AR (WebXR)
    ├── poster.glb      <- seu modelo 3D (56 × 86 cm)
    └── vercel.json     <- faz o Vercel servir o .glb corretamente

O `ar.html` procura o modelo em `./poster.glb` (mesma pasta). Se mover o
GLB para uma subpasta, atualize `MODEL_URL` no topo do `ar.html`.

## Correção: QR travado em "gerando QR…"

A versão anterior do `index.html` carregava a biblioteca de QR de um CDN
externo (jsdelivr). Quando esse CDN é bloqueado ou falha, o script nunca
carrega e a página fica presa em "gerando QR…" sem erro visível.

Corrigido: o `index.html` agora tem um **gerador de QR embutido** no
próprio arquivo (algoritmo de domínio público). Não depende de nenhum CDN
para o QR — funciona mesmo offline. Se algo falhar, agora aparece uma
mensagem de erro em vez de travar silenciosamente.

O QR gerado foi testado e decodifica corretamente para a URL do `ar.html`.

## Deploy com GitHub Desktop + Vercel

1. No GitHub Desktop: **File > New Repository**, escolha uma pasta.
2. Copie os 4 arquivos para dentro dessa pasta (na raiz).
3. **Commit** e **Publish repository**.
4. Em vercel.com: **Add New > Project**, importe o repositório.
5. Framework Preset: **Other**. Sem build. Clique **Deploy**.
6. O Vercel dá uma URL `https://...vercel.app` — já é HTTPS.

Importante: depois de substituir os arquivos, faça um novo **commit** no
GitHub Desktop e o Vercel redeploy automático. Ao abrir a URL no celular,
use **recarregar forçado** ou aba anônima para não pegar o index.html
antigo do cache.

## Como testar

1. No Android, abra a câmera e aponte para o QR (ou abra a URL no Chrome).
2. Toque para abrir no **Google Chrome** (não funciona em outro navegador).
3. Toque em "Iniciar câmera" e permita o acesso à câmera.
4. Aponte para a **parede** e mova o celular devagar.
5. Um contorno verde do tamanho real (56 × 86 cm) gruda na parede.
6. Toque na tela para fixar. Toque de novo em outro ponto para reposicionar.

## Configuração do modelo (já ajustada)

No topo do `ar.html`, o bloco CONFIG já está certo para o seu GLB:

    MODEL_URL       = './poster.glb'
    MODEL_SCALE     = 1.0      // GLB já veio em metros (Blender exporta em m)
    MODEL_REAL_SIZE = null     // usa o tamanho nativo: 0.56 × 0.86 m
    MODEL_AUTOCENTER = true

O Blender exporta glTF sempre em metros, mesmo com a Unit da cena em cm.
O bbox medido do seu GLB é 0.56 × 0.86 m — por isso `MODEL_SCALE = 1.0`,
e NÃO 0.01.

## Dependência de CDN restante (three.js)

O `ar.html` ainda carrega o **three.js** do CDN `unpkg.com`. Isso é
intencional: three.js é grande demais para embutir, e o unpkg é mais
estável que o CDN de QR. Se o unpkg também falhar no seu ambiente,
baixe os arquivos do three.js e sirva-os localmente — me avise que eu
ajusto o `ar.html` para apontar para cópias locais.

## Painel de debug (LOG)

Dentro do AR há um botão **LOG** no canto superior esquerdo. Toque nele
para abrir o painel de diagnóstico. Ele mostra em tempo real:

- Se o WebXR e o hit-test inicializaram
- Quantas superfícies o ARCore detecta por frame
- A normal de cada superfície e se passou no filtro de parede
- Cada transição de estado (achou parede / perdeu / não-parede)

Para reportar um problema de tracking: abra o AR, aponte para a parede e
mexa o celular por uns 15-20 segundos, depois toque em **Copiar log** e
cole o conteúdo no chat. Com esse log dá para ver exatamente se o ARCore
não está detectando nada, ou se está detectando mas o filtro de parede
está rejeitando.

## Ajuste de tracking (se necessário)

- `VERTICAL_DOT_MIN` (0.82): se recusar paredes válidas, baixe p/ ~0.70.
  Se aceitar o chão, suba p/ ~0.90.
- `WALL_OFFSET` (0.012 m): distância do quadro para fora da parede.

Para depurar no Android: cabo USB + `chrome://inspect` no PC mostra o
console do `ar.html`.

## Limitações conhecidas

- **iPhone não funciona**: Safari não suporta WebXR AR.
- Paredes lisas e sem textura dão menos pontos ao ARCore — por isso o
  fluxo é "mira + toque".
- A textura do GLB é um pôster licenciado (Star Wars). Ok para teste
  interno; troque por arte licenciada ao cliente antes de uso público.
