---
title: "Migrera en databas med Import och Export i Azure-databas för PostgreSQL | Microsoft Docs"
description: "Beskriver hur extrahera en PostgreSQL-databas till en skriptfil och importera data till måldatabasen från filen."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 937423d0b844f6ce30e532bbae12b85071a6f8d4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrera din PostgreSQL-databas med hjälp av export och import
Du kan använda [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) att extrahera en PostgreSQL-databas till en skriptfil och [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) att importera data till måldatabasen från filen.

## <a name="prerequisites"></a>Krav
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

## <a name="import-the-data-on-target-azure-database-for-postrgesql"></a>Importera data på målet Azure-databas för PostrgeSQL
Du kan använda kommandoraden psql och--dbname-parameter (-d) att importera data till Azure-databas för PostgreSQL-servern och Läs in data från sql-filen.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Det här exemplet används psql verktyget och en skriptfil med namnet **testdb.sql** från föregående steg att importera data till databasen **mypgsqldb** på målservern  **mypgserver 20170401.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med hjälp av dump och återställning finns [migrera din PostgreSQL-databas med hjälp av dump och återställning](howto-migrate-using-dump-and-restore.md)
