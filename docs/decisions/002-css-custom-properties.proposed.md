---
status: "proposed"
date: 2026-04-14
decision-makers: [Tom Howard, Claude Code]
consulted: [wr-architect:agent, wr-style-guide:agent]
informed: []
---

# Use CSS custom properties for component theming

## Context and Problem Statement

The AddressAutocomplete component ships with hardcoded CSS values (colors, spacing, typography) across three framework packages (React CSS Modules, Svelte scoped styles, Vue scoped styles). Consumers cannot customize the appearance without copying the entire component or using `!important` overrides. Agencies building for clients need to match brand guidelines, and the current all-or-nothing styling prevents this.

## Decision Drivers

- Agencies need brand theming without forking components
- Three framework packages must share a consistent token set
- Consumers should be able to override selectively (e.g., just border color)
- Defaults must preserve current appearance (non-breaking change)
- CSS custom properties inherit through the DOM regardless of scoping strategy

## Considered Options

1. **CSS custom properties with `var(--addressr-*, default)`**
2. **Component props for each style value** (e.g., `borderColor`, `focusColor`)
3. **CSS-in-JS theme object** (e.g., `theme={{ borderColor: '#000' }}`)
4. **CSS `::part()` pseudo-elements** (Shadow DOM only)

## Decision Outcome

Chosen option: **1 — CSS custom properties**, because they work across all three scoping strategies (CSS Modules, Svelte scoped, Vue scoped), require zero JavaScript API changes, and follow web platform standards. Consumers set properties on any ancestor element to override defaults.

### Token naming convention

All tokens use the `--addressr-` prefix. Names describe purpose, not visual appearance.

### Token list

| Property | Default | Usage |
|----------|---------|-------|
| `--addressr-font-family` | `system-ui, -apple-system, sans-serif` | All text |
| `--addressr-border-color` | `#767676` | Input/menu borders |
| `--addressr-border-radius` | `0.25rem` | Corners |
| `--addressr-focus-color` | `#005fcc` | Focus ring |
| `--addressr-highlight-bg` | `#e8f0fe` | Hovered option bg |
| `--addressr-bg` | `#fff` | Menu bg |
| `--addressr-text-color` | `inherit` | Primary text |
| `--addressr-muted-color` | `#555` | Loading/no-results |
| `--addressr-error-color` | `#d32f2f` | Error text |
| `--addressr-mark-color` | `inherit` | Highlight mark text |
| `--addressr-mark-weight` | `700` | Highlight mark weight |
| `--addressr-padding-x` | `0.75rem` | Horizontal padding |
| `--addressr-padding-y` | `0.625rem` | Vertical padding |
| `--addressr-shadow` | `0 4px 6px rgba(0,0,0,0.1)` | Menu shadow |
| `--addressr-z-index` | `1000` | Menu stacking |

The token list is extensible — future features (e.g., skeleton animation) may add tokens with the same `--addressr-` prefix convention.

## Consequences

### Good

- Consumers can selectively override any visual token via CSS
- Works identically across React CSS Modules, Svelte scoped styles, and Vue scoped styles
- Zero JavaScript API changes — no new props, no breaking changes
- Defaults preserve current appearance exactly
- Standard web platform mechanism — no library dependency

### Neutral

- Token names become a compatibility surface once published
- Consumers need to know CSS custom property syntax

### Bad

- JSDOM has limited CSS custom property support, making visual testing harder (mitigated by static analysis tests)
- Consumers overriding `--addressr-highlight-bg` must ensure their mark text still meets 4.5:1 contrast

## Confirmation

- All hardcoded color/spacing values in CSS replaced with `var(--addressr-*, <default>)`
- No raw hardcoded hex values remain in component CSS (verified by static analysis tests)
- Visual appearance unchanged when no custom properties are set
- `pnpm build` produces valid CSS for all three packages

## Pros and Cons of the Options

### CSS custom properties

- Good: Works across all scoping strategies
- Good: Zero JS API surface
- Good: Web platform standard
- Bad: Limited JSDOM testing support
- Bad: Token names are a compatibility commitment

### Component props

- Good: Type-safe, discoverable via IDE
- Bad: Explodes the prop count (15+ style props)
- Bad: Different API per framework (React props vs Svelte props vs Vue props)
- Bad: Requires JavaScript bridge between props and CSS

### CSS-in-JS theme object

- Good: Type-safe theme object
- Bad: Requires runtime CSS generation or style injection
- Bad: Not idiomatic for Svelte/Vue
- Bad: Adds bundle size

### CSS `::part()`

- Good: Granular styling control
- Bad: Requires Shadow DOM — not used by any of the three packages
- Bad: Poor browser support for complex selectors

## Reassessment Criteria

- If Shadow DOM adoption increases and the components migrate to web components, reconsider `::part()`
- If the token list grows beyond 25 properties, consider grouping into sub-namespaces
- If a shared design system with its own token convention is adopted upstream
