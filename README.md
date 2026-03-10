# mkpipe-loader-sqlite

SQLite loader plugin for [MkPipe](https://github.com/mkpipe-etl/mkpipe). Writes Spark DataFrames into SQLite database files via JDBC.

## Documentation

For more detailed documentation, please visit the [GitHub repository](https://github.com/mkpipe-etl/mkpipe).

## License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details.

---

## Connection Configuration

```yaml
connections:
  sqlite_target:
    variant: sqlite
    database: /full/path/to/mydb.db
```

---

## Table Configuration

```yaml
pipelines:
  - name: pg_to_sqlite
    source: pg_source
    destination: sqlite_target
    tables:
      - name: public.users
        target_name: stg_users
        replication_method: full
        batchsize: 5000
```

---

## Write Throughput

```yaml
      - name: public.users
        target_name: stg_users
        replication_method: full
        batchsize: 5000
```

### Performance Notes

- SQLite uses file-level locking — **`write_partitions` has no benefit** here as concurrent writes will serialize or fail. Set `write_partitions: 1` to avoid contention.
- Keep `batchsize` moderate (1,000–10,000) — large transactions in SQLite can cause memory pressure.
- SQLite is best suited for small-to-medium outputs (local development, testing, lightweight pipelines).

---

## All Table Parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `name` | string | required | Source table name |
| `target_name` | string | required | SQLite destination table name |
| `replication_method` | `full` / `incremental` | `full` | Replication strategy |
| `batchsize` | int | `10000` | Rows per JDBC batch insert |
| `write_partitions` | int | — | Set to `1` to avoid SQLite lock contention |
| `dedup_columns` | list | — | Columns used for `mkpipe_id` hash deduplication |
| `tags` | list | `[]` | Tags for selective pipeline execution |
| `pass_on_error` | bool | `false` | Skip table on error instead of failing |