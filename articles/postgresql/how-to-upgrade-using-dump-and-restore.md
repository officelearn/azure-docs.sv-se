---
title: Uppgradera med dumpa och Återställ-Azure Database for PostgreSQL-enskild server
description: Beskriver några metoder för att dumpa och återställa databaser för att migrera till en högre version Azure Database for PostgreSQL-enskild server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 26154f4501daba373f1f8b108f1ee7105b1b194f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294346"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Uppgradera PostgreSQL-databasen med hjälp av dumpa och Återställ

I Azure Database for PostgreSQL-enskild server rekommenderar vi att du uppgraderar PostgreSQL-databasmotorn till en högre huvud version med någon av följande metoder:
* Offline-metod med PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). I den här metoden utför du först dumpningen från käll servern och återställer sedan den dumpen på mål servern.
* Online-metod med [**Database migration service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Den här metoden sparar mål databasen i Sync med källan och du kan välja när du vill klippa ut. Det finns dock vissa krav och begränsningar som ska åtgärdas. 

Du kan använda följande rekommendationer när du väljer mellan online-och offline-metoder för att utföra större versions uppgraderingar.

| **Databas** | **Dumpa/Återställ (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Du har en liten databas och kan erbjuda drift stopp vid uppgradering  | X | |
| Små databaser (< 10 GB)  | X | X | 
| Liten – medels databaser (10 GB – 100 GB) | X | X |
| Stora databaser (> 100 GB) |  | X |
| Kan ge ett drift stopp vid uppgraderingen (oavsett databasens storlek) | X |  |
| Kan du ta itu med [krav för DMS,](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) inklusive en omstart? |  | X |
| Kan du undvika DDLs och ej loggade tabeller under uppgraderings processen? | |  X |

Den här instruktions guiden innehåller två exempel metoder för hur du uppgraderar dina databaser med hjälp av PostgreSQL pg_dump och pg_restore kommandon. Processen i det här dokumentet kallas **uppgradering** , trots att databasen  **migreras** från käll servern till mål servern. 

> [!NOTE]
> PostgreSQL-dump och återställning kan utföras på många sätt. Du kan välja att använda olika metoder än vad som anges i det här dokumentet. Om du till exempel vill göra en dump som följs av återställning från en PostgreSQL-klient, se [dokumentationen](./howto-migrate-using-dump-and-restore.md) för steg-för-steg-instruktioner och bästa praxis. För detaljerad dumpning och återställning av syntax med ytterligare parametrar, se artiklarna [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Krav för att använda dump och återställning med Azure PostgreSQL
 
För att gå igenom den här instruktions guiden behöver du:
- En käll databas som kör 9,5, 9,6 eller 10 (Azure Database for PostgreSQL – enskild server)
- Mål databas server med önskad PostgreSQL huvud version [Azure Database for postgresql-server](quickstart-create-server-database-portal.md). 
- Ett klient system (Linux) med PostgreSQL installerat och har [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kommando rads verktyg installerade. 
- Du kan också använda [Azure Cloud Shell](https://shell.azure.com) eller genom att klicka på Azure Cloud Shell i meny fältet längst upp till höger i [Azure Portal](https://portal.azure.com). Du måste logga in på ditt konto `az login` innan du kör dump-och Restore-kommandona.
- Din PostgreSQL-klient plats, till exempel en virtuell dator som helst som körs i samma region som käll-och mål servrar). 

## <a name="additional-details-and-considerations"></a>Ytterligare information och överväganden
- Du kan hitta anslutnings strängen för käll-och mål databaserna genom att klicka på anslutnings strängarna i portalen. 
- Du kan köra mer än en databas på servern. Du kan hitta listan över databaser genom att ansluta till käll servern och köra `\l` .
- Skapa motsvarande databaser i mål databas servern.
- Du kan hoppa över uppgradering `azure_maintenance` eller mall databaser.
- Se tabellerna ovan för att kontrol lera att databasen är lämplig för det här läget för migrering.
- Om du vill använda Azure Cloud Shell tids gränsen för sessionen efter 20 minuter. Om databas storleken är < 10 GB kan du slutföra uppgraderingen utan tids gräns. Annars kan du behöva hålla sessionen öppen på annat sätt, till exempel trycka på <Enter> en tangent en gång om 10-15 minuter. 

> [!TIP] 
> - Om du använder samma lösen ord för käll-och mål databasen kan du ange `PGPASSWORD=yourPassword` miljövariabeln.  Du behöver inte ange lösen ord varje gång du kör kommandon som psql, pg_dump och pg_restore.  På samma sätt kan du konfigurera ytterligare variabler `PGUSER` , t `PGSSLMODE` . ex., osv. se postgresql- [miljövariabler](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Om din PostgreSQL-Server kräver TLS/SSL-anslutningar (som standard i Azure Database for PostgreSQL-servrar) anger du en miljö variabel `PGSSLMODE=require` så att pg_restore-verktyget ansluter med TLS. Utan TLS kan felet läsa  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - På kommando raden i Windows kör du kommandot `SET PGSSLMODE=require` innan du kör kommandot pg_restore. I Linux eller bash kör du kommandot `export PGSSLMODE=require` innan du kör kommandot pg_restore.

## <a name="example-database-used-in-this-guide"></a>Exempel databas som används i den här guiden

 | **Beskrivning** | **Värde** |
 | ------- | ------- |
 | Käll Server (v 9.5) | pg-95.postgres.database.azure.com |
 | Käll databas | bench5gb |
 | Storlek på käll databas | 5 GB |
 | Käll användar namn | pg@pg-95 |
 | Mål server (V11) | pg-11.postgres.database.azure.com |
 | Mål databas | bench5gb |
 | Mål användar namn | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Metod 1: uppgradera med strömmande säkerhets kopior till målet 

I den här metoden strömmas hela databas dumpningen direkt till mål databas servern och den lagrar inte dumpen i klienten. Detta kan därför användas med en-klient med begränsad lagring och kan även köras från Azure Cloud Shell. 

1. Kontrol lera att databasen finns på mål servern med hjälp av `\l` kommandot. Om databasen inte finns skapar du databasen.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```bash
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Kör dumpen och Återställ som en enda kommando rad med en pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Exempel:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. När uppgraderings processen (migreringen) är klar kan du testa programmet med mål servern. 
4. Upprepa processen för alla databaser på servern.

 I följande tabell visas hur lång tid det tog att uppgradera med den här metoden. Data fylls med hjälp av [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Eftersom din databas kan ha olika antal objekt med varierande storlekar än pgbench genererade tabeller och index, rekommenderar vi starkt att du testar dumpning och återställning av databasen för att förstå den faktiska tid det tar att uppgradera databasen. 

| **Databas storlek** | **Uppskattad tids åtgång** | 
| ----- | ------ |
| 1 GB  | 1-2 minuter |
| 5 GB | 8-10 minuter |
| 10 GB | 15-20 minuter |
| 50 GB | 1 – 1,5 timmar |
| 100 GB | 2,5 – 3 timmar|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Metod 2: uppgradera med parallell dump och återställning 

Den här metoden är användbar om du har några större tabeller i databasen och vill parallellisera dumpning och återställning för den databasen. Du behöver tillräckligt med lagrings utrymme på hård disken för att hantera säkerhets kopior för dina databaser. Den här parallella dumpnings-och återställnings processen minskar tids förbrukningen för att slutföra hela migreringen/uppgraderingen. Till exempel slutfördes den 50 GB pgbench-databasen som tog 1 – 1,5 timmar att migrera, på mindre än 30 minuter.

1. Skapa en motsvarande databas på mål servern för varje databas på käll servern.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Exempel:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Kör kommandot pg_dump i ett katalog format med antalet jobb = 4 (antalet tabeller i databasen). Med större beräknings nivå och med fler tabeller kan du öka antalet till en högre siffra. Pg_dump skapar en katalog för att lagra komprimerade filer för varje jobb.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Exempel:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Återställ sedan säkerhets kopian på mål servern.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Exempel:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Den process som anges i det här dokumentet kan också användas för att uppgradera Azure Database for PostgreSQL-flexibla servern, som är en för hands version. Den största skillnaden är anslutnings strängen för det flexibla Server målet utan `@dbName` .  Om användar namnet till exempel är `pg` , kommer den enskilda serverns användar namn i anslutnings strängen att vara `pg@pg-95` , men med flexibel Server kan du bara använda `pg` .

## <a name="next-steps"></a>Nästa steg

- När du är nöjd med mål databas funktionen kan du släppa den gamla databas servern. 
- Om du vill använda samma databas slut punkt som käll servern, kan du skapa en Läs replik med det gamla databas Server namnet efter att du har tagit bort den gamla käll databas servern. När du har upprättat ett stabilt tillstånd kan du stoppa repliken så att replik servern blir en oberoende Server. Se [replikering](./concepts-read-replicas.md) för mer information.
- Kom ihåg att testa och validera dessa kommandon i en test miljö innan du använder dem i produktion.
