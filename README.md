# AR Try-On de Parede — protótipo

Try-on em AR para projetar quadros/posters na parede, com tracking de
superfície vertical e escala real, via **WebXR**. Funciona em **Android + Chrome**.

## Estrutura dos arquivos

Os 4 arquivos vão **todos juntos na raiz** do repositório, sem subpastas:

    ar-tryon/
    ├── index.html      <- página com o QR code (gerador embutido, sem CDN)
    ├── ar.html         <- a experiência de AR (WebXR)
    ├── poster.glb      <- modelo 3D do pôster
    └── vercel.json     <- faz o Vercel servir o .glb corretamente

## Novidades desta versão

- **Textos em inglês** em toda a interface.
- **3 botões de tamanho** no estilo do site AllPosters, no canto inferior
  da câmera (acima do botão de fechar, sem sobrepor):
  - 13" × 19"  (≈ 33,0 × 48,3 cm)
  - 15" × 22"  (≈ 38,1 × 55,9 cm)
  - 22" × 34"  (≈ 55,9 × 86,4 cm) — padrão, igual ao GLB atual
- O botão selecionado fica com fundo **azul** (o mesmo tom do print do site);
  os demais ficam brancos.
- Ao trocar de tamanho, o quadro **permanece no mesmo ponto da parede** e
  escala a partir do centro (o ponto onde foi fixado não se move).

## Como o redimensionamento funciona (sem precisar de 3 GLBs)

Os 3 tamanhos usam **o mesmo GLB**. O código mede o tamanho nativo do
modelo e aplica um fator de escala para cada medida em polegadas. A pose
na parede (posição + rotação) é guardada separada da escala, então mudar
de tamanho só reescala o quadro — o centro fica cravado no lugar.

Se algum dia quiser tamanhos com arte diferente por proporção, dá para
trocar por GLBs separados, mas para os 3 tamanhos atuais não é necessário.

Para editar os tamanhos, veja a tabela `SIZES` no topo do `ar.html`.

## Deploy com GitHub Desktop + Vercel

1. GitHub Desktop: **New Repository**, escolha uma pasta.
2. Copie os 4 arquivos para a raiz dessa pasta.
3. **Commit** e **Publish repository**.
4. vercel.com: **Add New > Project**, importe o repositório.
5. Framework Preset: **Other**. Sem build. **Deploy**.
6. URL `https://...vercel.app` — já é HTTPS.

Ao trocar arquivos: novo **commit** no GitHub Desktop, o Vercel redeploya.
No celular, use recarregar forçado ou aba anônima para evitar cache.

## Painel de debug (LOG)

Botão **LOG** no canto superior esquerdo dentro do AR. Mostra em tempo
real: inicialização do WebXR/hit-test, superfícies detectadas por frame,
a normal de cada uma e se passou no filtro de parede, e cada transição
de estado. Toque em **Copy log** para copiar e colar no chat.

## Correções desta versão (análise do log)

**Pôster voando à frente da parede** — corrigido. Havia dois erros: o
deslocamento da parede (WALL_OFFSET) era aplicado duas vezes (no reticle
e no quadro), e a escala dos tamanhos era multiplicada na matriz de pose,
amplificando deslocamentos internos do modelo. Agora a escala é aplicada
via posterModel.scale (propriedade do three.js) e o offset é aplicado uma
única vez, como distância fixa de 1,5 cm ao longo da normal da parede.

**Tracking instável / pisca** — melhorado dentro do possível:

- Suavização: mantém a última pose válida por 600 ms após uma falha
  momentânea e interpola suave, então o reticle desliza em vez de piscar.
- Âncoras WebXR: ao fixar o quadro, é criada uma XRAnchor. O ARCore passa
  a gerenciar esse ponto e o reposiciona conforme refina o mapa do
  ambiente. O painel de LOG mostra "anchor created" quando funciona.
- Anti-salto: se a âncora dá um pulo maior que 25 cm num único frame
  (o ARCore às vezes "glitcha" em ambientes com muitos objetos), o
  código rejeita esse frame e segura a última pose boa.

**Corrigido — quadro pulava para frente.** O offset da parede estava
sendo re-somado a cada frame (o loop ressincronizava a pose com a âncora
e somava o offset de novo), acumulando ~1 cm por frame. Agora o offset é
embutido UMA vez, na criação da âncora; o loop apenas copia a pose. O
quadro fica estável a 1 cm da parede.

**Nota sobre detecção:** uma tentativa anterior de filtrar o hit-test só
por planos (entityTypes:plane) deixou a detecção MUITO mais lenta (~36 s
contra ~13 s) em parede lisa. Revertido — o hit-test usa pontos de
característica + planos, que aparecem bem antes.

Limitação honesta: a dificuldade de achar parede à noite / em parede lisa
é física do ARCore (precisa de contraste visual). O código suaviza o
resultado, mas não cria pontos de tracking onde não há. Filmar com boa
luz e ter algum detalhe na parede (quadro, móvel) continua ajudando.

## Ajuste de tracking (se necessário)

- `VERTICAL_DOT_MIN` (0.82): se recusar paredes válidas, baixe p/ ~0.70.
  Se aceitar o chão, suba p/ ~0.90.
- `WALL_OFFSET` (0.012 m): distância do quadro para fora da parede.

A detecção ocasionalmente falha em paredes muito lisas/uniformes — é
limitação do ARCore (poucos pontos de textura). Mover o celular devagar
e ter algum detalhe na parede ajuda.

## Limitações conhecidas

- **iPhone não funciona**: Safari não suporta WebXR AR.
- O `ar.html` carrega o three.js do CDN unpkg.com. Se falhar, é possível
  servir uma cópia local — peça o ajuste.
- A textura do GLB é um pôster licenciado (Star Wars). Ok para teste e
  pitch interno; troque por arte licenciada ao cliente antes de uso público.
