# Free shipping progress bar (mini-cart)

## Summary

Adds a progress bar to the cart drawer (mini-cart) showing customers how much
more they need to spend to unlock free shipping. The threshold is configured
by merchants in the theme editor, no code changes required per-store.

## Merchant-facing settings

Location: **Theme settings → Cart → Free shipping bar**
(`config/settings_schema.json`)

| Setting id | Type | Default | Description |
|---|---|---|---|
| `show_free_shipping_bar` | checkbox | `false` | Toggles the bar on/off in the mini-cart. |
| `free_shipping_bar_threshold` | number | `100` | Amount required for free shipping, entered in the store's currency major units (e.g. `100` for $100.00). Only shown when the checkbox above is enabled. |

## Behavior

- Hidden entirely unless `show_free_shipping_bar` is enabled and the threshold is `> 0`.
- While `cart.total_price` is below the threshold, shows:
  - `content.free_shipping_bar_remaining` — "Only {{ amount }} away from free shipping" (en) / "Plus que {{ amount }} avant la livraison gratuite" (fr), where `amount` is the remaining balance formatted with the `money` filter.
  - A fill bar sized to `cart.total_price / threshold` as a percentage (capped at 100%).
- Once the threshold is met or exceeded, shows:
  - `content.free_shipping_bar_achieved` — "You've unlocked free shipping!" (en) / "Livraison gratuite débloquée !" (fr).
  - The `.cart-free-shipping-bar--achieved` modifier class is added (bold message, full bar).
- No JavaScript: the bar is pure Liquid + CSS. Because the mini-cart's contents
  are already re-rendered via the Section Rendering API whenever the cart
  changes, the bar updates automatically. The fill's `width` transition
  (`transition: width var(--animation-speed) ease`) animates smoothly across
  that DOM morph.

## Currency assumption

`cart.total_price` is expressed in the shop's currency **minor unit** (cents
for 2-decimal currencies). The threshold setting is entered in **major
units**, so it's converted with `| times: 100` before comparison. This holds
for standard 2-decimal currencies (USD, EUR, GBP, ...) but will be off for
0-decimal (e.g. JPY) or 3-decimal (e.g. KWD) currencies. Acceptable for the
stores this theme currently targets; revisit if multi-currency stores with
non-2-decimal currencies adopt this theme.

## Files

- `config/settings_schema.json` — setting definitions (Cart category).
- `snippets/cart-free-shipping-bar.liquid` — renders the message + progress bar, defines its own `{% stylesheet %}`.
- `snippets/cart-drawer.liquid` — renders `cart-free-shipping-bar` inside `.cart-drawer__summary`, above `cart-summary`.
- `locales/en.default.json` / `locales/fr.json` — `content.free_shipping_bar_remaining`, `content.free_shipping_bar_achieved`.
- `locales/en.default.schema.json` — editor-facing labels (`t:content.free_shipping_bar` header, `t:settings.show_free_shipping_bar`, `t:settings.free_shipping_bar_threshold`, `t:info.free_shipping_bar_threshold`).

## Out of scope

- The cart **page** (`/cart`, non-drawer `cart_type`) does not show the bar — only the drawer/mini-cart, per the original request.
- No dismiss/collapse control; the bar is always visible when enabled and relevant.
