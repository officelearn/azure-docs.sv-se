---
title: Telemetri för Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services-telemetri.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e8b493881662483e66dd835d1cc68a471b18454
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803316"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services-telemetri  


> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services (AMS) kan du komma åt telemetri/mätvärden för sina tjänster. Den aktuella versionen av AMS kan du samla in telemetridata för live **kanal**, **StreamingEndpoint**, live och **Arkiv** entiteter. 

Telemetri skrivs till en lagringstabell i ett Azure Storage-konto som du anger (vanligtvis använder du lagringskontot som är associerade med ditt AMS-konto). 

Telemetrisystemet hanterar inte datakvarhållning. Du kan ta bort gamla telemetridata genom att ta bort storage-tabeller.

Det här avsnittet beskrivs hur du konfigurerar och använder AMS-telemetri.

## <a name="configuring-telemetry"></a>Konfigurera telemetri

Du kan konfigurera telemetri på en nivå granularitet för komponenten. Det finns två detaljnivåer ”Normal” och ”utförlig”. För närvarande kan returnera båda nivåerna samma information. Det rekommenderas att använda ”Normal. 

De följande avsnitten visar hur du aktiverar telemetri:

[Aktivera telemetri med .NET](media-services-dotnet-telemetry.md) 

[Aktivera telemetri med REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Förbrukar telemetriinformation

Telemetri skrivs till en Azure Storage-tabell i lagringskontot som du angav när du har konfigurerat telemetri för Media Services-kontot. Det här avsnittet beskrivs lagringstabeller för mätvärden.

Du kan använda dessa data på något av följande sätt:

- Läsa data direkt från Azure Table Storage (t.ex. med Storage SDK). Beskrivning av telemetri storage-tabeller finns i den **förbrukar telemetriinformation** i [detta](https://msdn.microsoft.com/library/mt742089.aspx) avsnittet.

Eller

- Använd stöd i Media Services .NET SDK för att läsa in lagringsdata, enligt beskrivningen i [detta](media-services-dotnet-telemetry.md) avsnittet. 


Schemat för telemetri som beskrivs nedan har utformats för att ge bra prestanda inom ramen för Azure Table Storage:

- Data har partitionerats med konto-ID och tjänst-ID för att tillåta telemetri från varje tjänst ska efterfrågas oberoende av varandra.
- Partitioner innehålla datum att ge en rimlig övre gräns på partitionsstorleken.
- Radnycklar finns i omvänd tidsordning så att de senaste telemetri-objekt som ska efterfrågas för en viss tjänst.

Det här ger många vanliga frågor för att effektivt:

- Parallella, oberoende hämtning av data för separata tjänster.
- Hämta alla data för en viss tjänst i ett visst datumintervall.
- Hämta senaste data för en tjänst.

### <a name="telemetry-table-storage-output-schema"></a>Telemetri tabellschemat storage utdata

Dessa data lagras i samlingen i en tabell, ”TelemetryMetrics20160321” där ”20160321” är datumet för den skapade tabellen. Telemetrisystem skapar en separat tabell för varje ny dag baserat på 00:00 UTC. Tabellen används för att lagra återkommande värden som matar in bithastighet inom en viss period av tid, skickade byte osv. 

Egenskap |Värde|Exempel/Anteckningar
---|---|---
PartitionKey|{account ID} _ {entitets-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Konto-ID är inkluderat i partitionsnyckel för att förenkla arbetsflöden där flera Media Services-konton skriver till samma lagringskonto.
RowKey|{seconds to midnight}_{random value}|01688_00199<br/><br/>Radnyckeln börjar med antalet sekunder till midnatt att tillåta övre n style frågor inom en partition. Mer information finns i [den här artikeln](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern). 
Tidsstämpel|Datum/tid|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Type|Typen av enhet som tillhandahåller telemetridata för|Kanalen eller StreamingEndpoint/arkivfiler<br/><br/>Händelsetypen är bara ett strängvärde.
Namn|Namnet på händelsen telemetri|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Den tid som telemetri händelsen inträffade (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Den observerade tid kommer från den entitet som skickar telemetri (till exempel en kanal). Det kan finnas tid göra en uppskattning av synkroniseringsproblem mellan komponenter så att det här värdet är
ServiceID|{service ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Egenskaper för entitet|Som definieras av händelsen|StreamName: stream1, bithastighet 10123 vid...<br/><br/>Återstående egenskaper har definierats för given händelsetyp. Azure innehållet är nyckelvärdepar.  (det vill säga olika rader i tabellen har olika uppsättningar med egenskaper).

### <a name="entity-specific-schema"></a>Schema för entiteter

Det finns tre typer av entiteter telemetriska dataposter vidare med följande frekvens:

- Slutpunkter för direktuppspelning: Med 30 sekunders mellanrum
- Livekanaler: Varje minut
- Live-arkivet: Varje minut

**Slutpunkten för direktuppspelning**

Egenskap |Värde|Exempel
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpel från Azure Table 2016-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
Namn|Namn|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Värdnamn|Värdnamnet för slutpunkten|builddemoserver.origin.mediaservices.windows.net
StatusCode|Poster HTTP-status|200
Resultatkod|Information om resultat|S_OK
RequestCount|Totala antalet begäranden i sammansättning|3
BytesSent|Sammanställda byte som skickats|2987358
ServerLatency|Genomsnittlig serversvarstid (inklusive storage)|129
E2ELatency|Genomsnittlig svarstid för slutpunkt till slutpunkt|250

**Live channel**

Egenskap |Värde|Exempel/Anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Type|Type|Kanal
Namn|Namn|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ av spåra video/ljud/text|video/audio
TrackName|Namnet på kursen|video/audio_1
Bithastighet|Spåra bithastighet|785000
CustomAttributes||   
IncomingBitrate|Faktiska inkommande bithastighet|784548
OverlapCount|Överlappar i för inmatning|0
DiscontinuityCount|Avvikelse för spår|0
LastTimestamp|Tidsstämpel för senaste insamlade data|1800488800
NonincreasingCount|Antal fragment förkastas på grund av icke-ökande tidsstämpel|2
UnalignedKeyFrames|Om vi har tagit emot fragmenten (över kvalitetsnivå) där nyckeln inte justerade bildrutor |True
UnalignedPresentationTime|Om vi har tagit emot fragmenten (alla kvalitet nivåer/spår) där presentation tid är inte justerad|True
UnexpectedBitrate|Värdet är true, == beräknade/faktiska bithastigheter för ljud/video spåra > 40 000 bit/s och IncomingBitrate eller IncomingBitrate och actualBitrate skiljer sig med 50% 0 |True
Felfri|SANT, om <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> är alla 0|True<br/><br/>Felfria är en sammansatt funktion som returnerar false när något av följande villkor håller:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**Live-arkivet**

Egenskap |Värde|Exempel/Anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Type|Type|Arkiv
Namn|Namn|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Program-url|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Namnet på kursen|Audio_1
TrackType|Typ av kursen|Audio/video
CustomAttribute|Hexadecimal sträng som skiljer mellan olika spåra med samma namn och bithastighet (multi kameravinkel)|
Bithastighet|Spåra bithastighet|785000
Felfri|SANT, om FragmentDiscardedCount == 0 & & ArchiveAcquisitionError false|True (dessa två värden finns inte i måttet men de finns i händelsen källa)<br/><br/>Felfria är en sammansatt funktion som returnerar false när något av följande villkor håller:<br/><br/>- FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Allmänna frågor och svar

### <a name="how-to-consume-metrics-data"></a>Hur man använder måttdata?

Måttdata lagras som en serie med Azure-tabeller i kundens lagringskonto. Dessa data kan användas med hjälp av följande verktyg:

- AMS SDK
- Microsoft Azure Storage Explorer (stöd för att exportera till CSV-format och bearbetade i Excel)
- REST-API

### <a name="how-to-find-average-bandwidth-consumption"></a>Så här hittar du genomsnittlig bandbreddsanvändning?

Den genomsnittliga bandbreddsanvändningen är medelvärdet för BytesSent under en tidsperiod.

### <a name="how-to-define-streaming-unit-count"></a>Hur du definierar antal strömmande enheter?

Antalet strömmande enhet kan definieras som högsta dataflödet från tjänstens slutpunkter för direktuppspelning dividerat med högsta dataflödet för en slutpunkt för direktuppspelning. Högsta användbara dataflödet för en slutpunkt för direktuppspelning är 160 Mbit/s.
Anta exempelvis att det högsta dataflöden från en kund-tjänsten är 40 Mbit/s (det maximala värdet BytesSent under en tidsperiod). Sedan det direktuppspelade antalet är lika med (40 Mbit/s) * (8 bitarna/byte) /(160 Mbps) = 2 enheter för strömning.

### <a name="how-to-find-average-requestssecond"></a>Så här hittar du medelvärde begäranden/sekund?

För att hitta det genomsnittliga antalet förfrågningar per sekund, för att beräkna det genomsnittliga antalet förfrågningar (RequestCount) under en tidsperiod.

### <a name="how-to-define-channel-health"></a>Hur du definierar kanal health?

Kanalen hälsa kan definieras som en sammansatt boolesk funktion som är false när något av följande villkor håller:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Så här identifierar avbrott?

Hitta alla dataposter för kanalen för att identifiera avbrott, där DiscontinuityCount > 0. Den motsvarande ObservedTime tidsstämpeln visar de tider då avbrott inträffade.

### <a name="how-to-detect-timestamp-overlaps"></a>Så identifierar tidsstämpel överlappar?

Hitta alla dataposter för kanalen för att identifiera tidsstämpel överlappningar där OverlapCount > 0. Den motsvarande ObservedTime tidsstämpeln visar de tider då tidsstämpeln överlappar inträffade.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Så här hittar du strömmande begäran fel och orsaker?

Hitta alla slutpunkt för direktuppspelning dataposter där Resultatkod inte är lika med S_OK för att hitta strömmande begäran fel och orsaker. Motsvarande StatusCode fält anger orsaken till felet i begäran.

### <a name="how-to-consume-data-with-external-tools"></a>Hur du använder data med externa verktyg?

Telemetriska data kan bearbetas och visualiseras med följande verktyg:

- PowerBI
- Application Insights
- Azure Monitor (tidigare lådan)
- Instrumentpanel för AMS-liveuppspelning
- Azure-portalen (väntande version)

### <a name="how-to-manage-data-retention"></a>Så här hanterar du datakvarhållning?

Telemetrisystemet ger inte datahantering för kvarhållning eller automatisk borttagning av den äldre poster. Därför måste du hantera och ta bort gamla poster manuellt från storage-tabell. Du kan referera till storage SDK för gör så.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
