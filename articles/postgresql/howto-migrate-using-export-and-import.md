---
title: Migrera en databas med Import och Export i Azure-databas för PostgreSQL
description: Beskriver hur extrahera en PostgreSQL-databas till en skriptfil och importera data till måldatabasen från filen.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 5250b10c2d9645e25c5b6f6f0f34daa50608e51b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736652"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrera din PostgreSQL-databas med hjälp av export och import
Du kan använda [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) att extrahera en PostgreSQL-databas till en skriptfil och [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) att importera data till måldatabasen från filen.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- En [Azure-databas för PostgreSQL server](quickstart-create-server-database-portal.md) med brandväggsregler som tillåter åtkomst och databasen under den.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) kommandoradsverktyget installerad
- [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) kommandoradsverktyget installerad

Följ dessa steg om du vill exportera och importera PostgreSQL-databas.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Skapa en skriptfil med pg_dump som innehåller data som ska hämtas
Exportera dina befintliga PostgreSQL-databas lokalt eller i en virtuell dator till en sql-skriptfilen kör du följande kommando i din befintliga miljö:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Till exempel om du har en lokal server och en databas som heter **testdb** i den:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importera data på målet Azure-databas för PostgreSQL
Du kan använda kommandoraden psql och--dbname-parameter (-d) att importera data till Azure-databas för PostgreSQL-servern och Läs in data från sql-filen.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Det här exemplet används psql verktyget och en skriptfil med namnet **testdb.sql** från föregående steg att importera data till databasen **mypgsqldb** på målservern  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med hjälp av dump och återställning finns [migrera din PostgreSQL-databas med hjälp av dump och Återställ](howto-migrate-using-dump-and-restore.md).
- Mer information om hur du migrerar databaser till Azure-databas för PostgreSQL, finns det [databasen Migreringsguide](http://aka.ms/datamigration). 
