# anagolay-catalog

OurBox application catalog for [Macula](https://github.com/johnbenac/macula) —
an IPFS-backed media hosting suite with image transforms and a web UI.

This repository follows the OurBox application catalog repository pattern
defined in [sw-ourbox-os](https://github.com/techofourown/sw-ourbox-os).

Shared tooling scripts are pulled automatically at CI time via `bootstrap.sh`
from the catalog-tooling OCI artifact. They are not checked into the repo.

## Apps

| App UID | Display name | Service | Port | Hostname |
|---|---|---|---|---|
| `anagolay/macula` | Macula | `macula-ui` | 80 | `macula.{box_host}` |

## Image sources

Images published by [`johnbenac/macula`](https://github.com/johnbenac/macula)
and consumed by this catalog:

- `ghcr.io/johnbenac/macula/macula-ui:latest`
- `ghcr.io/johnbenac/macula/macula-api:latest`

The `render-catalog-bundle.sh` script (pulled at CI time via bootstrap) resolves
these to digest-pinned refs at publish time. The checked-in `image-sources.json`
uses `:latest` tags; the generated `dist/images.lock.json` contains pinned refs only.

## Catalog bundle

On every push to `main`, the publish workflow:

1. runs `bootstrap.sh` to pull shared tooling scripts from the catalog-tooling OCI artifact
2. resolves all `:latest` image refs to digest-pinned refs via `oras resolve`
3. renders `catalog.json + images.lock.json + profile.env + manifest.env` into
   `dist/application-catalog-bundle.tar.gz`
4. publishes the bundle to `ghcr.io/johnbenac/anagolay-catalog`
5. updates the catalog index at `:catalog-amd64`

To use this catalog in `prepare-installer-media.sh`, choose the `c` option at
the application catalog selection step, skip or keep any official catalogs you
want, then enter this custom catalog ref:

```
ghcr.io/johnbenac/anagolay-catalog:catalog-amd64
```

If you want a specific pinned bundle, use the digest-pinned ref from the
latest CI run's publish record.

## References

- [App authoring guide](https://github.com/techofourown/sw-ourbox-os/blob/main/docs/reference/app-authoring-guide.md)
- [Application catalog repository contract](https://github.com/techofourown/sw-ourbox-os/blob/main/docs/reference/application-catalog-repository-contract.md)
- [Application catalog repo template](https://github.com/techofourown/sw-ourbox-os/tree/main/templates/application-catalog-repo)
- [Macula apps repo](https://github.com/johnbenac/macula)
