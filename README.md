# AR Try-On de Parede — protótipo

## Estrutura (todos os arquivos juntos na raiz)

    index.html       <- página de produto (réplica AllPosters) + viewer 3D + QR
    ar.html          <- experiência de AR (WebXR)
    poster.glb       <- modelo 3D do pôster
    studio.hdr       <- HDRI para iluminação do viewer 3D
    poster_image.jpg <- imagem do pôster (thumb 1 / imagem principal)
    poster_thumb.jpg <- miniatura do pôster
    room1/2/3.jpg    <- miniaturas estáticas
    vercel.json      <- MIME types para .glb e .hdr

A página de produto agora se chama **index.html** — abre direto na raiz
do site (.../). A antiga página separada de QR foi descartada: o QR agora
está dentro da própria página, na janela do viewer 3D.

## Por que antes abria a página de QR antiga

O Vercel serve o arquivo "index.html" quando se acessa a raiz do site.
Antes, o index.html era a página antiga de QR; a página de produto tinha
outro nome (product.html), por isso não abria sozinha. Agora a página de
produto É o index.html, então a raiz do site mostra ela.

## O que é interativo na página

Quase tudo é estático (réplica visual). Funcionam só:
1. As 5 miniaturas — as 4 primeiras trocam a imagem; a 5ª abre o viewer 3D.
2. O botão "View in your room (AR)" no viewer → janela com o QR code → ar.html.

## Deploy

1. Substitua TODOS os arquivos na raiz do repositório (note: a página
   principal agora se chama index.html — o antigo index.html de QR deve
   ser removido/sobrescrito).
2. Commit + push no GitHub Desktop. Vercel redeploya.
3. Abra a raiz .../ — já mostra a página de produto.
4. No celular: recarregar forçado / aba anônima (cache).

## Limitações

- iPhone não roda o AR (Safari não suporta WebXR AR).
- Detecção de parede depende de boa luz e algum detalhe na parede.
- Textura do pôster é licenciada (Star Wars) — ok para protótipo/pitch.
