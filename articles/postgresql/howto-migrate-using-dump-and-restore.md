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
ms.date: 09/22/2018
ms.openlocfilehash: 2aa59bcf0d56358601b81730abe330a56ca35d02
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966916"
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


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Återställa data till Azure-måldatabas för PostrgeSQL med pg_restore
När du har skapat måldatabasen, kan du använda kommandot pg_restore och -d, parametern--dbname för att återställa data i måldatabasen från dumpfilen.
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

## <a name="optimizing-the-migration-process"></a>Optimera migreringsprocessen

Ett sätt att migrera din befintliga PostgreSQL-databas till Azure Database för PostgreSQL-tjänsten är att säkerhetskopiera databasen på käll- och återställa det i Azure. Överväg att använda följande parametrar med backup och återställa kommandon för att minimera den tid som krävs för att slutföra migreringen.

> [!NOTE]
> Information om syntaxinformation finns i artiklarna [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>För säkerhetskopiering
- Utgår vi från säkerhetskopiering med växeln -Fc så att du kan utföra återställningen parallellt för att snabba upp. Exempel:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Vid återställningen
- Vi rekommenderar att du flyttar den säkerhetskopiera filen till en Azure-dator i samma region som Azure Database for PostgreSQL-server som du migrerar till och gör pg_restore från den virtuella datorn att minska Nätverksfördröjningen. Vi rekommenderar också att den virtuella datorn skapas med [nätverksaccelerering](../virtual-network/create-vm-accelerated-networking-powershell.md) aktiverat.
- Det bör göras redan som standard, men öppna dumpfilen för att verifiera att skapa index-instruktioner är efter infogningen av data. Om det inte är fallet, flytta create index-instruktioner när data infogas.
- Återställa med växlarna -Fc och -j *#* att parallellisera återställningen. *#* är antalet kärnor på målservern. Du kan också försöka med *#* inställd på två gånger antalet kärnor på målservern för att se hur. Exempel:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Du kan också redigera dumpfilen genom att lägga till kommandot *ange synchronous_commit = av;* i början och kommandot *ange synchronous_commit = on;* i slutet. Inte att aktivera det i slutet, innan apparna som ändrar data, kan det resultera i efterföljande förlust av data.

Kom ihåg att testa och validera dessa kommandon i en testmiljö innan du använder dem i produktion.

## <a name="next-steps"></a>Nästa steg
- Om du vill migrera en PostgreSQL-databas med exportera och importera, se [migrera din PostgreSQL-databas med exportera och importera](howto-migrate-using-export-and-import.md).
- Mer information om hur du migrerar databaser till Azure Database för PostgreSQL, finns i den [Guide för Databasmigrering](https://aka.ms/datamigration).
