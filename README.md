# Awesome S3 [![Awesome](https://awesome.re/badge-flat2.svg)](https://awesome.re)

> A curated list of systems that keep their durable state in S3-compatible object storage.

When S3 launched in 2006, its API was deliberately small: write an object, read it by key, list a bucket. S3 handled capacity, replication, hardware failure, and durability behind that API. Applications got a shared namespace that grew with their data and outlived the machines that accessed it, while paying only for storage and requests.

Early S3 was eventually consistent. A completed write could take time to appear in a read or listing, which made frequently updated database metadata difficult to coordinate. Databases therefore kept manifests and write-ahead log pointers close to compute, which meant attached disks and their own replication, repair, and rebalancing.

That changed in stages. GET, PUT, and LIST became strongly consistent in 2020; S3 Express One Zone added single-digit-millisecond access in 2023; conditional writes added create-if-absent and ETag-based compare-and-swap in 2024. S3 could now expose committed state immediately and atomically advance a metadata pointer. S3 Standard already supplied elastic capacity, usage-based pricing, and eleven nines of designed durability.

Log-structured engines fit these primitives. Write-ahead log entries and immutable segments become objects; RAM and NVMe cache hot data; compaction rewrites small updates into larger objects. Compute nodes rebuild from shared state, read capacity grows by adding workers, and storage scales independently of compute. Network latency, request charges, and cache behavior remain part of the design.

This list collects systems built around those properties.

"S3" is shorthand here for the wider object-storage model. Many projects also support Google Cloud Storage, Azure Blob Storage, Cloudflare R2, MinIO, Tigris, and other S3-compatible services.

## Contents

- [What belongs here](#what-belongs-here)
- [Databases, search, and observability](#databases-search-and-observability)
- [Streams, logs, and WALs](#streams-logs-and-wals)
- [Storage engines, caches, and libraries](#storage-engines-caches-and-libraries)
- [Filesystems and version control](#filesystems-and-version-control)
- [Table formats and lakehouses](#table-formats-and-lakehouses)
- [Reading](#reading)

## What belongs here

A project belongs when object storage is its system of record, write-ahead log, or authoritative immutable table state. It should be designed around object-store primitives and trade-offs such as conditional writes, immutable objects, batching, compaction, caching, request cost, and higher latency.

Supporting libraries, caches, and table formats belong when they are purpose-built for, or widely used by, object-native systems.

Backup-only integrations, optional archival tiers, generic S3 clients, and object-storage servers are outside the scope of this list.

## Databases, search, and observability

- [Databend](https://github.com/databendlabs/databend) - Cloud data warehouse with a unified architecture over S3-compatible storage.
- [GreptimeDB](https://github.com/GreptimeTeam/greptimedb) - Observability database for metrics, logs, and traces with object storage as its durable data layer.
- [HelixDB](https://github.com/HelixDB/helix-db) - Rust graph-vector database for AI memory and knowledge graphs that supports persistent S3-compatible storage.
- [InfluxDB 3 Core](https://github.com/influxdata/influxdb) - Diskless time-series and analytics engine that stores Apache Parquet in object storage or on local disk.
- [LanceDB](https://github.com/lancedb/lancedb) - Embedded multimodal retrieval database built on the Lance format, designed for data and indexes that live in object storage.
- [Milvus](https://github.com/milvus-io/milvus) - Distributed vector database that persists sealed segments and indexes to object storage and can use an object-native WAL.
- [NamiDB](https://github.com/namidb/namidb) - Embedded and server graph database whose S3-compatible bucket is the only source of truth.
- [Neon](https://github.com/neondatabase/neon) - Serverless PostgreSQL that separates compute from a distributed storage layer backed by object storage.
- [Omnigraph](https://github.com/ModernRelay/omnigraph) - Object-storage-native graph database with Git-style branching and merge workflows, built in Rust on Lance.
- [OpenData](https://github.com/opendata-oss/opendata) - Collection of object-native log, time-series, and vector databases built on SlateDB.
- [OpenObserve](https://github.com/openobserve/openobserve) - S3-native observability platform for logs, metrics, and traces, with WAL-backed ingesters and stateless query nodes over Parquet.
- [Parseable](https://github.com/parseablehq/parseable) - Object-storage-first observability platform that stages writes locally, stores Parquet in object storage, and scales ingestion and query nodes independently.
- [Quickwit](https://github.com/quickwit-oss/quickwit) - Cloud-native search engine that runs sub-second searches directly against immutable indexes in object storage.
- [RisingWave](https://github.com/risingwavelabs/risingwave) - Streaming database whose Hummock storage engine keeps tables, materialized views, and internal state in object storage with local caching.
- [TiDB X](https://docs.pingcap.com/tidbcloud/tidb-x-architecture/) - Distributed SQL architecture that uses object storage as the single source of truth and a shared row and columnar cache for performance.
- [turbopuffer](https://turbopuffer.com/docs/architecture) - Managed search database that keeps namespaces, indexes, queues, and WAL state in object storage behind RAM and NVMe caches.

## Streams, logs, and WALs

- [AutoMQ](https://github.com/AutoMQ/automq) - Diskless Kafka built around a shared S3 storage engine, configurable WAL, and stateless brokers.
- [Oswald](https://github.com/nvartolomei/oswald) - Experimental write-ahead log built exclusively from object-storage primitives.
- [S2](https://github.com/s2-streamstore/s2) - Durable Streams API whose open-source Lite implementation uses SlateDB and, in object-storage mode, acknowledges writes only after they are durable in the bucket.
- [WarpStream](https://www.warpstream.com/) - Diskless Kafka-compatible streaming platform that writes directly to object storage from stateless agents.
- [Woodpecker](https://github.com/zilliztech/woodpecker) - Cloud-native WAL for Milvus and other systems, with object storage as the durable log backend.

## Storage engines, caches, and libraries

- [Apache OpenDAL](https://github.com/apache/opendal) - Cross-language data-access layer that gives storage-native systems one API over S3, GCS, Azure Blob, and other backends.
- [Arrow `object_store`](https://github.com/apache/arrow-rs-object-store) - Async Rust object-store library used by DataFusion, InfluxDB, SlateDB, and other data systems.
- [Cachey](https://github.com/s2-streamstore/cachey) - Read-through cache for object storage from the S2 team.
- [EloqStore](https://github.com/eloqdata/eloqstore) - Hybrid-tier key-value engine with S3-compatible storage as the durable backend and local NVMe for low-latency reads.
- [Foyer](https://github.com/foyer-rs/foyer) - Hybrid RAM and disk cache used by object-native systems including RisingWave, SlateDB, Chroma, and ZeroFS.
- [objsync](https://github.com/dpeckett/objsync) - Distributed synchronization primitives for Go built on conditional object-store operations.
- [SlateDB](https://github.com/slatedb/slatedb) - Embedded LSM-tree that writes its WAL, SSTs, and manifests to object storage and uses local storage only as cache.
- [Tonbo](https://github.com/tonbo-io/tonbo) - Embedded database for serverless and edge runtimes with Parquet SSTables on S3 and manifest coordination through compare-and-swap.
- [WombatKV](https://github.com/Venkat2811/wombatkv) - Object-storage-native KV cache for sharing LLM prefix-cache blocks across processes, machines, and restarts.

## Filesystems and version control

- [Cursor Origin](https://cursor.com/origin) - Git storage designed like a database, with an S3-backed WAL, warm Git replicas on NVMe, and Git-aware compaction.
- [JuiceFS](https://github.com/juicedata/juicefs) - Distributed POSIX filesystem that stores file data in object storage and metadata in a separate transactional store.
- [lakeFS](https://github.com/treeverse/lakeFS) - Git-like version control layer for data in S3, GCS, and Azure Blob Storage.
- [LoonFS](https://github.com/loonfs/loonfs) - Durable filesystem whose contents, metadata history, manifests, and control state use object storage as the only durable substrate.
- [OpenData Git Server](https://github.com/opendata-oss/git-server) - Git server built on object storage and optimized for high-throughput repositories.
- [ZeroFS](https://github.com/Barre/ZeroFS) - Log-structured filesystem that exposes an S3-compatible bucket over NFS, 9P, and NBD with an object-native metadata LSM-tree.

## Table formats and lakehouses

- [Apache Hudi](https://github.com/apache/hudi) - Transactional data-lake platform for upserts, deletes, and incremental processing over object storage.
- [Apache Iceberg](https://github.com/apache/iceberg) - Open table format for large analytic datasets stored in object storage.
- [Apache Paimon](https://github.com/apache/paimon) - Lake format for streaming and batch workloads with LSM-style updates over object storage.
- [Delta Lake](https://github.com/delta-io/delta) - Transactional storage framework for lakehouse tables on cloud object stores.
- [DuckLake](https://github.com/duckdb/ducklake) - Lakehouse format that keeps metadata in a SQL database and stores table data as Parquet on local or object storage.
- [Lance](https://github.com/lance-format/lance) - Open lakehouse format for multimodal AI with vector search, random access, versioning, and object-store support.

## Reading

- [Amazon S3](https://aws.amazon.com/s3/) - AWS's overview of S3's elastic capacity, usage-based model, and eleven nines of designed durability.
- [Amazon S3 launch announcement](https://press.aboutamazon.com/2006/3/amazon-web-services-launches) - The original 2006 description of its minimal object API, unlimited objects, and usage-based pricing.
- [Amazon S3 is now strongly consistent](https://aws.amazon.com/blogs/aws/amazon-s3-update-strong-read-after-write-consistency/) - The 2020 change that made every GET, PUT, and LIST operation strongly consistent.
- [Building a Database on S3](https://cs.brown.edu/people/tkraska/pub/sigmod08-s3.pdf) - The 2008 SIGMOD paper that explored transactions with stateless clients, S3 pages, and SQS as the log.
- [Conditional writes in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/conditional-writes.html) - The compare-and-swap-style primitives behind many object-native coordination protocols.
- [Git at any scale](https://cursor.com/blog/git-at-any-scale) - Cursor's account of building Origin around an S3 WAL, Git replicas, and compaction.
- [S3 Express One Zone](https://aws.amazon.com/s3/storage-classes/express-one-zone/) - A single-Availability-Zone storage class with single-digit-millisecond access for latency-sensitive workloads.
- [S3 is the new network](https://www.pingcap.com/blog/s3-new-network-cloud-object-storage-database-architecture/) - PingCAP on the object-storage-first design of TiDB X.
- [SlateDB design](https://slatedb.io/docs/design/overview/) - How an LSM-tree changes when the durable medium is an object store rather than a local disk.
- [turbopuffer: fast search on object storage](https://turbopuffer.com/blog/turbopuffer) - The economics and cache hierarchy behind object-native search.
- [Zero Disks is Better for Kafka](https://www.warpstream.com/blog/zero-disks-is-better-for-kafka) - WarpStream's case for replacing replicated broker disks with object storage.

## Contributing

Contributions are welcome. Please read the [contribution guidelines](CONTRIBUTING.md) before opening a pull request.
