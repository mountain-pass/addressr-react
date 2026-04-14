# @mountainpass/addressr-react

React address autocomplete component for Australian address search, powered by [Addressr](https://addressr.io).

Part of the [addressr-ui](https://github.com/mountain-pass/addressr-ui) monorepo.

## Install

```bash
npm install @mountainpass/addressr-react
```

Peer dependencies: `react` >= 18, `react-dom` >= 18.

## Drop-in component

```tsx
import { AddressAutocomplete } from '@mountainpass/addressr-react';
import '@mountainpass/addressr-react/style.css';

function MyForm() {
  return (
    <AddressAutocomplete
      apiUrl="https://api.addressr.io/"
      onSelect={(address) => {
        console.log(address.sla);          // "1 GEORGE ST, SYDNEY NSW 2000"
        console.log(address.structured);   // { street, locality, state, postcode, ... }
        console.log(address.geocoding);    // { latitude, longitude, ... }
      }}
    />
  );
}
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `apiKey` | `string` | -- | RapidAPI key. Omit for direct API access. |
| `onSelect` | `(address: AddressDetail) => void` | **required** | Called when an address is selected |
| `label` | `string` | `"Search Australian addresses"` | Accessible label text |
| `placeholder` | `string` | `"Start typing an address..."` | Input placeholder |
| `className` | `string` | -- | Additional CSS class for the wrapper |
| `debounceMs` | `number` | `300` | Debounce delay in milliseconds |
| `apiUrl` | `string` | `"https://addressr.p.rapidapi.com/"` | API root URL |
| `apiHost` | `string` | `"addressr.p.rapidapi.com"` | RapidAPI host header |

## Headless hook

Build your own UI while keeping the search logic, debounce, pagination, and abort handling:

```tsx
import { useAddressSearch } from '@mountainpass/addressr-react';

function MyCustomAutocomplete() {
  const {
    query, setQuery,
    results, isLoading,
    hasMore, loadMore, isLoadingMore,
    selectedAddress, selectAddress,
    error, clear,
  } = useAddressSearch({ apiUrl: 'https://api.addressr.io/' });

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ul>
        {results.map((r) => (
          <li key={r.pid} onClick={() => selectAddress(r.pid)}>{r.sla}</li>
        ))}
        {hasMore && <li onClick={loadMore}>Load more...</li>}
      </ul>
    </div>
  );
}
```

### Return values

| Property | Type | Description |
|----------|------|-------------|
| `query` | `string` | Current input value |
| `setQuery` | `(q: string) => void` | Update query (triggers debounced search) |
| `results` | `AddressSearchResult[]` | Search results (accumulated across pages) |
| `isLoading` | `boolean` | Initial search in progress |
| `isLoadingMore` | `boolean` | Pagination fetch in progress |
| `hasMore` | `boolean` | More pages available |
| `loadMore` | `() => Promise<void>` | Load next page of results |
| `error` | `Error \| null` | Latest error |
| `selectedAddress` | `AddressDetail \| null` | Selected address detail |
| `selectAddress` | `(pid: string) => Promise<void>` | Fetch full address detail |
| `clear` | `() => void` | Reset all state |

## Accessibility

Built with [downshift](https://www.downshift-js.com/) for WAI-ARIA combobox pattern compliance:

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
