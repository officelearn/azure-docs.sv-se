---
title: Azure Media Services telemetri | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services telemetri.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 97df0876afd8b7258f985ab375b14f4aabde6e22
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetri

Azure Media Services (AMS) kan du komma åt telemetri/mätvärdesdata för dess tjänster. Den aktuella versionen av AMS kan du samla in telemetridata för live **kanal**, **StreamingEndpoint**, och live **Arkiv** entiteter. 

Telemetri skrivs till en tabell för lagring i ett Azure Storage-konto som du anger (vanligtvis använder du storage-konto som är associerade med AMS-kontot). 

Systemets telemetri hanterar inte datalagring. Du kan ta bort gamla telemetridata genom att ta bort storage-tabeller.

Det här avsnittet beskrivs hur du konfigurerar och använder AMS telemetri.

## <a name="configuring-telemetry"></a>Konfigurera telemetri

Du kan konfigurera telemetri på en nivå kornighet komponent. Det finns två detaljnivåer ”Normal” och ”utförlig”. För närvarande returnera båda nivåerna samma information. Det rekommenderas att använda ”Normal. 

De följande avsnitten visar hur du aktiverar telemetri:

[Aktivera telemetri med .NET](media-services-dotnet-telemetry.md) 

[Aktivera telemetri med övriga](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Förbrukar telemetri information

Telemetri skrivs till en tabell med Azure Storage i storage-konto som du angav när du har konfigurerat telemetri för Media Services-kontot. Det här avsnittet beskrivs storage-tabeller för mätvärdena.

Du kan använda telemetridata på något av följande sätt:

- Läsa data direkt från Azure Table Storage (t.ex. med Storage SDK: N). Beskrivning av telemetri storage-tabeller, finns det **förbrukar telemetri information** i [detta](https://msdn.microsoft.com/library/mt742089.aspx) avsnittet.

Eller

- Använd stöd i Media Services .NET SDK för att läsa in lagringsdata, enligt beskrivningen i [detta](media-services-dotnet-telemetry.md) avsnittet. 


Telemetri schemat som beskrivs nedan är utformad för att ge bra prestanda inom gränserna för Azure Table Storage:

- Data har partitionerats med konto-ID och tjänst-ID för att tillåta telemetri från varje tjänst ska efterfrågas oberoende av varandra.
- Partitioner innehålla datum att ge en rimlig övre gräns på partitionsstorleken.
- Raden nycklar är i omvänd tid ordning så att de senaste telemetri objekten ska efterfrågas för en viss tjänst.

Det här ger många vanliga frågor ska vara effektiva:

- Parallella, oberoende hämtning av data för olika tjänster.
- Hämta alla data för en viss tjänst i ett datumintervall.
- Hämta senaste data för en tjänst.

### <a name="telemetry-table-storage-output-schema"></a>Telemetri tabellschemat lagring utdata

Telemetridata lagras i mängd i en tabell, ”TelemetryMetrics20160321” där ”20160321” är datum för tabellen skapas. Telemetri systemet skapar en separat tabell för varje dag baserat på UTC 00:00. Tabellen används för att lagra återkommande värden som infognings-bithastighet inom en viss period tid, antal skickade byte osv. 

Egenskap|Värde|Exempel/Anteckningar
---|---|---
PartitionKey|{konto-ID} _ {enhets-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66 < br /<br/>Konto-ID ingår i partitionsnyckel att förenkla arbetsflöden där flera Media Services-konton skriver till samma lagringskonto.
RowKey|{sekunder till midnatt} _ {slumpmässigt värde}|01688_00199<br/><br/>Radnyckeln börjar med antalet sekunder som midnatt till att tillåta övre n format frågor inom en partition. Mer information finns i [den här artikeln](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern). 
Tidsstämpel|Datum/tid|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Typ|Typ av enhet som tillhandahåller telemetridata|Kanal-StreamingEndpoint-Arkiv<br/><br/>Händelsetypen är bara ett strängvärde.
Namn|Namnet på händelsen telemetri|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Den tid som händelsen telemetri inträffade (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Observerade tid som entiteten skicka telemetri (till exempel en kanal). Det kan finnas synkroniseringsproblem med mellan komponenter så att det här värdet är ungefärlig tid
ServiceID|{tjänsten ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitet-specifika egenskaper|Som definierats av händelsen|StreamName: stream1, bithastighet 10123 vid...<br/><br/>Återstående egenskaper har definierats för den angivna händelsen. Azure innehållet är nyckel/värde-par.  (det vill säga olika rader i tabellen har olika uppsättningar med egenskaper).

### <a name="entity-specific-schema"></a>Företagsspecifika schema

Det finns tre typer av företagsspecifika telemetriska poster varje pushas med följande frekvens:

- Strömningsslutpunkter: var 30 sekunder
- Live kanaler: varje minut
- Live Arkiv: varje minut

**Strömmande slutpunkt**

Egenskap|Värde|Exempel
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpel från Azure Table 2016-09-09T22:43:42.241Z
Typ|Typ|StreamingEndpoint
Namn|Namn|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Värdnamn|Värdnamnet för slutpunkten|builddemoserver.Origin.mediaservices.Windows.NET
statusCode|Registrerar HTTP-status|200
ResultCode|Information om resultat|S_OK
RequestCount|Totalt antal begäranden i aggregering|3
BytesSent|Sammanställda byte som skickats|2987358
ServerLatency|Genomsnittlig server svarstid (inklusive lagring)|129
E2ELatency|Genomsnittlig svarstid för slutpunkt till slutpunkt|250

**Live kanal**

Egenskap|Värde|Exempel/Anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Kanal
Namn|Namn|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ av spåra video ljud/text|ljud och
TrackName|Namnet på spåret|video/audio_1
Bithastighet|Spåra bithastighet|785000
CustomAttributes||   
IncomingBitrate|Faktiska inkommande bithastighet|784548
OverlapCount|Överlappar i infognings|0
DiscontinuityCount|Avvikelse för spår|0
LastTimestamp|Tidsstämpel för senaste infogade data|1800488800
NonincreasingCount|Antal fragment förkastas på grund av icke-ökande tidsstämpel|2
UnalignedKeyFrames|Om vi har tagit emot fragmenten (över kvalitet) där nyckeln ramar justeras inte |True
UnalignedPresentationTime|Om vi har tagit emot fragmenten (alla kvalitet nivåer/spår) där presentation tiden justeras inte|True
UnexpectedBitrate|Värdet är true, == beräknade/faktisk bithastighet för ljud och video spåra > 40 000 bit/s och IncomingBitrate eller IncomingBitrate och actualBitrate skiljer sig med 50% 0 |True
Felfri|True, om <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> är alla 0|True<br/><br/>Felfri är en sammansatt funktion som returnerar värdet false när något av följande villkor:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Live-Arkiv**

Egenskap|Värde|Exempel/Anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpeln från tabellen Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Arkiv
Namn|Namn|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Tjänst-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|URL: en för programmet|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba.ISM
TrackName|Namnet på spåret|Audio_1
TrackType|Typ av spåret|Ljud och video
CustomAttribute|Hexadecimala strängen som skiljer mellan olika spåra med samma namn och bithastighet (multi kameravinkel)|
Bithastighet|Spåra bithastighet|785000
Felfri|True, om FragmentDiscardedCount == 0 & & ArchiveAcquisitionError == False|True (dessa två värden finns inte i måttet men de finns i händelsen källa)<br/><br/>Felfri är en sammansatt funktion som returnerar värdet false när något av följande villkor:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Allmänna frågor och svar

### <a name="how-to-consume-metrics-data"></a>Hur du använder mått data?

Mått data lagras som en serie av Azure-tabeller i kundens lagringskonto. Den här informationen kan användas med hjälp av följande verktyg:

- AMS SDK
- Microsoft Azure Lagringsutforskaren (stöder exportera till CSV-format och bearbetade i Excel)
- REST-API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hur du söker efter genomsnittlig bandbreddsanvändning?

Genomsnittlig bandbreddsanvändningen är medelvärdet för BytesSent över en tidsperiod.

### <a name="how-to-define-streaming-unit-count"></a>Hur du definierar antal strömmande enheter?

Antalet strömmande enhet kan definieras som belastning genomströmning från tjänstens strömningsslutpunkter dividerat med belastning genomflödet av en strömmande slutpunkt. Belastning användbara genomflödet av en strömmande slutpunkten är 160 Mbit/s.
Anta exempelvis att belastning genomströmning från en kundservice är 40 Mbit/s (det maximala värdet BytesSent över en tidsperiod). Sedan antalet strömmande enhet är lika med (40 MBps) * (8 bitar/byte) /(160 Mbps) = 2 strömmande enheter.

### <a name="how-to-find-average-requestssecond"></a>Hur du söker efter medelvärde begäranden per sekund?

Om du vill hitta det genomsnittliga antalet begäranden per sekund, för att beräkna det genomsnittliga antalet förfrågningar (RequestCount) över en tidsperiod.

### <a name="how-to-define-channel-health"></a>Hur du definierar kanal hälsa?

Kanal hälsa kan definieras som en sammansatt boolesk funktion som är false när något av följande villkor:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Hur du identifierar avbrott?

Hitta alla poster för kanal-data för att identifiera avbrott, där DiscontinuityCount > 0. Motsvarande ObservedTime tidsstämpeln anger den tid då avbrott inträffade.

### <a name="how-to-detect-timestamp-overlaps"></a>Hur du identifierar tidsstämpel överlappar?

Hitta alla poster för kanal-data för att identifiera tidsstämpel överlappningar där OverlapCount > 0. Motsvarande ObservedTime tidsstämpeln anger de tider då tidsstämpeln överlappar inträffade.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hur du söker efter misslyckade direktuppspelade begäranden och orsaker?

Hitta alla Strömningsslutpunkt poster där ResultCode inte är lika med S_OK för att hitta misslyckade direktuppspelade begäranden och orsaker. Fältet motsvarande StatusCode anger varför begäran misslyckades.

### <a name="how-to-consume-data-with-external-tools"></a>Hur du använder data med externa verktyg?

Telemetriska data kan bearbetas och visualiseras med följande verktyg:

- PowerBI
- Application Insights
- Azure-Monitor (tidigare lådan)
- AMS Live instrumentpanelen
- Azure-portalen (väntar version)

### <a name="how-to-manage-data-retention"></a>Hur hanterar data finns kvar?

Telemetri system ger inte några kvarhållning datahantering eller automatisk borttagning av gamla poster. Därför måste du hantera och ta bort poster med gamla manuellt från lagringstabellen. Du kan referera till lagrings-SDK för hur du gör.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
