---
title: Stream-data som indata i Azure Stream Analytics
description: Lär dig mer om hur du konfigurerar en dataanslutning i Azure Stream Analytics. Indata innehåller en dataström från händelser och även referensdata.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2bd2a90c6bac280b6f68bde378b7f7d32b7fcd38
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248755"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream-data som indata till Stream Analytics

Stream Analytics har förstklassig integrering med Azure-dataströmmar som indata från tre typer av resurser:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indata resurser kan finnas i samma Azure-prenumeration som Stream Analytics-jobb eller en annan prenumeration.

### <a name="compression"></a>Komprimering
Stream Analytics stöder komprimering för alla stream inkommande datakällor. Referenstyper stöds för tillfället är: None, GZip, och Deflate-komprimering. Stöd för komprimering är inte tillgängligt för referensdata. Om Indataformatet är Avro-data som är komprimerade, hanteras den transparent. Du behöver inte ange Komprimeringstypen med Avro-serialisering. 

## <a name="create-edit-or-test-inputs"></a>Skapa, redigera eller testa indata
Du kan använda den [Azure-portalen](https://portal.azure.com) till [Skapa ny indata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-input-to-the-job) och visa eller redigera befintliga indata på ditt direktuppspelningsjobb. Du kan också testa inkommande anslutningar och [testfrågor](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query) från exempeldata. När du skriver en fråga visas indata i FROM-satsen. Du kan hämta listan över tillgängliga indata från den **fråga** i portalen. Om du vill använda flera inmatningar kan du `JOIN` dem eller skriva flera `SELECT` frågor.


## <a name="stream-data-from-event-hubs"></a>Strömma data med Event Hubs

Azure Event Hubs ger mycket skalbar publicerings-/ händelse ingestors. En händelsehubb kan samla in miljontals händelser per sekund, så att du kan bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Event Hubs och Stream Analytics ger du tillsammans, en lösning för slutpunkt till slutpunkt för analys i realtid. Händelsehubbar kan du skicka händelser till Azure i realtid och Stream Analytics-jobb kan bearbeta dessa händelser i realtid. Du kan exempelvis skicka web klick, sensoravläsningar eller online händelser till Event Hubs. Du kan sedan skapa Stream Analytics-jobb för att använda Event Hubs som indata-dataströmmar i realtid filtrering, aggregering och korrelation.

`EventEnqueuedUtcTime` tidsstämpeln för ankomst en händelse i en händelsehubb och är standard tidsstämpeln för händelser som kommer från Event Hubs till Stream Analytics. Bearbeta data som en dataström med en tidsstämpel i den händelse att nyttolasten, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics-händelsehubb indata har sin egen konsumentgrupp. När ett jobb innehåller en självkoppling eller har flera inmatningar, vissa indata kan läsas av mer än en läsare nedströms. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. För att inte Händelsehubbar högst fem läsare per konsumentgrupp per partition, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobbet. Det finns också en gräns på 20 konsumentgrupper per händelsehubb. Mer information finns i [felsöka Azure Stream Analytics indata](stream-analytics-troubleshoot-input.md).

### <a name="stream-data-from-event-hubs"></a>Strömma data med Event Hubs
I följande tabell beskrivs varje egenskap i den **nya indata** sidan på Azure portal till strömindata data från en händelsehubb:

| Egenskap  | Beskrivning |
| --- | --- |
| **Inmatat alias** |Ett eget namn som du använder i jobbets fråga för att referera till detta indata. |
| **Prenumeration** | Välj den prenumeration där Event hub-resurs finns. | 
| **Namnområde för Händelsehubb** | Event Hub-namnområde är en behållare för en uppsättning meddelandeentiteter. När du skapar en ny händelsehubb kan skapa du också namnområdet. |
| **Namn på Händelsehubb** | Namnet på händelsehubben för att använda som indata. |
| **Principnamn för Event Hub** | Princip för delad åtkomst som ger åtkomst till Händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Det här alternativet fylls i automatiskt, såvida du inte väljer alternativet för att skapa Event Hub-inställningar manuellt.|
| **Händelsehubbkonsumentgrupp** (rekommenderas) | Vi rekommenderar starkt att använda en distinkt konsumentgrupp för varje Stream Analytics-jobbet. Den här strängen identifierar konsumentgrupp du använder för att mata in data från händelsehubben. Om ingen konsumentgrupp har angetts använder Stream Analytics-jobbet konsumentgruppen $Default.  |
| **Händelseserialiseringsformat** | Serialisering formatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds Kodningsformatet. |
| **Komprimeringstyp för händelsen** | Den typ av komprimering som används för att läsa en inkommande dataström, till exempel ingen (standard), GZip eller Deflate. |

När data kommer från en Event Hub-strömindata, har du åtkomst till följande metadatafält i ditt Stream Analytics-fråga:

| Egenskap  | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av Event Hubs. |
| **Partitions-ID** |Nollbaserade partitions-ID för kortet indata. |

Till exempel kan använder de här fälten, du skriva en fråga som i följande exempel:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> När du använder Event Hub som en slutpunkt för IoT Hub vägar, du kan komma åt i IoT Hub medadata med hjälp av den [funktionen GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Stream-data från IoT Hub
Azure Iot Hub är en mycket skalbar lösning för publicering-prenumeration händelse optimerade för IoT-scenarier.

Standard-tidsstämpel för händelser som kommer från en IoT-hubb i Stream Analytics är tidsstämpeln som händelsen anlänt i IoT Hub, vilket är `EventEnqueuedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i den händelse att nyttolasten, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics IoT-hubb som indata har sin egen konsumentgrupp. När ett jobb som innehåller en självkoppling eller när den har flera inmatningar, kan vissa indata läsas av mer än en läsare nedströms. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. Om du vill undvika överstiger fem läsare per konsumentgrupp per partition Azure IoT Hub, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobbet.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurera en IoT-hubb som en dataström som indata
I följande tabell beskrivs varje egenskap i den **nya indata** sidan på Azure portal när du konfigurerar en IoT-hubb som en dataström som indata.

| Egenskap  | Beskrivning |
| --- | --- |
| **Inmatat alias** | Ett eget namn som du använder i jobbets fråga för att referera till detta indata.|
| **Prenumeration** | Välj den prenumeration som IoT Hub-resursen finns. | 
| **IoT Hub** | Namnet på IoT-hubben ska användas som indata. |
| **Slutpunkt** | Slutpunkt för IoT-hubben.|
| **Principnamn för delad åtkomst** | Princip för delad åtkomst som ger åtkomst till IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| **Principnyckel för delad åtkomst** | Den delade åtkomstnyckeln som används för att auktorisera åtkomst till IoT Hub.  Det här alternativet fylls automatiskt i såvida du inte väljer alternativet för att skapa Iot-hubben inställningar manuellt. |
| **Konsumentgrupp** | Vi rekommenderar starkt att du använder en annan konsumentgrupp för varje Stream Analytics-jobb. Konsumentgruppen används för att mata in data från IoT Hub. Stream Analytics använder konsumentgruppen $Default såvida inget annat anges.  |
| **Händelseserialiseringsformat** | Serialisering formatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds Kodningsformatet. |
| **Komprimeringstyp för händelsen** | Den typ av komprimering som används för att läsa en inkommande dataström, till exempel ingen (standard), GZip eller Deflate. |


När du använder strömdata från en IoT-hubb, har du åtkomst till följande metadatafält i ditt Stream Analytics-fråga:

| Egenskap  | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** | Datum och tid då händelsen bearbetades. |
| **EventEnqueuedUtcTime** | Datum och tid då händelsen togs emot av IoT Hub. |
| **Partitions-ID** | Nollbaserade partitions-ID för kortet indata. |
| **IoTHub.MessageId** | Ett ID som används för att korrelera dubbelriktad kommunikation i IoT Hub. |
| **IoTHub.CorrelationId** | Ett ID som används i meddelandesvar och feedback i IoT Hub. |
| **IoTHub.ConnectionDeviceId** | ID för autentisering används för att skicka det här meddelandet. Det här värdet är stämplad servicebound meddelanden av IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | Generationsid för den autentiserade enheten som används för att skicka meddelandet. Det här värdet är stämplad servicebound meddelanden av IoT Hub. |
| **IoTHub.EnqueuedTime** | Den tid när meddelandet togs emot av IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Stream-data från Blob storage
För scenarier med stora mängder Ostrukturerade data som lagras i molnet, erbjuder Azure Blob-lagring en kostnadseffektiv och skalbar lösning. Data i Blob storage anses vanligtvis data i vila; blob-data kan dock bearbetas som en dataström med Stream Analytics. 

Bearbetning av loggar är ett vanligt scenario för att använda Blob storage indata med Stream Analytics. I det här scenariot telemetri datafiler fångat från ett system och behöver parsas och bearbetas för att extrahera användbara data.

Standard-tidsstämpel för Blob storage-händelser i Stream Analytics är tidsstämpeln att blobben senast ändrades, vilket är `BlobLastModifiedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i den händelse att nyttolasten, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord. Ett Stream Analytics-jobb hämtar data från Azure Blob storage indata per sekund om blob-fil är tillgänglig. Om blob-fil är inte tillgänglig, är det en exponentiell backoff med Maximal fördröjning på 90 sekunder.

CSV-formaterad indata *kräver* en rubrikrad att definiera fält för datauppsättningen och alla rubrikfält rad måste vara unikt.

Stream Analytics stöder för närvarande inte deserialisering av AVRO-meddelanden som genereras av Event Hub avbildnings- eller anpassad slutpunkt för IoT Hub Azure Storage-behållare.

> [!NOTE]
> Stream Analytics stöder inte att lägga till innehåll på en befintlig blobfil. Stream Analytics Visa bara en gång varje fil och alla ändringar som görs i filen när jobbet har läst data bearbetas inte. Det är bra att överföra alla data för en blob-fil på en gång och sedan lägga till ytterligare nya händelser i en annan, en ny blob-fil.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurera Blob-lagring som en dataström som indata 

I följande tabell beskrivs varje egenskap i den **nya indata** sidan på Azure portal när du konfigurerar Blob storage som en dataström som indata.

| Egenskap  | Beskrivning |
| --- | --- |
| **Inmatat alias** | Ett eget namn som du använder i jobbets fråga för att referera till detta indata. |
| **Prenumeration** | Välj den prenumeration som IoT Hub-resursen finns. | 
| **Lagringskonto** | Namnet på det lagringskonto där blob-filerna finns. |
| **Lagringskontonyckel** | Den hemliga nyckeln som är associerade med lagringskontot. Det här alternativet fylls automatiskt i såvida du inte väljer alternativet för att skapa Blob storage-inställningarna manuellt. |
| **Behållare** | Behållare för blob som indata. Behållare är en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du laddar upp en blob till Azure Blob storage-tjänsten måste du ange en behållare för blobben. Du kan välja antingen **Använd befintlig** behållare eller **Skapa nytt** ha en ny behållare har skapats.|
| **Sökvägsmönster** (valfritt) | Filsökvägen som används för att hitta blobbar i den angivna behållaren. I sökvägen, kan du ange en eller flera instanser av följande tre variabler: `{date}`, `{time}`, eller `{partition}`<br/><br/>Exempel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exempel 2: `cluster1/logs/{date}`<br/><br/>Den `*` tecken är inte ett tillåtet värde för prefixet sökväg. Endast giltigt <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob-tecken</a> tillåts. Namn på behållare och filnamnen omfattas inte inte. |
| **Datumformat** (valfritt) | Om du använder variabeln datum i sökvägen, datumformat där filerna ordnas. Exempel: `YYYY/MM/DD` |
| **Tidsformat** (valfritt) |  Om du använder variabeln tid i sökvägen, där filerna ordnas tidsformatet. Det enda värdet som stöds är för närvarande `HH` i timmar. |
| **Händelseserialiseringsformat** | Serialisering formatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds Kodningsformatet för CSV och JSON. |
| **Komprimering** | Den typ av komprimering som används för att läsa en inkommande dataström, till exempel ingen (standard), GZip eller Deflate. |

När data kommer från en källa för Blob storage, har du åtkomst till följande metadatafält i ditt Stream Analytics-fråga:

| Egenskap  | Beskrivning |
| --- | --- |
| **BlobName** |Namnet på indata-blob som händelsen kommer ifrån. |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum och tid då blobben som senast ändrades. |
| **Partitions-ID** |Nollbaserade partitions-ID för kortet indata. |

Till exempel kan använder de här fälten, du skriva en fråga som i följande exempel:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
