---
title: Dumpa och återställa i Azure Database för PostgreSQL
description: Beskriver hur du extrahera en PostgreSQL-databas till en dumpfil och återställa från en fil som skapats av pg_dump i Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213658"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrera din PostgreSQL-databas med säkerhetskopiering och återställning
Du kan använda [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) att extrahera en PostgreSQL-databas till en dumpfil och [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) att återställa PostgreSQL-databasen från en arkivfil som skapats av pg_dump.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- En [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md) med brandväggsregler som tillåter åtkomst och databasen under den.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) kommandoradsverktyg som är installerad

Följ dessa steg för att dumpa och återställa din PostgreSQL-databas:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Skapa en kraschdump-fil med pg_dump som innehåller data som ska hämtas
Tillbaka till en befintlig PostgreSQL-databas lokalt eller i en virtuell dator, kör du följande kommando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Exempel: Om du har en lokal server och en databas som heter **testdb** i det.
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> Kopiera de säkerhetskopiera filerna till en Azure blobblagring och utföra återställningen därifrån, vilket borde vara mycket snabbare än att utföra återställningen via Internet.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Återställa data till Azure-måldatabas för PostrgeSQL med pg_restore
När du har skapat måldatabasen kan använda du kommandot pg_restore och -d,--dbname-parametern för att återställa data i måldatabasen från dumpfilen.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Inklusive--no-ägare parametern orsaker som alla objekt som skapas under återställningen som ägs av den användare som anges med--username. Mer information finns i officiella PostgreSQL-dokumentationen på [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> PostgreSQL-servern kräver SSL-anslutningar (på som standard i Azure Database for PostgreSQL-servrar), ange en miljövariabel `PGSSLMODE=require` så att verktyget pg_restore ansluter med SSL. Felet kan läsa utan SSL,  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Kör kommando i Windows command line `SET PGSSLMODE=require` innan du kör kommandot pg_restore. Kör kommando i Linux eller Bash `export PGSSLMODE=require` innan du kör kommandot pg_restore.
>

I det här exemplet kan du återställa data från dumpfilen **testdb.dump** till databasen **mypgsqldb** på målservern **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med exportera och importera, se [migrera din PostgreSQL-databas med exportera och importera](howto-migrate-using-export-and-import.md).
- Mer information om hur du migrerar databaser till Azure Database för PostgreSQL, finns i den [Guide för Databasmigrering](http://aka.ms/datamigration).
