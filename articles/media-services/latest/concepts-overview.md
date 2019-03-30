---
title: Azure Media Services-koncepten – Azure | Microsoft Docs
description: Det här avsnittet ger en kort översikt över Azure Media Services-begrepp och innehåller länkar för information.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8f21374ae5da76c9954acc5227f593ab4be19ce9
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630554"
---
# <a name="media-services-concepts"></a>Media Services-koncepten

Det här avsnittet ger en kort översikt över Azure Media Services-begrepp och innehåller länkar till artiklar med detaljerad förklaring av Media Services v3 begrepp och funktioner. De grundläggande begrepp som beskrivs i de här ämnena bör granskas innan utveckling påbörjas.

> [!NOTE]
> För närvarande kan använda du inte Azure-portalen för att hantera v3-resurser. Använd den [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), eller någon av stöds [SDK: er](developers-guide.md).

## <a name="cloud-upload-and-storage"></a>Uppladdning till och lagring i molnet

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto och ladda upp dina digitala filer till **tillgångar**.

- [Uppladdning till och lagring i molnet](storage-account-concept.md)
- [Koncept för tillgångar](assets-concept.md)

## <a name="encoding"></a>Kodning

När du överför din digitala mediefiler med hög kvalitet till tillgångar, kan du koda dem till format som kan spelas upp på en mängd olika webbläsare och enheter. 

Om du vill koda med Media Services v3, måste du skapa **omvandlar** och **jobb**.

![Transformering](./media/encoding/transforms-jobs.png)

- [Transformeringar och jobb](transforms-jobs-concept.md)
- [Encoding med Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Medieanalys

För att analysera din video och ljud filer kan du även behöva skapa **omvandlar** och **jobb**.

- [Analysera video-och ljudfiler](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Paketering, leverans, skydd

När ditt innehåll är kodat, kan du dra nytta av **dynamisk paketering**. **Slutpunkt för direktuppspelning** är tjänsten dynamisk paketering i Media Services som används för att leverera medieinnehåll till klienten spelare. Om du vill göra videor i utdatatillgången tillgängliga för klienter för uppspelning, måste du skapa en **Strömningspositionerare** och sedan skapa strömmande URL: er. 

När du skapar den **Strömningspositionerare**, förutom Tillgångsnamn, måste du ange **Streaming princip**. **Principer för direktuppspelning** kan du definiera strömningsprotokoll och kryptering alternativ (om sådan finns) för din **positionerare för direktuppspelning**.

Dynamisk paketering används om du strömma ditt innehåll live eller på begäran. Följande diagram visar strömning på begäran med dynamisk paketering arbetsflöde.

![Dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Med medietjänster kan du leverera live och på begäran innehåll dynamiskt krypterad med Advanced Encryption Standard (AES-128) eller / och några av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.

Om att ange alternativ för kryptering på din stream kan du skapa den **innehåll nyckel princip** och associera det med dina **Strömningspositionerare**. Den **innehåll nyckel princip** kan du konfigurera hur innehållsnyckeln levereras om du vill avsluta klienter.

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda innehåll](./media/content-protection/content-protection.svg)

&#42;dynamisk kryptering har stöd för AES-128 ”clear key” och CBCS CENC. 

Du kan använda Media Services **dynamiska manifest** att strömma endast en specifik återgivning eller underklipp av videon. I följande exempel har en kodare använts för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p 1080p). Den kodade tillgången kan dynamiskt paketeras i någon av de följande protokollen med direktuppspelning: HLS, MPEG DASH och Smooth.  Längst ned i diagrammet visas HLS-manifest för tillgången utan filter (den innehåller alla sju återgivningar).  HLS-manifest som ett filter med namnet ”ott” användes visas i nedre vänstra hörnet. Filtret ”ott” anger för att ta bort alla bithastigheter för utdata nedan 1 Mbit/s, vilket resulterade i kvalitetsnivå längst ned två tas bort i svaret. I nederkant högra visas HLS manifestet som ett filter med namnet ”mobil” användes. Filtret ”mobil” anger för att ta bort återgivningar där den är större än 720p, vilket resulterade i två 1080p återgivningar tas bort.

![Återgivningsfiltrering](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamisk paketering](dynamic-packaging-overview.md)
- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)
- [Principer för direktuppspelning](streaming-policy-concept.md)
- [Principer för innehållsnycklar](content-key-policy-concept.md)
- [Innehållsskydd](content-protection-overview.md)
- [Dynamiska manifest](filters-dynamic-manifest-overview.md)
- [Filter](filters-concept.md)

## <a name="live-streaming"></a>Liveuppspelning

Azure Media Services kan du leverera händelser till dina kunder på Azure-molnet. **Livehändelser** ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en **direktsänd händelse**, skapas en slutpunkt för indata som du kan använda för att skicka en direktsänd signal från en fjärransluten kodare. När du har dataströmmen väl flödar till den **direktsänd händelse**, kan du påbörja strömningshändelsen genom att skapa en **tillgången**, **Live utdata**, och **Strömningspositionerare** . **Live utdata** arkiverar dataströmmen till den **tillgången** och gör den tillgänglig för visning via den **Strömningsslutpunkt**. En **direktsänd händelse** kan vara något av två typer: **direktautentisering** och **direktsänd kodning**.

Följande bild illustrerar arbetsflödet direkt typ:

![direktautentisering](./media/live-streaming/pass-through.svg)

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Livehändelser och liveutdata](live-events-outputs-concept.md)

## <a name="monitoring"></a>Övervakning

### <a name="event-grid"></a>Event Grid

Om du vill se förloppet för jobbet, bör du använda **Event Grid**. Media Services genererar även Live-händelse-typer. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. 

- [Hantera Event Grid-händelser](reacting-to-media-services-events.md)
- [Scheman](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program fungerar med Azure Monitor.

- [Mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)
- [Scheman för diagnostikloggar](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Player-klienter

Du kan använda Azure Media Player för att spela upp mediainnehåll strömmas av Media Services på en mängd olika webbläsare och enheter. Azure Media Player används branschstandarder som HTML5, Media käll-tillägg (MSE) och tillägg EME (Encrypted Media) för att ge en avancerad och anpassningsbar direktuppspelning. 

- [Azure Media Player-översikt](use-azure-media-player.md)

## <a name="next-steps"></a>Nästa steg

* [Koda fjärrfil och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda överförda filen och strömma video – .NET](stream-files-tutorial-with-api.md)
* [Stream live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamisk kryptering – .NET](protect-with-aes128.md)
* [Kryptera dynamiskt med multi-DRM - .NET](protect-with-drm.md) 
