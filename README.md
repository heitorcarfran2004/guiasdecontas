# Guias de Contas — app de membros

Entrega do produto **100 Projetos de Guias de Contas** (201 páginas: capa + 100 modelos × 2),
com os 4 bônus (ainda "Em breve") e o upsell de vários fios (com cadeado, sem venda).
Mesma interface do app das Carteiras (clubedanina.vercel.app), com a marca das Guias:
verde-escuro #1F3A2C, dourado #C6A15B, creme #F8F5EC, títulos em Playfair Display.

    node servidor.cjs     # http://localhost:4231

**Login de teste (só funciona em localhost/127.0.0.1):**
`teste@guiasdecontas.com` (apostila + bônus), `varios@guiasdecontas.com` (+ upsell liberado) e
`bumps@guiasdecontas.com` (+ os 3 order bumps, "em preparação").

## Como é montado

O `index.html` **não se edita à mão**: ele é gerado.

    node scripts/gc-app-arquivos.cjs          # páginas webp, PDF, capas, fotos do login, ícones
    node scripts/gc-app-arquivos.cjs capas    # só capas, login e ícones
    node scripts/gc-app-montar.cjs            # referencia/carteiras.html -> index.html

(os `scripts/` ficam no repo de trabalho "Visual St - Claude")

- `referencia/carteiras.html`: o `index.html` do app das Carteiras congelado em 26/10/2026. O montador
  faz trocas com conferência: se a referência mudar e algum trecho sumir, ele para com "não achei".
- Índice "Modelos": títulos de `entregaveis/60-guias-de-contas/modelos.json` (1–56) e
  `modelos-novos.json` (57–100; o 57 é o antigo 60, Terminal e Fecho), agrupados nas 5 coleções.
- Sem aba Aulas (o produto não tem vídeo): o guia "Antes da primeira conta" fica no Início.

## Acesso (quem vê o quê)

O login chama a Edge Function `membros-acesso` (Supabase `dxybmayffxvepmwbyezc`), que lê
`membros_compras`, alimentada pelo webhook da Wiapy (`wiapy-webhook`, regra em
`apps/micanga-membros/supabase/functions/wiapy-webhook/mapa.ts`).

| compra | item | o que abre |
|---|---|---|
| checkout `6aded3e9…` "Guias de Contas", qualquer oferta (R$ 17,90 / R$ 9,90) | `guias` | apostila, PDF e os 4 bônus (em breve) |
| order bump Fundamentos e Consagração das Guias | `guias-bump-fundamentos` | "Em preparação" (conteúdo não existe) |
| order bump 50 Orações e Preces de Umbanda | `guias-bump-oracoes` | idem |
| order bump Dicionário da Umbanda - 100 Termos Explicados | `guias-bump-dicionario` | idem |
| combo dos 3 bumps | os 3 itens acima | idem |
| upsell de vários fios (sem checkout ainda) | `guias-varios` | Guias de Vários Fios (6 págs.) |

**Ainda não ligado:** a mudança do `mapa.ts` está em `supabase/proposta-webhook/` (`mapa.ts` + `mapa.diff`),
aguardando aprovação. Até publicar, venda das Guias chega como "fora da miçanga" e ninguém entra.
Depois de publicar: vendas antigas com `node scripts/gc-membros-vendas.cjs <tmp>/vendas.json`
(aba da Wiapy logada no Chrome 9222), conferir com
`node scripts/membros-importar.cjs <tmp>/vendas.json - --checkout=6aded3e92c5feb79f3bbdbed --simular`
e enviar trocando `-` pelo token do webhook e tirando `--simular`.

## Conteúdo

| no app | origem |
|---|---|
| 100 Projetos de Guias de Contas (201 págs.) | `entregaveis/60-guias-de-contas/capa/CAPA.png` + `paginas/M01-p1 … M100-p2.png` |
| PDF (`pdf/100-Projetos-de-Guias-de-Contas.pdf`) | cópia de `pdf/100-Projetos-de-Guias-de-Contas (comprimido).pdf` |
| Bônus 1–4 (o 2 é "Guia Completo dos Orixás e Entidades" desde 26/10) | só as capas: **"Em breve"** |
| Order bumps (Fundamentos, Orações, Dicionário) | só as capas: cadeado para quem não comprou, "Em preparação" para quem comprou |

Capas: tabela `CAPAS` no `scripts/gc-app-arquivos.cjs`; capa que ainda não existe sai provisória (verde, "em breve").
| Guias de Vários Fios (upsell) | `~/Downloads/Guia de Contas/Upsell - Vários Fios/` (M57–M59, 6 págs.), com cadeado |

## Suporte e push

Chamados em `membros_suporte` (Edge Function `membros-suporte`, `app = 'guias'`); avisos pela
`membros-push` (`app = 'guias'`). As funções são as mesmas das Carteiras.

## Publicar (ainda não)

Repo próprio na Vercel (sugestão: projeto `guiasdecontas` → guiasdecontas.vercel.app).
Páginas ~47 MB + PDF ~48 MB ficam no repo. `.vercelignore` tira referência, supabase, servidor e README do ar.
