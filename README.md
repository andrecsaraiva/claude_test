# AR Try-On de Parede — protótipo

Try-on em AR para projetar quadros/posters na parede (Android + Chrome),
agora com uma página de produto réplica do site AllPosters.

## Arquivos (todos juntos na raiz do repositório)

    ar-tryon/
    ├── product.html      <- página de produto (réplica AllPosters) + viewer 3D
    ├── ar.html           <- experiência de AR (WebXR)
    ├── index.html        <- página simples só com o QR (versão antiga, opcional)
    ├── poster.glb         <- modelo 3D do pôster
    ├── studio.hdr         <- HDRI para iluminação do viewer 3D
    ├── poster_image.jpg   <- imagem do pôster (thumb 1 / imagem principal)
    ├── poster_thumb.jpg   <- miniatura do pôster
    ├── room1/2/3.jpg      <- miniaturas estáticas (cenas de quarto)
    └── vercel.json        <- MIME types corretos para .glb e .hdr

A página principal agora é a **product.html**. O index.html antigo (só QR)
pode ser mantido ou removido — não é mais necessário.

## product.html — o que é interativo

A página replica a parte superior da página de produto do AllPosters.
Quase tudo é estático (botões, preço, frames) — propositalmente. Só dois
elementos funcionam:

1. **As 5 miniaturas (thumbs).** As 4 primeiras trocam a imagem principal.
   A 5ª (ícone 3D/AR) abre um **viewer 3D** no lugar da imagem: o GLB do
   pôster renderizado com three.js, iluminação HDRI e reflexos, que você
   gira arrastando com o dedo/mouse.

2. **Botão "View in your room (AR)"** dentro do viewer 3D → abre uma
   janela com o **QR code**. Escaneando, abre o ar.html (o AR de parede).

O viewer 3D usa: HDRI real para iluminação ambiente, tone mapping
ACES Filmic, luzes direcionais de apoio e reflexos no material do quadro.

## Correção desta versão

**Mudar de tamanho movia o pôster.** Quando o pôster já estava fixado e
você tocava num botão de tamanho, o toque também disparava o evento
'select' do WebXR — que re-posicionava o pôster onde a mira apontava.
Corrigido com um "guard": qualquer toque num botão da interface abre uma
janela de 400 ms na qual o onSelect ignora o 'select'. Agora trocar de
tamanho só re-escala no lugar, como deveria.

**Painel de LOG escondido.** O motor de debug continua rodando por baixo
(é barato), mas o botão LOG e o painel ficam ocultos. Para reativar:
em ar.html, remova a classe "hidden" do botão #showLogBtn.

## HDRI

O HDRI original (glasshouse interior, 15 MB) foi otimizado para 1 K
(studio.hdr, ~1,3 MB) — pesado demais para web na resolução cheia. A
qualidade da iluminação praticamente não muda, pois o ambiente aparece
desfocado nos reflexos. three.js carrega o .hdr nativamente (RGBELoader).

## Deploy (GitHub Desktop + Vercel)

1. Substitua/adicione todos os arquivos na raiz do repositório.
2. Commit + push no GitHub Desktop.
3. Vercel redeploya sozinho.
4. Abra .../product.html — é a nova página principal.
5. No celular, use recarregar forçado / aba anônima (cache).

## Limitações conhecidas

- iPhone não roda o AR (Safari não suporta WebXR AR).
- ar.html e product.html carregam o three.js do CDN unpkg.com.
- Detecção de parede depende de boa luz e de algum detalhe na parede.
- A textura do pôster é licenciada (Star Wars) — ok para protótipo e
  pitch interno; troque por arte licenciada antes de uso público.
