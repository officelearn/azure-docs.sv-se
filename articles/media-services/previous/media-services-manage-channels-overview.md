---
title: Översikt över Liveströmning med Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över direktsänd strömning med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d71f9fc2b69e1a99b518c2317cccde712ab40fec
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-live-streaming-using-azure-media-services"></a>Översikt över Liveströmning med Azure Media Services

> [!NOTE]
> Startar den 12 maj 2018 live-kanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) infognings-protokoll.

## <a name="overview"></a>Översikt
När leverera händelser via strömning live med Azure Media Services ingår oftast följande komponenter:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning.

    Du kan också välja flera kodare som synkroniserar tiden live. För vissa viktiga live-händelser som kräver mycket hög tillgänglighet och kvalitet rekommenderar vi att du använder redundanta aktiv/aktiv-kodare med tidssynkronisering för problemfri sömlös redundans utan att förlora data.
* En liveuppspelningstjänst som ger dig möjlighet att göra följande:

  * infoga innehållet via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming)
  * (valfritt) koda strömmen till ström med anpassningsbar bithastighet
  * förhandsgranska din liveuppspelning
  * spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand)
  * leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

**Microsoft Azure Media Services** (AMS) ger möjligheten att infoga, koda, förhandsgranska, lagra och leverera liveuppspelningsinnehåll.

Målet är att tillhandahålla en video av hög kvalitet till olika enheter under olika nätverksförhållanden när du levererar ditt innehåll till kunder. Använda livekodare för att uppnå detta för att koda strömmen till en video dataström med flera bithastigheter (anpassningsbar bithastighet).  Använd Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) för att ta hand om strömning på olika enheter och dynamiskt ompaketera din ström till olika protokoll. Media Services har stöd för leverans av följande tekniker för direktuppspelning med anpassningsbar bithastighet: HTTP Live Streaming(HLS), Smooth Streaming, MPEG DASH.

I Azure Media Services hanterar **kanaler**, **program** och **strömningsslutpunkter** alla funktioner för liveuppspelning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans.

En **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal kan ta emot en live-indataström på följande sätt:

* En lokal livekodare skickar **RTMP** eller **Smooth Streaming** med flera bithastigheter (fragmenterad MP4) till den kanal som är konfigurerad för **direkt** leverans. Den **direkta** leveransen är när de infogade strömmarna passerar genom **kanalerna** utan vidare bearbetning. Du kan använda de följande livekodare som skickar Smooth Streaming i flera bithastigheter: MediaExcel, Ateme, anta kommunikation, Envivio, Cisco och Elemental. Följande livekodare skickar RTMP: Adobe Flash Media Live-kodare (FMLE), Telestream Wirecast, Haivision, Teradek och Tricaster-omkodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). RTP (MPEG-TS) stöds också, förutsatt att du har en dedicerad anslutning till Azure-datacenter. Följande livekodare med RTMP-utdata är känt att arbeta med kanaler av den här typen: Telestream Wirecast, FMLE. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

Från och med Media Services 2.10-versionen när du skapar en kanal kan ange du i hur du vill använda för din kanal tar emot Indataströmmen och om huruvida du vill använda att utföra live encoding av strömmen kanalen. Du kan välja mellan två alternativ:

* **Ingen** (genomströmning) – Ange det här värdet om du planerar att använda en lokal livekodare som kommer att skrivas ut flera bithastigheter dataström (en direkt stream). I det här fallet den inkommande dataströmmen som skickats till utdata utan kodning. Det här är beteendet för en kanal före 2,10 versionen.  
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda dina direktsänd dataström med enkel bithastighet till dataström med multibithastighet. Den här metoden är mer ekonomiskt för snabbt skala upp för sällan händelser. Tänk på att det finns en fakturering påverkan för live encoding och ska du komma ihåg att lämna en live encoding kanal i tillståndet ”körs” kommer avgifter faktureringsinformation.  Vi rekommenderar att du stoppar du omedelbart dina kanaler som körs när din direktsänd strömning händelse är klar att undvika extra timvis avgifter.

## <a name="comparison-of-channel-types"></a>Jämförelse av kanaltyper
Följande tabell innehåller en guide för att jämföra två kanaltyper som stöds i Media Services

| Funktion | Direkt-kanal | Standard-kanal |
| --- | --- | --- |
| Enkel bithastighet indata kodas till flera olika bithastigheter i molnet |Nej |Ja |
| Maximal upplösning, antalet lager |1080p, 8 lager 60 + fps |720p, 6 lager 30 fps |
| Inkommande protokoll |RTMP, Smooth Streaming |RTMP, Smooth Streaming och RTP |
| Pris |Finns det [sida med priser](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live Video” |Finns det [sida med priser](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal körtid |Dygnet runt |8 timmar |
| Stöd för att infoga pekdatorer |Nej |Ja |
| Stöd för ad-signalering |Nej |Ja |
| Direkt CEA 608/708 etiketter |Ja |Ja |
| Möjlighet att återställa från kort bås i bidrag feed |Ja |Nej (kanalen kommer att börja slating efter 6 + sekunder utan indata) |
| Stöd för icke-enhetlig inkommande GOPs |Ja |Nej – indata måste åtgärdas 2 sek GOPs |
| Stöd för variabeln ram hastighet indata |Ja |Nej – indata som måste åtgärdas bildfrekvens.<br/>Mindre variationer användas, till exempel under hög rörelse i bakgrunden. Men kodaren går inte att släppa till 10 bildrutor per sekund. |
| Auto-avslutning av kanaler när indata-feeden går förlorad |Nej |Efter 12 timmar, om det finns inga Program som körs |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)
I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Live-arbetsflöde](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström i flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services
I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Beskrivning av en kanal och dess relaterade komponenter
### <a name="channel"></a>Kanal
I Media Services [kanal](https://docs.microsoft.com/rest/api/media/operations/channel)s ansvarar för bearbetning av liveströmmat innehåll. En kanal som tillhandahåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en levande transcoder. Kanalen tar emot live indata från live transcoder och gör den tillgänglig för strömning via en eller flera Strömningsslutpunkter. Kanaler ger också förhandsgranskningsslutpunkten (förhandsgranskning URL) som används för att förhandsgranska och verifiera strömmen innan ytterligare bearbetning och leverans.

Du kan få infognings-URL och förhandsgransknings-URL när du skapar kanalen. För att få dessa URL: er, behöver kanalen inte vara i startat tillstånd. När du är redo att börja skicka data från en levande transcoder till kanalen måste kanalen vara igång. När levande transcoder startar mata in data, kan du förhandsgranska dataströmmen.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera Strömningsslutpunkter. Beroende på behov bandbredds- och kan StreamingEndpoint tjänster dedikeras till en eller flera kanaler. Alla StreamingEndpoint dra från varje kanal.

### <a name="program"></a>Program
En [programmet](https://docs.microsoft.com/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för programmet genom att ange den **ArchiveWindowLength** egenskapen. Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength avgör också hur lång tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

## <a name="billing-implications"></a>Konsekvenser för fakturering
En kanal börjar fakturering så snart den är tillståndsövergångar till ”körs” via API: et.  

I följande tabell visas hur kanal tillstånd mappas till fakturering tillstånd i API och Azure-portalen. Observera att tillstånden är något annorlunda mellan API och portalen UX. När en kanal är i tillståndet ”körs” via API: et eller statusen ”klar” eller ”Streaming” i Azure-portalen, fakturering kommer att vara aktiv.

Du måste stoppa kanalen via API: et eller i Azure-portalen för att stoppa kanalen från fakturering du ytterligare.
Du ansvarar för att stoppa dina kanaler när du är klar med kanalen. Det gick inte att stoppa kanalen leder fortsatt fakturering.

> [!NOTE]
> När du arbetar med vanliga kanaler automatiskt AMS avslutning någon kanal som är fortfarande tillståndet ”körs” 12 timmar efter inkommande feeden går förlorade och inga program som körs. Men debiteras du fortfarande för den tid som kanalen har tillståndet ”körs”.
>
>

### <a id="states"></a>Kanal tillstånd och hur de mappas till fakturerings-läge
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppats**. Inledningsvis i kanalen är efter skapades (om inte autostart har valts i portalen.) Ingen fakturering inträffar i det här tillståndet. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Starta**. Kanalen startas. Ingen fakturering inträffar i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar. Det nu fakturering användning. Du måste stoppa kanalen för att förhindra att ytterligare fakturering.
* **Stoppa**. Kanalen stoppas. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Om du tar bort**. Kanalen tas bort. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläge.

| Kanaltillstånd | Portalgränssnittsindikatorer | Är det fakturering? |
| --- | --- | --- |
| Startar |Startar |Nej (övergångsläge) |
| Körs |Klart (inga program körs)<br/>eller<br/>Strömmar (minst ett program körs) |JA |
| Stoppas |Stoppas |Nej (övergångsläge) |
| Stoppad |Stoppad |Nej |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Azure Media Services fragmenterad MP4 Live infognings-specifikationen](media-services-fmp4-live-ingest-overview.md)

[Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)

[Kvoter och begränsningar](media-services-quotas-and-limitations.md).  

[Media Services-begrepp](media-services-concepts.md)
