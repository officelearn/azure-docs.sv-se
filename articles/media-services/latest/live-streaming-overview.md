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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: 0f63896239615699db63c9f48be6b291208408db
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247179"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Liveuppspelning med Azure Media Services v3

Azure Media Services kan du leverera händelser till dina kunder på Azure-molnet. För att strömma din direktsända händelser med Media Services behöver du följande:  

- En kamera som används för att hämta live-händelse.<br/>Tips för installationen kan ta en titt [enkel och portabel video gear Händelseinställningar]( https://link.medium.com/KNTtiN6IeT).
- En live-videokodare som konverterar signaler från en kamera (eller en annan enhet, till exempel en bärbar dator) till ett bidrag feed som skickas till Media Services. Bidrag feed kan innehålla signaler som rör reklam, till exempel SCTE 35 markörer.<br/>En lista över rekommenderade livekodare för direktuppspelning finns i [liveuppspelning kodare](recommended-on-premises-live-encoders.md). Kolla dessutom in den här bloggen: [Live direktuppspelning produktion med OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenterna i Media Services, som gör det möjligt att mata in, förhandsgranska, paket, registrera, kryptera och sända live-händelse till dina kunder eller till ett nätverk för Innehållsleverans för vidare distribution.

Med medietjänster kan du dra nytta av **dynamisk paketering**, där du kan förhandsgranska och sända direktsändningar i [format som MPEG DASH, HLS och Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) från bidraget feed att skicka till tjänsten. Användarna kan spela upp den direktsända dataströmmen med valfri kompatibel spelare HLS, DASH eller Smooth Streaming. Du kan använda [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) i dina webb- och mobilprogram att leverera din dataström i någon av de här protokollen.

Media Services kan du leverera ditt innehåll dynamiskt krypterad (**dynamisk kryptering**) med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser till auktoriserade klienter. Mer information om hur du kryptera ditt innehåll med Media Services finns i [skyddar innehåll – översikt](content-protection-overview.md)

Du kan också använda dynamisk filtrering, som kan användas för att styra antalet spår, format, bithastigheter för utdata, och presentation tidsfönster som skickas till spelarna. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).

Den här artikeln ger en översikt och riktlinjerna för direktsänd strömning med Media Services.

## <a name="prerequisites"></a>Förutsättningar

För att förstå live direktuppspelning arbetsflödet i Media Services v3, som du behöver och förstå följande begrepp: 

- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Händelser och Live utdata](live-events-outputs-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)

## <a name="live-streaming-workflow"></a>Live-arbetsflöde

Här följer stegen för en live arbetsflöde:

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** körs. 
2. Skapa en [direktsändning](live-events-outputs-concept.md). <br/>När du skapar händelsen, kan du ange att autostart den. Du kan också starta händelsen när du är redo att börja direktuppspelning.<br/> När autostart är satt till SANT, Live-händelsen kommer att startas rätt när du har skapat. Faktureringen startar när Live-händelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera din lokala kodare för att använda Webbadressen för att skicka bidraget feed.<br/>Se [rekommenderas livekodare](recommended-on-premises-live-encoders.md).
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.<br/>Den **Live utdata** arkiverar dataströmmen till den **tillgången**.
7. Skapa en **Strömningspositionerare** med inbyggt **Streaming princip** typer.<br/>Om du vill kryptera ditt innehåll, granska [Content protection översikt](content-protection-overview.md).
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda (dessa är deterministisk).
9. Hämta värdnamnet för den **Strömningsslutpunkt** du vill strömma från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Om du vill stoppa att göra din **direktsänd händelse** kan visas, måste du stoppa strömning händelse och ta bort den **Strömningspositionerare**.

## <a name="other-important-articles"></a>Andra viktiga artiklar

- [Rekommenderade livekodare](recommended-on-premises-live-encoders.md)
- [Med hjälp av ett moln-DVR](live-event-cloud-dvr.md)
- [Live händelsetyper funktionsjämförelse](live-event-types-comparison.md)
- [Tillstånd och fakturering](live-event-states-billing.md)
- [Svarstid](live-event-latency.md)

## <a name="next-steps"></a>Nästa steg

* [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
* [Riktlinjer för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md)
