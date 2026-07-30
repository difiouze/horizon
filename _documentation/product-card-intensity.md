# Product card intensity (tea leaf rating)

## Summary

Adds a private theme block, `_product-intensity`, that displays a horizontal
row of leaf icons (1 to 5) representing an intensity value read from a
product metafield. Merchants choose which metafield to read; the block can
only be added inside a product card, not exposed as a general-purpose block.

## Merchant-facing settings

Location: block settings panel, after adding the **Tea intensity** block
inside a product card.
(`blocks/_product-intensity.liquid`)

| Setting id | Type | Default | Description |
|---|---|---|---|
| `metafield` | text | `custom.intensity` | Namespace and key of the product metafield to read, in `namespace.key` format. Value must be a number from 1 to 5. |
| `label` | text | `Intensity:` (`t:settings.product_intensity.default_label`) | Text shown before the icon row. Leave blank to hide. |
| `icon_color` | color | theme foreground color | Color of the filled/empty leaf icons. |
| `icon_size` | range (8–60px) | `16px` | Icon width/height. |
| `padding-block-start` / `padding-block-end` / `padding-inline-start` / `padding-inline-end` | range (0–100px) | `0` | Standard block padding. |

## Behavior

- Reads `product.metafields[namespace][key].value`, with `namespace`/`key`
  parsed from the `metafield` setting (split on `.`; default
  `custom.intensity`).
- The value is clamped to 0–5 (`at_least: 0 | at_most: 5`) and coerced to a
  number (`times: 1`), so text-type metafields storing a plain number also
  work, not just number-type metafields.
- If the metafield has no value, the block renders nothing — no wrapper, no
  icons.
- Renders 5 leaf icons total: the first *N* (N = intensity value) get the
  `.intensity-icon--filled` modifier (solid fill); the remaining `5 - N`
  render at low opacity (`--opacity-20`) as the "empty" state.
- The leaf shape is reused from the shared icon path library
  (`snippets/icon.liquid`, `'leaf'` case) via an inline `<symbol>`/`<use>`
  pair, so the path data isn't duplicated 5 times — same pattern as the star
  rating in `blocks/review.liquid`.
- The optional `label` setting renders as `<span class="intensity-label">`
  before the icon row; the wrapper uses `display: flex` so the label and the
  icons sit on one line.
- Accessibility: the icon row has
  `aria-label="Intensity of this product is {{ value }} out of 5"`
  (`accessibility.product_intensity`); individual `<svg>` icons use
  `role="presentation"` since they're decorative, mirroring `review.liquid`.
- Theme editor preview: when previewed with no real product in context
  (`request.visual_preview_mode` and `product == blank`), falls back to
  `collections.all.products.first` and a default value of `3`, so merchants
  see a populated example instead of an empty block.

## Scoping — private, product-card-only

- The file is prefixed `_` (`blocks/_product-intensity.liquid`), following
  this repo's private/static block convention.
- Registered in the `blocks` accepts array of exactly three files:
  `blocks/_product-card.liquid`, `blocks/product-card.liquid`, and
  `blocks/_product-card-group.liquid` — the three surfaces that make up "the
  product card" in Horizon (the static card, the standalone product-card
  theme block, and the row/column grouping block nested inside cards).
- No other section or block references `_product-intensity`, so it can't be
  added anywhere else in the editor.

## Files

- `blocks/_product-intensity.liquid` — the block itself (markup,
  `{% stylesheet %}`, `{% schema %}`).
- `blocks/_product-card.liquid`, `blocks/product-card.liquid`,
  `blocks/_product-card-group.liquid` — `"type": "_product-intensity"` added
  to each `blocks` accepts array.
- `locales/en.default.json` — `accessibility.product_intensity`.
- `locales/en.default.schema.json` — `names.product_intensity`,
  `content.resource_reference_product_intensity`,
  `settings.product_intensity.{metafield,metafield_info,label,default_label}`.

## Out of scope

- No icon-choice setting — the leaf icon is fixed, since a tea-intensity
  picto is the point of the block.
- No alignment/justify setting; the label + icon row always follows normal
  inline flow (left-to-right in DOM order).
- Other theme locales (e.g. `fr.json`, `fr.schema.json`) are not updated —
  per the project's localization convention, only `en.default.json` /
  `en.default.schema.json` are authored here in English; translators handle
  the rest.
