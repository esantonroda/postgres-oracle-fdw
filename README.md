# Intro
All the oracle client library and FDW extention is in the _sdk_ bacause is not possbile to get it through wget from Oracle site.
If you need another version download it from [Oracle site](https://www.oracle.com/database/technologies/instant-client/downloads.html) an put it into sdk folder.
oracle_fdw zip file download from [oracle_fdw](https://github.com/laurenz/oracle_fdw/releases)
# Build image
```console
docker build -t postgres-ora-fdw:14 .
```
## Supported args

| Arg                   | default       |
| -------------         | ------------- |
| postgres_version      | 14            |
| oracle_fdw_version    | 2_4_0         |
| instantclient_version | 19_3          |

*Example*
```console
docker build --build-arg postgres_version=14 -t postgres-ora-fdw:14 .
```
# Run image
```console
docker run -d  --name test-postgres-ora-fdw \
-e POSTGRES_PASSWORD=mysecretpassword \
-p 5432:5432 \
postgres-ora-fdw:14
```

# Create Foreign Data Wrapper
Enter into container
```console
docker exec -it test-postgres-ora-fdw bash
```

then

```console
psql
```

then create the extension and the connection to remote DB

```console
CREATE EXTENSION oracle_fdw;
CREATE SERVER oradb FOREIGN DATA WRAPPER oracle_fdw
          OPTIONS (dbserver '//<host>:1521/<database>');
GRANT USAGE ON FOREIGN SERVER oradb TO postgres;
CREATE USER MAPPING FOR postgres SERVER oradb OPTIONS (user 'user', password 'password');
```
then import table (this is like creating a symbolic link to remote database, no data is imported)
```console
IMPORT FOREIGN SCHEMA "<foreignSchemaName>"
    FROM SERVER oradb
    INTO <localSchemaName>;
```

more info on [PostgreSQL docs](https://www.postgresql.org/docs/14/sql-importforeignschema.html).
