---
title: "Dump och återställa i Azure-databas för PostgreSQL"
description: "Beskriver hur du extrahera en PostgreSQL-databas till en dumpfil och återställa från en fil som skapats av pg_dump i Azure-databas för PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6ea839c10bffc9a024af38132081f2c9bd7dfc0a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrera din PostgreSQL-databas med hjälp av dump och återställning
Du kan använda [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) att extrahera en PostgreSQL-databas till en dumpfil och [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) att återställa PostgreSQL-databas från en fil skapas av pg_dump.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- En [Azure-databas för PostgreSQL server](quickstart-create-server-database-portal.md) med brandväggsregler som tillåter åtkomst och databasen under den.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) kommandoradsverktyg som installerats

Följ dessa steg för att dumpa och återställa PostgreSQL-databas:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Skapa en dumpfil med pg_dump som innehåller data som ska hämtas
Om du vill säkerhetskopiera en befintlig PostgreSQL-databas lokalt eller i en virtuell dator, kör du följande kommando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Till exempel om du har en lokal server och en databas som heter **testdb** i den
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Återställa data till mål-Azure-databas för PostrgeSQL med pg_restore
När du har skapat måldatabasen, kan du använda kommandot pg_restore och -d,--dbname-parametern för att återställa data i måldatabasen från dumpfilen.
```bash
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
I det här exemplet kan du återställa data från dumpfilen **testdb.dump** i databasen **mypgsqldb** på målservern **mydemoserver.postgres.database.azure.com**.
```bash
pg_restore -v --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med hjälp av export och import finns [migrera din PostgreSQL-databas med hjälp av export och import](howto-migrate-using-export-and-import.md)