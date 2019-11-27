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
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384165"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Använda SQL Database DAC-paket och Stream Analytics jobb med SQL Database Edge

Azure SQL Database Edge Preview är en optimerad Relations databas motor som är avsedd för IoT-och Edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Server Database Engine, som ger branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Tillsammans med de branschledande Relations databas hanterings funktionerna i SQL Server kan Azure SQL Database Edge tillhandahålla inbyggda strömnings möjligheter för analys i real tid och komplex händelse bearbetning.

Azure SQL Database Edge tillhandahåller också en inbyggd implementering av SqlPackage. exe som gör att du kan distribuera ett [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -paket under distributionen av SQL Database Edge.

Azure SQL Database Edge exponerar två valfria parametrar via alternativet `module twin's desired properties` i IoT Edge-modulen:

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
| SqlPackage | Azure Blob Storage-URI för *. zip-filen som innehåller SQL Database DAC-paketet.
| ASAJobInfo | Azure Blob Storage URI för jobbet ASA Edge. Mer information finns i [publicera ett ASA Edge-jobb för SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Använda SQL Database DAC-paket med SQL Database Edge

Gör så här om du vill använda ett SQL Database DAC-paket (*. dacpac) med SQL Database Edge:

1. Skapa eller extrahera ett SQL Database DAC-paket. Mer information om hur du skapar ett DAC-paket för en befintlig SQL Server databas finns i [extrahera en DAC från en databas](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .

2. Zip The *. dacpac och ladda upp det till ett Azure Blob Storage-konto. Mer information om hur du laddar upp filer till Azure Blob Storage finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generera en signatur för delad åtkomst för zip-filen med hjälp av Azure Portal. Mer information finns i [Delegera åtkomst med signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

4. Uppdatera SQL Database Edge module-konfigurationen så att den innehåller URI för delad åtkomst för DAC-paketet. Gör så här för att uppdatera SQL Database Edge-modulen:

    1. I Azure Portal går du till din IoT Hub-distribution.

    2. I den vänstra rutan väljer du **IoT Edge**.

    3. På sidan **IoT Edge** söker du efter och väljer den IoT-Edge där modulen SQL Database Edge har distribuerats.

    4. På sidan **IoT Edge enhets** enhet väljer du **ange modul**.

    5. På sidan **Ange moduler** väljer du **konfigurera** mot modulen SQL Database Edge.

    6. I fönstret **IoT Edge anpassade moduler** väljer du **Ange önskade egenskaper för modulens dubbla**. Uppdatera önskade egenskaper för att inkludera URI: n för `SQLPackage` alternativet, som visas i följande exempel.

        > [!NOTE]
        > SAS-URI: n i följande JSON är bara ett exempel. Ersätt URI: n med faktisk URI från distributionen.

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

    9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan.

5. När modulen har uppdaterats hämtas, zippas och distribueras DAC-paketfilen mot SQL Database Edge-instansen.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Använda strömmande jobb med SQL Database Edge

Azure SQL Database Edge har en inbyggd implementering av Stream Analytics-körningen. Med den här implementeringen kan du skapa ett Azure Stream Analytics Edge-jobb och distribuera jobbet som ett SQL Database Edge streaming-jobb. Gör så här för att skapa ett Stream Analytics Edge-jobb:

1. Gå till Azure Portal med hjälp av [URL: en](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)för för hands versionen. Med den här URL: en för för hands versionen kan du konfigurera SQL Database utdata för ett Stream Analytics Edge-jobb.

2. Skapa ett nytt **Azure Stream Analytics på IoT Edge** -jobb. Välj den värd miljö som riktar sig mot **Edge**.

3. Definiera indata och utdata för Azure Stream Analytics jobbet. Varje SQL-utdata, som du konfigurerar här, är knutna till en enda tabell i databasen. Om du behöver strömma data till flera tabeller måste du skapa flera SQL Database-utdata. Du kan konfigurera SQL-utdata så att de pekar på olika databaser.

    **Inmatade**. Välj EdgeHub som indatamängd för Edge-jobbet och ange resursinformation.

    **Utdata**. Välj SQL Database som utdata. Välj **ange SQL Database inställningar manuellt**. Ange konfigurations information för databasen och tabellen.

    |Fält      | Beskrivning |
    |---------------|-------------|
    |Utdataalias | Namn på utdata-alias.|
    |Databas | Namnet på SQL-databasen. Det måste vara ett giltigt namn för en databas som finns på SQL Database Edge-instansen.|
    |servernamn | Namn (eller IP-adress) och port nummer information för SQL-instansen. För en SQL Database Edge-distribution kan du använda **TCP:., 1433** som server namn.|
    |Användarnamn | SQL-inloggningsnamn som har data läsare och data skrivar åtkomst till den databas som du angav tidigare.|
    |lösenordsinställning | Lösen ord för det SQL-inloggningsnamn som du angav tidigare.|
    |Tabell | Namnet på den tabell som ska matas ut för strömnings jobbet.|
    |Ärv partitionering| Aktiverar arv av partitionerings schema för föregående fråga-steg eller indatatyper. När det här alternativet är aktiverat kan du vänta på att se bättre data flöde när du skriver till en disk-baserad tabell och har en helt parallell topologi för jobbet.|
    |Batchstorlek| Det maximala antalet poster som skickas med varje Mass infognings transaktion.|

    Här är en exempel på indata/utdata-konfiguration:

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
    > Mer information om SQL-utmatnings kortet för Azure Stream Analytics finns [Azure Stream Analytics utdata till Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definiera jobb frågan ASA för Edge-jobbet. Den här frågan ska använda de definierade aliasen för in-/utdata som indata och utdata i frågan. Mer information finns i [språk referens för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ange lagrings konto inställningarna för Edge-jobbet. Lagrings kontot används som publicerings mål för Edge-jobbet.

6. Under **Konfigurera**väljer du **publicera**och väljer sedan knappen **publicera** . Spara SAS-URI: n för användning med modulen SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Distribuera Stream Analytics Edge-jobbet till SQL Database Edge

Om du vill distribuera strömnings jobbet till SQL Database Edge-modulen uppdaterar du konfigurationen för SQL Database Edge-modulen så att den innehåller SAS-URI: n för strömnings jobbet från det tidigare steget. Så här uppdaterar du SQL Database Edge-modulen:

1. I Azure Portal går du till din IoT Hub-distribution.

2. I den vänstra rutan väljer du **IoT Edge**.

3. På sidan **IoT Edge** söker du efter och väljer den IoT-Edge där modulen SQL Database Edge har distribuerats.

4. På sidan **IoT Edge enhets** enhet väljer du **ange modul**.

5. På sidan **Ange moduler** väljer du **konfigurera** mot modulen SQL Database Edge.

6. I fönstret **IoT Edge anpassade moduler** väljer du **Ange önskade egenskaper för modulens dubbla**. Uppdatera önskade egenskaper för att inkludera URI: n för `ASAJobInfo` alternativet, som visas i följande exempel.

    > [!NOTE]
    > SAS-URI: n i följande JSON är bara ett exempel. Ersätt URI: n med faktisk URI från distributionen.

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

9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan.

10. Efter uppdateringen laddas Stream Analytics-jobb filen ned, zippas upp och distribueras mot SQL Database Edge-instansen.

## <a name="next-steps"></a>Nästa steg

- Information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Förfrågan om att aktivera Azure SQL Database Edge för din prenumeration.
- Information om hur du kommer igång finns i [distribuera SQL Database Edge via Azure Portal](deploy-portal.md).
