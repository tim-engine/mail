<p align="center">
  <img src="https://raw.githubusercontent.com/tim-engine/tim/main/.github/tim_logo.png" alt="Tim - Template Engine" width="120px" height="120px"><br>
  A Tim Engine package for creating responsive email templates<br>
</p>

<p align="center">
  <code>tim install mail</code>
</p>

## Features

- **Full document shell** (`mail-body`) — `html` with Outlook namespaces, head meta tags, a responsive `<style>` block, hidden preview preheader text, and a `role="article"` wrapper. Tim re-adds `<!DOCTYPE html>` at render.
- **MJML-style layout** (`section` + `column`) — nested `role="presentation"` tables with zero `font-size` gutters, Outlook conditional wrappers, and centered max-width container.
- **Outlook support throughout** — every structural macro emits `<!--[if mso | IE]>` wrappers; sections with `background-url` additionally emit a VML `v:rect` block for desktop Outlook plus `bgcolor`/`background` fallbacks for everyone else.
- **Bulletproof buttons** (`mail-button`) — `bgcolor` + `mso-padding-alt` carry Outlook, the padded `<a>` carries every other client. Prefixed to avoid clashing with bootstrap's `button`.
- **Fluid images** (`image`) — block-display image tables with optional link wrapping (`href`).
- **Dividers** (`divider`) — `border-top` paragraph plus the empty Outlook table fallback.
- **Spacers** (`spacer`) — hair-space divs that Outlook won't collapse. Keep these out of `@text`; use spacers for vertical rhythm.
- **Responsive by default** — columns stack to 100% below `breakpoint` (default `480px`) via the `.mail-column` media query. Pass explicit widths for multi-column rows (`@column("50%")` + `@column("50%")`).
- **MJML defaults, kebab-case params** — every param mirrors its MJML attribute and default, so MJML knowledge transfers directly.
- **Named or positional args** — `name=value` and `$name=value` both work, in any order, mixed with positionals. Unknown names, duplicates, and missing required args are compile errors.
- **Template data in content** — `content` params are plain expressions, so `$this`/`$app` flow in naturally (`@text("Hello, " & $this["name"] & "!")`).
- **Single file, zero dependencies** — `src/mail.timl` holds every macro; only `tim` itself is required.

## Use

```timl
@import "pkg/mail"

@mail-body($title="Welcome", $background-color="#f4f4f4", $preview="Preview text")
  @section > @column
    @text("Hello, ")
    @divider($border-color="#cccccc", $border-width="1px")
    @mail-button($label="Get started", $href="https://example.com/start", $background-color="#e74c3c")
```

See `test/welcome.timl` for a full example exercising every macro
(including a VML background section with two columns) and
`test/named.timl` for the named-arg style.

## API

Every param carries an MJML default — pass only what you override.
Names are kebab-case, mirroring MJML attributes.

| Macro | Params (in order) |
|---|---|
| `mail-body` | `width="600px"`, `background-color`, `breakpoint="480px"`, `title=""`, `preview=""` |
| `section` | `background-color`, `background-url=""`, `padding="20px 0"`, `text-align="center"`, `direction="ltr"` |
| `column` | `width="100%"`, `vertical-align="top"`, `background-color=""`, `padding="0px"` |
| `text` | `content=""`, `color`, `font-size="13px"`, `font-family`, `line-height="1"`, `align="left"`, `padding="10px 25px"` |
| `image` | `src` (required), `alt=""`, `href=""`, `width="100%"`, `align="center"`, `padding="10px 25px"` |
| `divider` | `border-color`, `border-style="solid"`, `border-width="4px"`, `width="100%"`, `align="center"`, `padding="10px 25px"` |
| `mail-button` | `label`, `href`, `background-color="#414141"`, `color="#ffffff"`, `border-radius="3px"`, `font-size="13px"`, `font-family`, `font-weight="normal"`, `inner-padding="10px 25px"`, `align="center"` |
| `spacer` | `height="20px"` |

## Call rules

- **Positional or named.** `name=value` and `$name=value` both work,
  in any order, mixed with positionals (`@section("#fff", $padding="0")`).
  Kebab-case names work (`$background-color="..."`). Unknown names,
  duplicates, and missing required args are compile errors.
- **Trailing `:` blocks hold element/macro calls.**
  A single bare literal there is statement content, never a positional
  arg — but a bare literal is not a valid statement on its own, so keep
  content in `text`'s `content` param instead of a trailing string.
  Leaf macros (`text`, `image`, `divider`, `mail-button`, `spacer`)
  are called **without** a trailing colon.
- **Inline `>` nesting.** `@section > @column` plus an indented block
  renders exactly like the nested form — the trailing block always
  belongs to the deepest node. Chains mix freely with elements
  (`@section > div`, `div > @column`) and carry args/parens
  (`@section($padding="0") > @column()`). Siblings after the chain stay
  siblings. (Plain `fn()` calls can't nest this way: `>` after a
  function is the greater-than operator, and functions have no body
  slot for a trailing block.)
- **One file.** `src/mail.timl` holds every macro: the engine drops
  transitive imports, so an aggregator re-exporting per-file modules
  resolves to an empty module.

## Email behavior

- Every structural macro emits `role="presentation"` tables plus
  `<!--[if mso | IE]>` Outlook wrappers.
- `section` with `background-url` additionally emits a VML `v:rect`
  block (dynamic URL is string-concatenated into element content,
  since raw `@html`/comment blocks cannot interpolate) plus a
  `background` attribute for non-Outlook clients.
- Columns stack below `breakpoint` via the `.mail-column` media
  query in `mail-body`'s `<style>`. Pass explicit widths for
  multi-column rows (`@column("50%")` + `@column("50%")`).
- The outer Outlook wrapper table carries no width (a static
  comment cannot interpolate one) — inner content is still
  constrained by the 600px div.

## License
This package is released under MIT license
