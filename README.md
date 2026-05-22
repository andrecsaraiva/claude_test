# AR Try-On de Parede — protótipo

## Estrutura (todos os arquivos juntos na raiz)

    index.html         <- página de produto (réplica AllPosters) + viewer 3D + QR
    ar.html            <- experiência de AR (WebXR) — base da Versão 1
    poster.glb         <- modelo 3D do pôster
    studio.hdr         <- HDRI para iluminação do viewer 3D
    poster_image.jpg   <- imagem do pôster (thumb 1 / principal)
    poster_thumb.jpg   <- miniatura do pôster
    room1/2/3.jpg      <- imagens do pôster em ambientes (thumbs 2-4)
    room1/2/3_thumb.jpg<- miniaturas dessas imagens
    vercel.json        <- MIME types para .glb e .hdr

## ar.html — reconstruído sobre a Versão 1

O ar.html agora é a **Versão 1** (a que funcionava: posiciona sempre na
parede, nunca voa, fica firme) com APENAS os botões de tamanho adicionados.

As funções de tracking (poseOnWall, surfaceNormalFromPose, isWall) são
IDÊNTICAS à V1 — não foi tocada nenhuma linha da lógica de posicionamento.

Como os botões de tamanho funcionam sem afetar o tracking:
- O quadro fixado é um wrapper com a matriz de pose CONGELADA (lógica V1).
- O modelo 3D fica DENTRO desse wrapper, como filho.
- Os botões de tamanho escalam só o filho (X e Y, a partir do centro).
- A matriz congelada do wrapper NUNCA é alterada → o quadro não pode
  voar nem pular ao trocar de tamanho. É estruturalmente impossível.

Também: guard de toque na UI (tocar num botão não dispara mais o
posicionamento do AR) e painel de LOG escondido.

## index.html — página de produto

Réplica da parte superior da página do AllPosters. Quase tudo é estático.
Funcionam só:
1. As 5 miniaturas. As 4 primeiras trocam a imagem principal (pôster +
   3 ambientes reais). A 5ª (3D/AR) abre o viewer 3D do GLB com HDRI.
2. Botão "View in your room (AR)" no viewer → janela com QR → ar.html.

## Deploy

1. Substitua TODOS os arquivos na raiz do repositório.
2. Commit + push no GitHub Desktop. Vercel redeploya.
3. Abra a raiz .../ — mostra a página de produto.
4. No celular: recarregar forçado / aba anônima (cache).

## Limitações

- iPhone não roda o AR (Safari não suporta WebXR AR).
- Detecção de parede depende de boa luz e algum detalhe na parede.
- Textura do pôster é licenciada (Star Wars) — ok para protótipo/pitch.
