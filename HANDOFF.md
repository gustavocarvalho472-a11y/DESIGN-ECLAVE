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
- `index.html` → #buy (produto voa ao carrinho) → 1,3s → `upsell.html`
- `upsell.html` → "Sim" (loading → ✓) → 1,4s → `obrigado.html`; "Não quero o desconto" → 1,2s → `obrigado.html`
- `obrigado.html` → "Voltar à loja" → `index.html`
Em produção, troque os redirects pelo checkout real mantendo os estados visuais.

## Screens

### 1. Landing (`index.html`) — atualizado
Seções (cada uma com `data-screen-label`): Header (encolhe no scroll) + barra de progresso → Banner → Produto → Faixa marquee → Problema → Quiz (3 perguntas → plano 30/60/90 dias em localStorage) → Benefícios → Ativos → Antes/depois (slider) → Como usar → Comparativo → Depoimentos → FAQ → CTA final → Footer. Toast de prova social + barra fixa de compra no mobile.
**Não há mais faixa animada no topo** (topbar removida).

**Banner**
- Desktop: foto `hero-sorriso.jpg` deslocada à esquerda (width 118%, left -20%) para o rosto ficar na metade esquerda; texto estreito à direita (max-width min(420px,30vw)) sobre degradê creme lateral.
- ≤1180px (mobile/tablet): **só foto + texto, sem botão**. Banner compacto `aspect-ratio:4/3`, max-height 64svh, para os produtos aparecerem no 1º scroll. Foto `object-fit:cover; object-position:0% 30%` → fundo bege à esquerda, rosto à direita. Sem overlay sobre a modelo. Texto alinhado à esquerda embaixo, max-width ~42–44%, h1 clamp(28px,7.6vw,46px).

**Produto**
- Galeria: 1ª imagem = `serum-rosto.jpg` (modelo segurando o frasco); depois textura, mão/rosa, caixa.
- Mobile: `.info` é flex-column e usa `order` — botão de compra + linha de pagamento sobem; **pílulas de ativos e timer ficam abaixo do CTA**; selos de confiança por último.

**CTA (`.btn`)**
- Figtree 700, caixa alta, letter-spacing .04em, 15px (#buy 16px, min-height 64px).
- Brilho dourado atravessando todos os botões a cada 4s (::before).
- #buy pulsa (scale 1.025 + onda 16px) a cada 2s; pausa no hover/clique.

**Faixa marquee abaixo dos produtos:** reta (sem rotação).

**Footer:** mesmo vidro Apple da página Obrigado (ver item 3.5): foto `caixa-frasco.jpg` ao fundo + overlay mauve; card de vidro com 5 colunas — logo vertical branco + frase + CNPJ/endereço + selos de pagamento (PIX/VISA/MASTER/ELO/BOLETO), Ajuda, Redes, Contatos (+ horário), botões Ver Instagram / Comprar agora; linha de copyright + "Compra 100% segura"; wordmark gigante. 2 colunas ≤980px, 1 coluna ≤600px.

### 2. Upsell (`upsell.html`) — atualizado
- **Fundo preto** `#0D0B0C` com textura do símbolo em cinza (#8C8C8C) a opacity .06 — quase imperceptível.
- **Faixa do topo vermelha**: linear-gradient(90deg,#9E0F1C,#C8162A,#9E0F1C), texto e spinner **champanhe #F7D9A6**, sombra vermelha suave.
- **Logo no topo** (não no rodapé): `eclave-logo-vertical-white.png`, h 56px (46px mobile).
- h1 Serotiva clamp(30–52px) branco, 1ª linha em `--gold`.
- Card `--blush-2`, radius 32px, borda dupla dourada; selo 30% OFF com anel de texto girando; preço "12x de / R$" empilhado + número Serotiva 650 com centavos em sup.
- **Timer**: caixa branca com borda conic-gradient vinho/branco girando (5s); ponto vinho pulsando; **dígitos brancos em caixas vinho** (gradiente #6A1A31→#5A1428), Serotiva 46px; barra de tempo vinho sincronizada.
- **Box de atenção**: texto em CAIXA ALTA 800 13px, fundo #F8EEF0, borda 1.5px #7A1F36, ícone de alerta piscando; "apenas N unidades" em pílula vinho com texto branco.
- **CTA**: margin-top 14px do box; pill 64px com seta em círculo dourado; brilho a cada 4s + **batida** (scale 1.035 + onda 18px, 1.8s); pausa no hover; "Compra segura · 1 clique" e link de recusa abaixo.
- Leque de fotos com entrada escalonada + flutuação suave; barra fixa no mobile.
- **Tweaks** (só no editor; `tweaks-panel.jsx` + React podem ser removidos em produção): Fundo (Noite/Vinho/Mauve), Urgência (Calma/Equilibrada/Máxima), Acento do CTA (Mauve/Vinho/Ouro). Valores escolhidos: Noite / Equilibrada / Vinho — aplicados via `data-*` no `<html>`; em produção, fixe as classes equivalentes.

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
- **Urgência (upsell):** faixa vermelha #9E0F1C/#C8162A com texto champanhe #F7D9A6. Vinho `#5A1428` / `#7A1F36` / `#9C3A55`, fundo `#F8EEF0`. Uso restrito ao timer; nunca usar laranja.
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
