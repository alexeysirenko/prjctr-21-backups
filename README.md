# prjctr-21-backups

```
docker compose up -d
PGPASSWORD=postgres docker exec -i postgresql-b psql -U postgres -d books_db < init.sql
docker exec -it app python write_data.py 1000 --batch-size 100
```

## Full backup

```
docker exec -it postgresql-b mkdir /backups/full
PGPASSWORD=postgres docker exec -t postgresql-b pg_dump -U postgres -d books_db -f /backups/full/books_db.sql
docker exec -it postgresql-b ls -lh /backups/full/
```
