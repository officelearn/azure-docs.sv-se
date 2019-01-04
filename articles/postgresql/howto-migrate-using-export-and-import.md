---
title: Migrera en databas med importera och exportera i Azure Database för PostgreSQL
description: Beskriver hur extrahera en PostgreSQL-databas till en skriptfil och importera data till måldatabasen från filen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: ecd7dc225379fc9d3eda6fb2e80e3c47a73db49b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547634"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrera din PostgreSQL-databas med exportera och importera
Du kan använda [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) att extrahera en PostgreSQL-databas till en skriptfil och [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) att importera data till måldatabasen från filen.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- En [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md) med brandväggsregler som tillåter åtkomst och databasen under den.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) kommandoradsverktyget installerad
- [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) kommandoradsverktyget installerad

Följ stegen nedan för att exportera och importera PostgreSQL-databasen.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Skapa en skriptfil med pg_dump som innehåller data som ska hämtas
Att exportera dina befintliga PostgreSQL-databasen lagras lokalt eller i en virtuell dator till en sql-skriptfilen, kör du följande kommando i din befintliga miljö:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Exempel: Om du har en lokal server och en databas som heter **testdb** i den:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importera data i Azure-måldatabas för PostgreSQL
Du kan använda psql-kommandoraden och parametern--dbname (-d) att importera data till Azure Database for PostgreSQL-server och läsa in data från sql-filen.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Det här exemplet används psql-verktyget och en skriptfil med namnet **testdb.sql** från föregående steg för att importera data till databasen **mypgsqldb** på målservern  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med säkerhetskopiering och återställning, se [migrera din PostgreSQL-databas med säkerhetskopiering och återställning](howto-migrate-using-dump-and-restore.md).
- Mer information om hur du migrerar databaser till Azure Database för PostgreSQL, finns i den [Guide för Databasmigrering](https://aka.ms/datamigration). 
