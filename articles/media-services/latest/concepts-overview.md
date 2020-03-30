---
title: Terminologi och begrepp för Media Services
titleSuffix: Azure Media Services
description: Läs mer om terminologi och begrepp för Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500088"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologi och begrepp för Media Services

Det här avsnittet ger en kort översikt över Azure Media Services terminologi och begrepp. Artikeln innehåller också länkar till artiklar med en djupgående förklaring av Media Services v3 begrepp och funktionalitet.

De grundläggande begrepp som beskrivs i dessa ämnen bör ses över innan utvecklingen påbörjas.

> [!NOTE]
> För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att: hantera Media Services v3 [Live Events](live-events-outputs-concept.md), visa (inte hantera) [v3-tillgångar](assets-concept.md)och [få information om hur du använder API:er](access-api-portal.md).
> För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) and [Content protection)](content-protection-overview.md)använder du [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 terminologi

|Period|Beskrivning|
|---|---|
|Live-evenemang|En **Live Event** representerar en pipeline för intag, omkodning (eventuellt) och paketering av livestreamar av video-, ljud- och realtidsmetadata.<br/><br/>För kunder som migrerar från Api:er för Media Services v2 ersätter **Live-händelsen** **kanalentiteten** i v2. Mer information finns i [Migrera från v2 till v3](migrate-from-v2-to-v3.md).|
|Slutpunkt för direktuppspelning/förpackning/ursprung|En slutpunkt för **direktuppspelning** representerar en dynamisk (just-in-time)-förpackning och ursprungstjänst som kan leverera ditt live- och on-demand-innehåll direkt till ett klientspelarprogram. Den använder ett av de vanliga protokollen för direktuppspelningsmedia (HLS eller DASH). Dessutom ger **slutpunkten för direktuppspelning** dynamisk (just-in-time) kryptering till branschledande DRM-system (Digital Rights Management Systems).<br/><br/>I mediestreamingindustrin kallas den här tjänsten ofta för en **paketerare** eller **Origin**.  Andra vanliga termer i branschen för den här funktionen är JITP (just-in-time-packager) eller JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>Media Services v3-begrepp

|Begrepp|Beskrivning|Länkar|
|---|---|---|
|Tillgångar och ladda upp innehåll|Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto och ladda upp dina digitala filer till **Tillgångar**.|[Uppladdning till och lagring i molnet](storage-account-concept.md)<br/><br/>[Tillgångar koncept](assets-concept.md)|
|Koda innehåll|När du har laddat upp dina digitala mediefiler av hög kvalitet till tillgångar kan du koda dem till format som kan spelas upp på en mängd olika webbläsare och enheter. <br/><br/>Om du vill koda med Media Services v3 måste du skapa **transformeringar** och **jobb**.|[Transformeringar och jobb](transforms-jobs-concept.md)<br/><br/>[Kodning med Medietjänster](encoding-concept.md)|
|Analysera innehåll (Video Indexer)|Med Media Services v3 kan du extrahera insikter från dina video- och ljudfiler med hjälp av Media Services v3-förinställningar. Om du vill analysera innehållet med hjälp av mediatjänst v3-förinställningar måste du skapa **transformeringar** och **jobb**.<br/><br/>Om du vill ha mer detaljerade insikter använder du [Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) direkt.|[Analysera video- och ljudfiler](analyzing-video-audio-files-concept.md)|
|Paketering och leverans|När ditt innehåll har kodats kan du dra nytta av **dynamisk förpackning**. I Media Services är en slutpunkt för **direktuppspelning** den dynamiska förpackningstjänst som används för att leverera medieinnehåll till klientspelare. Om du vill göra videor i utdatatillgången tillgängliga för klienter för uppspelning måste du skapa en **streamingpositionerare** och sedan skapa strömmande webbadresser. <br/><br/>När du skapar **streamingpositioneraren**måste du, utöver tillgångens namn, ange **direktuppspelningsprincip**. **Med streamingprinciper** kan du definiera direktuppspelade protokoll och krypteringsalternativ (om sådana finns) för dina **streamingpositionerare**. Dynamisk förpackning används oavsett om du streamar ditt innehåll live eller på begäran. <br/><br/>Du kan använda **dynamiska manifest för** Media Services för att bara strömma en viss återgivning eller underklipp i videon.|[Dynamisk paketering](dynamic-packaging-overview.md)<br/><br/>[Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)<br/><br/>[Positionerare för direktuppspelning](streaming-locators-concept.md)<br/><br/>[Principer för direktuppspelning](streaming-policy-concept.md)<br/><br/>[Dynamiska manifest](filters-dynamic-manifest-overview.md)<br/><br/>[Filter](filters-concept.md)|
|Innehållsskydd|Med Media Services kan du leverera ditt live- och on-demand-innehåll krypterat dynamiskt med Advanced Encryption Standard (AES-128) eller/och något av de tre stora DRM-systemen: Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. <br/><br/>Om du anger krypteringsalternativ på flödet skapar du **innehållsnyckelprincipen** och associerar den med din **streamingpositionerare**. Med **innehållsnyckelprincipen** kan du konfigurera hur innehållsnyckeln levereras till slutklienter.<br/><br/> Försök att återanvända principer när samma alternativ behövs.| [Viktiga innehållsprinciper](content-key-policy-concept.md)<br/><br/>[Innehållsskydd](content-protection-overview.md)|
|Liveuppspelning|Med Media Services kan du leverera livehändelser till dina kunder i Azure-molnet. **Livehändelser** ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en **livehändelse**skapas en indataslutpunkt som du kan använda för att skicka en livesignal från en fjärrkodare. När du har strömmen flödar in i **Live Event**kan du börja strömma händelsen genom att skapa en **tillgång,** **Live Output**och **Streaming Locator**. **Live Output** arkiverar strömmen till **tillgången** och gör den tillgänglig för tittare via **slutpunkten för direktuppspelning**. En livehändelse kan ställas in på antingen en *genomströmning* (en lokal live-kodare skickar en multibitrate-ström) eller *livekodning* (en lokal live-kodare skickar en enda bitrate-ström). |[Översikt över livestreaming](live-streaming-overview.md)<br/><br/>[Livehändelser och liveresultat](live-events-outputs-concept.md)|
|Övervakning med händelserutnät|Använd **Händelserutnät**förloppet . Media Services avger också typerna för liveevenemang. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. |[Hantera Event Grid-händelser](reacting-to-media-services-events.md)<br/><br/>[Scheman](media-services-event-schemas.md)|
|Övervakning med Azure Monitor|Övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina appar fungerar med Azure Monitor.|[Mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)<br/><br/>[Scheman för diagnostikloggar](media-services-diagnostic-logs-schema.md)|
|Player-klienter|Du kan använda Azure Media Player för att spela upp medieinnehåll som strömmas av Media Services på en mängd olika webbläsare och enheter. Azure Media Player använder branschstandarder, till exempel HTML5, MSE (Media Source Extensions) och EME (Encrypted Media Extensions) för att ge en berikad adaptiv direktuppspelningsupplevelse. |[Azure Media Player-översikt](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

* [Koda fjärrfiler och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda uppladdade filer och strömma video – NET](stream-files-tutorial-with-api.md)
* [Strömma live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128, dynamisk kryptering – .NET](protect-with-aes128.md)
* [Kryptera dynamiskt med multi-DRM - .NET](protect-with-drm.md)
