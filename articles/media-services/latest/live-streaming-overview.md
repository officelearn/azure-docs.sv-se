---
title: Översikt över Liveströmning med Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över direktsänd strömning med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: b8c9375d8ad915200cbc8b2e1a62979fd1b7d179
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238304"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Live strömning med Azure Media Services v3

När leverera händelser via strömning live med Azure Media Services ingår oftast följande komponenter:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran (eller en annan enhet, till exempel bärbara datorer) till dataströmmar som skickas till Media Services live streaming service. Signalerna också innefatta reklam SCTE 35 och Ad-tips. 
* Media Services liveströmningstjänst kan du mata in, förhandsgranska, paket, registrera, kryptera och broadcast innehållet till dina kunder eller till en CDN för vidare distribution.

Den här artikeln ger en detaljerad översikt och inkluderar diagram över viktiga komponenter som ingår i direktsänd strömning med Media Services.

## <a name="overview-of-main-components"></a>Översikt över huvudkomponenter

I Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för bearbetning av liveströmmat innehåll. En LiveEvent innehåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en lokal livekodare. LiveEvent tar emot live indata från livekodaren i RTMP eller Smooth Streaming-format och gör den tillgänglig för strömning via en eller flera [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints). En [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) gör att du kan styra publicering, registrering och DVR fönstret inställningarna för den direktsända dataströmmen. LiveEvent ger också förhandsgranskningsslutpunkten (förhandsgranskning URL) som används för att förhandsgranska och verifiera strömmen innan ytterligare bearbetning och leverans. 

Media Services tillhandahåller **dynamisk paketering**, där du kan förhandsgranska och broadcast ditt innehåll i MPEG DASH, HLS, Smooth Streaming strömningsformat utan att paketera manuellt till dessa strömningsformat. Du kan spela upp med HLS, DASH eller Smooth kompatibla spelare. Du kan också använda [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) att testa din dataström.

Media Services kan du leverera ditt innehåll krypteras dynamiskt (**dynamisk kryptering**) med Advanced Encryption Standard (AES-128) eller någon av de tre huvudsakliga digital rights management (DRM) system: Microsoft PlayReady Google Widevines och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady Widevine och FairPlay) licenser till auktoriserade klienter.

Om du vill kan du också använda **dynamisk filtrering**, som kan användas för att styra antalet spår, format, bithastighet som skickas till spelarna. Media Services stöder även ad infogning.


## <a name="liveevent-types"></a>LiveEvent typer

En [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direktsänd kodning och direkt. 

### <a name="live-encoding-with-media-services"></a>Live encoding med Media Services

![Live encoding](./media/live-streaming/live-encoding.png)

En lokal livekodare skickar en dataström med enkel bithastighet till LiveEvent som är aktiverad för att utföra live encoding med Media Services i något av följande protokoll: RTMP eller Smooth Streaming (fragmenterad MP4). LiveEvent utför sedan live encoding av strömmen dataström med enkel bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

När du skapar den här typen av LiveEvent ange **grundläggande** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Direkt

![Direkt](./media/live-streaming/pass-through.png)

Direkt är optimerad för tidskrävande live dataströmmar eller 24 x 7 linjär live encoding med hjälp av en lokal livekodare. Lokala kodare skickar multibithastighet **RTMP** eller **Smooth Streaming** (fragmenterad MP4) till LiveEvent som är konfigurerad för **direkt** leverans. Den **direkt** leveransen är när de infogade strömmarna passerar **LiveEvent**s utan vidare bearbetning. 

Direkt LiveEvents stöder upp till 4K-upplösning och HEVC passera när det används med Smooth Streaming infogningsprotokollet. 

När du skapar den här typen av LiveEvent ange **ingen** (LiveEventEncodingType.None).

> [!NOTE]
> Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>LiveEvent typer jämförelse 

I följande tabell jämförs funktionerna i de två typerna av LiveEvent.

| Funktion | Direkt LiveEvent | Grundläggande LiveEvent |
| --- | --- | --- |
| Enkel bithastighet indata kodas till flera olika bithastigheter i molnet |Nej |Ja |
| Maximal upplösning, antalet lager |4Kp30  |720p, 6 lager 30 fps |
| Inkommande protokoll |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Pris |Finns det [sida med priser](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live Video” |Finns det [sida med priser](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal körtid |Dygnet runt |Dygnet runt |
| Stöd för att infoga pekdatorer |Nej |Ja |
| Stöd för ad-signalering via API|Nej |Ja |
| Stöd för ad-signalering via SCTE35 inband|Ja |Ja |
| Direkt CEA 608/708 etiketter |Ja |Ja |
| Möjlighet att återställa från kort bås i bidrag feed |Ja |Nej (LiveEvent börjar slating efter 6 + sekunder utan indata)|
| Stöd för icke-enhetlig inkommande GOPs |Ja |Nej – indata måste åtgärdas 2 sek GOPs |
| Stöd för variabeln ram hastighet indata |Ja |Nej – indata som måste åtgärdas bildfrekvens.<br/>Mindre variationer användas, till exempel under hög rörelse i bakgrunden. Men kodaren går inte att släppa till 10 bildrutor per sekund. |
| Auto-avslutning av LiveEvent när indata-feeden går förlorad |Nej |Efter 12 timmar, om det inte finns några LiveOutput körs |

## <a name="liveevent-states"></a>LiveEvent tillstånd 

Det aktuella tillståndet för en LiveEvent. Möjliga värden omfattar:

|Status|Beskrivning|
|---|---|
|**Stoppades**| Det här är LiveEvent initialt tillstånd när skapades (om inte autostart valdes anges.) Ingen fakturering inträffar i det här tillståndet. I det här tillståndet LiveEvent egenskaper kan uppdateras men strömning tillåts inte.|
|**Starta**| LiveEvent startas. Ingen fakturering inträffar i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar, returnerar LiveEvent till stoppat tillstånd.|
|**Kör**| LiveEvent kan bearbetning live dataströmmar. Det nu fakturering användning. Du måste stoppa LiveEvent för att förhindra att ytterligare fakturering.|
|**Stoppa**| LiveEvent stoppas. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.|
|**Ta bort**| LiveEvent tas bort. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.|

## <a name="liveoutput"></a>LiveOutput

En [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) gör att du kan styra publicering, registrering och DVR fönstret inställningarna för den direktsända dataströmmen. Relationen mellan LiveEvent och LiveOutput liknar traditionella media där en kanal (LiveEvent) har en konstant ström av innehåll och ett program (LiveOutput) är begränsad till viss tidsinställd händelse på den LiveEvent.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för LiveOutput genom att ange den **ArchiveWindowLength** egenskapen. **ArchiveWindowLength** är en ISO 8601 timespan längden (Digital Video inspelning eller DVR). Det här värdet kan anges från minst 5 minuter till högst 25 timmar. 

**ArchiveWindowLength** avgör också det maximala antalet klienter kan söka bakåt i tiden från den aktuella direktsända positionen. LiveOutputs kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje LiveOutput är associerad med en [tillgången](https://docs.microsoft.com/rest/api/media/assets), och registrerar data i en behållare i Azure storage kopplas till Media Services-kontot. Om du vill publicera LiveOutput, måste du skapa en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En LiveEvent stöder upp till tre LiveOutputs som körs samtidigt så du kan skapa flera Arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Till exempel dina affärsbehov att sända en 24 x 7 live linjär feed, men du vill skapa ”inspelningar” program per dag för att erbjuda till kunder som innehåll på begäran för catch-up visning.  I det här scenariot skapar du först en primär LiveOutput med en kort arkivfönster 1 timme eller mindre för kunder att lyssna på som den primära direktsända dataströmmen. Du skapar en StreamingLocator för den här LiveOutput och publicera det till programmet eller webbplatsen som ”Live”-feed.  Eftersom feeden körs, kan du skapa en andra samtidiga LiveOutput programmässigt i början av ett program (eller 5 minuter för tidigt att tillhandahålla vissa referenser till trim senare.) Den här andra LiveOutput kan stoppas 5 minuter efter det programmet eller den händelse att gälla och du kan sedan skapa en ny StreamingLocator för att publicera det här programmet som en tillgång på begäran i katalogen för ditt program.  Du kan upprepa processen flera gånger för andra program gränser eller markeras som du vill dela som på begäran omedelbart, alla medan ”Live” feed från den första LiveOutput fortsätter att sända linjär feeden.  Du kan dessutom dra nytta av dynamiska Filter-supporten om du vill rensa början och i slutet av arkivet från LiveOutput som du har introducerats för ”överlappande” mellan program och få en mer exakt början och slutet av innehållet. Arkiverat innehåll kan också skickas till en [transformera](https://docs.microsoft.com/rest/api/media/transforms) för kodning eller ram korrekt subclipping till flera utdataformat som ska användas som syndikering till andra tjänster.

## <a name="streamingendpoint"></a>StreamingEndpoint

När du har dataströmmen väl flödar till LiveEvent kan påbörja du strömningshändelsen genom att skapa en tillgång och LiveOutput StreamingLocator. Detta arkiverar dataströmmen och gör den tillgänglig för visning via den [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

När ditt Media Services-konto har skapats läggs standard strömmande slutpunkten till ditt konto i stoppat tillstånd. Om du vill starta strömning ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering, måste den strömningsslutpunkt från vilken du vill strömma innehåll vara i tillståndet körs.

## <a name="billing"></a>Fakturering

En LiveEvent börjar fakturering när dess tillstånd övergår till ”körs”. Om du vill stoppa LiveEvent från fakturering, måste du stoppa LiveEvent.

> [!NOTE]
> När **LiveEventEncodingType** på din [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) har angetts till Basic, Media Services automatiskt avslutning alla LiveEvent är fortfarande i tillståndet ”körs” 12 timmar efter inkommande feeden går förlorad och det finns inga LiveOutputs körs. Men debiteras du fortfarande för den tid som LiveEvent var i tillståndet ”körs”.
>

Tabellen nedan visar hur LiveEvent tillstånd mappas till fakturerings-läge.

| LiveEvent tillstånd | Är det fakturering? |
| --- | --- |
| Startar |Nej (övergångsläge) |
| Körs |JA |
| Stoppas |Nej (övergångsläge) |
| Stoppad |Nej |

## <a name="next-steps"></a>Nästa steg

[Självstudier för direktsänd strömning](stream-live-tutorial-with-api.md)
