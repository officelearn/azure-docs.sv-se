---
title: 'Dataanslutningen: dataströmmen indata från en händelseström | Microsoft Docs'
description: Lär dig mer om hur du konfigurerar en dataanslutning till Stream Analytics kallas ”indata'. Indata innehåller en dataström från händelser och även referensdata.
keywords: dataströmmen, dataanslutning, händelseströmmen
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 405fcf190ab03b84ec463da8a1942adb8e326498
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Dataanslutningen: Lär dig mer om data dataströmmen indata från händelser till Stream Analytics
Dataanslutningen till ett Stream Analytics-jobb är en dataström med händelser från datakällan, vilket kallas jobbets *inkommande*. Stream Analytics har förstklassigt integrering med Azure strömmen datakällor, inklusive [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/), och [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/). Dessa indatakällor kan vara från samma Azure-prenumerationen som analytics-jobb, eller från en annan prenumeration.

## <a name="data-input-types-data-stream-and-reference-data"></a>Data indatatyper: Data ström och referensdata data
När data skickas till en datakälla har det används av Stream Analytics-jobbet och bearbetas i realtid. Indata är indelade i två typer: data strömma indata och referera till indata.

### <a name="data-stream-inputs"></a>Dataströmmen indata
En dataström är en unbounded sekvens av händelser över tid. Stream Analytics-jobb måste innehålla minst en inkommande dataström. Händelsehubbar, IoT-hubb och Blob storage stöds som inkommande datakällor för dataströmmen. Händelsehubbar används för att samla in händelseströmmar från flera enheter och tjänster. Dessa strömmar kan innehålla sociala medier aktivitetsfeeds, lager handel information eller data från sensorer. IoT-hubbar är optimerade för att samla in data från anslutna enheter i scenarier i Sakernas Internet (IoT).  BLOB-lagring kan användas som en Indatakällan för att föra in stora mängder data som en dataström som loggfiler.  

### <a name="reference-data"></a>Referensdata
Stream Analytics stöder också indata som kallas *referensdata*. Det är extra data som är fasta eller ändrar långsamt. Den används vanligtvis för att utföra korrelation och sökningar. Exempelvis kan du ansluta till data i inkommande dataström till data i referensdata, ungefär som du vill utföra en SQL-koppling för att leta upp statiska värden. Azure Blob storage är för närvarande endast stöds Indatakällan för referensdata. Referens för datakällan blobbar är begränsade till 100 MB i storlek.

Information om hur du skapar referens indata finns [Använd referensdata](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Komprimering

Azure Stream Analytics stöder komprimering över alla dataströmmen inkommande datakällor (Händelsehubbar, IoT-hubb och Blob-lagring). Den här funktionen lägger till ett nytt alternativ i listrutan till den **nya indata** bladet i Azure-portalen så att du kan välja att komprimera dataströmmar. Stöds för närvarande referens typer är - ingen GZip, och Deflate-komprimering. Det finns inte stöd för komprimering för referensdata.

Du behöver inte ange Komprimeringstypen med Avro-serialisering. Om inkommande Avro-data komprimeras, hanteras den transparent. 

## <a name="create-data-stream-input-from-event-hubs"></a>Skapa en inkommande dataström från Event Hubs

Händelsehubbar i Azure ger skalbara händelse ingestors att publicera och prenumerera. En händelsehubb kan samla in miljontals händelser per sekund, så att du kan bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Händelsehubbar och Stream Analytics tillsammans ger dig en lösning för slutpunkt till slutpunkt för realtidsanalys – Händelsehubbar kan du feed händelser till Azure i realtid och Stream Analytics-jobb kan bearbeta dessa händelser i realtid. Du kan exempelvis skicka web klick, sensoravläsningar eller online logghändelser till Händelsehubbar. Du kan sedan skapa Stream Analytics-jobb för att använda Händelsehubbar som indata dataströmmar för realtid filtrering och aggregering korrelation.

Standard-tidsstämpel för händelser som kommer från Händelsehubbar i Stream Analytics är tidsstämpeln som händelsen anlänt i hubben, som är `EventEnqueuedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics-händelsehubb indata har sin egen konsumentgrupp. Indata kan läsas av mer än en läsare nedströms när ett jobb som innehåller en självkoppling eller när den har flera inmatningar. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. För att undvika överstiger Händelsehubbar fem läsare per konsumentgrupp per partition, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobb. Det finns en gräns på 20 konsumentgrupper per händelsehubb. Mer information finns i [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Konfigurera en händelsehubb som en dataström som indata
I följande tabell beskrivs varje egenskap i den **nya indata** bladet i Azure-portalen när du konfigurerar en händelsehubb som indata.

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** |Ett eget namn som du använder i jobbets fråga refererar detta indata. |
| **Service bus-namnrymd** |Ett Azure Service Bus-namnområde som är en behållare för en uppsättning meddelandeentiteter. När du skapar en ny händelsehubb kan skapa du också en Service Bus-namnrymd. |
| **Namnet på händelsehubben** |Namnet på händelsehubben för att använda som indata. |
| **Namnet på händelsehubben princip** |Princip för delad åtkomst som ger åtkomst till händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| **Event hub konsumentgrupp** (valfritt) |Konsumentgrupp att använda för att mata in data från event hub. Om ingen konsumentgrupp har angetts använder Stream Analytics-jobbet förinställd konsumentgrupp. Vi rekommenderar att du använder en distinkta konsumentgrupp för varje Stream Analytics-jobbet. |
| **Händelsen serialiseringsformat** |Serialiseringsformatet (JSON, CSV eller Avro) för inkommande dataström. |
| **Kodning** | UTF-8 är för närvarande endast stöds kodningsformat. |
| **Komprimering** (valfritt) | Komprimeringstypen (None, GZip och Deflate) för inkommande dataström. |

När data kommer från en händelsehubb, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av Händelsehubbar. |
| **PartitionId** |Nollbaserade partitions-ID för inkommande adaptern. |

Till exempel kan med hjälp av dessa fält, du skriva en fråga som i följande exempel:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> När du använder Event Hub som en slutpunkt för IoT-hubb vägar, du har åtkomst till en IoT-hubb medadata med hjälp av den [GetMetadataPropertyValue funktionen](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Skapa en inkommande dataström från IoT-hubb
Azure Iot Hub är en mycket skalbar publicera och prenumerera händelseinmatare som är optimerade för IoT-scenarier.

Standard-tidsstämpel för händelser som kommer från en IoT-hubb i Stream Analytics är tidsstämpeln som händelsen anlänt i IoT-hubb som är `EventEnqueuedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

> [!NOTE]
> Endast meddelanden som skickas med en `DeviceClient` egenskapen kan bearbetas.
> 
> 

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics IoT-hubb som indata till har sin egen konsumentgrupp. Indata kan läsas av mer än en läsare nedströms när ett jobb som innehåller en självkoppling eller när den har flera inmatningar. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. För att undvika överstiger Azure IoT Hub fem läsare per konsumentgrupp per partition, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobb.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurera en IoT-hubb som en dataström som indata
I följande tabell beskrivs varje egenskap i den **nya indata** bladet i Azure-portalen när du konfigurerar en IoT-hubb som indata.

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** |Ett eget namn som du använder i jobbets fråga refererar detta indata.|
| **IoT-hubb** |Namnet på IoT-hubben ska användas som indata. |
| **slutpunkt** |Slutpunkten för IoT-hubb.|
| **Principnamn för delad åtkomst** |Princip för delad åtkomst som ger åtkomst till IoT-hubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| **Princip för delade åtkomstnyckeln** |Den delade åtkomstnyckeln som används för att auktorisera åtkomst till IoT-hubben. |
| **Konsumentgrupp** (valfritt) |Konsumentgrupp att använda för att mata in data från IoT-hubben. Om ingen konsumentgrupp har angetts använder Stream Analytics-jobbet förinställd konsumentgrupp. Vi rekommenderar att du använder en annan konsumentgrupp för varje Stream Analytics-jobbet. |
| **Händelsen serialiseringsformat** |Serialiseringsformatet (JSON, CSV eller Avro) för inkommande dataström. |
| **Kodning** |UTF-8 är för närvarande endast stöds kodningsformat. |
| **Komprimering** (valfritt) | Komprimeringstypen (None, GZip och Deflate) för inkommande dataström. |

När data kommer från en IoT-hubb, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av IoT-hubben. |
| **PartitionId** |Nollbaserade partitions-ID för inkommande adaptern. |
| **IoTHub.MessageId** | Ett ID som används för att korrelera dubbelriktad kommunikation i IoT-hubb. |
| **IoTHub.CorrelationId** |Ett ID som används i meddelandesvar och feedback i IoT-hubb. |
| **IoTHub.ConnectionDeviceId** |ID för autentisering används för att skicka meddelandet. Det här värdet är stämplad på servicebound meddelanden av IoT-hubben. |
| **IoTHub.ConnectionDeviceGenerationId** |Generationsid för den autentiserade enheten som används för att skicka meddelandet. Det här värdet är stämplad på servicebound meddelanden av IoT-hubben. |
| **IoTHub.EnqueuedTime** |Den tid då meddelandet togs emot av IoT-hubben. |
| **IoTHub.StreamId** |En anpassad händelse-egenskap som lagts till av avsändaren enheten. |


## <a name="create-data-stream-input-from-blob-storage"></a>Skapa en inkommande dataström från Blob storage
Azure Blob storage erbjuder en kostnadseffektiv och skalbar lösning för scenarier med stora mängder Ostrukturerade data som lagras i molnet. Data i Blob storage anses vanligtvis data i vila. Det kan dock bearbetas som en dataström med Stream Analytics. Ett typiskt scenario för Blob storage indata med Stream Analytics är bearbetar. I det här scenariot telemetridata har spelats in från ett system och behöver parsas och bearbetas om du vill extrahera meningsfull information.

Standard-tidsstämpel för Blob storage-händelser i Stream Analytics är tidsstämpeln att blob senast ändrades, vilket är `BlobLastModifiedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

CSV-formaterad indata *kräver* rubrikrad att definiera fält för datauppsättningen. Alla rubrikfält raden måste dessutom vara unikt.

> [!NOTE]
> Stream Analytics stöder inte att lägga till innehåll i en befintlig blob-fil. Stream Analytics Visa bara en gång varje fil och alla ändringar som görs i filen när jobbet har läsa data bearbetas inte. Det är bra att överföra alla data för en blob-fil på en gång och sedan lägga till ytterligare nya händelser i en annan, ny blob-fil.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Konfigurera Blob storage som en dataström som indata

I följande tabell beskrivs varje egenskap i den **nya indata** bladet i Azure-portalen när du konfigurerar Blob storage som indata.

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** | Ett eget namn som du använder i jobbets fråga refererar detta indata. |
| **Lagringskonto** | Namnet på det lagringskonto där blob-filerna lagras. |
| **Lagringskontonyckel** | Den hemliga nyckeln som associeras med lagringskontot. |
| **Behållaren** | Behållare för blob som indata. Behållare innehåller en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blob till Azure Blob storage-tjänst måste du ange en behållare för blobben. |
| **Sökvägar** (valfritt) | Den filsökväg som används för att hitta blobbar i den angivna behållaren. Du kan ange en eller flera instanser av följande tre variabler i sökvägen: `{date}`, `{time}`, eller `{partition}`<br/><br/>Exempel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exempel 2: `cluster1/logs/{date}`<br/><br/>Den `*` tecken är inte ett tillåtet värde för prefixet sökväg. Endast giltiga <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob-tecken</a> tillåts. |
| **Datumformatet** (valfritt) | Om du använder variabeln datum i sökvägen, där filerna ordnas datumformat. Exempel: `YYYY/MM/DD` |
| **Tidsformat** (valfritt) |  Om du använder variabeln tid i sökvägen, där filerna ordnas tidsformatet. Det enda värdet som stöds är för närvarande `HH`. |
| **Händelsen serialiseringsformat** | Serialiseringsformat (JSON, CSV eller Avro) för inkommande dataströmmar. |
| **Kodning** | UTF-8 är för närvarande endast stöds Kodningsformatet för CSV och JSON. |
| **Komprimering** (valfritt) | Komprimeringstypen (None, GZip och Deflate) för inkommande dataström. |

När data kommer från en källa för Blob storage, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **BlobName** |Namnet för blob som händelsen kommer ifrån. |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum och tid då blobben senast ändrades. |
| **PartitionId** |Nollbaserade partitions-ID för inkommande adaptern. |

Till exempel kan med hjälp av dessa fält, du skriva en fråga som i följande exempel:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig om anslutningsalternativ för data i Azure för Stream Analytics-jobb. Mer information om Stream Analytics finns:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
