---
title: Översikt över liveuppspelning med Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över direktsänd strömning med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c5dd0146fe59e7dc85787f146b10cfde7d6addb4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867900"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Översikt över liveuppspelning med Media Services

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Översikt

När du ska leverera liveuppspelningshändelser med Azure Media Services ingår oftast följande komponenter:

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

Med medietjänster kan du dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md), vilket gör att du sända dina direktsända strömmar i MPEG-DASH, HLS eller och Smooth Streaming-format från bidraget feed som skickas till tjänsten. Användarna kan spela upp den direktsända dataströmmen med valfri kompatibel spelare HLS, DASH eller Smooth Streaming. Du kan använda Azure Media Player i din webbplats eller mobila program för att leverera din dataström i någon av de här protokollen.

> [!NOTE]
> Startar den 12 maj 2018 livekanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) inmatningsprotokollen.

## <a name="streaming-endpoints-channels-programs"></a>Strömmande slutpunkter, kanaler, program

I Azure Media Services hanterar **kanaler**, **program** och **strömningsslutpunkter** alla funktioner för liveuppspelning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans.

En **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal kan ta emot en live-indataström på följande sätt:

* En lokal livekodare skickar **RTMP** eller **Smooth Streaming** med flera bithastigheter (fragmenterad MP4) till den kanal som är konfigurerad för **direkt** leverans. Den **direkta** leveransen är när de infogade strömmarna passerar genom **kanalerna** utan vidare bearbetning. Du kan använda följande livekodare som Smooth Streaming med flera bithastigheter: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. I följande livekodare RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek och Tricaster-omkodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Följande livekodare med RTMP-utdata är kända för att arbeta med kanaler för den här typen: Telestream Wirecast, FMLE. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

Från och med Media Services 2.10, när du skapar en kanal, kan du ange i vilken grad du vill använda för din kanal tar emot Indataströmmen och om huruvida du vill använda för kanalen för att utföra live encoding av strömmen. Du kan välja mellan två alternativ:

* **Ingen** (genomströmning) – Ange det här värdet om du planerar att använda en lokal livekodare som kommer utdata med flera bithastigheter stream (en direkt stream). I det här fallet den inkommande dataströmmen skickas vidare till utdata utan kodning. Det här är beteendet för en kanal innan 2,10 versionen.  
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda din direktsänd dataström med enkel bithastighet till en dataström med multibithastighet. Den här metoden är mer ekonomiskt för att skala upp snabbt efter ovanliga händelser. Tänk på att det finns en fakturering inverkan för live encoding och du bör komma ihåg att lämna en livekanal med kodning i tillståndet ”körs” debiteras debiterade avgifterna.  Vi rekommenderar att du omedelbart stoppa dina kanaler som körs när din liveströmmat evenemang är klar att undvika extra debitering.

## <a name="comparison-of-channel-types"></a>Jämförelse av kanaltyper

Följande tabell innehåller en guide för att jämföra de två kanal-typer som stöds i Media Services

| Funktion | Direkt-kanal | Standard-kanal |
| --- | --- | --- |
| Enkel bithastighet indata kodas till flera olika bithastigheter i molnet |Nej |Ja |
| Maximal upplösning, antalet lager |1080p, 8 lager 60 + fps |720p, 6 lager 30 bilder per sekund |
| Inkommande protokoll |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Pris |Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video” |Se den [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal körtid |Dygnet runt |8 timmar |
| Stöd för att infoga pekdatorer |Nej |Ja |
| Stöd för ad-signalering |Nej |Ja |
| Genomströmning CEA 608/708-undertexter |Ja |Ja |
| Stöd för icke-enhetlig inkommande GOPs |Ja |Nej – indata måste åtgärdas 2 sek GOPs |
| Stöd för variabeln ramens rate indata |Ja |Nej – indata som måste åtgärdas bildfrekvens.<br/>Smärre variationer användas, till exempel under hög rörelse scener. Men encoder kan därför inte släppas till 10 bildrutor per sekund. |
| Automatisk-avslutning av kanaler när indata-flöde går förlorad |Nej |Efter 12 timmar, om det finns inget Program körs |

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

I Media Services [kanal](https://docs.microsoft.com/rest/api/media/operations/channel)s ansvarar för bearbetning av liveuppspelningsinnehåll. En kanal som innehåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en levande transkodare. Kanalen tar emot inkommande direktsändningar från live transkodare och gör den tillgänglig för direktuppspelning via en eller flera Strömningsslutpunkter. Kanaler tillhandahåller även en slutpunkt för förhandsversionen (förhandsgransknings-URL) som används för att förhandsgranska och validera dataströmmen inför vidare behandling och leverans.

Du kan hämta URL: en för inmatning och förhandsgransknings-URL när du skapar kanalen. För att få dessa URL: er, behöver inte kanalen vara tillståndet startad. När du är redo att börja skicka data från en levande transkodare till kanalen startas kanalen. När live transkodare startar mata in data, kan du förhandsgranska dataströmmen.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera Strömningsslutpunkter. Beroende på behov bandbredds- och kan StreamingEndpoint-tjänster vara dedikerad till en eller flera kanaler. Alla StreamingEndpoint kan hämta från alla kanaler.

När du skapar en kanal kan ange du tillåtna IP-adresser på något av följande format: IpV4-adress med 4 siffror, CIDR-adressintervall.

### <a name="program"></a>Program
En [programmet](https://docs.microsoft.com/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för programmet genom att ange den **ArchiveWindowLength** egenskapen. Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength avgör också längsta tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

## <a name="billing-implications"></a>Fakturering effekter
En kanal börjar fakturering som ändrar status till ”körs” är det via API: et.  

I följande tabell visar hur kanaltillstånd mappas till fakturering tillstånd i API: et och Azure-portalen. Observera att tillstånden kan variera mellan API och -portalen UX. När en kanal är i tillståndet ”körs” via API: et eller statusen ”klar” eller ”strömning” i Azure-portalen, aktiveras fakturering.

Du måste stoppa kanalen via API: et eller i Azure portal om du vill stoppa kanalen från fakturering du ytterligare.
Du ansvarar för att stoppa dina kanaler när du är klar med kanalen. Det gick inte att stoppa kanalen leder fortsatt fakturering.

> [!NOTE]
> När du arbetar med Standard-kanaler, automatiskt AMS avslutning alla kanaler som finns kvar i tillståndet ”körs” 12 timmar efter inkommande flödet går förlorad och det finns inga program som körs. Dock kommer du fortfarande att debiteras för den tid som kördes kanalen i tillståndet ”körs”.
>
>

### <a id="states"></a>Kanaltillstånd och hur de mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppad**. Det här är starttillståndet för kanalen när den har skapandet (såvida inte autostart har valts i portalen.) Ingen debitering sker i det här tillståndet. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**. Kanalen startas. Ingen debitering sker i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar. Det nu Debiterad användning. Du måste stoppa kanalen för att förhindra att ytterligare fakturering.
* **Stoppa**. Kanalen stoppas. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Tar bort**. Kanalen tas bort. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.

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
[Specifikation för Azure Media Services fragmenterad MP4 Live-inmatning](media-services-fmp4-live-ingest-overview.md)

[Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)

[Kvoter och begränsningar](media-services-quotas-and-limitations.md).  

[Media Services-begrepp](media-services-concepts.md)
