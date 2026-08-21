# Contributing

Thanks for helping make Awesome S3 useful.

## Inclusion criteria

A project must use S3-compatible object storage as one of the following:

- the authoritative system of record;
- the durable write-ahead log; or
- the authoritative store for immutable table or index state.

The architecture should be intentionally designed around object-store behavior: immutable objects, conditional writes, batching, caching, compaction, request costs, or high-latency access.

Please do not submit:

- backup, export, or archival integrations;
- systems where object storage is only an optional cold tier;
- generic S3 SDKs or command-line clients;
- S3-compatible storage servers;
- abandoned demos without a usable implementation or substantial design document; or
- affiliate links, referral links, tracking parameters, or marketing copy.

Both open-source and commercial projects are welcome when their object-storage architecture is publicly documented.

## Pull requests

- Add one project per pull request.
- Put it in the most specific section and preserve alphabetical order.
- Link the project name to its canonical repository, documentation, or product page.
- Write one factual sentence explaining what it does and how object storage is architectural.
- Link primary architecture evidence in the pull-request description.
- Do not add stars, funding, customer counts, benchmarks, or other fast-changing claims.
- Check spelling and every changed link before submitting.

Use this format:

```markdown
- [Project](https://example.com) - What it does and why object storage is part of its primary durability model.
```

## Adding reading or X posts

Reading material should teach a reusable architecture, primitive, or trade-off. X posts should add a concrete technical explanation or capture a meaningful part of the current discussion, rather than simply announcing a product.
