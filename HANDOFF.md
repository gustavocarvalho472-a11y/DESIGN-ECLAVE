# Handoff: Site Eclave — Landing, Upsell e Obrigado

## Overview
Funil de venda do **Sérum Clareador Noturno Eclave (30ml)**, em três páginas:
1. `index.html`: landing page (produto, quiz de diagnóstico, antes/depois, ativos, depoimentos, FAQ, CTA).
2. `upsell.html`: oferta de 1 clique após a compra (+1 sérum com 30% OFF + nécessaire).
3. `obrigado.html`: confirmação da compra, indique e ganhe, comunidade, rodapé.

**Objetivo imediato:** publicar no **GitHub Pages**. Depois disso, ligar à loja real (checkout, estoque, cupons).

## About the Design Files
Os arquivos em `site/` são **referências de design em HTML**, já funcionais como site estático. Há dois caminhos:
- **A. Publicar como está (recomendado agora):** HTML/CSS/JS puros, sem build. Servem direto no GitHub Pages.
- **B. Migrar para um framework** (Next.js, Astro, Shopify theme etc.) recriando o visual com os tokens em `design-system/tokens.css`.

## Fidelity
**High-fidelity.** Cores, tipografia, espaçamentos, animações e textos são finais. A exceção são os dados simulados listados em “Pendências”.

## Publicar no GitHub Pages (caminho A)
```bash
cd site
git init && git add . && git commit -m "Eclave site v1"
gh repo create eclave-site --public --source=. --push
gh api -X POST repos/{owner}/eclave-site/pages -f "source[branch]=main" -f "source[path]=/"
```
URL: `https://<usuario>.github.io/eclave-site/`

Notas:
- Todos os caminhos são relativos (`assets/...`), então funciona em subpasta.
- Nomes de arquivo sem espaço: manter assim.
- `image-slot.js` é um componente de placeholder usado na landing. Pode ser mantido, ou trocado por `<img src="..." loading="lazy">` com `object-fit:cover` (recomendado para produção; os `src` já estão nos atributos).
- Adicionar `<meta name="description">`, Open Graph e favicon (`assets/eclave-simbolo.png`) antes de divulgar.

## Fluxo entre páginas (já ligado nesta pasta)
- `index.html` → botão `#buy` (“Quero meu clareador”) anima o produto voando até o carrinho e, após 1,3s, vai para `upsell.html`.
- `upsell.html`:
  - “Sim” mostra o estado de processando, depois o ✓, e após 1,4s vai para `obrigado.html`;
  - “Não quero o desconto” vai para `obrigado.html` após 1,2s.
- `obrigado.html` → “Voltar à loja” vai para `index.html`.

Em produção, trocar esses redirecionamentos pelas chamadas do checkout (ex.: Yampi, Cartpanda, Shopify), mantendo os estados visuais.

## Screens

### 1. Landing (`index.html`)
Seções em ordem (cada uma tem `data-screen-label`):
1. Topbar com marquee.
2. Header, que encolhe ao rolar, e barra de progresso no topo.
3. Banner.
4. Produto, com:
   - galeria de 4 imagens com miniaturas;
   - kits 1/2/3 (`.bundle[data-q]`);
   - estoque e preço.
5. Faixa marquee inclinada.
6. Problema.
7. Quiz de 3 perguntas, que gera um plano de 30/60/90 dias salvo em localStorage.
8. Benefícios.
9. Ativos: Retinaldeído, Centella, Copper Peptide.
10. Resultados: antes/depois com slider arrastável (`--p`).
11. Como usar: 3 passos.
12. Comparativo.
13. Depoimentos.
14. FAQ.
15. CTA final.
16. Footer.

Elementos flutuantes: toast de prova social e barra fixa de compra no mobile.

Breakpoint principal: abaixo de 1180px, o texto do banner vai para baixo da imagem.

### 2. Upsell (`upsell.html`)
- **Fundo:** `--mauve-d` com textura do símbolo, gerada em canvas, em opacidade .09 e com drift de 80s.
- **Faixa do topo:** fundo `--ink`, spinner dourado e o texto “Sua compra está sendo processada!”.
- **Header:** h1 em Serotiva clamp(30–52px), com a 1ª linha em `--gold`.
- **Card:** max-width 880px, fundo `--blush-2`, raio 32px. A borda é dupla, feita com box-shadow: 1px `--gold-d` + 8px blush + 1px dourado translúcido.
- **Conteúdo do card:**
  - h2 com o destaque `.hl` (fundo `--rose`, texto branco).
  - Foto 16:9 com um selo de 124px:
    - anel dourado com o texto circular girando a cada 22s;
    - núcleo `--mauve-d` com “30%” em Serotiva 600.
  - Preço:
    - “12x de” e “R$” empilhados à esquerda;
    - número em Serotiva 650, clamp(84–120px), com os centavos em `<sup>` a .42em;
    - o preço antigo é riscado por uma linha animada.
  - **Timer:**
    - caixa branca com borda em conic-gradient vinho/branco girando a cada 5s;
    - ponto vinho “ao vivo” pulsando;
    - dígitos em caixas `#F8EEF0` com Serotiva 46px em `--wine`;
    - barra de tempo em degradê vinho, sincronizada com o timer;
    - texto de estoque abaixo.
  - **CTA:**
    - pill de 64px de altura, `--mauve`, com uma seta num círculo dourado de 48px;
    - brilho dourado atravessando a cada 4s;
    - pulsa quando faltam menos de 60s;
    - linha “Compra segura · 1 clique” e o link de recusa abaixo.
  - Leque de 5 fotos (3 no mobile): entram uma a uma ao aparecer na tela e depois flutuam 6px em ciclo de 5s. No desktop, abrem em leque no hover.
- **Mobile:** barra fixa com o CTA quando o botão principal sai da tela.

### 3. Obrigado (`obrigado.html`) — atualizado
1. **Confirmação** (fundo `--blush-2`, sem textura)
   - Logo vertical símbolo+ECLAVE mauve (`assets/eclave-logo-vertical-mauve.png`, h 64px, margin-bottom 22px).
   - Ícone de sucesso 64px: gradiente azul bebê `#B9DDF7 → #7FBCEB`, ✓ branco desenhado (stroke-dashoffset, .6s), halo `rgba(143,197,238,.22)` 8px. Dois anéis 1.5px `rgba(127,188,235,.55)` expandem scale .85→1.45 e somem (2.8s, defasados 1.4s).
   - h1 Serotiva clamp(38–64px) `--mauve-d`; 2 parágrafos `--muted`.
   - CTA “Indique & ganhe” (pill mauve, seta em círculo dourado apontando para baixo): brilho dourado varrendo a cada 3.6s, anel de pulso 14px a cada 2.4s, seta oscilando 3px. Rola até `#indique`.
2. **Fotos** — leque de 5 (3 no mobile) com entrada escalonada + flutuação 6px/5s; wordmark gigante atrás a opacity .32.
3. **Indique e ganhe — tela de MacBook (só a tela, sem teclado)**
   - Moldura: `.win` radius 16px, overflow hidden, bg #2B2B2E; anéis por box-shadow: 1px rgba(0,0,0,.6), 9px #1C1C1E, 1px #48484C.
   - Barra: gradiente #3A3A3D→#2E2E31, semáforo 12px (#FF5F57 / #FEBC2E / #28C840), pílula de URL central #1F1F21 com cadeado “eclave.com.br/indique” (oculta no mobile).
   - Corpo: foto ocupa a janela inteira; painel de **vidro** à esquerda (grid 6fr/6fr): bg linear-gradient(140deg, rgba(255,255,255,.22), rgba(255,255,255,.06)), backdrop-filter blur(24px) saturate(1.6), borda 1px rgba(255,255,255,.35), highlight interno no topo, radius 22px, margin 22px.
   - Cupom: borda tracejada creme, fundo rgba(62,42,46,.4), código em `--paper` clamp(16–22px), botão Copiar dourado (clipboard + estado “Copiado”), quebra de linha se faltar espaço.
   - 3 pílulas de faixa (1/5/10) à direita, entram em sequência ao rolar.
4. **Comunidade** — fundo `--mauve-d` liso, h2 com #RitualEclave dourado, CTA claro, marquee dourado **reto** (sem rotação).
5. **Footer — vidro estilo Apple**
   - Foto de fundo opacity .55 + overlay mauve em gradiente vertical.
   - Card: radius 30px; bg linear-gradient(160deg, rgba(255,244,236,.20), rgba(255,236,226,.07) 55%, rgba(255,244,236,.12)); backdrop-filter blur(40px) saturate(1.8) brightness(1.05); borda especular via ::before com mask (gradiente branco forte nos cantos sup-esq e inf-dir); brilho radial no canto sup-esq via ::after; inner highlights + sombra externa suave.
   - 4 colunas separadas por divisores 1px rgba(255,255,255,.12): logo vertical branco (`eclave-logo-vertical-white.png`, h 52px) + CNPJ, Redes, Contatos, botões (Ver Instagram creme / Voltar à loja dourado).
   - Responsivo: 2 colunas ≤820px, 1 coluna ≤640px (divisores viram horizontais).

## Interactions & Behavior
- **Entrada:** `.rv` com opacity e translateY de 18px → 0 em .7s `--ease-out`, escalonada via `--d`.
- **Contagem do preço:** de 0 a 11,08 em 900ms, com ease-out cúbico.
- **Timer:** 10 minutos. O fim é salvo em `localStorage['eclave-upsell-end']` e reinicia ao zerar. Só o dígito que muda faz o “tick”.
- **Estoque no upsell:** cai 1 unidade após 14s. É simulado.
- **Respeito a `prefers-reduced-motion`:** todas as animações são desligadas.

## State
- Landing: kit selecionado, slide da galeria, passo e respostas do quiz (localStorage), estoque.
- Upsell: fim do timer (localStorage), estoque, aceite (idle → loading → done).
- Obrigado: estado do botão Copiar.

## Pendências antes de publicar
- **Dados simulados:**
  - estoque (landing e upsell);
  - toasts de compra;
  - timer que reinicia;
  - 4,9 / número de avaliações.
- **Placeholders no Obrigado:** @Instagram, TikTok, telefone, e-mail, CNPJ, endereço, link do grupo, código do cupom (`INDICAECLAVE`), recompensas por faixa e a hashtag `#RitualEclave`, que é uma sugestão.
- **Integração:** checkout, upsell 1 clique e programa de indicação.

## Design Tokens
Veja `design-system/tokens.css`. Resumo:
- **Principal:** Mauve `#7A5359` / `#5C3D43`, Rose `#A5707D`.
- **Fundos:** Cream `#F6E3CB` / `#FBF1E4` / `#FFFAF4`, Blush `#F3C9CC` / `#FBE4E3`.
- **Destaque:** Gold `#E4C9A2` / `#C9A676`.
- **Texto:** Ink `#3E2A2E`, Muted `#6E5358`.
- **Urgência:** Vinho `#5A1428` / `#7A1F36` / `#9C3A55`, fundo `#F8EEF0`. Uso restrito ao timer; nunca usar laranja.
- **Fontes:**
  - Serotiva (variável, `assets/Serotiva-VF.ttf`): títulos e números.
  - Figtree (Google Fonts): corpo e labels.
- **Raios:** 10 / 14 / 22 / 28 / 32 / pill.

### Regras de marca
- Não repetir o símbolo em selos, ícones e botões. Na página Obrigado não há textura de fundo; logo principal = versão vertical símbolo+ECLAVE.
- Cor de sucesso: azul bebê `#B9DDF7`/`#7FBCEB` (uso exclusivo do ícone de confirmação).
- Ícones são de linha: stroke 1,8–2, cantos arredondados, 14–18px. Usar poucos e com função.
- Botões principais são pílulas mauve com a seta em círculo dourado. A recusa é sempre um link de texto.

## Assets
- `site/assets/`: Serotiva-VF.ttf, wordmark e símbolos (`eclave-wordmark.png`, `eclave-simbolo.png`, `eclave-simbolo-bege.png`, `simbolo-mauve.png` etc.).
- `site/assets/img/`: fotos de produto e lifestyle em JPG (até 1600px).
- `design-system/logos/`: logos oficiais em alta resolução (@3x).

## Files
- `site/index.html`, `site/upsell.html`, `site/obrigado.html`, `site/image-slot.js`, `site/assets/`
- `design-system/tokens.css`, `design-system/logos/`
