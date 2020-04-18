---
title: Översikt över direktuppspelning med Azure Media Services | Microsoft-dokument
description: Den här artikeln innehåller en översikt över livestreaming med Microsoft Azure Media Services.
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
ms.openlocfilehash: f875b4a5c4f1322f4a992dc3738ab1ce6431149d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641130"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Översikt över direktuppspelning med hjälp av Media Services

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Översikt

När du levererar livestreaminghändelser med Azure Media Services är följande komponenter ofta involverade:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning.

    Du kan också välja flera kodare som synkroniserar tiden live. För vissa viktiga live-händelser som kräver mycket hög tillgänglighet och kvalitet rekommenderar vi att du använder redundanta aktiv/aktiv-kodare med tidssynkronisering för problemfri sömlös redundans utan att förlora data.
* En liveuppspelningstjänst som ger dig möjlighet att göra följande:

  * infoga innehållet via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming)
  * (valfritt) koda strömmen till ström med anpassningsbar bithastighet
  * förhandsgranska din liveuppspelning
  * spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand)
  * leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

**Microsoft Azure Media Services** (AMS) ger möjligheten att infoga, koda, förhandsgranska, lagra och leverera liveströmmat innehåll.

Med Media Services kan du dra nytta av [dynamiska förpackningar](media-services-dynamic-packaging-overview.md), som gör att du kan sända dina livestreams i MPEG DASH, HLS och Smooth Streaming format från bidragsflödet som skickas till tjänsten. Dina tittare kan spela upp livestreamen med alla HLS-, DASH- eller Smooth Streaming-kompatibla spelare. Du kan använda Azure Media Player i dina webb- eller mobilappar för att leverera din ström i något av dessa protokoll.

> [!NOTE]
> Från och med den 12 maj 2018 stöder livekanalerna inte längre rtp/MPEG-2-transportströmmens introllningsprotokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmenterade MP4-protokoll (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Slutpunkter för direktuppspelning, kanaler, program

I Azure Media Services hanterar **kanaler**, **program** och **strömningsslutpunkter** alla funktioner för liveuppspelning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans.

En **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal kan ta emot en live-indataström på följande sätt:

* En lokal livekodare skickar **RTMP** eller **Smooth Streaming** med flera bithastigheter (fragmenterad MP4) till den kanal som är konfigurerad för **direkt** leverans. Den **direkta** leveransen är när de infogade strömmarna passerar genom **kanalerna** utan vidare bearbetning. Du kan använda följande live-kodare som matar ut multibitrate Smooth Streaming: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. Följande live-kodare matar ut RTMP: Telestream Wirecast, Haivision, Teradek-omkodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* En lokal live-kodare skickar en enbitrate-ström till kanalen som är aktiverad för att utföra live-kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Följande live-kodare med RTMP-utgång är kända för att fungera med kanaler av den här typen: Telestream Wirecast. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

Från och med mediatjänsterna 2.10-versionen kan du när du skapar en kanal ange på vilket sätt du vill att kanalen ska ta emot indataströmmen och om du vill att kanalen ska utföra livekodning av din ström eller inte. Du kan välja mellan två alternativ:

* **Ingen** (genomströmning) – Ange det här värdet om du planerar att använda en lokal live-kodare som ska mata ut flerbitrateström (en direktström). I det här fallet gick den inkommande strömmen vidare till utdata utan kodning. Detta är beteendet hos en kanal innan 2.10 släpps.  
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda din enda bithastighetsstream till flerbitrateström. Den här metoden är mer ekonomisk för att snabbt skala upp för ovanliga händelser. Tänk på att det finns en faktureringseffekt för live-kodning och du bör komma ihåg att om du lämnar en direkt kodningskanal i tillståndet "Kör" medför faktureringsavgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när ditt livestreamingevenemang är klart för att undvika extra timkostnader.

## <a name="comparison-of-channel-types"></a>Jämförelse av kanaltyper

Följande tabell innehåller en guide för att jämföra de två kanaltyper som stöds i Media Services

| Funktion | Genomgående kanal | Standardkanal |
| --- | --- | --- |
| En bithastighetsindata kodas till flera bithastigheter i molnet |Inga |Ja |
| Maximal upplösning, antal lager |1080p, 8 lager, 60+ fps |720p, 6 lager, 30 fps |
| Indataprotokoll |RTMP, Smidig streaming |RTMP, Smidig streaming |
| Pris |Se [prissidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "Live Video" |Se [prissidan](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal körtid |24x7 |8 timmar |
| Stöd för att infoga skiffer |Inga |Ja |
| Stöd för annonssignalering |Inga |Ja |
| Genomgång CEA 608/708 bildtexter |Ja |Ja |
| Stöd för icke-enhetliga ingående gops |Ja |Nej – indata måste vara fasta 2sec GOPs |
| Stöd för indata för variabel bildhastighet |Ja |Nej – indata måste vara fast bildrutehastighet.<br/>Mindre variationer tolereras, till exempel under hög rörelse scener. Men kodaren kan inte sjunka till 10 bildrutor/sek. |
| Automatisk avstängning av kanaler när inmatningsflödet går förlorat |Inga |Efter 12 timmar, om det inte finns något program som körs |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Live-arbetsflöde](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Beskrivning av en kanal och dess relaterade komponenter

### <a name="channel"></a>Kanal

I Media Services ansvarar [Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s för att bearbeta livestreaminginnehåll. En kanal innehåller en ingångsslutpunkt (inmatnings-URL) som du sedan tillhandahåller en direktomkodare. Kanalen tar emot live-indataströmmar från live-omkodaren och gör den tillgänglig för direktuppspelning via en eller flera StreamingEndpoints. Kanalerna tillhandahåller också en förhandsgranskningsslutpunkt (förhandsgransknings-URL) som du använder för att förhandsgranska och validera flödet innan du bearbetar och levererar vidare.

Du kan hämta den intbeteringsadressen och förhandsgranskningsadressen när du skapar kanalen. För att få dessa webbadresser behöver kanalen inte vara i startat tillstånd. När du är redo att börja skicka data från en live-omkodare till kanalen måste kanalen startas. När den aktiva omkodaren börjar inta data kan du förhandsgranska din ström.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera StreamingEndpoints. Beroende på bandbredd och säkerhetsbehov kan StreamingEndpoint-tjänster desdiceras till en eller flera kanaler. Alla StreamingEndpoint kan hämta från vilken kanal som helst.

När du skapar en kanal kan du ange tillåtna IP-adresser i något av följande format: IPV4-adress med 4 nummer, CIDR-adressintervall.

### <a name="program"></a>Program
Med [ett program](https://docs.microsoft.com/rest/api/media/operations/program) kan du styra publicering och lagring av segment i en livestream. Kanaler hanterar program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.
Du kan ange hur många timmar du vill behålla det inspelade innehållet för programmet genom att ange egenskapen **ArchiveWindowLength.** Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArkivWindowLength dikterar också den maximala tid klienter kan söka tillbaka i tiden från den aktuella live-positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

## <a name="billing-implications"></a>Konsekvenser för fakturering
En kanal börjar faktureras så fort det är tillståndsövergångar till "Körs" via API:et.  

Följande tabell visar hur kanaltillstånd mappas till faktureringstillstånd i API- och Azure-portalen. Observera att tillstånden skiljer sig något mellan API och Portal UX. Så snart en kanal är i tillståndet "Kör" via API:et, eller i tillståndet "Klar" eller "Streaming" i Azure-portalen, är faktureringen aktiv.

Om du vill stoppa kanalen från att fakturera dig vidare måste du stoppa kanalen via API:et eller i Azure-portalen.
Du är ansvarig för att stoppa dina kanaler när du är klar med kanalen. Om du inte stoppar kanalen kommer det att leda till fortsatt fakturering.

> [!NOTE]
> När du arbetar med standardkanaler stängs AMS automatiskt av alla kanaler som fortfarande är i "Kör" läge 12 timmar efter att inmatningsflödet har förlorats och det inte finns några program som körs. Du kommer dock fortfarande att faktureras för den tid kanalen var i "Kör" tillstånd.
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanallägen och hur de mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden inkluderar:

* **Stoppad**. Det här är kanalens ursprungliga tillstånd efter att den har skapats (såvida inte autostart har valts i portalen.) Ingen fakturering sker i det här tillståndet. I det här läget kan kanalegenskaperna uppdateras men direktuppspelning är inte tillåtet.
* **Start**. Kanalen startas. Ingen fakturering sker i det här tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta livestreamar. Det är nu faktureringsanvändning. Du måste stoppa kanalen för att förhindra ytterligare fakturering.
* **Stoppa**. Kanalen stoppas. Ingen fakturering sker i det här tillfälliga tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.
* **Ta bort**. Kanalen tas bort. Ingen fakturering sker i det här tillfälliga tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläget.

| Kanaltillstånd | Indikatorer för portalgränssnitt | Är det fakturering? |
| --- | --- | --- |
| Startar |Startar |Nej (tillfälligt tillstånd) |
| Körs |Klar (inga program som körs)<br/>eller<br/>Streaming (minst ett program som körs) |JA |
| Stoppas |Stoppas |Nej (tillfälligt tillstånd) |
| Stoppad |Stoppad |Inga |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Fragmenterad MP4 Live-specifikation för Azure Media Services](../media-services-fmp4-live-ingest-overview.md)

[Arbeta med kanaler som är aktiverade för att utföra livekodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)

[Kvoter och begränsningar](media-services-quotas-and-limitations.md).  

[Begrepp för medietjänster](media-services-concepts.md)
