# prjctr-21-backups

```
docker compose up -d
PGPASSWORD=postgres docker exec -i postgresql-b psql -U postgres -d books_db < init.sql
docker exec -it app python write_data.py 1000 --batch-size 100
```
