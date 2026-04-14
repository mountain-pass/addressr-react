# @mountainpass/addressr-vue

Vue address autocomplete component for Australian address search, powered by [Addressr](https://addressr.io).

Part of the [addressr-ui](https://github.com/mountain-pass/addressr-ui) monorepo.

## Install

```bash
npm install @mountainpass/addressr-vue
```

Peer dependencies: `vue` >= 3.3.

## Drop-in component

```vue
<script setup>
import { AddressAutocomplete } from '@mountainpass/addressr-vue';
import '@mountainpass/addressr-vue/style.css';

function handleSelect(address) {
  console.log(address.sla);          // "1 GEORGE ST, SYDNEY NSW 2000"
  console.log(address.structured);   // { street, locality, state, postcode, ... }
  console.log(address.geocoding);    // { latitude, longitude, ... }
}
</script>

<template>
  <AddressAutocomplete
    api-url="https://api.addressr.io/"
    @select="handleSelect"
  />
</template>
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `apiKey` | `string` | -- | RapidAPI key. Omit for direct API access. |
| `label` | `string` | `"Search Australian addresses"` | Accessible label text |
| `placeholder` | `string` | `"Start typing an address..."` | Input placeholder |
| `debounceMs` | `number` | `300` | Debounce delay in milliseconds |
| `apiUrl` | `string` | `"https://addressr.p.rapidapi.com/"` | API root URL |
| `apiHost` | `string` | `"addressr.p.rapidapi.com"` | RapidAPI host header |

### Events

| Event | Payload | Description |
|-------|---------|-------------|
| `select` | `AddressDetail` | Emitted when an address is selected |

## Headless composable

Build your own UI while keeping the search logic, debounce, pagination, and abort handling:

```vue
<script setup>
import { useAddressSearch } from '@mountainpass/addressr-vue';

const {
  query, setQuery,
  results, isLoading,
  hasMore, loadMore, isLoadingMore,
  selectedAddress, selectAddress,
  error, clear,
} = useAddressSearch({ apiUrl: 'https://api.addressr.io/' });
</script>

<template>
  <input :value="query" @input="setQuery($event.target.value)" />
  <ul>
    <li v-for="r in results" :key="r.pid" @click="selectAddress(r.pid)">
      {{ r.sla }}
    </li>
    <li v-if="hasMore" @click="loadMore()">Load more...</li>
  </ul>
</template>
```

### Return values

All return values are Vue `Ref`s (use `.value` in script, auto-unwrapped in templates):

| Property | Type | Description |
|----------|------|-------------|
| `query` | `Ref<string>` | Current input value |
| `results` | `Ref<AddressSearchResult[]>` | Search results (accumulated across pages) |
| `isLoading` | `Ref<boolean>` | Initial search in progress |
| `isLoadingMore` | `Ref<boolean>` | Pagination fetch in progress |
| `hasMore` | `Ref<boolean>` | More pages available |
| `error` | `Ref<Error \| null>` | Latest error |
| `selectedAddress` | `Ref<AddressDetail \| null>` | Selected address detail |

### Methods

| Method | Description |
|--------|-------------|
| `setQuery(q)` | Update query (triggers debounced search) |
| `loadMore()` | Load next page of results |
| `selectAddress(pid)` | Fetch full address detail |
| `clear()` | Reset all state |

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
