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
ms.openlocfilehash: 58ecca6251e4c428ae5f834379e8b45059b0efc9
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042765"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scenarier och tillgängligheten för Media Services-funktioner i datacenter

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

AMS körs på flera datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). 

Det här avsnittet visar vanliga scenarier för att leverera innehåll [Live](#live_scenarios) eller på begäran. Ämnet innehåller också information om tillgängligheten för mediefunktioner och tjänster i datacenter.

## <a name="overview"></a>Översikt

### <a name="prerequisites"></a>Krav

Om du vill börja använda Azure Media Services ska du ha följande:

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
* Ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
* Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs** .

    När ditt AMS-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning ha tillståndet **Körs** .

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Vanliga objekt när du utvecklar mot AMS OData-modellen

Följande bild visar några av de vanligast använda objekten när du utvecklar mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

[![Diagram som visar några av de vanligaste objekten när du utvecklar mot Azure Media Services objekts data modell.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

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
5. Konfigurera en princip för tillgångsleveranser (används av dynamisk paketering och dynamisk kryptering).
6. Publicera tillgången genom att skapa en OnDemand-positionerare.
7. Strömma publicerat innehåll.

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Använd Media Analytics för att härleda insikter som går att åtgärda från dina videor

Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som går att åtgärda från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](./legacy-components.md).

1. Överför en mediefil av hög kvalitet till en tillgång.
2. Bearbeta videor med någon av de medieanalystjänster som beskrivs i [översikten över medieanalys](./legacy-components.md).
3. Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor har producerat en MP4-fil kan du hämta filen progressivt. Om en medieprocessor har producerat en JSON-fil kan du hämta filen från Azure Blob Storage.

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="deliver-progressive-download"></a>Leverera progressiv nedladdning

1. Överför en mediefil av hög kvalitet till en tillgång.
2. Koda till en enda MP4-fil.
3. Publicera tillgången genom att skapa en positionerare på begäran eller en SAS-positionerare.

    Om du använder en SAS-positionerare hämtas innehåll från Azure Blob Storage. I så fall behöver du inte ha slutpunkter för direktuppspelning med tillståndet Startad.
4. Progressivt hämtat innehåll

## <a name="delivering-live-streaming-events"></a><a id="live_scenarios"></a>Leverera liveuppspelningshändelser 

1. Infoga innehåll via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming).
2. (Valfritt) Koda strömmen till ström med anpassningsbar bithastighet.
3. Förhandsgranska din liveuppspelning.
4. Leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

    \- eller -

    Spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand).

Vid liveuppspelning kan du välja någon av följande vägar:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning** .

![Diagram som visar de viktigaste delarna av en M S-plattform som ingår i "genom strömning"-arbets flödet.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](#availability).

## <a name="consuming-content"></a>Konsumera innehåll

Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. 

## <a name="enabling-azure-cdn"></a>Aktivera Azure CDN

Media Services stöder integration med Azure CDN. Information om hur du aktiverar Azure CDN finns i [Hur du hanterar strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a><a id="scaling"></a>Skala ett Media Services-konto

AMS-kunder kan skala slutpunkter för direktuppspelning, mediebearbetning och lagring i sina AMS-konton.

* Media Services-kunder kan antingen välja en **Standard** -slutpunkt för direktuppspelning eller en **Premium** -slutpunkt för direktuppspelning. En **Standard** -slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Innehåller samma funktioner som **Premium** -slutpunkter för direktuppspelning och skalar utgående bandbredd automatiskt. 

    **Premium** -slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium** -slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om hur du skalar **Premium** -slutpunkter för direktuppspelning finns i avsnittet [Scaling streaming endpoints](media-services-portal-scale-streaming-endpoints.md) (Skala slutpunkter för direktuppspelning).

* Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1** , **S2** och **S3** . Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1** .

    Förutom att ange typ av reserverad enhet kan du ange att ditt konto ska etableras med **reserverade enheter** (ru: er). Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

    >[!NOTE]
    >RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

    Mer information finns i [skala medie bearbetning](media-services-portal-scale-media-processing.md).
* Du kan även skala ditt Media Services-konto genom att lägga till lagringskonton. Varje lagringskonto är begränsat till 500 TB. Du kan välja att koppla flera lagringskonton för till ett enda Media Services-konto om du vill expandera din lagring utöver standardbegränsningarna. Mer information finns i [Hantera lagringskonton](./media-services-managing-multiple-storage-accounts.md).

## <a name="availability-of-media-services-features-across-datacenters"></a><a id="availability"></a> Tillgänglighet för Media Services-funktioner i datacenter

Det här avsnittet innehåller information om tillgängligheten för Media Services-funktioner i datacenter.

### <a name="ams-accounts"></a>AMS-konton

#### <a name="availability"></a>Tillgänglighet

Använd [Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) för att avgöra om Media Services är tillgängligt i ett särskilt data Center.

### <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning 

Media Services-kunder kan antingen välja en **Standard** -slutpunkt för direktuppspelning eller en **Premium** -slutpunkt för direktuppspelning. Mer information finns i avsnittet om [skalning](#scaling).

#### <a name="availability"></a>Tillgänglighet

|Name|Status|Datacenter
|---|---|---|
|Standard|Allmän tillgänglighet (GA)|Alla|
|Premium|Allmän tillgänglighet (GA)|Alla|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Tillgänglighet

Tillgängligt i alla data Center förutom: Tyskland, södra Brasilien, västra Indien, södra Indien och centrala Indien. 

### <a name="encoding-media-processors"></a>Mediebearbetare för kodning

AMS erbjuder två kodare på begäran: **Media Encoder Standard** och **Media Encoder Premium Workflow** . Mer information finns i [Overview and comparison of Azure on-demand media encoders](media-services-encode-asset.md) (Översikt och jämförelse av Azures mediekodare på begäran). 

#### <a name="availability"></a>Tillgänglighet

|Namn på mediebearbetare|Status|Datacenter
|---|---|---|
|Media Encoder Standard|Allmän tillgänglighet (GA)|Alla|
|Arbetsflöde för Media Encoder Premium|Allmän tillgänglighet (GA)|Alla utom Kina|

### <a name="analytics-media-processors"></a>Mediebearbetare för analys

Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](./legacy-components.md).

> [!NOTE]
> Vissa analys medie processorer kommer att dras tillbaka. Information om datum för indragning finns i avsnittet om [äldre komponenter](legacy-components.md) .

#### <a name="availability"></a>Tillgänglighet

|Namn på mediebearbetare|Status|Datacenter
|---|---|---|
|Azure Media Face Detector|Förhandsgranskning|Alla|
|Azure Media Indexer|Allmän tillgänglighet (GA)|Alla|
|Azure Media Motion Detector|Förhandsgranskning|Alla|
|Azure Media OCR|Förhandsgranskning|Alla|
|Azure Media Redactor|Allmän tillgänglighet (GA)|Alla|
|Azure Media Video Thumbnails|Förhandsgranskning|Alla|

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

Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: S1, S2 och S3.

Mer information finns i avsnittet om [skalning](#scaling).

#### <a name="availability"></a>Tillgänglighet

|Namn på RU-typ|Status|Datacenter
|---|---|---|
|S1|Allmän tillgänglighet (GA)|Alla|
|S2|Allmän tillgänglighet (GA)|Alla utom Brasilien, södra och Indien, västra|
|S3|Allmän tillgänglighet (GA)|Allt utom Indien, västra|

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]