---
title: Azure Media Services terminologi och koncept – Azure | Microsoft Docs
description: Det här avsnittet innehåller en kort översikt över Azure Media Services terminologi och begrepp och innehåller länkar till mer information.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910301"
---
# <a name="media-services-concepts"></a>Media Services begrepp

Det här avsnittet ger en kort översikt över Azure Media Services terminologi och begrepp. Artikeln innehåller också länkar till artiklar med djupgående förklaring av Media Services v3-koncept och-funktioner. 

De grundläggande begrepp som beskrivs i de här ämnena bör granskas innan utveckling påbörjas.

> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminologi

Det här avsnittet visar hur några vanliga bransch villkor mappar till Media Services v3 API.

### <a name="live-event"></a>Direktsändning

En **Live-händelse** representerar en pipeline för inmatning, kodning (valfritt) och paketering av direktsända video-, ljud-och real tids metadata.

För kunder som migrerar från Media Services v2-API: er ersätter **Live-händelsen** **kanal** enheten i v2. Mer information finns i [Migrera från v2 till v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Slut punkt för direkt uppspelning (paketering och ursprung)

En **slut punkt för direkt uppspelning** representerar en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och på begäran-innehåll direkt till ett klients pelar program med hjälp av ett av de vanliga protokollen för strömmande media (HLS eller tank streck). Dessutom tillhandahåller **direkt uppspelnings slut punkten** dynamisk kryptering (just-in-Time) för att branschledande DRM: er.

I medie direkt uppspelnings branschen kallas den här tjänsten ofta en **paketerare** eller ett **ursprung**.  Andra vanliga termer i branschen för den här funktionen är JITP (just-in-Time-packageer) eller JITE (just-in-Time-Encryption). 
 
## <a name="cloud-upload-and-storage"></a>Uppladdning till och lagring i molnet

För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services konto och ladda upp dina digitala filer till **till gångar**.

- [Uppladdning till och lagring i molnet](storage-account-concept.md)
- [Till gångs koncept](assets-concept.md)

## <a name="encoding"></a>Kodning

När du har överfört digitala mediefiler med hög kvalitet till till gångar kan du koda dem till-format som kan spelas upp i en mängd olika webbläsare och enheter. 

Om du vill koda med Media Services v3 måste du skapa **transformeringar** och **jobb**.

![Transformering](./media/encoding/transforms-jobs.png)

- [Transformeringar och jobb](transforms-jobs-concept.md)
- [Kodning med Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Mediaanalys

Om du vill analysera dina video-och ljudfiler måste du också skapa **transformeringar** och **jobb**.

- [Analysera video-och ljudfiler](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Paketering, leverans, skydd

När ditt innehåll är kodat kan du dra nytta av **dynamisk paketering**. I Media Services är en **strömnings slut punkt**/Origin den dynamiska packnings tjänsten som används för att leverera medie innehåll till klient spelarna. Om du vill göra videor i utmatnings till gången tillgängliga för klienter för uppspelning, måste du skapa en **strömmande lokaliserare** och sedan skapa strömmande URL: er. 

När du skapar **streaming Locator**, förutom till gångens namn, måste du ange en **strömmande princip**. Med **direkt uppspelnings principer** kan du definiera strömmande protokoll och krypterings alternativ (om det finns några) för dina **strömmande positionerare**.

Dynamisk paketering används oavsett om du strömmar ditt innehåll Live eller på begäran. I följande diagram visas strömning på begäran med det dynamiska arbets flödet för paketering.

![Dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller/och någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.

Om du anger krypterings alternativ för data strömmen skapar du **innehålls nyckel principen** och associerar den med din **strömmande positionerare**. Med **princip för innehålls nyckel** kan du konfigurera hur innehålls nyckeln levereras till slut klienter.

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda innehåll](./media/content-protection/content-protection.svg)

&#42;dynamisk kryptering stöder AES-128 "Clear Key", CBCS och CENC. 

Du kan använda Media Services **dynamiska manifest** för att endast strömma en speciell åter givning eller under klipp av videon. I följande exempel användes en kodare för att koda en mezzaninfil till sju ISO hastigheter video åter givningar (från 180p till 1080p). Kodad till gång kan paketeras dynamiskt i något av följande strömnings protokoll: HLS, MPEG-streck och smidighet.  Överst i diagrammet visas HLS-manifestet för till gången utan filter (det innehåller alla sju åter givningar).  I det nedre vänstra hörnet visas HLS-manifestet som ett filter med namnet "ott" användes för. Filtret "ott" anger att alla bit hastigheter ska tas bort under 1 Mbit/s, vilket resulterade i att de nedre två kvalitets nivåerna avbröts i svaret. Längst ned till höger visas HLS-manifestet som ett filter med namnet "mobil" har tillämpats på. Filtret "mobil" anger att du vill ta bort renderingar där upplösningen är större än 720p, vilket ledde till att de två 1080p-återgivningarna har tagits bort.

![Åter givnings filtrering](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamisk paketering](dynamic-packaging-overview.md)
- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)
- [Principer för direktuppspelning](streaming-policy-concept.md)
- [Principer för innehållsnycklar](content-key-policy-concept.md)
- [Innehålls skydd](content-protection-overview.md)
- [Dynamiska manifest](filters-dynamic-manifest-overview.md)
- [Filter](filters-concept.md)

## <a name="live-streaming"></a>Liveuppspelning

Med Azure Media Services kan du leverera Live-händelser till dina kunder i Azure-molnet. **Livehändelser** ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en **Live-händelse**skapas en ingångs slut punkt som du kan använda för att skicka en Live-signal från en fjär kodare. När strömmen flödar in i Live- **evenemanget**kan du starta den strömmande händelsen genom att skapa en **till gång**, en **Live-utgång**och en **strömmande positionerare**. **Live-utdata** kommer att arkivera strömmen till **till gången** och göra den tillgänglig för användare via **slut punkten för direkt uppspelning**. En **Live-händelse** kan vara en av två typer: direkt kodning och **Live** **-** kodning.

Följande bild illustrerar arbets flödet genom strömnings typ:

![direkt](./media/live-streaming/pass-through.svg)

- [Översikt över direkt uppspelning](live-streaming-overview.md)
- [Livehändelser och liveutdata](live-events-outputs-concept.md)

## <a name="monitoring"></a>Övervakning

### <a name="event-grid"></a>Event Grid

Om du vill se förloppet för jobbet bör du använda **Event Grid**. Media Services utskriver också direkt händelse typer. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. 

- [Hantera Event Grid händelser](reacting-to-media-services-events.md)
- [Scheman](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program presterar med Azure Monitor.

- [Mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)
- [Diagnostiska loggar scheman](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Player-klienter

Du kan använda Azure Media Player för att spela upp medie innehåll som strömmas av Media Services på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att ge en omfattande anpassningsbar strömmande upplevelse. 

- [Azure Media Player-översikt](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Koda fjärrfil och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda uppladdad fil och strömma video – .NET](stream-files-tutorial-with-api.md)
* [Strömma live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamisk kryptering – .NET](protect-with-aes128.md)
* [Kryptera dynamiskt med multi-DRM-.NET](protect-with-drm.md) 
