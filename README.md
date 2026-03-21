# anagolay-catalog

OurBox application catalog for [Macula](https://github.com/johnbenac/macula) —
an IPFS-backed media hosting suite with image transforms and a web UI.

This repository follows the OurBox application catalog repository pattern
defined in [sw-ourbox-os](https://github.com/techofourown/sw-ourbox-os).

## Apps

| App UID | Display name | Service | Port | Hostname |
|---|---|---|---|---|
| `anagolay/macula` | Macula | `macula-ui` | 80 | `macula.{box_host}` |

## Image sources

Images published by [`johnbenac/macula`](https://github.com/johnbenac/macula)
and consumed by this catalog:

- `ghcr.io/johnbenac/macula/macula-ui:latest`
- `ghcr.io/johnbenac/macula/macula-api:latest`

The `render-catalog-bundle.sh` script resolves these to digest-pinned refs at
publish time. The checked-in `image-sources.json` uses `:latest` tags; the
generated `dist/images.lock.json` contains pinned refs only.

## Catalog bundle

On every push to `main`, the publish workflow:

1. resolves all `:latest` image refs to digest-pinned refs via `oras resolve`
2. renders `catalog.json + images.lock.json + profile.env + manifest.env` into
   `dist/application-catalog-bundle.tar.gz`
3. publishes the bundle to `ghcr.io/johnbenac/anagolay-catalog`
4. updates the catalog index at `:catalog-amd64`

To use this catalog in `prepare-installer-media.sh`, choose the `c` option at
the application catalog selection step, skip or keep any official catalogs you
want, then enter this custom catalog ref:

```
ghcr.io/johnbenac/anagolay-catalog:catalog-amd64
```

Prefer the catalog index ref above for installer selection. It lets the
installer resolve the newest stable bundle whose
`OURBOX_PLATFORM_CONTRACT_DIGEST` matches the selected OS payload contract.

A floating bundle tag such as `:latest` points at one concrete bundle only, so
it can be rejected when that bundle was published against a different platform
contract digest than the selected OS payload.

If you intentionally want one exact bundle, use the digest-pinned ref from the
latest CI run's publish record instead.

## References

- [App authoring guide](https://github.com/techofourown/sw-ourbox-os/blob/main/docs/reference/app-authoring-guide.md)
- [Application catalog repository contract](https://github.com/techofourown/sw-ourbox-os/blob/main/docs/reference/application-catalog-repository-contract.md)
- [Application catalog repo template](https://github.com/techofourown/sw-ourbox-os/tree/main/templates/application-catalog-repo)
- [Macula apps repo](https://github.com/johnbenac/macula)
