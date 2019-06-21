---
title: Översikt över direktsänd strömning med Azure Media Services v3 | Microsoft Docs
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
ms.date: 06/16/2019
ms.author: juliako
ms.openlocfilehash: 0abc3eec380cccae2672d0e9aa4a3a4c7199362f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295668"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Liveuppspelning med Azure Media Services v3

Azure Media Services kan du leverera händelser till dina kunder på Azure-molnet. För att strömma din direktsända händelser med Media Services behöver du följande:  

- En kamera som används för att hämta live-händelse.<br/>Tips för installationen kan ta en titt [enkel och portabel video gear Händelseinställningar]( https://link.medium.com/KNTtiN6IeT).

    Om du inte har åtkomst till en kamera verktyg som [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) kan användas för Generera en direktsändningen från en videofil.
- En live-videokodare som konverterar signaler från en kamera (eller en annan enhet, till exempel en bärbar dator) till ett bidrag feed som skickas till Media Services. Bidrag feed kan innehålla signaler som rör reklam, till exempel SCTE 35 markörer.<br/>En lista över rekommenderade livekodare för direktuppspelning finns i [liveuppspelning kodare](recommended-on-premises-live-encoders.md). Kolla dessutom in den här bloggen: [Live direktuppspelning produktion med OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenterna i Media Services, som gör det möjligt att mata in, förhandsgranska, paket, registrera, kryptera och sända live-händelse till dina kunder eller till ett nätverk för Innehållsleverans för vidare distribution.

Den här artikeln ger en översikt och riktlinjerna för direktsänd strömning med Media Services och länkar till andra relevanta artiklar.
 
> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dynamisk paketering

Med medietjänster kan du dra nytta av [dynamisk paketering](dynamic-packaging-overview.md), där du kan förhandsgranska och sända direktsändningar i [format som MPEG DASH, HLS och Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) från bidraget feed som skickas till tjänsten. Användarna kan spela upp den direktsända dataströmmen med valfri kompatibel spelare HLS, DASH eller Smooth Streaming. Du kan använda [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) i dina webb- och mobilprogram att leverera din dataström i någon av de här protokollen.

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Dynamisk kryptering kan du dynamiskt kryptera innehållet live eller på begäran med AES-128 eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. Mer information finns i [dynamisk kryptering](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dynamic Manifest

Dynamisk filtrering används för att styra antalet spår, format, olika bithastigheter och presentation tidsfönster som skickas till spelarna. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Live händelsetyper

[Livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända videofeeds. En direktsänd händelse kan vara något av två typer: direkt och live encoding. Mer information om liveströmning i Media Services v3 finns [Live-händelser och Live utdata](live-events-outputs-concept.md).

### <a name="pass-through"></a>Direkt

![direkt](./media/live-streaming/pass-through.svg)

När du använder direkt **direktsänd händelse**, du förlita dig på din lokala livekodare för att generera en videoström för flera bithastigheter och skicka att som bidraget feed på Live-händelsen (med RTMP eller fragmenterad MP4 indataprotokollet). Live-händelsen har sedan via inkommande videon upp direkt till dynamisk Paketeraren (slutpunkt för direktuppspelning) utan någon ytterligare transkodning. Sådana en direkt direktsänd händelse är optimerad för tidskrävande direktsändningar eller 24 x 365 linjär liveuppspelning. 

### <a name="live-encoding"></a>Live Encoding  

![livekodning](./media/live-streaming/live-encoding.svg)

När du använder molnet encoding med Media Services kan du konfigurera din lokala livekodare för att skicka en enkel bithastighet video som bidraget feed (upp till 32Mbps sammanställd) på Live-händelsen (med RTMP eller fragmenterad MP4 indataprotokollet). Direktsänd händelse omkodar den inkommande, enkel bithastigheten, strömma till [flera bithastigheter videoflöden](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) på olika lösningar för att förbättra leverans och gör dem tillgängliga för leverans till uppspelningsenheter via branschens standardprotokoll t.ex. MPEG-DASH, Apple HTTP Live Streaming (HLS) och Microsoft Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Live-arbetsflöde

För att förstå live direktuppspelning arbetsflödet i Media Services v3, du måste först läsa och förstå följande begrepp: 

- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Livehändelser och liveutdata](live-events-outputs-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)

### <a name="general-steps"></a>Allmänna steg

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** (ursprungliga) körs. 
2. Skapa en [direktsändning](live-events-outputs-concept.md). <br/>När du skapar händelsen, kan du ange att autostart den. Du kan också starta händelsen när du är redo att börja direktuppspelning.<br/> När autostart är satt till SANT, Live-händelsen kommer att startas rätt när du har skapat. Faktureringen startar när Live-händelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera din lokala kodare för att använda Webbadressen för att skicka bidraget feed.<br/>Se [rekommenderas livekodare](recommended-on-premises-live-encoders.md).
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.<br/>Den **Live utdata** arkiverar dataströmmen till den **tillgången**.
7. Skapa en **Strömningspositionerare** med den [inbyggda Streaming principtyper](streaming-policy-concept.md)
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda (dessa är deterministisk).
9. Hämta värdnamnet för den **Strömningsslutpunkt** (ursprungliga) som du vill strömma från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Om du vill stoppa att göra din **direktsänd händelse** kan visas, måste du stoppa strömning händelse och ta bort den **Strömningspositionerare**.

## <a name="other-important-articles"></a>Andra viktiga artiklar

- [Rekommenderade livekodare](recommended-on-premises-live-encoders.md)
- [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
- [Live händelsetyper funktionsjämförelse](live-event-types-comparison.md)
- [Tillstånd och fakturering](live-event-states-billing.md)
- [Svarstid](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
* [Riktlinjer för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md)
