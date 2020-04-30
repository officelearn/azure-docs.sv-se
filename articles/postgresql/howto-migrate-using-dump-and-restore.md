---
title: Dumpa och Återställ-Azure Database for PostgreSQL-enskild server
description: Beskriver hur du extraherar en PostgreSQL-databas till en dumpfil och återställer från en fil som skapats av pg_dump i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 90a014e44c728c1881c1fd3d9e189554ed8f44da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146329"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrera din PostgreSQL-databas med dumpning och återställning
Du kan använda [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) för att extrahera en PostgreSQL-databas till en dumpfil och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) för att återställa PostgreSQL-databasen från en arkivfil som skapats av pg_dump.

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md) med brand Väggs regler för att tillåta åtkomst och databas under den.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kommando rads verktyg som är installerade

Följ dessa steg för att dumpa och återställa PostgreSQL-databasen:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Skapa en dumpfil med pg_dump som innehåller de data som ska läsas in
Om du vill säkerhetskopiera en befintlig PostgreSQL-databas lokalt eller i en virtuell dator kör du följande kommando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Om du till exempel har en lokal server och en databas med namnet **testdb**
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Återställa data till mål Azure Database for PostgreSQL med pg_restore
När du har skapat mål databasen kan du använda kommandot pg_restore och parametern-d,--dbname för att återställa data till mål databasen från dumpfilen.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Inklusive parametern--No-Owner gör att alla objekt som skapas under återställningen ägs av den användare som anges med--username. Mer information finns i den officiella PostgreSQL-dokumentationen om [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Om din PostgreSQL-Server kräver TLS/SSL-anslutningar (som standard i Azure Database for PostgreSQL-servrar) anger du en `PGSSLMODE=require` miljö variabel så att pg_restore-verktyget ansluter med TLS. Utan TLS kan felet läsa`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> På kommando raden i Windows kör du kommandot `SET PGSSLMODE=require` innan du kör kommandot pg_restore. I Linux eller bash kör du kommandot `export PGSSLMODE=require` innan du kör kommandot pg_restore.
>

I det här exemplet återställer du data från dump-filen **testdb. dump** till databasen **mypgsqldb** på mål servern **mydemoserver.postgres.Database.Azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimera migreringsprocessen

Ett sätt att migrera den befintliga PostgreSQL-databasen till Azure Database for PostgreSQL-tjänsten är att säkerhetskopiera databasen på källan och återställa den i Azure. Överväg att använda följande parametrar med kommandona för säkerhets kopiering och återställning för att minimera tiden som krävs för att slutföra migreringen.

> [!NOTE]
> Detaljerad information om syntaxen finns i artiklarna [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>För säkerhets kopieringen
- Ta säkerhets kopian med-FC-växeln så att du kan utföra återställningen parallellt för att göra den snabbare. Ett exempel:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>För återställning
- Vi rekommenderar att du flyttar säkerhets kopian till en virtuell Azure-dator i samma region som den Azure Database for PostgreSQL server som du migrerar till och gör pg_restore från den virtuella datorn för att minska nätverks fördröjningen. Vi rekommenderar också att den virtuella datorn skapas med [accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) aktiverat.

- Det bör redan utföras som standard, men öppna dumpfilen för att kontrol lera att Create index-instruktionerna är efter infogning av data. Om så inte är fallet flyttar du Create index-instruktionerna när data har infogats.

- Återställ med växlarna-FC och-j *#* för att parallellisera återställningen. *#* är antalet kärnor på mål servern. Du kan också prova med *#* att ange dubbelt så många kärnor på mål servern för att se effekten. Ett exempel:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Du kan också redigera dumpfilen genom att lägga till kommando *uppsättningen synchronous_commit = off;* i början och kommandot *set synchronous_commit = på;* i slutet. Om du inte aktiverar den i slutet innan apparna ändrar data kan det leda till efterföljande förlust av data.

- På mål Azure Database for PostgreSQL servern kan du göra följande innan du återställer:
    - Inaktivera prestanda spårning av frågor eftersom denna statistik inte behövs under migreringen. Det kan du göra genom att ange pg_stat_statements. Track, pg_qs. query_capture_mode och pgms_wait_sampling. query_capture_mode till ingen.

    - Använd en hög beräknings-och hög minnes-SKU, till exempel 32 vCore-minne som är optimerat, för att påskynda migreringen. Du kan enkelt skala tillbaka till den önskade SKU: n när återställningen har slutförts. Ju högre SKU, desto mer parallellitet kan du uppnå genom att öka motsvarande `-j` parameter i pg_restore kommandot. 

    - Mer IOPS på mål servern kan förbättra prestandan för återställning. Du kan etablera mer IOPS genom att öka serverns lagrings storlek. Den här inställningen går inte att ångra, men fundera över om en högre IOPS skulle gynna din faktiska arbets belastning i framtiden.

Kom ihåg att testa och validera dessa kommandon i en test miljö innan du använder dem i produktion.

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med export och import, se [migrera din PostgreSQL-databas med export och import](howto-migrate-using-export-and-import.md).
- Mer information om hur du migrerar databaser till Azure Database for PostgreSQL finns i [Guide för databas migrering](https://aka.ms/datamigration).
