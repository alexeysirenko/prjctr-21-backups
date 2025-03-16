# prjctr-21-backups

```
docker compose up -d --build
docker compose exec barman barman receive-wal --create-slot postgres
docker compose exec barman barman switch-wal --archive --archive-timeout 60 postgres
docker compose exec barman barman check postgres
```
