---
title: Utdata från Azure Stream Analytics
description: I den här artikeln beskrivs alternativ för datautdata som är tillgängliga för Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/2/2020
ms.openlocfilehash: 95607b78ff80566b76b8e6aa20462957249015b4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097659"
---
# <a name="outputs-from-azure-stream-analytics"></a>Utdata från Azure Stream Analytics

Ett Azure Stream Analytics jobb består av indata, frågor och utdata. Det finns flera typer av utdata som du kan skicka transformerade data till. Den här artikeln innehåller Stream Analytics utdata som stöds. När du utformar din Stream Analytics fråga kan du se namnet på utdata genom att använda into- [satsen](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Du kan använda en enda utmatning per jobb eller flera utdata per direkt uppspelnings jobb (om du behöver dem) genom att lägga till flera INTO-satser i frågan.

Om du vill skapa, redigera och testa Stream Analytics jobb-utdata kan du använda [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/)och [Visual Studio](stream-analytics-quick-create-vs.md).

Vissa utmatnings typer stöder [partitionering](#partitioning)och [utgående batch-storlekar](#output-batch-size) är beroende av att optimera data flödet. I följande tabell visas de funktioner som stöds för varje Utdatatyp:

| Utdatatyp | Partitionering | Säkerhet | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Yes|Azure Active Directory användare </br> MSI|
|[Azure SQL Database](sql-database-output.md)|Ja, valfritt.|SQL User auth </br> MSI (för hands version)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Yes|SQL User auth|
|[Blob Storage och Azure Data Lake gen 2](blob-storage-azure-data-lake-gen2-output.md)|Yes|MSI </br> Åtkomstnyckel|
|[Azure Event Hubs](event-hubs-output.md)|Ja, du måste ange en kolumn för partitionsnyckel i konfigurationen av utdata.|Åtkomstnyckel|
|[Power BI](power-bi-output.md)|No|Azure Active Directory användare </br> MSI|
|[Azure Table Storage](table-storage-output.md)|Yes|Kontonyckel|
|[Azure Service Bus-köer](service-bus-queues-output.md)|Yes|Åtkomstnyckel|
|[Azure Service Bus ämnen](service-bus-topics-output.md)|Yes|Åtkomstnyckel|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Yes|Åtkomstnyckel|
|[Azure Functions](azure-functions-output.md)|Yes|Åtkomstnyckel|

## <a name="partitioning"></a>Partitionering

Stream Analytics stöder partitioner för alla utdata förutom Power BI. Mer information om partitionerings nycklar och antalet utgående skrivare finns i artikeln för den angivna utdatatypen som du är intresse rad av. Alla utdata-artiklar är länkade i föregående avsnitt.  

För mer avancerad justering av partitionerna kan dessutom antalet utgående skrivare kontrol leras med hjälp av en `INTO <partition count>` (se [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count))-sats i din fråga, vilket kan vara till hjälp när du vill uppnå en önskad jobb sto pol Ogin. Om ditt utmatnings kort inte är partitionerat, orsakar brist på data i en partition upp till den sena tiden. I sådana fall slås utdata samman till en enda skrivare, vilket kan orsaka Flask halsar i din pipeline. Om du vill veta mer om principen för att komma i beaktande, se [Azure Stream Analytics händelse ordning](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

Alla utmatningar har stöd för batching, men bara en viss support grupp storlek explicit. Azure Stream Analytics använder batchar av variabel storlek för att bearbeta händelser och skriva till utdata. Vanligt vis skriver Stream Analyticss motorn ett meddelande i taget och använder batchar för effektivitet. När hastigheten för både inkommande och utgående händelser är hög använder Stream Analytics större batchar. När utgående frekvensen är låg använder den mindre batchar för att hålla svars tiderna låg.

## <a name="parquet-output-batching-window-properties"></a>Parquet för batch-fönster

När du använder Azure Resource Manager mal Lav drift sättning eller REST API, är de två batch-fönstret Egenskaper:

1. *timeWindow*

   Maximal vänte tid per batch. Värdet ska vara en sträng med TimeSpan. Till exempel "00:02:00" i två minuter. Efter den här tiden skrivs batchen till utdata även om kravet på minsta rader inte är uppfyllt. Standardvärdet är 1 minut och det högsta tillåtna värdet är 2 timmar. Om BLOB-utdata har Sök vägs mönster frekvens, kan vänte tiden inte vara högre än tidsintervallet för partitionen.

2. *sizeWindow*

   Antalet minsta rader per batch. För Parquet skapar varje batch en ny fil. Det aktuella standardvärdet är 2 000 rader och det tillåtna Max värdet är 10 000 rader.

Dessa egenskaper för batch-fönster stöds endast av API **-versionen 2017-04-01-Preview** . Nedan visas ett exempel på JSON-nyttolasten för ett REST API-anrop:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
