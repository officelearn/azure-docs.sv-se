---
title: Översikt över liveuppspelning med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över liveuppspelning med Azure Media Services v3.
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
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: 3be7ad84cf0d45276c136465d7247ec43621aceb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810966"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Liveuppspelning med Azure Media Services v3

Azure Media Services kan du leverera händelser till dina kunder på Azure-molnet. För att strömma din direktsända händelser med Media Services behöver du följande:  

- En kamera som används för att hämta live-händelse.
- En live-videokodare som konverterar signaler från kameran (eller en annan enhet, till exempel en bärbar dator) till ett bidrag feed som skickas till Media Services. Bidrag feed kan innehålla signaler som rör reklam, till exempel SCTE 35 markörer.
- Komponenterna i Media Services, som gör det möjligt att mata in, förhandsgranska, paket, registrera, kryptera och sända live-händelse till dina kunder eller till ett nätverk för Innehållsleverans för vidare distribution.

Den här artikeln ger en detaljerad översikt vägledning, tillsammans med diagram över huvudkomponenterna som ingår i direktsänd strömning med Media Services.

## <a name="live-streaming-workflow"></a>Live-arbetsflöde

Här följer stegen för en live arbetsflöde:

1. Kontrollera att den **StreamingEndpoint** körs. 
2. Skapa en **LiveEvent**. 
  
    När du skapar händelsen, kan du ange att autostart den. Du kan också starta händelsen när du är redo att börja direktuppspelning.<br/> När autostart är satt till SANT, Live-händelsen kommer att startas rätt när du har skapat. Det innebär att, faktureringen startar när Live-händelsen körs. Du måste explicit anropa Stop på LiveEvent resursen för att stoppa ytterligare fakturering. Mer information finns i [LiveEvent tillstånd och](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera din lokala kodare för att använda Webbadressen för att skicka bidraget feed.<br/>Se [rekommenderas livekodare](recommended-on-premises-live-encoders.md).
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **LiveOutput** och använda Tillgångsnamn som du skapade.

     Den **LiveOutput** arkiverar dataströmmen till den **tillgången**.
7. Skapa en **StreamingLocator** med inbyggt **StreamingPolicy** typer.

    Om du vill kryptera ditt innehåll, granska [Content protection översikt](content-protection-overview.md).
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda (dessa är deterministisk).
9. Hämta värdnamnet för den **Strömningsslutpunkt** du vill strömma från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Om du vill stoppa att göra din **LiveEvent** kan visas, måste du stoppa strömning händelse och ta bort den **StreamingLocator**.

Mer information finns i den [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md).

## <a name="overview-of-main-components"></a>Översikt över huvudkomponenterna

Om du vill leverera på begäran eller live-dataströmmar med Media Services, måste du ha minst en [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). När ditt Media Services-konto skapas en **standard** StreamingEndpoint läggs till i ditt konto i den **stoppad** tillstånd. Du måste starta StreamingEndpoint där du vill kunna strömma ditt innehåll till användarna. Du kan använda standardgrupperna **StreamingEndpoint**, eller skapar en ny anpassad **StreamingEndpoint** med önskad konfiguration och CDN-inställningarna. Du kan välja att aktivera flera Strömningsslutpunkter, var och en riktar in sig på en annan CDN och ge ett unikt värdnamn för leverans av innehåll. 

I Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsänd video feeds. När du skapar en LiveEvent, skapas en slutpunkt för indata som du kan använda för att skicka en direktsänd signal från en fjärransluten kodare. Remote livekodare skickar bidraget till som indata slutpunkten med hjälp av antingen den [RTMP](https://www.adobe.com/devnet/rtmp.html) eller [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmenterad MP4)-protokollet. För Smooth Streaming-inmatningsprotokollet, URL-scheman som stöds är `http://` eller `https://`. RTMP-infogningsprotokollet, URL-scheman som stöds är `rtmp://` eller `rtmps://`. Mer information finns i [rekommenderas livestreaming av kodare](recommended-on-premises-live-encoders.md).<br/>
När du skapar en **LiveEvent**, du kan ange tillåtna IP-adresser i något av följande format: IpV4-adress med 4 siffror, CIDR-adressintervall.

När den **LiveEvent** börjar ta emot bidrag feed, som du kan använda dess förhandsgranskningsslutpunkten (förhandsgransknings-URL att förhandsgranska och validera att du får den direktsända dataströmmen innan du ytterligare publicerar. När du har kontrollerat att förhandsgranska dataströmmen är bra, du kan använda LiveEvent så att den direktsända dataströmmen analysleverans via en eller flera (färdiga) **Strömningsslutpunkter**. För att åstadkomma detta måste du skapa ett nytt [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) på den **LiveEvent**. 

Den **LiveOutput** objekt som liknar en inspelningar som ska fånga upp och registrera den direktsända dataströmmen till en tillgång i Media Services-kontot. Inspelat innehåll ska behållas i Azure Storage-kontot som hör till ditt konto till den behållare som definieras av tillgången resursen.  Den **LiveOuput** också låter dig styra vissa egenskaper för utgående direktsändningen, till exempel hur mycket av dataströmmen som sparas i arkivet inspelningen (till exempel kapaciteten för moln DVR). Arkivet på disken är en cirkulär Arkiv ”fönster” som innehåller endast mängden innehåll som har angetts i den **archiveWindowLength** egenskapen för den **LiveOutput**. Innehållet som ligger utanför det här fönstret tas automatiskt bort från storage-behållare och kan inte återställas. Du kan skapa flera LiveOutputs (upp till tre maximalt) på en LiveEvent med olika Arkiv längd och inställningar.  

Med medietjänster kan du dra nytta av **dynamisk paketering**, där du kan förhandsgranska och sända direktsändningar i [format som MPEG DASH, HLS och Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) från bidraget feed att skicka till tjänsten. Användarna kan spela upp den direktsända dataströmmen med valfri kompatibel spelare HLS, DASH eller Smooth Streaming. Du kan använda [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) i dina webb- och mobilprogram att leverera din dataström i någon av de här protokollen.

Media Services kan du leverera ditt innehåll dynamiskt krypterad (**dynamisk kryptering**) med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser till auktoriserade klienter. Mer information om hur du kryptera ditt innehåll med Media Services finns i [skyddar innehåll – översikt](content-protection-overview.md)

Om du vill kan använda du också dynamisk filtrering, som kan användas för att styra antalet spår, format, olika bithastigheter och presentation tidsfönster som skickas till spelarna. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Information om nya funktioner för liveuppspelning i v3 finns i [riktlinjer för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md).

## <a name="liveevent-types"></a>LiveEvent typer

En [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direkt och live encoding. 

### <a name="pass-through"></a>Direkt

![direktautentisering](./media/live-streaming/pass-through.png)

När du använder direkt LiveEvent kan du förlita dig på din lokala livekodare för att generera en videoström för flera bithastigheter och skicka att som bidraget flöde till LiveEvent (med RTMP eller fragmenterad MP4-protokollet). LiveEvent innebär sedan igenom inkommande video strömmar utan vidare bearbetning. En direkt LiveEvent är optimerad för tidskrävande direktsändningar eller 24 x 365 linjär liveuppspelning. När du skapar den här typen av LiveEvent kan du ange None (LiveEventEncodingType.None).

Du kan skicka bidraget feed på lösningar upp till 4 K och vid en bildruta andelen 60 bildrutor per sekund, med H.264/AVC eller H.265/HEVC codec för indatavideo och AAC (AAC-LC, HE-AACv1 eller HE-AACv2) ljudcodec.  Se den [LiveEvent skriver jämförelse och begränsningar](live-event-types-comparison.md) nedan för mer information.

> [!NOTE]
> Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
> 

Se en direktsänd exempel i [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.png)

När du använder live encoding med Media Services, kan du konfigurera din lokala livekodare för att skicka en enkel bithastighet video som bidrag till LiveEvent (med RTMP eller fragmenterad Mp4-protokollet). LiveEvent kodar den inkommande, enkel bithastigheten, strömma till en [flera video bithastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), gör den tillgänglig för leverans för uppspelning av protokoll som MPEG-DASH, HLS och Smooth Streaming-enheter. När du skapar den här typen av LiveEvent, ange kodningstyp som **Standard** (LiveEventEncodingType.Standard).

Du kan skicka bidraget feed på upp till 1080 p upplösning vid en bildfrekvens av 30 bilder/sekund, med H.264/AVC video-codec och AAC (AAC-LC, HE-AACv1 eller HE-AACv2) ljudcodec. Se den [LiveEvent skriver jämförelse och begränsningar](live-event-types-comparison.md) nedan för mer information.

## <a name="liveevent-types-comparison"></a>LiveEvent typer jämförelse

I följande artikel innehåller en tabell som jämför funktionerna i de två typerna av LiveEvent: [Jämförelse av](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

En [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) låter dig kontrollera egenskaperna för den utgående direktsända dataströmmen, till exempel hur mycket av strömmen registreras (t.ex, kapaciteten för moln-DVR) och om huruvida användarna kan börjar titta på live stream. Förhållandet mellan en **LiveEvent** och dess **LiveOutput**s relation liknar traditionella TV-sändning, där en kanal (**LiveEvent**) representerar en konstant ström av video och en inspelning (**LiveOutput**) är begränsad till en viss tidpunkt-segment (till exempel kvällar nyheter från 18:30:00 till 19:00:00). Du kan spela TV med hjälp av en Digital Video Recorder (DVR) – den motsvarande funktionen i LiveEvents hanteras via egenskapen ArchiveWindowLength. Det är en ISO 8601-timespan varaktighet (till exempel PTHH:MM:SS) som anger kapaciteten för DVR och kan anges från minst 3 minuter till högst 25 timmar.

> [!NOTE]
> **LiveOutput**s börjar vid skapandet och avbryts när tas bort. När du tar bort den **LiveOutput**, du inte tar bort den underliggande **tillgången** och innehåll i tillgången. 
>
> Om du har publicerat den **LiveOutput** tillgången med hjälp av en **StreamingLocator**, **LiveEvent** (upp till DVR fönstret längd) fortsätter att vara synliga tills **StreamingLocator**'s slutar att gälla eller tas bort som inträffar först.

Mer information finns i [med molnbaserade DVR](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

När du har dataströmmen väl flödar till den **LiveEvent**, kan du påbörja strömningshändelsen genom att skapa en **tillgången**, **LiveOutput**, och **StreamingLocator** . Detta kommer arkiverar dataströmmen och gör den tillgänglig för visning via den [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

När ditt Media Services-konto skapas läggs en standardslutpunkt för direktuppspelning till ditt konto i tillståndet stoppad. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste måste slutpunkten för direktuppspelning som du vill spela upp innehåll vara i tillståndet körs.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />LiveEvent tillstånd och fakturering

En LiveEvent börjar fakturering när dess status övergår till **kör**. Du måste stoppa LiveEvent om du vill stoppa LiveEvent från fakturering.

Detaljerad information finns i [tillstånd och fakturering](live-event-states-billing.md).

## <a name="latency"></a>Svarstid

Detaljerad information om LiveEvents svarstid finns i [svarstid](live-event-latency.md).

## <a name="next-steps"></a>Nästa steg

- [LiveEvent typer jämförelse](live-event-types-comparison.md)
- [Tillstånd och fakturering](live-event-states-billing.md)
- [Svarstid](live-event-latency.md)
