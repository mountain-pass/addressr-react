# @mountainpass/addressr-svelte

Svelte address autocomplete component for Australian address search, powered by [Addressr](https://addressr.io).

Part of the [addressr-ui](https://github.com/mountain-pass/addressr-ui) monorepo.

## Install

```bash
npm install @mountainpass/addressr-svelte
```

Peer dependencies: `svelte` >= 4.

## Drop-in component

```svelte
<script>
  import { AddressAutocomplete } from '@mountainpass/addressr-svelte';
  import '@mountainpass/addressr-svelte/style.css';
</script>

<AddressAutocomplete
  apiUrl="https://api.addressr.io/"
  onSelect={(address) => {
    console.log(address.sla);          // "1 GEORGE ST, SYDNEY NSW 2000"
    console.log(address.structured);   // { street, locality, state, postcode, ... }
    console.log(address.geocoding);    // { latitude, longitude, ... }
  }}
/>
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `apiKey` | `string` | -- | RapidAPI key. Omit for direct API access. |
| `onSelect` | `(address: AddressDetail) => void` | -- | Called when an address is selected |
| `label` | `string` | `"Search Australian addresses"` | Accessible label text |
| `placeholder` | `string` | `"Start typing an address..."` | Input placeholder |
| `debounceMs` | `number` | `300` | Debounce delay in milliseconds |
| `apiUrl` | `string` | `"https://addressr.p.rapidapi.com/"` | API root URL |
| `apiHost` | `string` | `"addressr.p.rapidapi.com"` | RapidAPI host header |

## Headless store

Build your own UI while keeping the search logic, debounce, pagination, and abort handling:

```svelte
<script>
  import { createAddressSearch } from '@mountainpass/addressr-svelte';

  const search = createAddressSearch({ apiUrl: 'https://api.addressr.io/' });

  function handleInput(e) {
    search.setQuery(e.target.value);
  }
</script>

<input value={$search.query} on:input={handleInput} />

{#if $search.isLoading}
  <p>Searching...</p>
{/if}

<ul>
  {#each $search.results as result}
    <li on:click={() => search.selectAddress(result.pid)}>{result.sla}</li>
  {/each}
  {#if $search.hasMore}
    <li on:click={() => search.loadMore()}>Load more...</li>
  {/if}
</ul>
```

### Store state (`$search`)

| Property | Type | Description |
|----------|------|-------------|
| `query` | `string` | Current input value |
| `results` | `AddressSearchResult[]` | Search results (accumulated across pages) |
| `isLoading` | `boolean` | Initial search in progress |
| `isLoadingMore` | `boolean` | Pagination fetch in progress |
| `hasMore` | `boolean` | More pages available |
| `error` | `Error \| null` | Latest error |
| `selectedAddress` | `AddressDetail \| null` | Selected address detail |

### Store methods

| Method | Description |
|--------|-------------|
| `setQuery(q)` | Update query (triggers debounced search) |
| `loadMore()` | Load next page of results |
| `selectAddress(pid)` | Fetch full address detail |
| `clear()` | Reset all state |
| `destroy()` | Clean up timers and abort controllers |

## Accessibility

Implements the WAI-ARIA combobox pattern:

- Full keyboard navigation (Arrow keys, Enter, Escape)
- Screen reader announcements for results count and loading state
- Visible focus indicators (3:1 contrast)
- Touch targets >= 44px
- Accessible label always present
- Infinite scroll with loading indicator

## Re-exports

This package re-exports everything from [`@mountainpass/addressr-core`](../core) for convenience -- `createAddressrClient`, `parseHighlight`, and all types.

## License

Apache-2.0
