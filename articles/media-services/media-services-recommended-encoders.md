---
title: "Lär dig mer om kodare som rekommenderas av Azure Media Services | Microsoft Docs"
description: "Lär dig mer om kodare som rekommenderas av media services"
services: media-services
keywords: kodning; kodare; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Rekommenderade lokala kodare
När direktsänd strömning med Azure Media Services kan du ange hur du vill att din kanal tar emot Indataströmmen. Om du väljer att använda en lokal kodare med en live encoding kanal din kodare ska push-installera en dataström med enkel bithastighet av hög kvalitet som utdata. Om du väljer att använda en lokal kodare med en sändning via kanalen din kodare ska push-installera en dataström med multibithastighet som utdata med alla önskade utgående egenskaper. Mer information finns i [direktsänd strömning med lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services rekommenderar att du använder ett av följande livekodare som har RTMP som utdata:
- Adobe Flash Media Livekodaren 3.2
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Teradek segment 756
- TriCaster 8000
- HD Tricaster Mini-4

Azure Media Services rekommenderar att du använder något av följande livekodare som har flera bithastigheter Smooth Streaming som utdata:
- Ateme TITAN Live
- Cisco digitala Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Anta att kommunikation Selenio MCP3
- Media Excel hjälte Live

> [!NOTE]
> En livekodare kan skicka en dataström med enkel bithastighet till en sändning via kanalen, men den här konfigurationen rekommenderas inte eftersom den inte tillåter att klienten strömning med anpassningsbar bithastighet.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Hur ska bli en partner för lokala kodare
Som ett Azure Media Services lokala kodare partner befordrar Media Services produkten av rekommenderar din kodare för enterprise-kunder. Om du vill bli en partner för lokala kodare, måste du kontrollera kompatibiliteten för din lokala kodare med Media Services. Gör du genom att utföra följande kontroller:

Passera kanal verifiering
1. Skapa eller besök Azure Media Services-konto
2. Skapa och starta en **direkt** kanal
3. Konfigurera din kodare för att vidarebefordra en direktsänd dataström med multibithastighet.
4. Skapa en publicerad direktsänd händelse
5. Kör ditt livekodaren i cirka 10 minuter
6. Stoppa direktsänd händelse
7. Registrera Tillgångsnummer, publicerade strömmande URL för live-Arkiv och inställningar och version som används från din livekodare
8. Återställa tillståndet kanalen när du har skapat varje prov
9. Upprepa steg 3 till 8 för alla konfigurationer som stöds av din kodare (med och utan ad-signalering/bildtexter/olika kodning hastigheter)

Live encoding kanal verifiering
1. Skapa eller besök Azure Media Services-konto
2. Skapa och starta en **live encoding** kanal
3. Konfigurera din kodare för att vidarebefordra en direktsänd dataström med enkel bithastighet.
4. Skapa en publicerad direktsänd händelse
5. Kör ditt livekodaren i cirka 10 minuter
6. Stoppa direktsänd händelse
7. Registrera Tillgångsnummer, publicerade strömmande URL för live-Arkiv och inställningar och version som används från din livekodare
8. Återställa tillståndet kanalen när du har skapat varje prov
9. Upprepa steg 3 till 8 för alla konfigurationer som stöds av din kodare (med och utan ad-signalering/bildtexter/olika kodning hastigheter)

Varaktigheten verifiering
1. Skapa eller besök Azure Media Services-konto
2. Skapa och starta en **direkt** kanal
3. Konfigurera din kodare för att vidarebefordra en direktsänd dataström med multibithastighet.
4. Skapa en publicerad direktsänd händelse
5. Kör ditt livekodare för en vecka eller längre
6. Stoppa direktsänd händelse
7. Registrera Tillgångsnummer, publicerade strömmande URL för live-Arkiv och inställningar och version som används från din livekodare

Slutligen skicka dina inspelade inställningar och live Arkiv parametrar till Media Services genom att skicka e-post amsstreaming@microsoft.com. Mottagit utför Media Services verifieringstester på prover från din direktsända kodaren. Du kan kontakta Media Services med frågor om den här processen.