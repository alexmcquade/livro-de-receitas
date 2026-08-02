# Livro de Receitas — recipe translation pipeline

A recipe library for our Brazilian Portuguese-speaking home chef. US recipes go in,
practical pt-BR versions come out, published as a static site on GitHub Pages that
she can bookmark on her phone (no app, no login, no account).

## How it works

1. Paste a US recipe (URL or text) into a Claude Code session in this folder and say "add this".
2. Claude translates it to natural Brazilian Portuguese and converts measurements
   using the rules below.
3. The recipe is saved as a markdown file in `recipes/` (source of truth, with the
   original US amounts preserved for reference).
4. Claude adds it to `index.html`, commits, and pushes to `main`. GitHub Pages
   redeploys within a minute — **the URL never changes**, so her bookmark keeps working.
5. Send her a quick WhatsApp: "nova receita no livro" + the link.

## Running locally

No build step — `index.html` is a single self-contained file. Open it directly:

```
open index.html
```

## Hosting

GitHub Pages, deployed from `main` at the repo root. Live at the URL in the repo's
About section (Settings → Pages). Every viewer always gets the latest version — this
replaced a Claude Artifact, which pinned shares to a specific version and required
manually re-pointing the share on every update.

## Conversion rules (practical, not literal)

- **Shopping-friendly rounding** for savory cooking: 1 lb → 500 g, ½ lb → 250 g,
  1 oz → 30 g. Amounts she can actually ask for at a shop or find on a package.
- **Ingredient-aware volume→weight**: 1 cup flour → 120 g, 1 cup sugar → 200 g,
  1 cup butter (2 sticks) → 225 g, 1 cup liquid → 240 ml. Never convert cups
  generically.
- **Baking keeps precision**: where ratios matter, round to the nearest 5 g and say so.
- **Oven temps** to normal dial stops: 350°F → 180°C, 375°F → 190°C, 425°F → 220°C.
- **Brazilian ingredient names**, not dictionary translations (cilantro → coentro),
  plus substitution notes when a US ingredient is hard to find locally.
- **Portugal shopping context** (the chef is Brazilian, but we live in Portugal —
  she shops in Portuguese supermarkets):
  - When the Portugal shelf name differs from the Brazilian term, show both in the
    ingredient line: "gergelim (nas lojas: sésamo)", "cebolinha (cebolinho)",
    "gotas de chocolate (pepitas de chocolate)".
  - When the names are near-identical, just use the shelf name: açúcar mascavado,
    papel vegetal (not papel-manteiga).
  - In Portugal "limão" is the yellow lemon (what Brazilians call limão-siciliano);
    limes are "limas". Never add Brazil-market lemon notes.
  - Common shelf equivalents: heavy cream → natas para bater (35%), creme de leite
    → natas, soy sauce → molho de soja (shoyu understood), cornstarch → amido de
    milho / Maizena, vanilla extract → aroma or extrato de baunilha.
- **Spoons stay spoons, but always show the weight too.** tsp → colher de chá,
  tbsp → colher de sopa, each followed by the metric amount in parentheses so she
  can use a scale or a measuring jug instead of hunting for the right spoon:
  `1 colher de chá (6 g) de sal`, `2 colheres de sopa (30 ml) de molho de pimenta`.
  Use **ml for liquids** (oil, sauces, extracts) and **g for dry** (salt, spices,
  butter, herbs). Reference values:
  - 1 colher de sopa líquida = 15 ml · 1 colher de chá líquida = 5 ml
  - sal: 1 colher de chá = 6 g · manteiga: 1 colher de sopa = 15 g
  - especiarias em pó (cebola em pó, páprica, caiena): 1 colher de chá ≈ 2–3 g
  - pimenta-do-reino moída: 1 colher de chá = 2 g (¼ = 0,5 g)
  - ervas frescas picadas: 1 colher de sopa ≈ 3 g
  - açúcar: 1 colher de sopa = 12 g · farinha: 1 colher de sopa = 8 g

## Structure

- `recipes/*.md` — one file per recipe, pt-BR, with frontmatter metadata and the
  original US recipe at the bottom for reference.
- `index.html` — the generated single-page recipe book (this is the site).
- `.githooks/pre-commit` — gitleaks secret scan, wired up via `core.hooksPath`.

## Batch sizes (bulk cooking)

She cooks in bulk so the household eats through the week, so most recipes get a
doubled version. **Write it by hand — never let the page compute it.** Naive ×2
math produces bad cooking: doubled salt, an overcrowded pan that steams, "bake
twice as long."

**Not every recipe should have one.** Ask whether doubling actually serves the
week before writing a `dobro` block. Skip it when:

- the recipe is already a bulk recipe at its normal size (the egg casserole makes
  6 portions from one dish — that's the week already);
- the dish degrades on reheating past a few days (egg dishes turn rubbery and
  weep; a dish that only keeps 3–4 days shouldn't yield 12 portions);
- the effort saved is small (10 minutes of prep is not worth a worse result — she
  can just make it fresh again).

A recipe with no `dobro` block simply shows no batch toggle; the page handles that
on its own. Making a second, different dish is often the better answer to "cook
once for the week" than making twice as much of one.

Rules for authoring the doubled version:

- **Amounts**: double, then round to shoppable numbers (500 g → 1 kg, 180 ml →
  360 ml). Salt, spices, and leaveners often want slightly less than double —
  taste-check the ratio rather than multiplying blindly.
- **Change the method when doubling breaks it.** The real question is "how would
  someone actually cook this much?", not "what's 2× the recipe?" Stir-fries are
  the clearest case: doubling sesame chicken on the stove means four back-to-back
  skillet batches, which nobody will do, so the doubled version bakes the coated
  chicken on two sheet pans at 220°C while the sauce thickens in a pot. Roasts
  need pan space and rack swaps, not double time (two chickens ≈ +15 min). Cookies
  bake in rounds of two sheets.
- **Say why in the notes** when the doubled method differs, so she trusts it.
- **Re-check doneness when the method changes.** Chicken, pork, and ground meat
  need a stated internal temperature (poultry 74°C), not just a time — oven loads,
  pan crowding, and piece size all shift timing. Watch especially for steps that
  quietly stop finishing the cooking: the small sesame chicken finishes in the
  simmering sauce, but the doubled oven version only tosses it at the end, so the
  chicken must leave the oven fully cooked. State that explicitly.
- **Timing is not linear** — state the real time, and flag hands-on steps that do
  scale (shaping 48 cookies takes twice as long as 24).
- Add bulk-specific storage notes: fridge life, freezing, reheating.

In the page each language block carries a `dobro` object (`porcoes`, `rotulo`,
`ingredientes`, `preparo`, `notas`) that fully overrides the normal version; the
recipe file gets a matching "Dose dupla" section.

## Linking to the original

Each recipe entry has a `fonte` field with the source URL (`null` if there isn't
one). The page shows an "Receita original / Original recipe ↗" link on the recipe,
in both languages — useful for photos and for checking the source later.

## The PT/EN toggle

`index.html` has a language toggle (PT default). It exists so Alex can browse in
English when picking the week's menu, then copy the Portuguese recipe name for
WhatsApp (English mode shows a "Portuguese name" box with a copy button on each
recipe). Rules:

- **Measurements are identical in both languages** — metric, practical amounts.
  Only the words are translated. Never convert back to cups/lb for the EN version.
- Each recipe in the page's `RECIPES` array therefore has `pt` and `en` blocks
  (titulo, categoria, porcoes, ingredientes, preparo, notas); `id` and `tempo`
  are shared. When adding a recipe, write both blocks.
- Toggling preserves the open recipe, search, category filter, and checked-off
  ingredients — keep it that way when changing the page.

## Recipe file format

```markdown
---
id: kebab-case-id
titulo: Nome em português
titulo_original: Original US name
categoria: Carnes | Massas | Sobremesas | Saladas | Sopas | Acompanhamentos
porcoes: 4
tempo: 1h30
fonte: URL or description
adicionada: YYYY-MM-DD
---

## Ingredientes
...

## Modo de preparo
...

## Notas
(substitutions, tips)

## Original (referência)
(the US recipe as received)
```
