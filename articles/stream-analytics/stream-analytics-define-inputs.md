---
title: Dataströmmen data som indata till Azure Stream Analytics
description: Lär dig mer om hur du konfigurerar en dataanslutning i Azure Stream Analytics. Indata innehåller en dataström från händelser och även referensdata.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2b2ef68622f96d87a25d203d3d67aa0877397072
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Dataströmmen data som indata till Stream Analytics

Stream Analytics har förstklassigt integrering med Azure-dataströmmar som indata från tre typer av resurser:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indata resurser kan finnas kvar i samma Azure-prenumeration som Stream Analytics-jobb, eller från en annan prenumeration.

### <a name="compression"></a>Komprimering
Stream Analytics stöder komprimering över alla dataströmmen inkommande datakällor. Referenstyper stöds för närvarande är: None, GZip, och Deflate-komprimering. Det finns inte stöd för komprimering för referensdata. Om Indataformatet är Avro-data som är komprimerade, hanteras den transparent. Du behöver inte ange Komprimeringstypen med Avro-serialisering. 

## <a name="create-or-edit-inputs"></a>Skapa eller redigera indata
För att skapa nya indata, och visa eller redigera befintliga indata på din direktuppspelningsjobbet, kan du använda Azure-portalen:
1. Öppna den [Azure-portalen](https://portal.azure.com) att leta upp och markera Stream Analytics-jobbet.
2. Välj den **indata** alternativ den **inställningar** rubrik. 
4. Den **indata** sidan visas alla befintliga indata. 
5. På den **indata** väljer **lägga till strömindata** eller **Lägg till referens indata** att öppna sidan.
6. Välj en befintlig indata för att redigera informationen och välj **spara** att redigera en befintlig indata.
7. Välj **Test** på sidan inkommande information för att verifiera att anslutningsalternativen är giltiga och fungerar. 
8. Högerklicka på namnet på en befintlig indata och välj **exempeldata från indata** som behövs för att ytterligare tester.


## <a name="stream-data-from-event-hubs"></a>Dataströmmen data från Händelsehubbar

Händelsehubbar i Azure ger skalbara händelse ingestors att publicera och prenumerera. En händelsehubb kan samla in miljontals händelser per sekund, så att du kan bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Händelsehubbar och Stream Analytics ger tillsammans dig en lösning för slutpunkt till slutpunkt för realtidsanalys. Händelsehubbar kan du feed händelser till Azure i realtid och Stream Analytics-jobb kan bearbeta dessa händelser i realtid. Du kan exempelvis skicka web klick, sensoravläsningar eller online logghändelser till Händelsehubbar. Du kan sedan skapa Stream Analytics-jobb för att använda Händelsehubbar som indata dataströmmar för realtid filtrering och aggregering korrelation.

Standard-tidsstämpel för händelser som kommer från Händelsehubbar i Stream Analytics är tidsstämpeln som händelsen anlänt i hubben, som är `EventEnqueuedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics-händelsehubb indata har sin egen konsumentgrupp. Indata kan läsas av mer än en läsare nedströms när ett jobb som innehåller en självkoppling eller när den har flera inmatningar. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. För att undvika överstiger Händelsehubbar fem läsare per konsumentgrupp per partition, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobb. Det finns en gräns på 20 konsumentgrupper per händelsehubb. Mer information finns i [felsöka Azure Stream Analytics med händelsehubbmottagare](stream-analytics-event-hub-consumer-groups.md).

### <a name="stream-data-from-event-hubs"></a>Dataströmmen data från Händelsehubbar
I följande tabell beskrivs varje egenskap i den **nya indata** sida i Azure portal och strömindata data från en Händelsehubb:

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** |Ett eget namn som du använder i jobbets fråga refererar detta indata. |
| **Prenumeration** | Välj den prenumeration där Event hub resursen finns. | 
| **Event Hub namnområde** | Event Hub-namnrymd är en behållare för en uppsättning meddelandeentiteter. När du skapar en ny händelsehubb kan skapa du också namnområdet. |
| **Namnet på Händelsehubben** | Namnet på händelsehubben för att använda som indata. |
| **Namnet på Händelsehubben princip** | Princip för delad åtkomst som ger åtkomst till Händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Det här alternativet fylls automatiskt i om du inte väljer alternativet för att ange Event Hub-inställningar manuellt.|
| **Event Hub konsumentgrupp** (rekommenderas) | Vi rekommenderar starkt att använda en distinkta konsumentgrupp för varje Stream Analytics-jobbet. Den här strängen identifierar konsumentgrupp att använda för att mata in data från event hub. Om ingen konsumentgrupp har angetts använder Stream Analytics-jobbet $Default konsumentgrupp.  |
| **Händelsen serialiseringsformat** | Serialiseringsformatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera JSON-format med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds kodningsformat. |
| **Komprimering händelsetyp** | Komprimeringstypen som används för att läsa inkommande dataström, till exempel ingen (standard), GZip och Deflate. |

När data kommer från en Händelsehubb strömindata, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av Händelsehubbar. |
| **partitions-ID** |Nollbaserade partitions-ID för inkommande adaptern. |

Till exempel kan med hjälp av dessa fält, du skriva en fråga som i följande exempel:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> När du använder Event Hub som en slutpunkt för IoT-hubb vägar, du har åtkomst till en IoT-hubb medadata med hjälp av den [GetMetadataPropertyValue funktionen](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Dataströmmen data från IoT-hubb
Azure Iot Hub är en mycket skalbar publicera och prenumerera händelseinmatare som är optimerade för IoT-scenarier.

Standard-tidsstämpel för händelser som kommer från en IoT-hubb i Stream Analytics är tidsstämpeln som händelsen anlänt i IoT-hubb som är `EventEnqueuedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

> [!NOTE]
> Endast meddelanden som skickas med en `DeviceClient` egenskapen kan bearbetas.
> 

### <a name="consumer-groups"></a>Konsumentgrupper
Du bör konfigurera varje Stream Analytics IoT-hubb som indata till har sin egen konsumentgrupp. Indata kan läsas av mer än en läsare nedströms när ett jobb som innehåller en självkoppling eller när den har flera inmatningar. Den här situationen påverkar antalet läsare i en enskild konsument-grupp. För att undvika överstiger Azure IoT Hub fem läsare per konsumentgrupp per partition, är det en bra idé att utse en konsumentgrupp för varje Stream Analytics-jobb.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurera en IoT-hubb som en dataström som indata
I följande tabell beskrivs varje egenskap i den **nya indata** sida i Azure-portalen när du konfigurerar en IoT-hubb som en dataström som indata.

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** | Ett eget namn som du använder i jobbets fråga refererar detta indata.|
| **Prenumeration** | Välj den prenumeration som IoT-hubb resursen finns. | 
| **IoT Hub** | Namnet på IoT-hubben ska användas som indata. |
| **slutpunkt** | Slutpunkten för IoT-hubb.|
| **Principnamn för delad åtkomst** | Princip för delad åtkomst som ger åtkomst till IoT-hubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| **Princip för delade åtkomstnyckeln** | Den delade åtkomstnyckeln som används för att auktorisera åtkomst till IoT-hubben.  Det här alternativet fylls automatiskt i om du inte väljer alternativet för att ange inställningar för Iot-hubben manuellt. |
| **Konsumentgrupp** | Vi rekommenderar starkt att du använder en annan konsumentgrupp för varje Stream Analytics-jobbet. Konsumentgruppen används för att mata in data från IoT-hubben. Stream Analytics använder konsumentgrupp $Default såvida inget annat anges.  |
| **Händelsen serialiseringsformat** | Serialiseringsformatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera JSON-format med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds kodningsformat. |
| **Komprimering händelsetyp** | Komprimeringstypen som används för att läsa inkommande dataström, till exempel ingen (standard), GZip och Deflate. |


När du använder dataströmmen data från en IoT-hubb, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** | Datum och tid då händelsen bearbetades. |
| **EventEnqueuedUtcTime** | Datum och tid då händelsen togs emot av IoT-hubben. |
| **partitions-ID** | Nollbaserade partitions-ID för inkommande adaptern. |
| **IoTHub.MessageId** | Ett ID som används för att korrelera dubbelriktad kommunikation i IoT-hubb. |
| **IoTHub.CorrelationId** | Ett ID som används i meddelandesvar och feedback i IoT-hubb. |
| **IoTHub.ConnectionDeviceId** | ID för autentisering används för att skicka meddelandet. Det här värdet är stämplad på servicebound meddelanden av IoT-hubben. |
| **IoTHub.ConnectionDeviceGenerationId** | Generationsid för den autentiserade enheten som används för att skicka meddelandet. Det här värdet är stämplad på servicebound meddelanden av IoT-hubben. |
| **IoTHub.EnqueuedTime** | Den tid då meddelandet togs emot av IoT-hubben. |
| **IoTHub.StreamId** | En anpassad händelse-egenskap som lagts till av avsändaren enheten. |


## <a name="stream-data-from-blob-storage"></a>Dataströmmen data från Blob storage
Azure Blob storage erbjuder en kostnadseffektiv och skalbar lösning för scenarier med stora mängder Ostrukturerade data som lagras i molnet. Data i Blob storage anses vanligtvis data i vila. Blob-data kan dock bearbetas som en dataström med Stream Analytics. 

Bearbetar är ett scenario som används ofta för att använda Blob storage indata med Stream Analytics. I det här scenariot telemetri datafiler har spelats in från ett system och behöver parsas och bearbetas om du vill extrahera meningsfull information.

Standard-tidsstämpel för Blob storage-händelser i Stream Analytics är tidsstämpeln att blob senast ändrades, vilket är `BlobLastModifiedUtcTime`. Bearbeta data som en dataström med en tidsstämpel i händelseloggen nyttolast, måste du använda den [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) nyckelord.

CSV-formaterad indata *kräver* rubrikrad att definiera fält för datauppsättningen och alla rubrikfält rad måste vara unika.

Stream Analytics stöder för närvarande inte Deserialiserar AVRO-meddelanden som genereras av Event Hub avbildning eller anpassade slutpunkter i IoT-hubb Azure Storage-behållare.

> [!NOTE]
> Stream Analytics stöder inte att lägga till innehåll i en befintlig blob-fil. Stream Analytics Visa bara en gång varje fil och alla ändringar som görs i filen när jobbet har läsa data bearbetas inte. Det är bra att överföra alla data för en blob-fil på en gång och sedan lägga till ytterligare nya händelser i en annan, ny blob-fil.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurera Blob storage som en dataström som indata 

I följande tabell beskrivs varje egenskap i den **nya indata** sida i Azure-portalen när du konfigurerar Blob storage som en dataström som indata.

| Egenskap | Beskrivning |
| --- | --- |
| **Ett inmatat alias** | Ett eget namn som du använder i jobbets fråga refererar detta indata. |
| **Prenumeration** | Välj den prenumeration som IoT-hubb resursen finns. | 
| **Lagringskonto** | Namnet på det lagringskonto där blob-filerna lagras. |
| **Lagringskontonyckel** | Den hemliga nyckeln som associeras med lagringskontot. Det här alternativet fylls i automatiskt om du inte väljer alternativet för att tillhandahålla Blob storage-inställningar manuellt. |
| **Behållaren** | Behållare för blob som indata. Behållare innehåller en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blob till Azure Blob storage-tjänst måste du ange en behållare för blobben. Du kan välja antingen **Använd befintliga** behållare eller **Skapa nytt** har en ny behållare som skapas.|
| **Sökvägar** (valfritt) | Den filsökväg som används för att hitta blobbar i den angivna behållaren. Du kan ange en eller flera instanser av följande tre variabler i sökvägen: `{date}`, `{time}`, eller `{partition}`<br/><br/>Exempel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exempel 2: `cluster1/logs/{date}`<br/><br/>Den `*` tecken är inte ett tillåtet värde för prefixet sökväg. Endast giltiga <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob-tecken</a> tillåts. |
| **Datumformatet** (valfritt) | Om du använder variabeln datum i sökvägen, där filerna ordnas datumformat. Exempel: `YYYY/MM/DD` |
| **Tidsformat** (valfritt) |  Om du använder variabeln tid i sökvägen, där filerna ordnas tidsformatet. Det enda värdet som stöds är för närvarande `HH` i timmar. |
| **Händelsen serialiseringsformat** | Serialiseringsformatet (JSON, CSV eller Avro) för inkommande dataström.  Kontrollera JSON-format med specifikationen och inte innehåller inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande endast stöds Kodningsformatet för CSV och JSON. |
| **Komprimering** | Komprimeringstypen som används för att läsa inkommande dataström, till exempel ingen (standard), GZip och Deflate. |

När data kommer från en källa för Blob storage, har du tillgång till följande metadatafält i Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **BlobName** |Namnet för blob som händelsen kommer ifrån. |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum och tid då blobben senast ändrades. |
| **partitions-ID** |Nollbaserade partitions-ID för inkommande adaptern. |

Till exempel kan med hjälp av dessa fält, du skriva en fråga som i följande exempel:

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
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
