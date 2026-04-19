---
'@mountainpass/addressr-core': minor
---

Add `searchPostcodes`, `searchLocalities`, and `searchStates` methods to `createAddressrClient`. Each method mirrors `searchAddresses` (debounce, abort, retry, HATEOAS root discovery) but targets the new narrower endpoints via distinct rel URIs.

New public types: `PostcodeSearchResult`, `LocalitySearchResult`, `StateSearchResult`.

`SearchPage` is now generic: `SearchPage<T = AddressSearchResult>`. Existing consumers referencing the concrete `SearchPage` type remain source-compatible; the default type argument preserves behaviour.

Internally extracted a private `searchByRel<T>(rel, query, signal)` helper that holds the shared search body; `searchAddresses` now delegates to it. No change to existing `searchAddresses` behaviour or signature.

See ADR 006 for the architectural rationale.

**Note**: consumers behind RapidAPI may need to wait for the edge cache to refresh before the new rels become discoverable from the API root. See PROB-004.
