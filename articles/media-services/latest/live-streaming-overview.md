---
title: Översikt över liveuppspelning med Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över liveuppspelning med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/08/2018
ms.author: juliako
ms.openlocfilehash: a4569505cb9a42f6682391a8b06725dea5e539dc
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344985"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Liveuppspelning med Azure Media Services v3

När du ska leverera liveuppspelningshändelser med Azure Media Services ingår oftast följande komponenter:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran (eller en annan enhet, till exempel bärbara datorer) till dataströmmar som skickas till tjänsten Live Streaming. Signalerna också innefatta annonsbaserad SCTE 35 och Ad-tips. 
* Media Services Live Streaming-tjänsten kan du mata in, förhandsgranska, paket, registrera, kryptera och skicka innehållet till dina kunder eller till ett nätverk för Innehållsleverans för vidare distribution.

Den här artikeln ger en detaljerad översikt och inkluderar diagram över viktiga komponenter som ingår i direktsänd strömning med Media Services.

## <a name="overview-of-main-components"></a>Översikt över huvudkomponenterna

I Media Services ansvarar [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En LiveEvent innehåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en lokal livekodare. LiveEvent tar emot inkommande direktsändningar från livekodaren i RTMP eller Smooth Streaming-format och gör den tillgänglig för direktuppspelning via en eller flera [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints). En [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) gör att du kan styra publicering, spela in och DVR fönstret inställningarna för den direktsända dataströmmen. LiveEvent innehåller också en förhandsversion slutpunkt (förhandsversion URL) som används för att förhandsgranska och validera dataströmmen inför vidare behandling och leverans. 

Media Services tillhandahåller **dynamisk paketering**, där du kan förhandsgranska och skicka innehållet i MPEG-DASH, HLS eller Smooth Streaming strömningsformat utan att du behöver manuellt packa om till dessa strömningsformat. Du kan spela upp med HLS, DASH eller Smooth kompatibla spelare. Du kan också använda [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) att testa din dataström.

Media Services kan du leverera ditt innehåll dynamiskt krypterad (**dynamisk kryptering**) med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.

Om du vill kan du också använda **dynamisk filtrering**, som kan användas för att styra antalet spår, format, bithastighet som skickas till spelarna. Media Services stöder också ad-insättning.

### <a name="new-live-encoding-improvements"></a>Nya förbättringar för Live encoding

Följande nya förbättringar har utförts i den senaste versionen.

- Nya läget för låg latens. Mer information finns i [svarstid](#latency).
- Förbättrad RTMP support (ökad stabilitet och mer käll-kodare stöder).
- RTMPS säker mata in.

    När du skapar en LiveEvent kan du få 4 mata in URL: er. 4 mata in URL: er är nästan identiska, har samma strömmande token (AppId), bara den numeriska delen port är olika. Två av de URL: er är primär och sekundär för RTMPS.   
- 24-timmars transkodning stöd. 
- Förbättrat stöd för ad-signalering i RTMP via SCTE35.

## <a name="liveevent-types"></a>LiveEvent typer

En [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direktsänd kodning och direktlagringsdiskar. 

### <a name="live-encoding-with-media-services"></a>Live encoding med Media Services

![Live encoding](./media/live-streaming/live-encoding.png)

En lokal livekodare skickar en dataström med enkel bithastighet till LiveEvent som är aktiverade för att utföra live encoding med Media Services i något av följande protokoll: RTMP eller Smooth Streaming (fragmenterad MP4). LiveEvent utför sedan live encoding av strömmen enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

När du skapar den här typen av LiveEvent ange **grundläggande** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Direkt

![direktautentisering](./media/live-streaming/pass-through.png)

Direktautentisering är optimerat för tidskrävande direktsända strömmar eller 24 x 7 linjär direktsänd kodning med hjälp av en lokal livekodare. Lokala kodare skickar med flera bithastigheter **RTMP** eller **Smooth Streaming** (fragmenterad MP4) till LiveEvent som är konfigurerad för **direkt** leverans. Den **direktautentisering** leveransen är när de infogade strömmarna passerar genom **LiveEvent**utan vidare bearbetning. 

Direkt LiveEvents kan stöd för upp till 4K-upplösning och – HEVC passera när det används med Smooth Streaming-inmatningsprotokollet. 

När du skapar den här typen av LiveEvent ange **ingen** (LiveEventEncodingType.None).

> [!NOTE]
> Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>LiveEvent typer jämförelse 

I följande tabell jämförs funktionerna i de två typerna av LiveEvent.

| Funktion | Direktautentisering LiveEvent | Standard LiveEvent |
| --- | --- | --- |
| Enkel bithastighet indata kodas till flera olika bithastigheter i molnet |Nej |Ja |
| Maximal upplösning, antalet lager |4Kp30  |720p, 6 lager 30 bilder per sekund |
| Inkommande protokoll |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Pris |Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video” |Se den [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal körtid |Dygnet runt |Dygnet runt |
| Stöd för att infoga pekdatorer |Nej |Ja |
| Stöd för ad-signalering via API: et|Nej |Ja |
| Stöd för ad-signalering via SCTE35 inband|Ja |Ja |
| Genomströmning CEA 608/708-undertexter |Ja |Ja |
| Återställa från kort bås i bidrag feed |Ja |Nej (LiveEvent börjar slating efter 6 + sekunder utan indata)|
| Stöd för icke-enhetlig inkommande GOPs |Ja |Nej – indata måste åtgärdas 2 sek GOPs |
| Stöd för variabeln ramens rate indata |Ja |Nej – indata som måste åtgärdas bildfrekvens.<br/>Smärre variationer användas, till exempel under hög rörelse scener. Men encoder kan därför inte släppas till 10 bildrutor per sekund. |
| Automatisk-avslutning av LiveEvent när indata-flöde går förlorad |Nej |Efter 12 timmar, om det finns inga LiveOutput som körs |

## <a name="liveevent-states"></a>LiveEvent tillstånd 

Det aktuella tillståndet för en LiveEvent. Möjliga värden omfattar:

|Status|Beskrivning|
|---|---|
|**Stoppad**| Det här är starttillståndet för LiveEvent när den har skapandet (såvida inte autostart har valts anges.) Ingen debitering sker i det här tillståndet. I det här tillståndet LiveEvent egenskaper kan uppdateras, men strömning är inte tillåtet.|
|**Startar**| LiveEvent startas. Ingen debitering sker i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår LiveEvent till tillståndet stoppad.|
|**Kör**| LiveEvent kan bearbeta direktsända strömmar. Det nu Debiterad användning. Du måste stoppa LiveEvent för att förhindra att ytterligare fakturering.|
|**Stoppar**| LiveEvent stoppas. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.|
|**Tar bort**| LiveEvent tas bort. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.|

## <a name="liveoutput"></a>LiveOutput

En [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) gör att du kan styra publicering, spela in och DVR fönstret inställningarna för den direktsända dataströmmen. Relationen mellan LiveEvent och LiveOutput liknar traditionella media där en kanal (LiveEvent) har en konstant ström av innehåll och ett program (LiveOutput) är begränsad till viss tidsinställd händelse på den LiveEvent.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för LiveOutput genom att ange den **ArchiveWindowLength** egenskapen. **ArchiveWindowLength** är en ISO 8601 timespan arkiveringsfönstret (Digital Video Recorder eller DVR). Det här värdet kan anges från minst 5 minuter till högst 25 timmar. 

**ArchiveWindowLength** avgör också det maximala antalet klienter kan söka bakåt i tiden från den aktuella direktsända positionen. LiveOutputs kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje LiveOutput är associerad med en [tillgången](https://docs.microsoft.com/rest/api/media/assets), och registrerar data i en behållare i Azure storage kopplade till Media Services-kontot. Om du vill publicera LiveOutput, måste du skapa en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En LiveEvent stöder upp till tre som körs samtidigt LiveOutputs så att du kan skapa flera Arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Till exempel ditt verksamhetsbehov kan vara att sända en 24 x 7 linjär direktsändningen, men du vill skapa ”inspelningar” av program per dag för att erbjuda kunder som innehåll på begäran för catch-up visning.  Det här scenariot kan du att först skapa en primär LiveOutput och med en kort arkivfönster 1 timme eller mindre för kunder att lyssna på som den primära direktsända dataströmmen. Du skapar en StreamingLocator för den här LiveOutput och publicera den till ditt program eller en webbplats som ”Live”-feed.  Som i flödet körs, kan du programmässigt skapar en andra samtidiga LiveOutput i början av ett program (eller 5 minuter för tidigt att tillhandahålla vissa hanterar att trimma senare.) Den här andra LiveOutput kan stoppas 5 minuter efter det programmet eller den händelse att ut och du kan sedan skapa en ny StreamingLocator om du vill publicera det här programmet som en på begäran-tillgång i katalogen för ditt program.  Du kan upprepa processen flera gånger för andra programgränser eller visar att du vill dela som på begäran direkt, medan ”Live” feed från den första LiveOutput fortsätter att sända linjär feeden.  Dessutom kan du dra nytta av dynamisk Filter-supporten om du vill trimma början och i slutet av arkivet från LiveOutput som du har introducerats till ”överlappning safety” mellan program och få en mer exakta början och slutet av innehållet. Arkiverat innehåll kan även skickas till en [transformera](https://docs.microsoft.com/rest/api/media/transforms) för kodning eller ramens korrekt underklippning till flera utdataformat som ska användas som syndikering till andra tjänster.

## <a name="streamingendpoint"></a>StreamingEndpoint

När dataströmmen väl flödar till LiveEventet kan du påbörja strömningshändelsen genom att skapa en tillgång, ett LiveOutput och en StreamingLocator. Detta kommer arkiverar dataströmmen och gör den tillgänglig för visning via den [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

När ditt Media Services-konto skapas läggs en standardslutpunkt för direktuppspelning till ditt konto i tillståndet stoppad. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste måste slutpunkten för direktuppspelning som du vill spela upp innehåll vara i tillståndet körs.

## <a name="latency"></a>Svarstid

Det här avsnittet beskrivs vanliga resultat som visas när du använder inställningar för låg latens och olika spelare. Resultatet varierar beroende på CDN och nätverksfördröjning.

Om du vill använda den nya funktionen för LowLatency, du kan ange den **StreamOptionsFlag** till **LowLatency** på LiveEvent. När den är igång, kan du använda den [Azure Media Player](http://ampdemo.azureedge.net/) (AMP) demonstration sidan och ange uppspelningsalternativ att använda ”låg latens heuristik profilen”.

### <a name="pass-through-liveevents"></a>Direktautentisering LiveEvents

||2S GOP låg latens som aktiverats|1s GOP låg latens som aktiverats|
|---|---|---|
|BINDESTRECK i AMP|10-tal|8S|
|HLS på interna iOS player|14s|10-tal|
|HLS. JS i Mixer-spelare|30 sekunder|16s|

## <a name="billing"></a>Fakturering

En LiveEvent börjar fakturering när dess status övergår till ”körs”. Du måste stoppa LiveEvent om du vill stoppa LiveEvent från fakturering.

> [!NOTE]
> När **LiveEventEncodingType** på din [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) har angetts till grundläggande, Media Services automatiskt avslutning alla LiveEvent som finns kvar i tillståndet ”körs” 12 timmar efter inkommande flödet går förlorad och det finns inga LiveOutputs körs. Dock kommer du fortfarande att debiteras för den tid som LiveEvent var i tillståndet ”körs”.
>

I följande tabell visas hur LiveEvent tillstånd mappas till faktureringsläget.

| LiveEvent tillstånd | Är det fakturering? |
| --- | --- |
| Startar |Nej (övergångsläge) |
| Körs |JA |
| Stoppas |Nej (övergångsläge) |
| Stoppad |Nej |

## <a name="next-steps"></a>Nästa steg

[Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
