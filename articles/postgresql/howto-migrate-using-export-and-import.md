---
title: Migrera en databas - Azure Database för PostgreSQL - Single Server
description: Beskriver hur du extraherar en PostgreSQL-databas till en skriptfil och importerar data till måldatabasen från den filen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770211"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrera din PostgreSQL-databas med export och import
Du kan använda [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) för att extrahera en PostgreSQL-databas till en skriptfil och [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att importera data till måldatabasen från den filen.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md) med brandväggsregler för att tillåta åtkomst och databas under den.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) kommandoradsverktyget installerat
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) kommandoradsverktyget installerat

Följ dessa steg för att exportera och importera din PostgreSQL-databas.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Skapa en skriptfil med pg_dump som innehåller de data som ska läsas in
Om du vill exportera din befintliga PostgreSQL-databas lokalt eller i en virtuell dator till en SQL-skriptfil kör du följande kommando i din befintliga miljö:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Om du till exempel har en lokal server och en databas som heter **testdb** i den:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importera data på mål Azure Database för PostgreSQL
Du kan använda psql-kommandoraden och parametern --dbname parameter (-d) för att importera data till Azure-databasen för PostgreSQL-server och läsa in data från SQL-filen.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
I det här exemplet används psql-verktyget och en skriptfil med namnet **testdb.sql** från föregående steg för att importera data till databasen **mypgsqldb** på målservern **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Nästa steg
- Information om hur du migrerar en PostgreSQL-databas med dump och återställning finns i [Migrera din PostgreSQL-databas med dump och återställning](howto-migrate-using-dump-and-restore.md).
- Mer information om hur du migrerar databaser till Azure Database för PostgreSQL finns i [guiden För databasmigrering](https://aka.ms/datamigration). 
