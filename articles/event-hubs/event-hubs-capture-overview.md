---
title: "Översikt över Azure Event Hubs avbilda | Microsoft Docs"
description: Samla in telemetridata med Event Hubs avbilda
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: sethm;darosa
ms.openlocfilehash: c4fd365ec8eeb389f0df9f53cd2f2a18f4c9b52a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-hubs-capture"></a>Azure Event Hubs avbildning

Azure Event Hubs avbilda kan du automatiskt leverera strömmande data i Händelsehubbar till en [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) konto du väljer, med den ytterligare flexibiliteten Ange ett intervall för tid, eller storlek. Ställa in avbildningen är fast det inte finns några administrativa kostnader för att köra den och skalningen automatiskt med Händelsehubbar [genomflödesenheter](event-hubs-features.md#capacity). Event Hubs avbilda är det enklaste sättet att läsa in strömmande data i Azure och gör det möjligt att fokusera på databehandling i stället för datainsamling.

Event Hubs avbilda gör att du kan bearbeta i realtid och batch-baserade pipelines på samma dataström. Det innebär att du kan skapa lösningar som växa med dina behov över tid. Om du utvecklar batch-baserade system idag med ett öga mot framtida realtidsbearbetning, eller om du vill lägga till en effektiv cold sökväg till en befintlig lösning i realtid, gör Event Hubs avbilda arbeta med strömmande data enklare.

## <a name="how-event-hubs-capture-works"></a>Så här fungerar Event Hubs avbilda

Händelsehubbar är en tid kvarhållning varaktiga buffert för telemetriingång, liknar en distribuerad logg. Nyckeln till skalning i Händelsehubbar är den [konsumentmönster indelat modellen](event-hubs-features.md#partitions). Varje partition är en oberoende segment av data och används oberoende av varandra. Med tiden åldras dessa data, baserat på konfigurerbara kvarhållningsperioden. Därför det finns en given händelsehubb för aldrig hämtar ”full”.

Event Hubs avbilda kan du ange egna Azure Blob storage-konto och en behållare eller ett Azure Data Lake Store-konto som används för att lagra den insamlade data. Dessa konton kan vara i samma region som din händelsehubb eller en annan region att lägga till funktionen Event Hubs avbilda flexibilitet.

Insamlade data skrivs i [Apache Avro] [ Apache Avro] format: ett compact, snabb, binära format som ger omfattande datastrukturer inlineschema. Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory. Mer information om hur du arbetar med Avro finns senare i den här artikeln.

### <a name="capture-windowing"></a>Avbilda fönsterhantering

Event Hubs avbilda kan du konfigurera ett fönster för att styra avbildning. Det här fönstret är en minsta storlek och konfiguration med en ”första WINS-princip”, vilket innebär att den första utlösaren påträffade orsakar en capture-åtgärd. Om du har en femton minuter fönstret capture 100 MB och skicka 1 MB per sekund, storlek fönstret utlösare innan tidsfönstret. Varje partition in separat och skriver en slutförd blockblob vid tidpunkten för insamlingen, med namnet för den tid då avbilda intervallet påträffades. Namngivningskonventionen för lagring är följande:

```
[namespace]/[event hub]/[partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Skalning till enheter

Event Hubs trafik styrs av [genomflödesenheter](event-hubs-features.md#capacity). En genomflödesenhet kan 1 MB per sekund eller 1 000 händelser per sekund på inkommande trafik och två gånger att mängden utgång. Standard Händelsehubbar kan konfigureras med 1-20 genomflödesenheter och du kan köpa fler med en kvot öka [supportbegäran][support request]. Användning utöver din inköpta genomflödesenheter begränsas. Event Hubs avbilda kopierar data direkt från den interna lagringen i Händelsehubbar kringgå genomströmning enhet utgång kvoter och spara din utgång för andra bearbetning, till exempel Stream Analytics eller Spark.

När du konfigurerat Event Hubs avbilda körs automatiskt när du skickar din första händelsen och fortsätter att köras. För att göra det enklare för din nedströms bearbetning veta att processen fungerar, skriver Händelsehubbar tomma filer när det finns inga data. Den här processen ger en förutsägbar takt och markör som kan mata batch-processorer.

## <a name="setting-up-event-hubs-capture"></a>Konfigurera Event Hubs avbilda

Du kan konfigurera avbildning på en händelse hubb skapa tid med hjälp av den [Azure-portalen](https://portal.azure.com), eller med hjälp av Azure Resource Manager-mallar. Mer information finns i följande artiklar:

- [Aktivera Event Hubs avbilda med Azure-portalen](event-hubs-capture-enable-through-portal.md)
- [Skapa ett namnområde för Händelsehubbar med en händelsehubb och aktivera avbildning med en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Utforska insamlade filer och arbeta med Avro

Event Hubs avbilda skapar filer i Avro-format, som har angetts på den konfigurerade tidsperioden. Du kan visa dessa filer i ett verktyg som [Azure Lagringsutforskaren][Azure Storage Explorer]. Du kan hämta filer lokalt för att arbeta med dem.

De filer som skapas av Event Hubs avbilda har följande Avro-schemat:

![][3]

Ett enkelt sätt att utforska Avro-filernas är med hjälp av den [Avro verktyg] [ Avro Tools] jar från Apache. När du har hämtat den här jar ser du schemat för en viss Avro-fil genom att köra följande kommando:

```
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Det här kommandot returnerar

```
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

Du kan också använda Avro-verktyg för att konvertera den till JSON-format och utföra andra bearbetning.

Om du vill utföra mer avancerade bearbetning, hämta och installera Avro för valet av plattform. När detta skrivs det finns implementeringar för C, C++, C\#, Java, NodeJS, Perl, PHP, Python eller Ruby.

Apache Avro har slutförts komma igång guider för [Java] [ Java] och [Python][Python]. Du kan också läsa den [komma igång med Event Hubs avbilda](event-hubs-capture-python.md) artikel.

## <a name="how-event-hubs-capture-is-charged"></a>Hur Event Hubs avbilda debiteras

Event Hubs avbilda mäts på samma sätt att genomflödesenheter: som timkostnaden. Tillägget är proportionell mot antalet genomflödesenheter för namnområdet. Genomflödesenheter ökas och minskas, Event Hubs avbilda mätare öka och minska för att tillhandahålla matchande prestanda. Mätaren uppstå tillsammans. Information om priser, se [priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Nästa steg

Event Hubs avbilda är det enklaste sättet att hämta data till Azure. Med Azure Data Lake och Azure Data Factory Azure HDInsight kan du utföra batchbearbetning och andra analytics med hjälp av välbekanta verktyg och plattformar du väljer, skaländras du behöver.

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Börja skicka och ta emot händelser](event-hubs-dotnet-framework-getstarted-send.md)
* [Översikt av händelsehubbar][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
