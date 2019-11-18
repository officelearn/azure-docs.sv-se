---
title: Använda SQL Database DAC-paket och Stream Analytics jobb med Azure SQL Database Edge | Microsoft Docs
description: Lär dig mer om hur du använder Stream Analytics jobb i SQL Database Edge
keywords: SQL Database Edge, Stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108756"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Använda SQL Database DAC-paket och Stream Analyticss jobb med SQL Database Edge

Azure SQL Database Edge Preview är en optimerad Relations databas motor som är avsedd för IoT-och Edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Server Database Engine, som tillhandahåller branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Tillsammans med de branschledande Relations databas hanterings funktionerna i SQL Server kan Azure SQL Database Edge tillhandahålla inbyggda strömnings möjligheter för analys i real tid och komplex händelse bearbetning.

Azure SQL Database Edge tillhandahåller också en inbyggd implementering av SQLPackage. exe som gör det möjligt för användare att distribuera ett [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -paket under distributionen av SQL Database Edge.

Azure SQL Database Edge exponerar två valfria parametrar via *modulens dubbla önskade egenskaps* alternativ i modulen IoT Edge.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Fält | Beskrivning |
|------|-------------|
| SQLPackage | Azure Blob Storage URI för *. zip-filen som innehåller DAC-paketet SQL Database.
| ASAJobInfo | Azure Blob Storage URI för jobbet ASA Edge. Om du vill ha mer information om hur du publicerar jobbet ASA Edge, se [publicera ett ASA Edge-jobb för SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Använda SQL Database DAC-paket med SQL Database Edge

Om du vill använda ett SQL Database DAC-paket (*. dacpac) med SQL Database Edge följer du stegen som beskrivs nedan.

1. Skapa eller extrahera ett SQL Database DAC-paket. Du kan använda de begrepp som anges i [EXTRAHERA DAC från en befintlig databas](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) för att skapa en DacPac för en befintlig SQL Database.

2. Zip The * *. dacpac* och upload till ett Azure Blob Storage-konto. Mer information om hur du laddar upp filer till Azure Blob Storage finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Skapa en SAS-signatur för zip-filen med hjälp av Azure Portal. Mer information finns i [Delegera åtkomst med signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

4. Uppdatera SQL Database Edge module-konfigurationen för att inkludera SAS-URI: n för DAC-paketet. Så här uppdaterar du modulen för SQL Database Edge

    1. Bläddra till din IoT Hub-distribution på Azure Portal.

    2. Klicka på **IoT Edge**i det vänstra fönstret.

    3. På sidan **IoT Edge** letar du upp och klickar på den IoT Edge där modulen SQL Database Edge distribueras.

    4. På sidan *IoT Edge enhets* enhet klickar du på **ange modul**. 

    5. På sidan **Ange moduler** klickar du på *konfigurera* mot modulen SQL Database Edge. 

    6. I fönstret **IoT Edge anpassade moduler** väljer du *önskade egenskaper för de angivna modulerna* och uppdaterar sedan de önskade egenskaperna så att de innehåller URI: n för alternativet SQLPackage, vilket visas i exemplet nedan. 

        > [!NOTE]
        > SAS-URI: n nedan är endast avsedd som illustration. Ersätt URI: n med den faktiska URI: n från distributionen.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Klicka på **Save** (Spara).

    8. Klicka på *Nästa*på sidan **Ange moduler** .

    9. Klicka på *Nästa* på sidan **Ange moduler** och klicka sedan på **Skicka**.

5. Om du vill publicera uppdateringen laddas DACPAC-filen ned, zippas upp och distribueras mot SQL Database Edge-instansen.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Använda strömmande jobb med SQL Database Edge

Azure SQL Database Edge har en inbyggd implementering av Stream Analytics-körningen. Detta gör det möjligt för användare att skapa ett Azure Stream Analytics Edge-jobb och distribuera jobbet som ett SQL Database Edge streaming-jobb. Följ stegen nedan om du vill skapa ett Stream Analytics Edge-jobb.

1. Bläddra till Azure Portal med hjälp av [URL: en](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)för för hands versionen. Den här för hands versionen av URL gör att användarna kan konfigurera SQL Database utdata för ett Stream Analytics Edge-jobb.

2. Skapa ett nytt **Azure Stream Analytics på IoT Edge** jobb och välj mål **gränsen**för värd miljön.

3. Definiera *indata* och *utdata* för Azure Stream Analytics jobbet. Varje SQL-utdata (konfigurerad nedan) är knutna till en enda tabell i databasen. Om du behöver strömma data till flera tabeller måste du skapa flera SQL Database-utdata. SQL-utdata kan konfigureras så att de pekar på olika databaser.

    *In-Välj EdgeHub som inskrivning för Edge-jobbet och fyll i resurs informationen.*

    *Utdata – Välj SQL Database som utdata, "Ange SQL Database inställningar manuellt" och ange konfigurations information för databasen och tabellen.*

    |Fält      | Beskrivning |
    |---------------|-------------|
    |Utdataalias | Namn på utdata-alias.|
    |Databas | Namnet på SQL Database. Detta måste vara ett giltigt databas namn som finns på SQL Database Edge-instansen.|
    |servernamn | Namn (eller IP-adress) och port nummer information för SQL-instansen. För en SQL Database Edge-distribution kan du använda **TCP:., 1433** som server namn.|
    |Användarnamn | SQL-inloggningsnamn som har data läsare och data skrivar åtkomst till den databas som anges ovan.|
    |Lösenord | Lösen ordet för det SQL-inloggningsnamn som anges ovan.|
    |Tabell | Namnet på den tabell som ska matas ut för strömnings jobbet.|
    |Ärv partitionering| Detta konfigurations alternativ för SQL-utdata gör att du kan ärva partitionerings schemat i föregående fråge steg eller indata. Med den här inställningen aktive rad, skriver du till en diskbaserad tabell och har en helt parallell topologi för ditt jobb, vilket förväntar dig att se bättre data flöde.|
    |Batchstorlek| Batchstorlek är det maximala antalet poster som skickas med varje Mass infognings transaktion.|

    Exempel på indata/utdata-konfiguration nedan:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Mer information om SQL-utmatnings kortet för Azure Stream Analytics finns [Azure Stream Analytics utdata till Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definiera jobb frågan ASA för Edge-jobbet. Den här frågan ska använda de definierade aliasen för in-/utdata som indata och utdata i frågan. Mer information finns i [språk referens för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ange lagrings konto inställningarna för Edge-jobbet. Lagrings kontot används som publicerings mål för Edge-jobbet.

6. Under Konfigurera väljer du publicera och klickar på knappen publicera. Spara SAS-URL för användning med modulen SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Distribuera Stream Analytics Edge-jobbet till SQL Database Edge

Om du vill distribuera strömnings jobbet till SQL Database Edge-modulen uppdaterar du konfigurationen för SQL Database Edge-modulen så att den inkluderar SAS-URI: n för strömnings jobbet från steget ovan. Så här uppdaterar du modulen för SQL Database Edge

1. Bläddra till din IoT Hub-distribution på Azure Portal.

2. Klicka på **IoT Edge**i det vänstra fönstret.

3. På sidan **IoT Edge** letar du upp och klickar på den IoT Edge där modulen SQL Database Edge distribueras.

4. På sidan *IoT Edge enhets* enhet klickar du på **ange modul**. 

5. På sidan **Ange moduler** klickar du på *konfigurera* mot modulen SQL Database Edge. 

6. I fönstret **IoT Edge anpassade moduler** väljer du *önskade egenskaper för set-modulerna* och uppdaterar sedan de önskade egenskaperna så att de innehåller URI: n för alternativet ASAJobInfo som visas i exemplet nedan. 

    > [!NOTE]
    > SAS-URI: n nedan är endast avsedd som illustration. Ersätt URI: n med den faktiska URI: n från distributionen.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Klicka på **Save** (Spara).

8. Klicka på *Nästa*på sidan **Ange moduler** .

9. Klicka på *Nästa* på sidan **Ange moduler** och klicka sedan på **Skicka**.

10. Publicera uppdateringen, Stream Analytics-jobbets fil hämtas, zippas upp och distribueras mot SQL Database Edge-instansen.

## <a name="next-steps"></a>Nästa steg

- Information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Begäran om att aktivera Azure SQL Database Edge för din prenumeration.
- För att komma igång, se följande:
  - [Distribuera SQL Database Edge via Azure Portal](deploy-portal.md)
