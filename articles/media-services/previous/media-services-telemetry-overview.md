---
title: Azure Media Services telemetri | Microsoft-dokument
description: Den här artikeln innehåller en översikt över Microsoft Azure Media Services-telemetrin.
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
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895783"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services-telemetri  


> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Med Azure Media Services (AMS) kan du komma åt telemetri-/måttdata för sina tjänster. Den aktuella versionen av AMS kan du samla in telemetri data för live **Channel,** **StreamingEndpoint**och live **Archive** enheter. 

Telemetri skrivs till en lagringstabell i ett Azure Storage-konto som du anger (vanligtvis använder du lagringskontot som är kopplat till ditt AMS-konto). 

Telemetrisystemet hanterar inte datalagring. Du kan ta bort de gamla telemetridata genom att ta bort lagringstabellerna.

I det här avsnittet beskrivs hur du konfigurerar och använder AMS-telemetrin.

## <a name="configuring-telemetry"></a>Konfigurera telemetri

Du kan konfigurera telemetri på en detaljrikedom på komponentnivå. Det finns två detaljnivåer "Normal" och "Utförlig". För närvarande returnerar båda nivåerna samma information. Det rekommenderas att använda "Normal. 

Följande avsnitt visar hur du aktiverar telemetri:

[Aktivera telemetri med .NET](media-services-dotnet-telemetry.md) 

[Aktivera telemetri med REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Konsumerar telemetriinformation

Telemetri skrivs till en Azure Storage-tabell i det lagringskonto som du angav när du konfigurerade telemetri för Media Services-kontot. I det här avsnittet beskrivs lagringstabellerna för måtten.

Du kan använda telemetridata på något av följande sätt:

- Läs data direkt från Azure Table Storage (t.ex. med hjälp av Storage SDK). Beskrivning av telemetrilagringstabeller finns i **information om hur du konsumerar telemetri** i det [här](https://msdn.microsoft.com/library/mt742089.aspx) avsnittet.

Eller

- Använd stödet i Media Services .NET SDK för läsning av lagringsdata enligt beskrivningen i [det här](media-services-dotnet-telemetry.md) avsnittet. 


Telemetrischemat som beskrivs nedan är utformat för att ge bra prestanda inom gränserna för Azure Table Storage:

- Data partitioneras efter konto-ID och tjänst-ID för att tillåta telemetri från varje tjänst som ska efterfrågas oberoende av varandra.
- Partitioner innehåller datumet för att ge en rimlig övre gräns på partitionens storlek.
- Radnycklar är i omvänd tid för att låta de senaste telemetriartiklarna efterfrågas för en viss tjänst.

Detta bör göra det möjligt för många av de vanligaste frågorna att vara effektiva:

- Parallell, oberoende nedladdning av data för separata tjänster.
- Hämtar alla data för en viss tjänst i ett datumintervall.
- Hämtar de senaste data för en tjänst.

### <a name="telemetry-table-storage-output-schema"></a>Utdataschema för lagring av telemetritabell

Telemetridata lagras sammanlagt i en tabell, "Telemetrimetrics20160321" där "20160321" är datumet för den skapade tabellen. Telemetrisystem skapar en separat tabell för varje ny dag baserat på 00:00 UTC. Tabellen används för att lagra återkommande värden som intjest bithastighet inom ett visst tidsfönster, skickade byte etc. 

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|{konto-ID}_{entitets-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Konto-ID ingår i partitionsnyckeln för att förenkla arbetsflöden där flera Media Services-konton skriver till samma lagringskonto.
RowKey|{sekunder till midnatt}_{slumpmässigt värde}|01688_00199<br/><br/>Radnyckeln börjar med antalet sekunder till midnatt för att tillåta topp n-formatfrågor i en partition. Mer information finns i [den här](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artikeln. 
Tidsstämpel|Date/Time|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241Z
Typ|Den typ av enhet som tillhandahåller telemetridata|Kanal/StreamingEndpoint/Arkiv<br/><br/>Händelsetyp är bara ett strängvärde.
Namn|Namnet på telemetrihändelsen|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime (ObservedTime)|Den tidpunkt då telemetrihändelsen inträffade (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Den observerade tiden tillhandahålls av den enhet som skickar telemetrin (till exempel en kanal). Det kan finnas tidssynkroniseringsproblem mellan komponenter så att det här värdet är ungefärlig
ServiceID|{tjänst-ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitetsspecifika egenskaper|Enligt definitionen i händelsen|StreamName: stream1, Bitrate 10123, ...<br/><br/>De återstående egenskaperna definieras för den angivna händelsetypen. Azure Table-innehåll är nyckelvärdespar.  (det vill exempel på olika rader i tabellen har olika uppsättningar egenskaper).

### <a name="entity-specific-schema"></a>Entitetsspecifikt schema

Det finns tre typer av entitetsspecifika telemetriska dataposter som var och en skjuts med följande frekvens:

- Slutpunkter för direktuppspelning: Var 30:e sekund
- Livekanaler: Varje minut
- Live arkiv: Varje minut

**Slutpunkt för direktuppspelning**

Egenskap|Värde|Exempel
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpel från Azure Table 2016-09-09T22:43:42.241Z
Typ|Typ|StreamingEndpoint
Namn|Namn|StreamingEndpointRequestLog
ObservedTime (ObservedTime)|ObservedTime (ObservedTime)|2016-09-09T22:42:36.924Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Värdnamn|Slutpunkts värdnamn|builddemoserver.origin.mediaservices.windows.net
Statuskod|Poster HTTP-status|200
Resultatkod|Information om resultatkod|S_OK
Begäranrring|Total begäran i aggregeringen|3
BytesSent|Aggregerade byte som skickats|2987358
ServerLatency (ServerLatency)|Genomsnittlig serverfördröjning (inklusive lagring)|129
E2ELatency (E2ELatency)|Genomsnittlig svarstid från till på sluten tid|250

**Live-kanal**

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241Z
Typ|Typ|Kanal
Namn|Namn|KanalHeartbeat
ObservedTime (ObservedTime)|ObservedTime (ObservedTime)|2016-09-09T22:42:36.924Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType (spårtyp)|Typ av spårvideo/ljud/text|video/ljud
TrackName (spårnamn)|Spårets namn|video/audio_1
Bitrate|Spåra bithastighet|785000
CustomAttributes||   
IncomingBitrate|Faktisk inkommande bithastighet|784548
Överlappande Antal|Överlappning i intag|0
Diskontinuitetsräknande|Avbrott för spår|0
LastTimestamp|Senast intas datatidsstämpel|1800488800
Icke-fallandeCount|Antal fragment som kasseras på grund av icke-ökande tidsstämpel|2
Ej injusterade Nyckelrutor|Om vi fick fragment (över kvalitetsnivåer) där nyckelbildrutor inte är i linje |True
Ej justeradpresentationTime|Om vi fick fragment (över kvalitetsnivåer/spår) där presentationstiden inte är justerad|True
Oväntatbitrat|Sant, om beräknad /faktisk bithastighet för ljud/videospår > 40 000 bps och IncomingBitrate == 0 ELLER IncomingBitrate och actualBitrate skiljer sig med 50 % |True
Felfri|Sant, om <br/>överlapparCount, <br/>Diskontinuitetsrutt, <br/>Icke-fallandeCount, <br/>Ej injusterade Nyckelrutor, <br/>Ej justeradpresentationTime, <br/>Oväntatbitrat<br/> är alla 0|True<br/><br/>Felfri är en sammansatt funktion som returnerar false när något av följande villkor finns:<br/><br/>- ÖverlappandeCount > 0<br/>- DiskontinuitetCount > 0<br/>- Icke-fallandeCount > 0<br/>- Ej justerade Nyckelrutor == Sant<br/>- UnalignedPresentationTime == Sant<br/>- UnexpectedBitrate == Sant

**Live arkiv**

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tidsstämpel|Tidsstämpel|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241Z
Typ|Typ|Arkiv
Namn|Namn|ArkivHeartbeat
ObservedTime (ObservedTime)|ObservedTime (ObservedTime)|2016-09-09T22:42:36.924Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Manifestnamn|Program url|tillgång-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName (spårnamn)|Spårets namn|audio_1
TrackType (spårtyp)|Typ av spår|Ljud/video
CustomAttribute|Hex sträng som skiljer mellan olika spår med samma namn och bithastighet (multi kameravinkel)|
Bitrate|Spåra bithastighet|785000
Felfri|Sant, om FragmentDiscardedCount == 0 && ArchiveAcquisitionError == Falskt|Sant (dessa två värden finns inte i måttet men de finns i källhändelsen)<br/><br/>Felfri är en sammansatt funktion som returnerar false när något av följande villkor finns:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArkivFörvärkningserror == Sant

## <a name="general-qa"></a>Allmänt Q&A

### <a name="how-to-consume-metrics-data"></a>Hur använder man måttdata?

Måttdata lagras som en serie Azure-tabeller i kundens lagringskonto. Dessa data kan förbrukas med hjälp av följande verktyg:

- AMS SDK
- Microsoft Azure Storage Explorer (stöder export till kommaavgränsat värdeformat och bearbetas i Excel)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hur hittar man genomsnittlig bandbreddsförbrukning?

Den genomsnittliga bandbreddsförbrukningen är genomsnittet av BytesSent under en tidsperiod.

### <a name="how-to-define-streaming-unit-count"></a>Hur definierar du antalet enheter för strömning?

Antalet strömningsenheter kan definieras som det högsta dataflödet från tjänstens slutpunkter för direktuppspelning dividerat med det högsta dataflödet för en slutpunkt för direktuppspelning. Det bästa användbara dataflödet för en slutpunkt för direktuppspelning är 160 Mbit/s.
Anta till exempel att toppdataflödet från en kunds tjänst är 40 MBps (det maximala värdet för BytesSent under en tidsperiod). Sedan är antalet strömningsenheter lika med (40 MBps)*(8 bitar/byte)/(160 Mbps) = 2 strömningsenheter.

### <a name="how-to-find-average-requestssecond"></a>Hur hittar man genomsnittliga förfrågningar/sekund?

Om du vill hitta det genomsnittliga antalet begäranden/sekund beräknar du det genomsnittliga antalet begäranden (RequestCount) över en tidsperiod.

### <a name="how-to-define-channel-health"></a>Hur definierar kanalhälsa?

Kanalhälsa kan definieras som en sammansatt boolesk funktion så att den är falsk när något av följande villkor gäller:

- OverlapCount > 0
- Diskontinuitetskonto > 0
- Icke-fallandeCount > 0
- Ej justerade Nyckelrutor == Sant 
- Ej justeradpresentationTid == Sant 
- UnexpectedBitrate == Sant


### <a name="how-to-detect-discontinuities"></a>Hur man upptäcker diskontinuiteter?

Om du vill identifiera avbrott hittar du alla kanaldataposter där DiscontinuityCount > 0. Motsvarande ObservedTime-tidsstämpel anger vid vilken tidpunkt avbrotten inträffade.

### <a name="how-to-detect-timestamp-overlaps"></a>Hur man upptäcker tidsstämpel överlappningar?

Om du vill identifiera tidsstämpelöverlappningar hittar du alla kanaldataposter där OverlapCount > 0. Motsvarande ObservedTime-tidsstämpel anger de tider då tidsstämpeln överlappade.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hur hittar man avbrott och orsaker till streamingbegäran?

Om du vill hitta fel och orsaker till direktuppspelningsbegäran hittar du alla dataposter för direktuppspelningsslutpunkter där ResultCode inte är lika med S_OK. Motsvarande statuskod-fält anger orsaken till begäran misslyckades.

### <a name="how-to-consume-data-with-external-tools"></a>Hur använder man data med externa verktyg?

Telemetriska data kan bearbetas och visualiseras med följande verktyg:

- Power BI
- Application Insights
- Azure Monitor (tidigare Shoebox)
- AMS Live-instrumentpanel
- Azure Portal (väntande utgivning)

### <a name="how-to-manage-data-retention"></a>Hur hanterar man datalagring?

Telemetrisystemet tillhandahåller inte datalagringshantering eller automatisk borttagning av gamla poster. Därför måste du hantera och ta bort gamla poster manuellt från lagringstabellen. Du kan referera till lagring SDK för hur man gör det.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
