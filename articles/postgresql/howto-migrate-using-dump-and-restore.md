---
title: Dumpa och återställa - Azure Database för PostgreSQL - Single Server
description: Beskriver hur du extraherar en PostgreSQL-databas till en dumpfil och återställer från en fil som skapats av pg_dump i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770245"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrera din PostgreSQL-databas med dumpning och återställning
Du kan använda [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) för att extrahera en PostgreSQL-databas till en dumpfil och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) för att återställa PostgreSQL-databasen från en arkivfil som skapats av pg_dump.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md) med brandväggsregler för att tillåta åtkomst och databas under den.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kommandoradsverktyg installerade

Så här dumpar och återställer du postgresQL-databasen:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Skapa en dumpfil med pg_dump som innehåller de data som ska läsas in
Om du vill säkerhetskopiera en befintlig PostgreSQL-databas lokalt eller i en virtuell dator kör du följande kommando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Om du till exempel har en lokal server och en databas som heter **testdb** i den
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Återställa data till målet Azure Database för PostgreSQL med pg_restore
När du har skapat måldatabasen kan du använda kommandot pg_restore och parametern -d, --dbname för att återställa data till måldatabasen från dumpfilen.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Om du inkluderar parametern --no-owner kan alla objekt som skapas under återställningen ägas av den användare som anges med --användarnamn. Mer information finns i den officiella PostgreSQL-dokumentationen [om pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Om postgresQL-servern kräver SSL-anslutningar (på som standard i Azure Database för `PGSSLMODE=require` PostgreSQL-servrar) anger du en miljövariabel så att verktyget pg_restore ansluter till SSL. Utan SSL kan felet läsas`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> På kommandoraden i Windows `SET PGSSLMODE=require` kör du kommandot innan du kör kommandot pg_restore. I Linux eller Bash `export PGSSLMODE=require` kör kommandot innan du kör kommandot pg_restore.
>

I det här exemplet återställer du data från dumpfilen **testdb.dump** till databasen **mypgsqldb** på målservern **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimera migreringsprocessen

Ett sätt att migrera din befintliga PostgreSQL-databas till Azure Database for PostgreSQL-tjänsten är att säkerhetskopiera databasen på källan och återställa den i Azure. För att minimera den tid som krävs för att slutföra migreringen bör du överväga att använda följande parametrar med kommandona för säkerhetskopiering och återställning.

> [!NOTE]
> Detaljerad syntaxinformation finns i artiklarna [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>För säkerhetskopiering
- Ta säkerhetskopian med växeln -Fc så att du kan utföra återställningen parallellt för att snabba upp den. Ett exempel:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>För återställning
- Vi föreslår att du flyttar säkerhetskopian till en Virtuell Azure-dator i samma region som Azure Database för PostgreSQL-server som du migrerar till och gör pg_restore från den virtuella datorn för att minska nätverksfördröjningen. Vi rekommenderar också att den virtuella datorn skapas med [snabbare nätverksaktiverade nätverk.](../virtual-network/create-vm-accelerated-networking-powershell.md)

- Det bör redan göras som standard, men öppna dumpfilen för att kontrollera att skapa index satser är efter infogningen av data. Om så inte är fallet flyttar du skapa indexsatser när data har infogats.

- Återställ med växlarna -Fc *#* och -j för att parallellisera återställningen. *#* är antalet kärnor på målservern. Du kan också *#* prova med inställt på dubbelt så många kärnor på målservern för att se effekten. Ett exempel:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Du kan också redigera dumpfilen genom att lägga till *kommandouppsättningen synchronous_commit = av;* i början och *kommandouppsättningen synchronous_commit = på;* i slutet. Om du inte aktiverar den i slutet, innan apparna ändrar data, kan det leda till att data går förlorade efterföljande.

- På Azure-servern för Målet för PostgreSQL kan du överväga att göra följande innan återställningen:
    - Inaktivera frågeprestandaspårning eftersom den här statistiken inte behövs under migreringen. Du kan göra detta genom att ange pg_stat_statements.track, pg_qs.query_capture_mode och pgms_wait_sampling.query_capture_mode till NONE.

    - Använd en hög beräkning och högt minne sku, som 32 vCore Minne Optimerad, för att påskynda migreringen. Du kan enkelt skala tillbaka ner till önskad sku när återställningen är klar. Ju högre sku, desto mer parallellism kan `-j` du uppnå genom att öka motsvarande parameter i pg_restore kommandot. 

    - Fler IOPS på målservern kan förbättra återställningsprestandan. Du kan etablera fler IOPS genom att öka serverns lagringsstorlek. Den här inställningen är inte reversibel, men tänk på om en högre IOPS skulle gynna din faktiska arbetsbelastning i framtiden.

Kom ihåg att testa och validera dessa kommandon i en testmiljö innan du använder dem i produktion.

## <a name="next-steps"></a>Nästa steg
- Information om hur du migrerar en PostgreSQL-databas med export och import finns i [Migrera din PostgreSQL-databas med export och import](howto-migrate-using-export-and-import.md).
- Mer information om hur du migrerar databaser till Azure Database för PostgreSQL finns i [guiden För databasmigrering](https://aka.ms/datamigration).
