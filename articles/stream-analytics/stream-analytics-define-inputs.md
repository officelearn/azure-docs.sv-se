---
title: Strömma data som indata till Azure Stream Analytics
description: Läs mer om hur du konfigurerar en dataanslutning i Azure Stream Analytics. Indata inkluderar en dataström från händelser och även referensdata.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254474"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Strömma data som indata i Stream Analytics

Stream Analytics har förstklassig integrering med Azure-dataströmmar som indata från tre typer av resurser:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-lagring](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indataresurser kan visas i samma Azure-prenumeration som ditt Stream Analytics-jobb eller en annan prenumeration.

### <a name="compression"></a>Komprimering

Stream Analytics stöder komprimering över alla inmatningskällor för dataström. Komprimeringstyper som stöds är: Ingen, GZip och Deflate-komprimering. Stöd för komprimering är inte tillgängligt för referensdata. Om indataformatet är Avro-data som är komprimerat hanteras det transparent. Du behöver inte ange komprimeringstyp med Avro-serialisering. 

## <a name="create-edit-or-test-inputs"></a>Skapa, redigera eller testa indata

Du kan använda [Azure Portal,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)och Visual [Studio Code](quick-create-vs-code.md) för att lägga till och visa eller redigera befintliga indata på ditt direktuppspelningsjobb. Du kan också testa indataanslutningar och [testfrågor](stream-analytics-manage-job.md#test-your-query) från exempeldata från Azure-portalen, [Visual Studio](stream-analytics-vs-tools-local-run.md)och Visual [Studio Code](visual-studio-code-local-run.md). När du skriver en fråga listar du indata i FROM-satsen. Du kan hämta listan över tillgängliga indata från **sidan Fråga** i portalen. Om du vill använda flera indata kan `JOIN` `SELECT` du dem eller skriva flera frågor.


## <a name="stream-data-from-event-hubs"></a>Strömma data med Event Hubs

Azure Event Hubs ger mycket skalbara publicerings-prenumerera händelse ingestors. En händelsehubb kan samla in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Tillsammans erbjuder Event Hubs och Stream Analytics en heltäckande lösning för analys i realtid. Med Event Hubs kan du mata in händelser i Azure i realtid, och Stream Analytics-jobb kan bearbeta dessa händelser i realtid. Du kan till exempel skicka webbklick, sensoravläsningar eller onlinelogghändelser till eventhubbar. Du kan sedan skapa Stream Analytics-jobb för att använda eventhubbar som indataströmmar för filtrering, aggregering och korrelation i realtid.

`EventEnqueuedUtcTime`är tidsstämpeln för en händelses ankomst till en händelsenav och är standardtidsstämpeln för händelser som kommer från Event Hubs till Stream Analytics. Om du vill bearbeta data som en ström med hjälp av en tidsstämpel i händelsenyttolasten måste du använda nyckelordet [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Konsumentgrupper för eventhubbar

Du bör konfigurera varje Stream Analytics-händelsehubbinmatning så att den har en egen konsumentgrupp. När ett jobb innehåller en självkoppling eller har flera indata kan vissa indata läsas av mer än en läsare nedströms. Denna situation påverkar antalet läsare i en enda konsumentgrupp. Om du vill undvika att överskrida gränsen för händelsehubbar på fem läsare per konsumentgrupp per partition är det en bra idé att ange en konsumentgrupp för varje Stream Analytics-jobb. Det finns också en gräns på 20 konsumentgrupper för en standardnivåhändelsenav. Mer information finns i [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Skapa en indata från eventhubbar

I följande tabell beskrivs varje egenskap på sidan **Ny indata** i Azure-portalen för att strömma dataindata från en händelsehubb:

| Egenskap | Beskrivning |
| --- | --- |
| **Indataalias** |Ett eget namn som du använder i jobbets fråga för att referera till indata. |
| **Prenumeration** | Välj den prenumeration där händelsenavresursen finns. | 
| **Namnområde för händelsehubb** | Namnet Event Hub är en behållare för en uppsättning meddelandeentiteter. När du skapar en ny händelsehubb skapar du även namnområdet. |
| **Namn på händelsehubben** | Namnet på händelsehubben som ska användas som indata. |
| **Principnamn för Event Hub** | Principen för delad åtkomst som ger åtkomst till händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. Det här alternativet fylls i automatiskt, såvida du inte väljer alternativet för att ange inställningarna för händelsehubben manuellt.|
| **Konsumentgrupp** för Event Hub (rekommenderas) | Vi rekommenderar starkt att du använder en separat konsumentgrupp för varje Stream Analytics-jobb. Den här strängen identifierar konsumentgruppen som ska användas för att använda för att använda data från händelsehubben. Om ingen konsumentgrupp har angetts använder Stream Analytics-jobbet $Default konsumentgrupp.  |
| **Partitionsnyckeln** | Om indata partitioneras av en egenskap kan du lägga till namnet på den här egenskapen. Partitionsnycklar är valfria och används för att förbättra frågans prestanda om den innehåller en PARTITION BY- eller GROUP BY-sats på den här egenskapen. |
| **Händelseserialiseringsformat** | Serialiseringsformatet (JSON, CSV, Avro eller [Annat (Protobuf, XML, proprietär...)](custom-deserializer.md)) för den inkommande dataströmmen.  Se till att JSON-formatet justeras mot specifikationen och inkluderar inte inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande det enda kodningsformat som stöds. |
| **Typ av händelsekomprimering** | Komprimeringstypen som används för att läsa den inkommande dataströmmen, till exempel Ingen (standard), GZip eller Töm. |

När dina data kommer från en indata för eventhubbström har du tillgång till följande metadatafält i din Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **HändelseprocessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av Event Hubs. |
| **Partitionid** |Det nollbaserade partitions-ID:et för indatakortet. |

Med hjälp av dessa fält kan du till exempel skriva en fråga som följande exempel:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> När du använder Event Hub som en slutpunkt för IoT Hub Routes kan du komma åt IoT Hub-metadata med hjälp av [funktionen GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Strömma data från IoT Hub

Azure IoT Hub är en mycket skalbar publicering-prenumerera händelse ingestor optimerad för IoT scenarier.

Standardtidsstämpeln för händelser som kommer från en IoT Hub i Stream Analytics är den tidsstämpel som händelsen kom till IoT Hub, som är `EventEnqueuedUtcTime`. Om du vill bearbeta data som en ström med hjälp av en tidsstämpel i händelsenyttolasten måste du använda nyckelordet [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Iot Hub Konsumentgrupper

Du bör konfigurera varje Stream Analytics IoT Hub-indata så att den har en egen konsumentgrupp. När ett jobb innehåller en självkoppling eller när det har flera indata kan vissa indata läsas av mer än en läsare nedströms. Denna situation påverkar antalet läsare i en enda konsumentgrupp. För att undvika att överskrida Azure IoT Hub-gränsen på fem läsare per konsumentgrupp per partition är det en bra idé att ange en konsumentgrupp för varje Stream Analytics-jobb.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurera en IoT Hub som en dataströmsinmatning

I följande tabell beskrivs varje egenskap på sidan **Ny indata** i Azure-portalen när du konfigurerar en IoT Hub som en indata för dataström.

| Egenskap | Beskrivning |
| --- | --- |
| **Indataalias** | Ett eget namn som du använder i jobbets fråga för att referera till indata.|
| **Prenumeration** | Välj den prenumeration där IoT Hub-resursen finns. | 
| **IoT Hub** | Namnet på IoT Hub som ska användas som indata. |
| **Slutpunkt** | Slutpunkten för IoT Hub.|
| **Principnamn för delad åtkomst** | Principen för delad åtkomst som ger åtkomst till IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. |
| **Principnyckel för delad åtkomst** | Den delade åtkomstnyckeln som används för att auktorisera åtkomst till IoT Hub.  Det här alternativet fylls i automatiskt om du inte väljer alternativet att ange Iot Hub-inställningarna manuellt. |
| **Konsumentgrupp** | Vi rekommenderar starkt att du använder en annan konsumentgrupp för varje Stream Analytics-jobb. Konsumentgruppen används för att få in data från IoT Hub. Stream Analytics använder $Default konsumentgrupp om du inte anger något annat.  |
| **Partitionsnyckeln** | Om indata partitioneras av en egenskap kan du lägga till namnet på den här egenskapen. Partitionsnycklar är valfria och används för att förbättra frågans prestanda om den innehåller en PARTITION BY- eller GROUP BY-sats på den här egenskapen. |
| **Händelseserialiseringsformat** | Serialiseringsformatet (JSON, CSV, Avro eller [Annat (Protobuf, XML, proprietär...)](custom-deserializer.md)) för den inkommande dataströmmen.  Se till att JSON-formatet justeras mot specifikationen och inkluderar inte inledande 0 för decimaltal. |
| **Kodning** | UTF-8 är för närvarande det enda kodningsformat som stöds. |
| **Typ av händelsekomprimering** | Komprimeringstypen som används för att läsa den inkommande dataströmmen, till exempel Ingen (standard), GZip eller Töm. |


När du använder strömma data från en IoT Hub har du tillgång till följande metadatafält i din Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **HändelseprocessedUtcTime** | Datum och tid då händelsen bearbetades. |
| **EventEnqueuedUtcTime** | Datum och tid då händelsen togs emot av IoT Hub. |
| **Partitionid** | Det nollbaserade partitions-ID:et för indatakortet. |
| **IoTHub.messageId** | Ett ID som används för att korrelera tvåvägskommunikation i IoT Hub. |
| **IoTHub.correlationId** | Ett ID som används i meddelandesvar och feedback i IoT Hub. |
| **IoTHub.ConnectionDeviceId** | Autentiserings-ID:et som används för att skicka det här meddelandet. Det här värdet stämplas på servicebound-meddelanden av IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | Genererings-ID:t för den autentiserade enhet som användes för att skicka det här meddelandet. Det här värdet stämplas på servicebound-meddelanden av IoT Hub. |
| **IoTHub.EnqueuedTime** | Den tidpunkt då meddelandet togs emot av IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Strömma data från Blob-lagring
För scenarier med stora mängder ostrukturerade data att lagra i molnet erbjuder Azure Blob-lagring en kostnadseffektiv och skalbar lösning. Data i Blob-lagring betraktas vanligtvis som data i vila. Blob-data kan dock bearbetas som en dataström av Stream Analytics. 

Loggbearbetning är ett vanligt scenario för att använda Blob-lagringsindata med Stream Analytics. I det här fallet har telemetridatafiler hämtats från ett system och måste tolkas och bearbetas för att extrahera meningsfulla data.

Standardtidsstämpeln för Blob-lagringshändelser i Stream Analytics är den tidsstämpel `BlobLastModifiedUtcTime`som bloben senast ändrades, vilket är . Om en blob överförs till ett lagringskonto klockan 13:00 och Azure Stream Analytics-jobbet startas med alternativet *Nu* klockan 13:01, hämtas inte bloben när dess ändrade tid faller utanför jobbkörningsperioden.

Om en blob överförs till en lagringskontobehållare klockan 13:00 och Azure Stream Analytics-jobbet startas med *anpassad tid* klockan 13:00 eller tidigare, hämtas bloben när dess ändrade tid faller inom jobbkörningsperioden.

Om ett Azure Stream Analytics-jobb startas med *Nu* klockan 13:00 och en blob överförs till lagringskontobehållaren klockan 13:01 hämtar Azure Stream Analytics blobben.

Om du vill bearbeta data som en ström med hjälp av en tidsstämpel i händelsenyttolasten måste du använda nyckelordet [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Ett Stream Analytics-jobb hämtar data från Azure Blob-lagringsindata varje sekund om blob-filen är tillgänglig. Om blob-filen inte är tillgänglig finns det en exponentiell backoff med en maximal tidsfördröjning på 90 sekunder.

CSV-formaterade indata kräver en rubrikrad för att definiera fält för datauppsättningen och alla rubrikradsfält måste vara unika.

> [!NOTE]
> Stream Analytics stöder inte att lägga till innehåll i en befintlig blob-fil. Stream Analytics visar varje fil bara en gång och alla ändringar som sker i filen efter att jobbet har läst data bearbetas inte. Bästa praxis är att ladda upp alla data för en blob-fil på en gång och sedan lägga till ytterligare nyare händelser till en annan, ny blob-fil.

Om du laddar upp ett mycket stort antal blobbar samtidigt kan Stream Analytics hoppa över att läsa några blobbar i sällsynta fall. Vi rekommenderar att du överför blobbar med minst 2 sekunders mellanrum till Blob-lagring. Om det här alternativet inte är möjligt kan du använda eventhubbar för att strömma stora volymer av händelser. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurera Blob-lagring som en indata för dataström 

I följande tabell beskrivs varje egenskap på sidan **Ny indata** i Azure-portalen när du konfigurerar Blob-lagring som en indata för dataström.

| Egenskap | Beskrivning |
| --- | --- |
| **Indataalias** | Ett eget namn som du använder i jobbets fråga för att referera till indata. |
| **Prenumeration** | Välj den prenumeration där IoT Hub-resursen finns. | 
| **Lagringskonto** | Namnet på lagringskontot där blob-filerna finns. |
| **Nyckeln för lagringskonto** | Den hemliga nyckeln som är associerad med lagringskontot. Det här alternativet fylls i automatiskt om du inte väljer alternativet att ange blob-lagringsinställningarna manuellt. |
| **Container** | Behållaren för blob-indata. Behållare ger en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blob till Azure Blob-lagringstjänsten måste du ange en behållare för den bloben. Du kan välja **antingen Använd befintlig** behållare eller Skapa **ny** om du vill att en ny behållare ska skapas.|
| **Banmönster** (valfritt) | Filsökvägen som används för att hitta blobbar i den angivna behållaren. Om du vill läsa blobbar från behållarens rot ska du inte ange något banmönster. I sökvägen kan du ange en eller flera förekomster av följande tre variabler: `{date}`, `{time}`eller`{partition}`<br/><br/>Exempel 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exempel 2:`cluster1/logs/{date}`<br/><br/>Tecknet `*` är inte ett tillåtet värde för sökvägsprefixet. Endast giltiga <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure-blob-tecken</a> är tillåtna. Ta inte med behållarnamn eller filnamn. |
| **Datumformat** (valfritt) | Om du använder datumvariabeln i sökvägen, det datumformat som filerna är ordnade i. Exempel: `YYYY/MM/DD` |
| **Tidsformat** (valfritt) |  Om du använder tidsvariabeln i sökvägen, det tidsformat som filerna är ordnade i. För närvarande är `HH` det enda värdet som stöds för timmar. |
| **Partitionsnyckeln** | Om indata partitioneras av en egenskap kan du lägga till namnet på den här egenskapen. Partitionsnycklar är valfria och används för att förbättra frågans prestanda om den innehåller en PARTITION BY- eller GROUP BY-sats på den här egenskapen. |
| **Händelseserialiseringsformat** | Serialiseringsformatet (JSON, CSV, Avro eller [Annat (Protobuf, XML, proprietär...)](custom-deserializer.md)) för den inkommande dataströmmen.  Se till att JSON-formatet justeras mot specifikationen och inkluderar inte inledande 0 för decimaltal. |
| **Kodning** | För CSV och JSON är UTF-8 för närvarande det enda kodningsformat som stöds. |
| **Komprimering** | Komprimeringstypen som används för att läsa den inkommande dataströmmen, till exempel Ingen (standard), GZip eller Töm. |

När dina data kommer från en Blob-lagringskälla har du tillgång till följande metadatafält i din Stream Analytics-fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **BlobName (BlobName)** |Namnet på den indatablob som händelsen kom från. |
| **HändelseprocessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum och tid då blobben senast ändrades. |
| **Partitionid** |Det nollbaserade partitions-ID:et för indatakortet. |

Med hjälp av dessa fält kan du till exempel skriva en fråga som följande exempel:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
