<p align="center">
  <img src="https://raw.githubusercontent.com/tim-engine/tim/main/.github/tim_logo.png" alt="Tim - Template Engine" width="120px" height="120px"><br>
  Tim Engine library for creating responsive email templates via Tim macros<br>
</p>

<p align="center">
  <code>tim install mail</code>
</p>

## Features
- 

## Use

```timl
@import "pkg/mail"

@mail-body($title="Welcome", $background-color="#f4f4f4", $preview="Preview text"):
  @section():
    @column():
      @text("Hello, " & $this["name"] & "!")
      @divider($border-color="#cccccc", $border-width="1px")
      @mail-button($label="Get started", $href="https://example.com/start", $background-color="#e74c3c")
```

Until the `pkg/` resolver is wired in the engine, import relatively:

```timl
@import "../src/mail"
```

See `test/welcome.timl` for a full example exercising every macro.

## API

All params are positional and carry MJML defaults — pass only what
you override. Param names are kebab-case, mirroring MJML attributes.

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

`mail-button` is prefixed to avoid clashing with bootstrap's `button`
when both packages are imported.

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