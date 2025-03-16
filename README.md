# prjctr-21-backups

PostgreSQL backups using [Barman](https://pgbarman.org/)

## Setup

```
docker compose up -d --build
PGPASSWORD=postgres docker exec -i postgres psql -U postgres -d books_db < init.sql
docker compose exec barman barman receive-wal --create-slot postgres
docker compose exec barman barman switch-wal --archive --archive-timeout 60 postgres
docker compose exec barman barman check postgres
docker exec -it app python write_data.py 1000 --batch-size 100
```

## Run full backup

```
docker compose exec barman barman backup postgres
docker compose exec barman barman list-backup postgres
```

Expected output (example):

```
postgres 20250316T203755 - Sun Mar 16 20:37:56 2025 - Size: 49.7 MiB - WAL Size: 0 B
```

To schedule automatic backups run:

```
docker compose exec barman bash -c "echo '0 0 * * * barman backup postgres' | crontab -"
```

## Setup incremental backups via WAL streaming

```
docker compose exec barman barman cron
docker compose exec barman barman receive-wal postgres &
docker compose exec barman barman replication-status postgres
```

Expected output (example):

```
Status of streaming clients for server 'postgres':
  Current LSN: 0/3000060
  Received LSN: 0/3000060
  latest WAL file: 000000010000000000000003
```

## Differential backups

Differential backups are automatically handled by PostgreSQL streaming. Barman continuously streams WAL files, capturing every change that occurs since the last full backup.

## Reverse delta backups

Reverse Delta is the default backup storage model used by Barman:

- Barman stores the latest full backup as a complete copy of the database (latest backup is always "full").
- Older backups are stored as reverse deltas, containing only the differences going backwards from the latest backup.

To see reverse-delta structure explicitly:

```
docker compose exec barman barman show-backup postgres latest
```

## Continuous Data Protection (CDP)

Barman achieves CDP explicitly using WAL streaming

To explicitly restore to an exact timestamp:

```
docker compose exec barman \
  barman recover postgres latest /var/lib/postgresql/data \
  --target-time "2025-03-17 11:22:00"
```
