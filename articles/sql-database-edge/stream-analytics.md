---
title: Använda SQL Database DAC-paket och Stream Analytics-jobb med Azure SQL Database Edge | Microsoft-dokument
description: Lär dig mer om hur du använder Stream Analytics-jobb i SQL Database Edge
keywords: sql-databaskant, strömanalys, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384165"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Använda SQL Database DAC-paket och Stream Analytics-jobb med SQL Database Edge

Azure SQL Database Edge Preview är en optimerad relationsdatabasmotor som är anpassad för IoT- och edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Server Database Engine, som ger branschledande prestanda-, säkerhets- och frågebearbetningsfunktioner. Tillsammans med de branschledande relationsdatabashanteringsfunktionerna i SQL Server tillhandahåller Azure SQL Database Edge inbyggda streamingfunktioner för analys i realtid och komplex händelsebearbetning.

Azure SQL Database Edge tillhandahåller också en inbyggd implementering av SqlPackage.exe som gör att du kan distribuera ett [SQL Database DAC-paket](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) under distributionen av SQL Database Edge.

Azure SQL Database Edge visar två `module twin's desired properties` valfria parametrar med alternativet IoT Edge-modulen:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Field | Beskrivning |
|------|-------------|
| SqlPackage | Azure Blob storage URI för filen *.zip som innehåller SQL Database DAC-paketet.
| ASAJobInfo | Azure Blob storage URI för ASA Edge-jobbet. Mer information finns i [Publicera ett ASA Edge-jobb för SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Använda SQL Database DAC-paket med SQL Database Edge

Så här använder du ett SQL Database DAC-paket (*.dacpac) med SQL Database Edge:

1. Skapa eller extrahera ett SQL Database DAC-paket. Se [Extrahera en DAC från en databas](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) för information om hur du genererar ett DAC-paket för en befintlig SQL Server-databas.

2. Zip *.dacpac och ladda upp den till ett Azure Blob-lagringskonto. Mer information om hur du laddar upp filer till Azure Blob-lagring finns i [Ladda ned, hämta och lista blobbar med Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generera en signatur för delad åtkomst för zip-filen med hjälp av Azure-portalen. Mer information finns i [Delegera åtkomst med SIGNATURER för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

4. Uppdatera SQL Database Edge-modulens konfiguration så att den innehåller URI för delad åtkomst för DAC-paketet. Så här uppdaterar du MODULEN SQL Database Edge:

    1. Gå till din IoT Hub-distribution i Azure-portalen.

    2. I den vänstra rutan väljer du **IoT Edge**.

    3. På **sidan IoT Edge** söker du efter och väljer den IoT-kant där SQL Database Edge-modulen distribueras.

    4. På enhetssidan **för IoT Edge Device** väljer du Ange **modul**.

    5. På sidan **Ange moduler** väljer du **Konfigurera** mot MODULEN SQL Database Edge.

    6. I fönstret **Anpassade moduler i IoT Edge** väljer du Ange **modultvillingens önskade egenskaper**. Uppdatera önskade egenskaper för att inkludera `SQLPackage` URI för alternativet, som visas i följande exempel.

        > [!NOTE]
        > SAS URI i följande JSON är bara ett exempel. Ersätt URI:n med själva URI:n från distributionen.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Välj **Spara**.

    8. På sidan **Ange moduler** väljer du **Nästa**.

    9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan .

5. Efter moduluppdateringen hämtas DAC-paketfilen, packas upp och distribueras mot SQL Database Edge-instansen.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Använda strömmande jobb med SQL Database Edge

Azure SQL Database Edge har en inbyggd implementering av stream analytics runtime. Med den här implementeringen kan du skapa ett Azure Stream Analytics-kantjobb och distribuera jobbet som ett SQL Database Edge-direktuppspelningsjobb. Så här skapar du ett Edge-jobb för Stream Analytics:

1. Gå till Azure-portalen med hjälp av [förhandsadressen](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true). Med den här url:en för förhandsversionen kan du konfigurera SQL Database-utdata för ett Edge-jobb för Stream Analytics.

2. Skapa en ny **Azure Stream Analytics på IoT Edge-jobb.** Välj den värdmiljö som riktar sig till **Edge**.

3. Definiera en indata och utdata för Azure Stream Analytics-jobbet. Varje SQL-utdata, som du ställer in här, är knuten till en enda tabell i databasen. Om du behöver strömma data till flera tabeller måste du skapa flera SQL Database-utdata. Du kan konfigurera SQL-utgångarna så att de pekar på olika databaser.

    **Ingång**. Välj EdgeHub som indata för kantjobbet och ange resursinformation.

    **Utdata**. Välj SQL Database som utdata. Välj **Ange inställningar för SQL-databas manuellt**. Ange konfigurationsinformation för databasen och tabellen.

    |Field      | Beskrivning |
    |---------------|-------------|
    |Utdataalias | Namnet på utdataaliaset.|
    |Databas | Namn på SQL-databasen. Det måste vara ett giltigt namn på en databas som finns på SQL Database Edge-instansen.|
    |servernamn | Namn (eller IP-adress) och portnummerinformation för SQL-instansen. För en SQL Database Edge-distribution kan du använda **tcp:.,1433** för servernamnet.|
    |Användarnamn | SQL-inloggningskonto som har dataläsare och data writer åtkomst till databasen som du angav tidigare.|
    |lösenord | Lösenord för SQL-inloggningskontot som du angav tidigare.|
    |Tabell | Namnet på tabellen som ska skrivas ut för direktuppspelningsjobbet.|
    |Ärva partitionering| Gör det möjligt att ärva partitioneringsschemat för föregående frågesteg eller indata. När det här alternativet är aktiverat kan du förvänta dig bättre dataflöde när du skriver till en diskbaserad tabell och har en helt parallell topologi för jobbet.|
    |Batchstorlek| Det maximala antalet poster som skickas med varje bulkinfogningstransaktion.|

    Här är ett exempel på indata-/utdatakonfiguration:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
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
    > Mer information om SQL-utdatakortet för Azure Stream Analytics finns i [Azure Stream Analytics-utdata till Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definiera ASA-jobbfrågan för kantjobbet. Den här frågan bör använda de definierade indata-/utdataaliasna som indata- och utdatanamn i frågan. Mer information finns i [Referens för Query Language för Stream Analytics.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

5. Ange inställningar för lagringskonto för kantjobbet. Lagringskontot används som publiceringsmål för kantjobbet.

6. Under **Konfigurera**väljer du **Publicera**och väljer sedan knappen **Publicera.** Spara SAS URI för användning med MODULEN SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Distribuera Edge-jobbet Stream Analytics till SQL Database Edge

Om du vill distribuera direktuppspelningsjobbet till SQL Database Edge-modulen uppdaterar du SQL Database Edge-modulens konfiguration så att den innehåller SAS URI för direktuppspelningsjobbet från det tidigare steget. Så här uppdaterar du SQL Database Edge-modulen:

1. Gå till din IoT Hub-distribution i Azure-portalen.

2. I den vänstra rutan väljer du **IoT Edge**.

3. På **sidan IoT Edge** söker du efter och väljer den IoT-kant där SQL Database Edge-modulen distribueras.

4. På enhetssidan **för IoT Edge Device** väljer du Ange **modul**.

5. På sidan **Ange moduler** väljer du **Konfigurera** mot MODULEN SQL Database Edge.

6. I fönstret **Anpassade moduler i IoT Edge** väljer du Ange **modultvillingens önskade egenskaper**. Uppdatera önskade egenskaper för att inkludera `ASAJobInfo` URI för alternativet, som visas i följande exempel.

    > [!NOTE]
    > SAS URI i följande JSON är bara ett exempel. Ersätt URI:n med själva URI:n från distributionen.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Välj **Spara**.

8. På sidan **Ange moduler** väljer du **Nästa**.

9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan .

10. Efter moduluppdateringen hämtas jobbfilen för dataflödesanalys, packas upp och distribueras mot SQL Database Edge-instansen.

## <a name="next-steps"></a>Nästa steg

- Information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Begär att du aktiverar Azure SQL Database Edge för din prenumeration.
- Information om hur du kommer igång finns [i Distribuera SQL Database Edge via Azure Portal](deploy-portal.md).
