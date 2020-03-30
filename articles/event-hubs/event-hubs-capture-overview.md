---
title: Fånga strömmande händelser – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller en översikt över capture-funktionen som gör att du kan fånga händelser som direktuppspelar via Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265017"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Samla in händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage
Med Azure Event Hubs kan du automatiskt samla in strömmande data i eventhubbar i ett [Azure Blob-lagrings-](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Storage Gen 1- eller Gen 2-konto](https://azure.microsoft.com/services/data-lake-store/) som du väljer, med den extra flexibiliteten att ange ett tids- eller storleksintervall. Det går snabbt att konfigurera Capture, det finns inga administrativa kostnader för att köra den och skalas automatiskt med [dataflödesenheter](event-hubs-scalability.md#throughput-units)för eventhubbar . Event Hubs Capture är det enklaste sättet att läsa in strömmande data i Azure och gör att du kan fokusera på databearbetning i stället för på datainsamling.

Med Event Hubs Capture kan du bearbeta pipelines i realtid och batchbaserade på samma ström. Detta innebär att du kan bygga lösningar som växer med dina behov över tiden. Oavsett om du bygger batchbaserade system idag med ett öga mot framtida realtidsbearbetning, eller om du vill lägga till en effektiv kallväg till en befintlig realtidslösning, gör Event Hubs Capture det enklare att arbeta med strömmande data.


## <a name="how-event-hubs-capture-works"></a>Så här fungerar Event Hubs Capture

Event Hubs är en varaktig buffert för hållbar tidskvarhållning för telemetriinträngning, liknande en distribuerad logg. Nyckeln till skalning i eventhubbar är den [partitionerade konsumentmodellen](event-hubs-scalability.md#partitions). Varje partition är ett oberoende segment av data och förbrukas oberoende av varandra. Med tiden åldras dessa data, baserat på den konfigurerbara lagringsperioden. Därför blir ett visst händelsenav aldrig "för fullt".

Med Event Hubs Capture kan du ange ditt eget Azure Blob-lagringskonto och -behållare, eller Azure Data Lake Storage-konto, som används för att lagra de infångade data. Dessa konton kan finnas i samma region som din händelsehubb eller i en annan region, vilket ökar flexibiliteten i funktionen Fånga händelsehubbar.

Infångade data skrivs i [Apache Avro-format:][Apache Avro] ett kompakt, snabbt, binärt format som ger omfattande datastrukturer med infogat schema. Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory. Mer information om hur du arbetar med Avro finns senare i den här artikeln.

### <a name="capture-windowing"></a>Fånga fönster

Med Event Hubs Capture kan du konfigurera ett fönster för att styra hämtningen. Det här fönstret är en minsta storleks- och tidskonfiguration med en "första wins-princip", vilket innebär att den första utlösaren som påträffades orsakar en insamlingsåtgärd. Om du har ett 100 MB hämtningsfönster på femton minuter och skickar 1 MB per sekund utlöser storleksfönstret före tidsfönstret. Varje partition fångar oberoende av varandra och skriver en slutförd blockblob vid tidpunkten för insamlingen, uppkallad efter den tidpunkt då infångningsintervallet påträffades. Namngivningskonventionen för lagring är följande:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Observera att datumvärdena är vadderade med nollor. ett exempel på filnamn kan vara:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

I händelse av att din Azure storage-blob är tillfälligt otillgänglig, behåller Event Hubs Capture dina data för datalagringsperioden som konfigurerats på din händelsehubb och fyller tillbaka data när ditt lagringskonto är tillgängligt igen.

### <a name="scaling-to-throughput-units"></a>Skala till dataflödesenheter

Event Hubs-trafiken styrs av [dataflödesenheter](event-hubs-scalability.md#throughput-units). En enhet med ett enda dataflöde tillåter 1 MB per sekund eller 1000 händelser per sekund av ingående och dubbelt så mycket utgående. Standardhändelsehubbar kan konfigureras med 1-20 dataflödesenheter och du kan köpa mer med en [supportbegäran för][support request]kvotökning . Användning utöver dina inköpta dataflödesenheter begränsas. Event Hubs Capture kopierar data direkt från den interna eventhubbar lagring, kringgå dataflöde enhet utgående kvoter och spara din utgående för andra bearbetning läsare, till exempel Stream Analytics eller Spark.

När händelsen hubbar har konfigurerats körs den automatiskt när du skickar den första händelsen och fortsätter att köras. För att göra det enklare för nedströmsbearbetningen att veta att processen fungerar, skriver Event Hubs tomma filer när det inte finns några data. Den här processen ger en förutsägbar kadens och markör som kan mata dina batchprocessorer.

## <a name="setting-up-event-hubs-capture"></a>Konfigurera hämtning av händelsehubbar

Du kan konfigurera Capture vid händelsehubbens skapandetid med hjälp av [Azure-portalen](https://portal.azure.com)eller använda Azure Resource Manager-mallar. Mer information finns i följande artiklar:

- [Aktivera avbildningsfunktionen i Event Hubs med Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Utforska de fångade filerna och arbeta med Avro

Event Hubs Capture skapar filer i Avro-format, enligt vad som anges i det konfigurerade tidsfönstret. Du kan visa dessa filer i alla verktyg som [Azure Storage Explorer][Azure Storage Explorer]. Du kan hämta filerna lokalt för att arbeta med dem.

De filer som produceras av Event Hubs Capture har följande Avro-schema:

![Avro-schema][3]

Ett enkelt sätt att utforska Avro filer är genom att använda [Avro Tools][Avro Tools] burken från Apache. Du kan också använda [Apache Drill][Apache Drill] för en lätt SQL-driven upplevelse eller [Apache Spark][Apache Spark] för att utföra komplex distribuerad bearbetning på introllade data. 

### <a name="use-apache-drill"></a>Använd Apache Drill

[Apache Drill][Apache Drill] är en "SQL-frågemotor med öppen källkod för utforskning av Big Data" som kan fråga strukturerade och halvstrukturerade data var de än är. Motorn kan köras som en fristående nod eller som ett stort kluster för bra prestanda.

Det finns ett inbyggt stöd för Azure Blob-lagring, vilket gör det enkelt att fråga efter data i en Avro-fil enligt beskrivningen i dokumentationen:

[Apache-borr: Plugin för Azure Blob Storage][Apache Drill: Azure Blob Storage Plugin]

Om du enkelt vill fråga tagna filer kan du skapa och köra en virtuell dator med Apache Drill aktiverad via en behållare för att komma åt Azure Blob-lagring:

https://github.com/yorek/apache-drill-azure-blob

Ett fullständigt end-to-end-exempel är tillgängligt i databasen Direktuppspelning vid skalning:

[Streaming i skala: Fånga in eventhubbar]

### <a name="use-apache-spark"></a>Använd Apache Spark

[Apache Spark][Apache Spark] är en "enhetlig analysmotor för storskalig databehandling". Den stöder olika språk, inklusive SQL, och kan enkelt komma åt Azure Blob-lagring. Det finns några alternativ för att köra Apache Spark i Azure, och var och en ger enkel åtkomst till Azure Blob-lagring:

- [HDInsight: Adressfiler i Azure-lagring][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob-lagring][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Använd Avro-verktyg

[Avro Tools][Avro Tools] finns som burkpaket. När du har hämtat jar-filen kan du se schemat för en viss Avro-fil genom att köra följande kommando:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Det här kommandot returnerar

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Du kan också använda Avro Tools för att konvertera filen till JSON-format och utföra annan bearbetning.

För att utföra mer avancerad bearbetning, ladda ner och installera Avro för ditt val av plattform. I skrivande stund finns implementeringar tillgängliga för C, C++, C,\#Java, NodeJS, Perl, PHP, Python och Ruby.

Apache Avro har kompletta Komma igång guider för [Java][Java] och [Python][Python]. Du kan också läsa artikeln [Komma igång med Händelsehubbar.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Så här debiteras insamling av händelsehubbar

Händelsehubbar capture mäts på samma sätt som dataflödesenheter: som en timladdning. Avgiften är direkt proportionell mot antalet dataflödesenheter som köpts för namnområdet. I takt med att dataflödesenheterna ökar och minskas ökar och minskar händelsehubbars mätare för att ge matchande prestanda. Mätarna förekommer i tandem. Prisinformation finns i [Priserhubbar.](https://azure.microsoft.com/pricing/details/event-hubs/) 

Observera att Capture inte förbrukar utgående kvot eftersom den faktureras separat. 

## <a name="integration-with-event-grid"></a>Integrering med händelserutnät 

Du kan skapa en Azure Event Grid-prenumeration med ett namnområde för eventhubbar som källa. Följande självstudiekurs visar hur du skapar en Event Grid-prenumeration med en händelsehubb som källa och en Azure Functions-app som en mottagare: [Bearbeta och migrera hämtade eventhubbar data till ett SQL Data Warehouse med hjälp av Event Grid och Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Nästa steg
Event Hubs Capture är det enklaste sättet att få data till Azure. Med Hjälp av Azure Data Lake, Azure Data Factory och Azure HDInsight kan du utföra batchbearbetning och andra analyser med välbekanta verktyg och plattformar som du väljer, i valfri skala.

Lär dig hur du aktiverar den här funktionen med azure-portalen och Azure Resource Manager-mallen:

- [Använda Azure Portal för att aktivera Event Hubs Capture](event-hubs-capture-enable-through-portal.md)
- [Använda en Azure Resource Manager-mall för att aktivera hämtning av händelsehubbar](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming i skala: Fånga in eventhubbar]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
