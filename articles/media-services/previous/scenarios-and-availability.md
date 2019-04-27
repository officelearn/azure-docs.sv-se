---
title: Microsoft Azure Media Services-scenarier och tillgängligheten för funktioner i datacenter | Microsoft Docs
description: Det här avsnittet ger en översikt över Microsoft Azure Media Services-scenarier och tillgängligheten för funktioner och tjänster i datacenter.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3576a50cd1510a4da562981ff9ae98e12a357fc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638260"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scenarier och tillgängligheten för Media Services-funktioner i datacenter

Microsoft Azure Media Services (AMS) gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

AMS körs på flera datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). 

Det här avsnittet beskriver vanliga scenarier för att leverera innehåll [live](#live_scenarios) eller på begäran. Ämnet innehåller också information om tillgängligheten för mediefunktioner och tjänster i datacenter.

## <a name="overview"></a>Översikt

### <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill börja använda Azure Media Services ska du ha följande:

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
* Ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
* Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

    När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning ha tillståndet **Körs**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Vanliga objekt när du utvecklar mot AMS OData-modellen

Följande bild visar några av de vanligast använda objekten när du utvecklar mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Du kan visa hela modellen [här](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Skydda lagrat innehåll och leverera strömmande media i klartext (okrypterat)

![VoD-arbetsflöde](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Överför en mediefil av hög kvalitet till en tillgång.

    Vi rekommenderar att du använder ett krypterat lagringsalternativ för tillgången så att du skyddar innehållet under överföringen och i vila i lagring.
2. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet.

    Vi rekommenderar att du använder ett krypterat lagringsalternativ för utdatatillgången så att du skyddar innehållet i vila.
3. Konfigurera principen för tillgångsleverans (används av dynamisk paketering).

    Om tillgången är lagringskrypterad **måste** du konfigurera principen för tillgångsleverans.
4. Publicera tillgången genom att skapa en OnDemand-positionerare.
5. Strömma publicerat innehåll.

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Skydda lagrat innehåll, leverera dynamiskt krypterade strömmande media

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Överför en mediefil av hög kvalitet till en tillgång. Använd krypterat lagringsalternativ för tillgången.
2. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet. Använd krypterat lagringsalternativ för utdatatillgången.
3. Skapa en innehållskrypteringsnyckel för den tillgång du vill kryptera dynamiskt under uppspelning.
4. Konfigurera principen för auktorisering av innehållsnyckel.
5. Konfigurera principen för tillgångsleverans (används av dynamisk paketering och dynamisk kryptering).
6. Publicera tillgången genom att skapa en OnDemand-positionerare.
7. Strömma publicerat innehåll.

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Använd Media Analytics för att härleda insikter som går att åtgärda från dina videor

Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som går att åtgärda från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](media-services-analytics-overview.md).

1. Överför en mediefil av hög kvalitet till en tillgång.
2. Bearbeta videor med någon av de medieanalystjänster som beskrivs i [översikten över medieanalys](media-services-analytics-overview.md).
3. Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor har producerat en MP4-fil kan du hämta filen progressivt. Om en medieprocessor har producerat en JSON-fil kan du hämta filen från Azure Blob Storage.

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="deliver-progressive-download"></a>Leverera progressiv nedladdning

1. Överför en mediefil av hög kvalitet till en tillgång.
2. Koda till en enda MP4-fil.
3. Publicera tillgången genom att skapa en positionerare på begäran eller en SAS-positionerare.

    Om du använder en SAS-positionerare hämtas innehåll från Azure Blob Storage. I så fall behöver du inte ha slutpunkter för direktuppspelning med tillståndet Startad.
4. Progressivt hämtat innehåll

## <a id="live_scenarios"></a>Leverera liveuppspelningshändelser 

1. Infoga innehåll via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming).
2. (Valfritt) Koda strömmen till ström med anpassningsbar bithastighet.
3. Förhandsgranska din liveuppspelning.
4. Leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

    ELLER

    Spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand).

Vid liveuppspelning kan du välja någon av följande vägar:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot direktsänd ström med flera bithastigheter från lokala kodare (genomströmning)

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Live-arbetsflöde](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="consuming-content"></a>Konsumera innehåll

Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. Följande avsnitt innehåller länkar till SDK:er och spelarramverk som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Media Services. Mer information finns i [Developing video player applications](media-services-develop-video-players.md) (Utveckla videospelarprogram)

## <a name="enabling-azure-cdn"></a>Aktivera Azure CDN

Media Services stöder integration med Azure CDN. Information om hur du aktiverar Azure CDN finns i [Hur du hanterar strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md).

## <a id="scaling"></a>Skala ett Media Services-konto

AMS-kunder kan skala slutpunkter för direktuppspelning, mediebearbetning och lagring i sina AMS-konton.

* Media Services-kunder kan antingen välja en **Standard**-slutpunkt för direktuppspelning eller en **Premium**-slutpunkt för direktuppspelning. En **Standard**-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Innehåller samma funktioner som **Premium**-slutpunkter för direktuppspelning och skalar utgående bandbredd automatiskt. 

    **Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om hur du skalar **Premium**-slutpunkter för direktuppspelning finns i avsnittet [Scaling streaming endpoints](media-services-portal-scale-streaming-endpoints.md) (Skala slutpunkter för direktuppspelning).

* Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** eller **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**.

    Förutom att ange typ av reserverad enhet kan du etablera **reserverade enheter** (RU:er) för ditt konto. Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

    >[!NOTE]
    >RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

    Mer information finns i [Skala mediebearbetning](media-services-portal-scale-media-processing.md).
* Du kan även skala ditt Media Services-konto genom att lägga till lagringskonton. Varje lagringskonto är begränsat till 500 TB. Du kan välja att koppla flera lagringskonton för till ett enda Media Services-konto om du vill expandera din lagring utöver standardbegränsningarna. Mer information finns i [Hantera lagringskonton](meda-services-managing-multiple-storage-accounts.md).

## <a id="availability"></a> Tillgänglighet för Media Services-funktioner i datacenter

Det här avsnittet innehåller information om tillgängligheten för Media Services-funktioner i datacenter.

### <a name="ams-accounts"></a>AMS-konton

#### <a name="availability"></a>Tillgänglighet

Om du vill fastställa om Media Services är tillgängligt i ett datacenter, går du till https://azure.microsoft.com/status/ och bläddra till MEDIA-tabellen.

### <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning 

Media Services-kunder kan antingen välja en **Standard**-slutpunkt för direktuppspelning eller en **Premium**-slutpunkt för direktuppspelning. Mer information finns i avsnittet om [skalning](#scaling).

#### <a name="availability"></a>Tillgänglighet

|Namn|Status|Datacenter
|---|---|---|
|Standard|Allmän tillgänglighet (GA)|Alla|
|Premium|Allmän tillgänglighet (GA)|Alla|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Tillgänglighet

Tillgängligt i alla datacenter förutom: Tyskland, Brasilien, södra, Indien, västra, Indien, södra och Indien, centrala. 

### <a name="encoding-media-processors"></a>Mediebearbetare för kodning

AMS erbjuder två kodare på begäran: **Media Encoder Standard** och **Media Encoder Premium Workflow**. Mer information finns i [Overview and comparison of Azure on-demand media encoders](media-services-encode-asset.md) (Översikt och jämförelse av Azures mediekodare på begäran). 

#### <a name="availability"></a>Tillgänglighet

|Namn på mediebearbetare|Status|Datacenter
|---|---|---|
|Media Encoder Standard|Allmän tillgänglighet (GA)|Alla|
|Arbetsflöde för Media Encoder Premium|Allmän tillgänglighet (GA)|Alla utom Kina|

### <a name="analytics-media-processors"></a>Mediebearbetare för analys

Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](media-services-analytics-overview.md).

#### <a name="availability"></a>Tillgänglighet

|Namn på mediebearbetare|Status|Datacenter
|---|---|---|
|Azure Media Face Detector|Förhandsversion|Alla|
|Azure Media Hyperlapse|Förhandsversion|Alla|
|Azure Media Indexer|Allmän tillgänglighet (GA)|Alla|
|Azure Media Motion Detector|Förhandsversion|Alla|
|Azure Media OCR|Förhandsversion|Alla|
|Azure Media Redactor|Förhandsversion|Alla|
|Azure Media Stabilizer|Förhandsversion|Alla|
|Azure Media Video Thumbnails|Förhandsversion|Alla|
|Azure Media Indexer 2|Förhandsversion|Allt utom Kina och federala myndigheter|

### <a name="protection"></a>Skydd

Med Microsoft Azure Media Services kan du skydda dina mediefiler från att filerna lämnar din dator och medan de lagras, bearbetas och levereras. Mer information finns i avsnittet om att [skydda AMS-innehåll](media-services-content-protection-overview.md).

#### <a name="availability"></a>Tillgänglighet

|Kryptering|Status|Datacenter|
|---|---|---| 
|Storage|Allmän tillgänglighet (GA)|Alla|
|128-bitars AES-nycklar|Allmän tillgänglighet (GA)|Alla|
|Fairplay|Allmän tillgänglighet (GA)|Alla|
|PlayReady|Allmän tillgänglighet (GA)|Alla|
|Widevine|Allmän tillgänglighet (GA)|Alla utom Tyskland, federala myndigheter och Kina.

### <a name="reserved-units-rus"></a>Reserverade enheter (RU:er)

Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. 

Mer information finns i avsnittet om [skalning](#scaling).

#### <a name="availability"></a>Tillgänglighet

Tillgängligt i alla datacenter.

### <a name="reserved-unit-ru-type"></a>Typ av reserverad enhet (RU)

Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: S1, S2 eller S3.

Mer information finns i avsnittet om [skalning](#scaling).

#### <a name="availability"></a>Tillgänglighet

|Namn på RU-typ|Status|Datacenter
|---|---|---|
|S1|Allmän tillgänglighet (GA)|Alla|
|S2|Allmän tillgänglighet (GA)|Alla utom södra Brasilien och västra Indien|
|S3|Allmän tillgänglighet (GA)|Allt utom västra Indien|

## <a name="next-steps"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

