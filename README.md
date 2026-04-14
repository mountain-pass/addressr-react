# Addressr UI

Address autocomplete components for Australian address search, powered by the [Addressr](https://addressr.io) API and the [Geocoded National Address File (G-NAF)](https://data.gov.au/dataset/ds-dga-19432f89-dc3a-4ef3-b943-5326ef1dbecc).

Pick your framework:

| Package | Install | Docs |
|---------|---------|------|
| **React** | `npm i @mountainpass/addressr-react` | [packages/react](./packages/react) |
| **Svelte** | `npm i @mountainpass/addressr-svelte` | [packages/svelte](./packages/svelte) |
| **Vue** | `npm i @mountainpass/addressr-vue` | [packages/vue](./packages/vue) |
| **Core** (any framework) | `npm i @mountainpass/addressr-core` | [packages/core](./packages/core) |

## Quick start

### React

```tsx
import { AddressAutocomplete } from '@mountainpass/addressr-react';
import '@mountainpass/addressr-react/style.css';

<AddressAutocomplete
  apiUrl="https://api.addressr.io/"
  onSelect={(address) => console.log(address)}
/>
```

### Svelte

```svelte
<script>
  import { AddressAutocomplete } from '@mountainpass/addressr-svelte';
  import '@mountainpass/addressr-svelte/style.css';
</script>

<AddressAutocomplete
  apiUrl="https://api.addressr.io/"
  onSelect={(address) => console.log(address)}
/>
```

### Vue

```vue
<script setup>
import { AddressAutocomplete } from '@mountainpass/addressr-vue';
import '@mountainpass/addressr-vue/style.css';
</script>

<template>
  <AddressAutocomplete
    api-url="https://api.addressr.io/"
    @select="(address) => console.log(address)"
  />
</template>
```

### Custom UI (any framework)

```ts
import { createAddressrClient } from '@mountainpass/addressr-core';

const client = createAddressrClient({ apiUrl: 'https://api.addressr.io/' });
const page = await client.searchAddresses('1 george st');

console.log(page.results);  // AddressSearchResult[]
console.log(page.nextLink); // Link | null — follow for next page
```

## Features

- **HATEOAS-native** -- discovers API endpoints via RFC 8288 Link headers, no hardcoded paths
- **Accessible by default** -- WCAG AA, WAI-ARIA combobox pattern, keyboard navigation, screen reader announcements
- **Infinite scroll pagination** -- loads more results as the user scrolls
- **Safe highlight rendering** -- search match highlighting via `<mark>` elements, never innerHTML
- **Headless or styled** -- use the drop-in component, or build your own UI with the headless hook/store/composable
- **Themeable** -- 17 CSS custom properties (`--addressr-*`) to match your design system
- **Customizable rendering** -- render props (React) or slots (Svelte/Vue) to override loading, empty, error, and item rendering
- **Skeleton loading** -- animated shimmer loading state, respects `prefers-reduced-motion`
- **Automatic retry** -- exponential backoff with jitter on network errors and 5xx responses
- **Form-ready** -- `name` and `required` props for native form integration
- **Framework-agnostic core** -- `@mountainpass/addressr-core` works anywhere, framework packages are thin wrappers
- **Optional API key** -- connect directly to an Addressr instance or use RapidAPI

## Monorepo structure

```
packages/
  core/     @mountainpass/addressr-core     API client, types, parseHighlight
  react/    @mountainpass/addressr-react    useAddressSearch hook + AddressAutocomplete
  svelte/   @mountainpass/addressr-svelte   createAddressSearch store + AddressAutocomplete
  vue/      @mountainpass/addressr-vue      useAddressSearch composable + AddressAutocomplete
```

## Data source

Address data from the [Geocoded National Address File (G-NAF)](https://data.gov.au/dataset/ds-dga-19432f89-dc3a-4ef3-b943-5326ef1dbecc), Australia's authoritative address database containing 15+ million addresses.

## Development

```bash
pnpm install
pnpm turbo build    # Build all packages
pnpm turbo test     # Run all tests
pnpm turbo lint     # Lint all packages
```

## License

Apache-2.0
