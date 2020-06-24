---
title: Använda Azure Stream Analytics Edge-jobb med Azure SQL Edge (för hands version)
description: Lär dig mer om att använda Stream Analytics jobb i Azure SQL Edge (för hands version)
keywords: SQL Edge, Stream Analytics,
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e323c56f22c3a7d32bda5afe03a3462d3263f0d0
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254080"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Använda Azure Stream Analytics jobb med SQL Edge

Azure SQL Edge (för hands version) är en optimerad Relations databas motor som är avsedd för IoT-och Edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Server Database Engine, som ger branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Tillsammans med de branschledande Relations databas hanterings funktionerna i SQL Server ger Azure SQL Edge inbyggd strömnings kapacitet för analys i real tid och komplex händelse bearbetning.

Azure SQL Edge har en inbyggd implementering av Stream Analytics-körningen. Med den här implementeringen kan du skapa ett Azure Stream Analytics Edge-jobb och distribuera jobbet som ett SQL Edge streaming-jobb. Azure Stream Analytics-jobb kan distribueras till SQL Edge med hjälp av parametern ASAJobInfo som exponeras via `module twin's desired properties` alternativet för SQL Edge-modulen:

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
| ASAJobInfo | Azure Blob Storage URI för jobbet ASA Edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Skapa ett Azure Stream Analytics Edge-jobb

1. Gå till Azure Portal.

2. Skapa ett nytt **Azure Stream Analytics på IoT Edge** -jobb. Välj den värd miljö som riktar sig mot **Edge**.

3. Definiera indata och utdata för Azure Stream Analytics jobbet. Varje SQL-utdata, som du konfigurerar här, är knutna till en enda tabell i databasen. Om du behöver strömma data till flera tabeller måste du skapa flera SQL Database-utdata. Du kan konfigurera SQL-utdata så att de pekar på olika databaser.

    **Inmatade**. Välj EdgeHub som indatamängd för Edge-jobbet och ange resursinformation.

    **Utdata**. Välj SQL Database som utdata. Välj **ange SQL Database inställningar manuellt**. Ange konfigurations information för databasen och tabellen.

    |Fält      | Beskrivning |
    |---------------|-------------|
    |Utdataalias | Namn på utdata-alias.|
    |Databas | Namnet på databasen. Det måste vara ett giltigt namn för en databas som finns på SQL Edge-instansen.|
    |Servernamn | Namn (eller IP-adress) och port nummer information för SQL-instansen. För en SQL Edge-distribution kan du använda **TCP:., 1433** som server namn.|
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

6. Under **Konfigurera**väljer du **publicera**och väljer sedan knappen **publicera** . Spara SAS-URI: n för användning med SQL Edge-modulen.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Distribuera Azure Stream Analytics Edge-jobb till SQL Edge

Om du vill distribuera strömnings jobbet till SQL Edge-modulen uppdaterar du konfigurationen för SQL Edge-modulen för att inkludera SAS-URI: n för strömnings jobbet från det tidigare steget. Så här uppdaterar du SQL Edge-modulen:

1. I Azure Portal går du till din IoT Hub-distribution.

2. I den vänstra rutan väljer du **IoT Edge**.

3. På sidan **IoT Edge** söker du efter och väljer den IoT Edge där SQL Edge-modulen har distribuerats.

4. På sidan **IoT Edge enhets** enhet väljer du **ange modul**.

5. På sidan **Ange moduler** väljer du **Konfigurera** mot SQL Edge-modulen.

6. I fönstret **IoT Edge anpassade moduler** väljer du **Ange önskade egenskaper för modulens dubbla**. Uppdatera önskade egenskaper för att inkludera URI för `ASAJobInfo` alternativet, som visas i följande exempel.

    > [!NOTE]
    > SAS-URI: n i följande JSON är bara ett exempel. Ersätt URI: n med faktisk URI från distributionen.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Välj **Spara**.

8. På sidan **Ange moduler** väljer du **Nästa**.

9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan.

10. Efter uppdateringen laddas Stream Analytics-jobb filen ned, zippas upp och distribueras mot SQL Edge-instansen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Edge via Azure Portal](deploy-portal.md).

- [Strömma data](stream-data.md)

- [Machine Learning och AI med ONNX i SQL Edge (för hands version)](onnx-overview.md)
