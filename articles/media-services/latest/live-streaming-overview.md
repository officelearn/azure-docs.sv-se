---
title: Översikt över direktuppspelning med Azure Media Services v3 | Microsoft-dokument
description: Den här artikeln innehåller en översikt över livestreaming med Azure Media Services v3.
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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068026"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Livestreaming med Azure Media Services v3

Med Azure Media Services kan du leverera livehändelser till dina kunder i Azure-molnet. Om du vill streama dina livehändelser med Medietjänster behöver du följande:  

- En kamera som används för att fånga livehändelsen.<br/>För setup idéer, kolla in [Enkel och bärbar händelse video redskap setup]( https://link.medium.com/KNTtiN6IeT).

    Om du inte har tillgång till en kamera kan verktyg som [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) användas för att generera en live-feed från en videofil.
- En livevideokodare som konverterar signaler från en kamera (eller en annan enhet, till exempel en bärbar dator) till ett bidragsflöde som skickas till Media Services. Bidragsflödet kan innehålla signaler relaterade till reklam, till exempel SCTE-35-markörer.<br/>En lista över rekommenderade direktuppspelningskodare finns i [direktuppspelningskodare](recommended-on-premises-live-encoders.md). Kolla också in den här bloggen: [Live streaming produktion med OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenter i Media Services, som gör att du kan inta, förhandsgranska, paketera, spela in, kryptera och sända livehändelsen till dina kunder, eller till ett CDN för vidare distribution.

Den här artikeln ger en översikt och vägledning av livestreaming med Medietjänster och länkar till andra relevanta artiklar.
 
> [!NOTE]
> Du kan använda [Azure-portalen](https://portal.azure.com/) för att hantera v3 [Live Events,](live-events-outputs-concept.md)visa v3-tillgångar och få information om hur du använder API:er. [Assets](assets-concept.md) För alla andra hanteringsuppgifter (till exempel Transformeringar och jobb) använder [du REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

## <a name="dynamic-packaging"></a>Dynamisk förpackning

Med Media Services kan du dra nytta av [dynamiska paketering,](dynamic-packaging-overview.md)som låter dig förhandsgranska och sända dina livestreamar i [MPEG DASH-, HLS- och Smooth Streaming-format från bidragsflödet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) som skickas till tjänsten. Dina tittare kan spela upp livestreamen med alla HLS-, DASH- eller Smooth Streaming-kompatibla spelare. Du kan använda [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) i dina webb- eller mobilappar för att leverera din ström i något av dessa protokoll.

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Med dynamisk kryptering kan du dynamiskt kryptera ditt live- eller on-demand-innehåll med AES-128 eller något av de tre stora DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. Mer information finns i [Dynamisk kryptering](content-protection-overview.md).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="dynamic-manifest"></a>Dynamiskt manifest

Dynamisk filtrering används för att styra antalet spår, format, bithastigheter och presentationstidsfönster som skickas ut till spelarna. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typer av livehändelse

[Livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända videofeeds. En livehändelse kan ställas in på antingen en *genomströmning* (en lokal live-kodare skickar en multibitrate-ström) eller *livekodning* (en lokal live-kodare skickar en enda bitrate-ström). Mer information om livestreaming i Media Services v3 finns i [Live Events och Live Outputs](live-events-outputs-concept.md).

### <a name="pass-through"></a>Direkt

![direkt](./media/live-streaming/pass-through.svg)

När du använder **direktuppspelningshändelsen**förlitar du dig på din lokala live-kodare för att generera en videoström med flera bithastigheter och skicka den som bidragsfeed till Live Event (med RTMP eller indataprotokollet fragmenterad MP4). Live-händelsen överförs sedan via inkommande videoströmmar till den dynamiska paketeraren (Streaming Endpoint) utan ytterligare omkodning. En sådan livehändelse är optimerad för långvariga liveevenemang eller 24x365 linjär livestreaming. 

### <a name="live-encoding"></a>Live Encoding  

![livekodning](./media/live-streaming/live-encoding.svg)

När du använder molnkodning med Media Services konfigurerar du den lokala live-kodaren för att skicka en enda bithastighetsvideo som bidragsfeed (upp till 32 Mbps-aggregat) till Live Event (med RTMP eller fragmenterad MP4-indataprotokoll). Live Event omkodar den inkommande enda bitrate ström i [flera bitrate videoströmmar](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) med varierande upplösningar för att förbättra leveransen och gör den tillgänglig för leverans till uppspelningsenheter via branschstandard protokoll som MPEG-DASH, Apple HTTP Live Streaming (HLS) och Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Live transkription (förhandsvisning)

Live transkription är en funktion som du kan använda med livehändelser som antingen är genomströmning eller live-kodning. Mer information finns i [direkt transkription](live-transcription.md). När den här funktionen är aktiverad använder tjänsten funktionen [Tal-till-text](../../cognitive-services/speech-service/speech-to-text.md) i Cognitive Services för att transkribera de talade orden i det inkommande ljudet till text. Denna text görs sedan tillgänglig för leverans tillsammans med video och ljud i MPEG-DASH och HLS protokoll.

> [!NOTE]
> För närvarande är live transkription tillgänglig som en förhandsgranskningsfunktion i västra USA 2.

## <a name="live-streaming-workflow"></a>Arbetsflöde för direktuppspelning

För att förstå arbetsflödet för direktuppspelning i Media Services v3 måste du först granska och förstå följande begrepp: 

- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Livehändelser och liveresultat](live-events-outputs-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)

### <a name="general-steps"></a>Allmänna steg

1. Kontrollera att slutpunkten för **direktuppspelning** (Origin) körs i ditt Media Services-konto. 
2. Skapa en [livehändelse](live-events-outputs-concept.md). <br/>När du skapar händelsen kan du ange att den ska startas automatiskt. Du kan också starta händelsen när du är redo att börja streama.<br/> När automatisk start är inställt på true startas Live-händelsen direkt efter skapandet. Faktureringen startar så snart livehändelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta de inmatade webbadresserna och konfigurera den lokala kodaren så att den använder URL:en för att skicka bidragsflödet.<br/>Se [rekommenderade live-kodare](recommended-on-premises-live-encoders.md).
4. Hämta förhandsgransknings-URL:en och använd den för att kontrollera att indata från kodaren verkligen tas emot.
5. Skapa ett nytt **tillgångsobjekt.** 

    Varje Live-utdata är associerad med en tillgång som används för att spela in videon i den associerade Azure blob-lagringsbehållaren. 
6. Skapa en **Live Output** och använd det tillgångsnamn som du skapade så att strömmen kan arkiveras i tillgången.

    Liveutdata startar när de skapas och avbryts när de tas bort. När du tar bort Live Output tar du inte bort den underliggande tillgången och innehållet i tillgången.
7. Skapa en **streamingpositionerare** med de [inbyggda typerna för direktuppspelningsprincip](streaming-policy-concept.md).

    Om du vill publicera liveutdata måste du skapa en direktuppsökare för den associerade tillgången. 
8. Lista sökvägarna i **streamingpositioneraren** för att få tillbaka webbadresserna som ska användas (dessa är deterministiska).
9. Hämta värdnamnet för den **strömmande slutpunkt** (Origin) som du vill strömma från.
10. Kombinera webbadressen från steg 8 med värdnamnet i steg 9 för att få den fullständiga webbadressen.
11. Om du vill sluta göra din **Live Event** synlig måste du sluta streama händelsen och ta bort **streamingpositioneraren**.
12. Följ stegen nedan om du är klar med strömningen av händelser och vill rensa de resurser som etablerades tidigare.

    * Stoppa sändningen av dataströmmen från kodaren.
    * Stoppa livehändelsen. När livehändelsen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
    * Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. Om livehändelsen är i stoppat tillstånd medför den inga avgifter.

Tillgången som liveutdatan arkiverar till blir automatiskt en tillgång på begäran när live-utdata tas bort. Du måste ta bort alla liveutdata innan en livehändelse kan stoppas. Du kan använda en valfri flagga [som tar bortOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) för att automatiskt ta bort liveutgångar vid stopp. 

> [!TIP]
> Se [Live streaming tutorial](stream-live-tutorial-with-api.md), artikeln undersöker koden som implementerar stegen som beskrivs ovan.

## <a name="other-important-articles"></a>Andra viktiga artiklar

- [Rekommenderade livekodare](recommended-on-premises-live-encoders.md)
- [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
- [Jämförelse av funktionen Live Event-typer](live-event-types-comparison.md)
- [Tillstånd och fakturering](live-event-states-billing.md)
- [Svarstid](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Se artikeln Vanliga frågor och [svar.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för livestreaming] (live-events-wirecast-quickstart.md(
* [Självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)
* [Migreringsvägledning för flyttning från Media Services v2 till v3](migrate-from-v2-to-v3.md)
