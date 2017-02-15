---
title: "Översikt över Azure Media Services och vanliga scenarier | Microsoft Docs"
description: "Det här avsnittet ger en översikt över Azure Media Services"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 946f6e480083a0007a88c85b744ddeafa0385990


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Översikt över Azure Media Services och vanliga scenarier

Microsoft Azure Media Services är en utökningsbar molnbaserad plattform som gör att utvecklare kan skapa skalbara program för mediehantering och medieleverans. Media Services baseras på REST API:er som gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

Du kan bygga arbetsflöden för slutpunkt till slutpunkt bara med Media Services. Du kan också välja att använda komponenter från tredje part för vissa delar av arbetsflödet. Koda till exempel med hjälp av en kodare från tredje part. Därefter kan du överföra, skydda, paketera och leverera med Media Services.

Du kan välja att strömma innehållet live eller på begäran. Det här avsnittet beskriver vanliga scenarier för att leverera innehåll [live](media-services-overview.md#live_scenarios) eller [på begäran](media-services-overview.md#vod_scenarios). Avsnittet innehåller också länkar till andra relevanta avsnitt.

## <a name="sdks-and-tools"></a>SDK:er och verktyg

Bygg Media Services-lösningar genom att använda:

* [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
* En av de tillgängliga klient-SDK:erna:
    * [Azure Media Services SDK för .NET](https://github.com/Azure/azure-sdk-for-media-services),
    * [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services för Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (detta är en icke-Microsoft-version av en Node.js SDK. Den underhålls av ett community och har för närvarande inte en 100-procentig täckning av AMS API:er).
* Befintliga verktyg:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) är ett Winforms/C#-program för Windows)

Följande bild visar några av de vanligast använda objekten när du utvecklar mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

<a href="https://docs.microsoft.com/en-us/azure/media-services/media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a>  

Du kan visa hela modellen [här](https://media.windows.net/API/$metadata?api-version=2.15).  


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
Du kan visa sökvägar för AMS-utbildning här:

* [Arbetsflöde för AMS-liveuppspelning](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Arbetsflöde för AMS-strömning på begäran](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Krav

Om du vill börja använda Azure Media Services ska du ha följande:

1. Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
2. Ett Azure Media Services-konto. Använd Azure Portal, .NET eller REST API för att skapa ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
3. (Valfritt) Konfigurera utvecklingsmiljön. Välj .NET eller REST API för din utvecklingsmiljö. Mer information finns i [Ställa in miljön](media-services-dotnet-how-to-use.md).

    Lär dig även att [ansluta programmässigt](media-services-dotnet-connect-programmatically.md).
4. En standard- eller premiumslutpunkt för direktuppspelning med tillståndet Startad.  Mer information finns i [Hantera strömningsslutpunkter](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints).

## <a name="concepts-and-overview"></a>Koncept och översikt
Azure Media Services-koncepten finns i [Koncept](media-services-concepts.md).

En instruktionsserie som ger en introduktion till huvudkomponenterna i Azure Media Services finns i [Azure Media Services stegvisa självstudier](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Den här serien ger en bra översikt över koncepten och använder verktyget AMSE för att demonstrera AMS-uppgifter. Observera att AMSE-verktyget är ett verktyg i Windows. Det här verktyget stöder de flesta av de uppgifter som du kan göra programmässigt med [AMS SDK för .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java) eller  [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

## <a name="a-idvodscenariosadelivering-media-on-demand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Leverera media på begäran med Azure Media Services: vanliga scenarier och uppgifter
Det här avsnittet beskriver vanliga scenarier och innehåller länkar till relevanta avsnitt. I följande diagram visas de huvudsakliga delarna i plattformen Media Services som är inblandade i att leverera innehåll på begäran.

![VoD-arbetsflöde](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
    
### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Skydda lagrat innehåll och leverera strömmande media i klartext (okrypterat)
1. Överför en mezzaninfil av hög kvalitet till en tillgång.

    Vi rekommenderar att du använder ett krypterat lagringsalternativ för tillgången så att du skyddar innehållet under överföringen och i vila i lagring.
2. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet.

    Vi rekommenderar att du använder ett krypterat lagringsalternativ för utdatatillgången så att du skyddar innehållet i vila.
3. Konfigurera principen för tillgångsleverans (används av dynamisk paketering).

    Om tillgången är lagringskrypterad **måste** du konfigurera principen för tillgångsleverans.
4. Publicera tillgången genom att skapa en OnDemand-positionerare.
5. Strömma publicerat innehåll.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Skydda lagrat innehåll, leverera dynamiskt krypterade strömmande media

1. Överför en mezzaninfil av hög kvalitet till en tillgång. Använd krypterat lagringsalternativ för tillgången.
2. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet. Använd krypterat lagringsalternativ för utdatatillgången.
3. Skapa en innehållskrypteringsnyckel för den tillgång du vill kryptera dynamiskt under uppspelning.
4. Konfigurera principen för auktorisering av innehållsnyckel.
5. Konfigurera principen för tillgångsleverans (används av dynamisk paketering och dynamisk kryptering).
6. Publicera tillgången genom att skapa en OnDemand-positionerare.
7. Strömma publicerat innehåll.

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Använd Media Analytics för att härleda insikter som går att åtgärda från dina videor
Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som går att åtgärda från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](media-services-analytics-overview.md).

1. Överför en mezzaninfil av hög kvalitet till en tillgång.
2. Använd någon av följande Media Analytics-tjänster för att bearbeta videor:

   * **Indexerare** – [bearbeta videor med Azure Media Indexer 2](media-services-process-content-with-indexer2.md)
   * **Videostabilisera** – [Videostabilisera mediefiler med Azure Media Hyperlapse](media-services-hyperlapse-content.md)
   * **Rörelseidentifiering** – [rörelseidentifiering för Azure Media Analytics](media-services-motion-detection.md).
   * **Ansiktsigenkänning och ansiktskänslor** – [Ansikts- och känsloigenkänning för Azure Media Analytics](media-services-face-and-emotion-detection.md).
   * **Sammanfattning av video** – [Använd Azure Media Video Thumbnails för att skapa en videosammanfattning](media-services-video-summarization.md)
3. Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor har producerat en MP4-fil kan du hämta filen progressivt. Om en medieprocessor har producerat en JSON-fil kan du hämta filen från Azure Blob Storage.

### <a name="deliver-progressive-download"></a>Leverera progressiv nedladdning
1. Överför en mezzaninfil av hög kvalitet till en tillgång.
2. Koda till en enda MP4-fil.
3. Publicera tillgången genom att skapa en positionerare på begäran eller en SAS-positionerare.

    Om du använder en SAS-positionerare hämtas innehåll från Azure Blob Storage. I så fall behöver du inte ha slutpunkter för direktuppspelning med tillståndet Startad.
4. Progressivt hämtat innehåll

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Leverera liveuppspelningshändelser med Azure Media Services
När du arbetar med liveuppspelning ingår oftast följande komponenter:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning.

Du kan också välja flera kodare som synkroniserar tiden live. För vissa viktiga live-händelser som kräver mycket hög tillgänglighet och kvalitet rekommenderar vi att du använder redundanta aktiv/aktiv-kodare med tidssynkronisering för sömlös redundans utan att förlora data.

* En liveuppspelningstjänst som ger dig möjlighet att göra följande:
* infoga innehållet via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming)
* (valfritt) koda strömmen till ström med anpassningsbar bithastighet
* förhandsgranska din liveuppspelning
* spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand)
* leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

**Microsoft Azure Media Services** (AMS) ger möjligheten att infoga, koda, förhandsgranska, lagra och leverera liveuppspelningsinnehåll.

Målet är att tillhandahålla en video av hög kvalitet till olika enheter under olika nätverksförhållanden när du levererar ditt innehåll till kunder. Ta hand om kvalitet och nätverk genom att använda livekodare för att koda strömmen till videoströmmar med flera bithastigheter (anpassningsbar bithastighet).  Använd Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) för att ta hand om strömning på olika enheter och dynamiskt ompaketera din ström till olika protokoll. Media Services har stöd för leverans av följande tekniker för direktuppspelning med anpassningsbar bithastighet: HTTP Live Streaming(HLS), Smooth Streaming, MPEG DASH.

I Azure Media Services hanterar **kanaler**, **program** och **strömningsslutpunkter** alla funktioner för liveuppspelning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans.

En **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal kan ta emot en live-indataström på följande sätt:

* En lokal livekodare skickar **RTMP** eller **Smooth Streaming** med flera bithastigheter (fragmenterad MP4) till den kanal som är konfigurerad för **direkt** leverans. Den **direkta** leveransen är när de infogade strömmarna passerar genom **kanalerna** utan vidare transkodning eller kodning. Du kan använda följande livekodare som skickar Smooth Streaming med flera bithastigheter: MediaExcel, Imagine Communications, Ateme, Envivio, Cisco och Elemental. Följande livekodare skickar RTMP: Adobe Flash Live Encoder, Haivision, Telestream Wirecast, Teradek och Tricaster-kodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

> [!NOTE]
> Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
>
>

* En lokal livekodare skickar en ström med en enda bithastighet till den kanal som är aktiverad för att utföra Live Encoding med Media Services i något av följande format: RTP (MPEG-TS) RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)
I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Live-arbetsflöde][live-overview2]

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services
I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde][live-overview1]

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Konsumera innehåll
Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. Följande avsnitt innehåller länkar till SDK:er och spelarramverk som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Media Services.

[Utveckla videouppspelningsprogram](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Aktivera Azure CDN
Media Services stöder integration med Azure CDN. Information om hur du aktiverar Azure CDN finns i [Hur du hanterar strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Skala ett Media Services-konto

Du kan skala **Media Services** genom att ange antalet **enheter som är reserverade för strömning** och **enheter som är reserverade för kodning** och som du vill att ditt konto ska etableras med.

Du kan även skala ditt Media Services-konto genom att lägga till lagringskonton. Varje lagringskonto är begränsat till 500 TB. Du kan välja att koppla flera lagringskonton för till ett enda Media Services-konto om du vill expandera din lagring utöver standardbegränsningarna.
Media Services-kunder väljer antingen en **Standard**-slutpunkt för direktuppspelning eller en eller flera **Premium**-slutpunkter för direktuppspelning, utifrån behov. Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Den innehåller samma funktioner som Premium-enheter för direktuppspelning. Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Om du har en avancerad arbetsbelastning, om dina kapacitetskrav för direktuppspelning inte passar genomflödesmål för standard-slutpunkter för direktuppspelning eller om du vill styra StreamingEndpoint-tjänstens kapacitet för att hantera växande bandbreddsbehov genom att justera skalningsenheter (kallas även premium-enheter för direktuppspelning) rekommenderas du att allokera skalningsenheter.

[Denna](media-services-portal-scale-streaming-endpoints.md) artikel länkar till relevanta avsnitt.

## <a name="support"></a>Support
[Azure-supporten](https://azure.microsoft.com/support/options/) tillhandahåller support för Azure, inklusive Media Services.

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)
* Vi garanterar 99,9 % tillgänglighet för REST API-transaktioner för Media Services Encoding.
* För Streaming svarar vi på serviceförfrågningar med 99,9 % tillgänglighet för befintligt medieinnehåll när en Standard- eller Premium-slutpunkt för direktuppspelning har köpts.
* För live-kanaler garanterar vi att kanaler som körs ska ha extern anslutning minst 99,9 % av tiden.
* När det gäller Content Protection garanterar vi att vi ska uppfyller viktiga förfrågningar minst 99,9 % av tiden.
* När det gäller Indexer ska vi utföra service för indexeringsuppgifter som bearbetas med en kodningsreserverad enhet 99,9 % av tiden.

Mer information finns i [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO2-->


