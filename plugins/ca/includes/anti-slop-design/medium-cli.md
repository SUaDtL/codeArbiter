# anti-slop-design · medium: CLI / terminal output

Load for output rendered in a terminal: status lines, progress, reports printed to stdout, log lines,
TUI panels. Pair with `core`. Type/color/layout leaves mostly do not apply (the terminal owns the
font; color is a constrained palette).

## 7.F CLI / terminal output

- **The terminal is a constrained medium; respect it.** Variable width, a 16/256/truecolor palette
  that the user's theme controls, monospace only, and a reader who often pipes or greps the output.
- **Degrade without color.** Never let color be the only carrier of meaning (core 3.B/§5 spirit): a
  red number must also read as bad when color is stripped. Honor `NO_COLOR`; detect a non-TTY pipe and
  drop ANSI (or emit plain) so piped output stays parseable.
- **Width is unknown.** Fit to the reported width with a margin; never assume 80. A line that wraps in
  a narrow terminal corrupts a box or table. Clamp and truncate deterministically.
- **Glyph width is real.** CJK and many emoji are two columns; combining marks are zero. Count visible
  columns, not characters, or box-drawing and alignment break.
- **No decoration tax.** Emoji sprinkled per line, gratuitous box-drawing, and rainbow ANSI are the
  terminal equivalent of clip art. One accent color, aligned columns, and whitespace carry it.
- **Copy laws still apply.** core §3.A/§3.B hold for any prose in help text, errors, and summaries.
  Error messages say what happened and what to do, not "an error occurred."

## Tells (CLI)

- Color as the sole signal; no `NO_COLOR` / non-TTY fallback.
- Hardcoded 80-column assumptions; lines that wrap and corrupt a box.
- Character-count math that misaligns on CJK/emoji width.
- Emoji or box-drawing as decoration rather than structure.
- Vague error strings ("something went wrong").

## Pre-flight slice (CLI)

- [ ] Renders correctly at narrow and wide widths; no wrap corruption.
- [ ] Plain/parseable output when piped or `NO_COLOR` is set; color never the only signal.
- [ ] Column math counts visible width (CJK/emoji safe).
- [ ] Decoration earns its place; errors are actionable.
