---
title: Läs mer om kodare som rekommenderas av Azure Media Services | Microsoft-dokument
description: I den här artikeln visas lokala kodare som rekommenderas av Azure Media Services.
services: media-services
keywords: kodning;kodare;media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 89b01a3fb066f181f5ec54b481b71feaa7a6ae08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131407"
---
# <a name="recommended-on-premises-encoders"></a>Rekommenderade lokala kodare

När du direktuppspelar med Azure Media Services kan du ange hur du vill att kanalen ska ta emot indataströmmen. Om du väljer att använda en lokal kodare med en direktkodningskanal bör kodaren driva en högkvalitativ enbitrate-ström som utdata. Om du väljer att använda en lokal kodare med en passera kanal, bör kodaren driva en multibitrate ström som utdata med alla önskade utdatakvaliteter. Mer information finns i [Direktuppspelning med lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Krav på kodare

Kodare måste ha stöd för TLS 1.2 när https- eller RTMPS-protokoll används.

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som matar ut RTMP 

Azure Media Services rekommenderar att du använder någon av följande live-kodare som har RTMP som utdata:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (version 13.0.2 eller senare på grund av TLS 1.2-kravet)

  Kodare måste ha stöd för TLS 1.2 när RTMPS-protokoll används.
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live-kodare som matar ut fragmenterad MP4 

Azure Media Services rekommenderar att du använder någon av följande live-kodare som har flera bithastigheter fragmenterad-MP4 (Smooth Streaming) som utdata:

- Media Excel Hero Live och Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (version 2.14.15 och högre på grund av TLS 1.2-kravet)

  Kodare måste ha stöd för TLS 1.2 när HTTPS-protokoll används.
- Envivio 4Caster C4 Gen III
- Föreställ kommunikation Selenio MCP3

> [!NOTE]
> En live-kodare kan skicka en enda bitrate-ström till en passera genom kanal, men den här konfigurationen rekommenderas inte eftersom den inte tillåter adaptiv bithastighetsströmning till klienten.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hur man blir en lokal kodare partner

Som Azure Media Services på lokal kodarpartner marknadsför Media Services din produkt genom att rekommendera din kodare till företagskunder. Om du vill bli en lokal kodarpartner måste du verifiera kompatibiliteten för din lokala kodare med Media Services. För att göra detta, fyll i följande verifieringar:

Passera kanalverifiering
1. Skapa eller besöka ditt Azure Media Services-konto
2. Skapa och starta en **direktkanal**
3. Konfigurera kodaren så att den skickar en livestream med flera bitar.
4. Skapa en publicerad livehändelse
5. Kör din live-kodare i cirka 10 minuter
6. Stoppa livehändelsen
7. Skapa, starta en slutpunkt för direktuppspelning, använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på den arkiverade tillgången för att säkerställa att uppspelningen inte har några synliga buggar för alla kvalitetsnivåer (Eller alternativt titta på och validera via förhandsversionen av webbadressen under livesessionen före steg 6)
8. Registrera tillgångs-ID: et, publicerad strömningsadress för livearkivet och de inställningar och den version som används från din live-kodare
9. Återställ kanaltillståndet när du har skapat varje exempel
10. Upprepa steg 3 till 9 för alla konfigurationer som stöds av kodaren (med och utan annonssignalering/bildtexter/olika kodningshastigheter)

Verifiering av direkt kodningskanaler
1. Skapa eller besöka ditt Azure Media Services-konto
2. Skapa och starta en **direktkodningskanal**
3. Konfigurera kodaren så att en enda bithastighets direktuppspelning ska tryckas in.
4. Skapa en publicerad livehändelse
5. Kör din live-kodare i cirka 10 minuter
6. Stoppa livehändelsen
7. Skapa, starta en slutpunkt för direktuppspelning, använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på den arkiverade tillgången för att säkerställa att uppspelningen inte har några synliga buggar för alla kvalitetsnivåer (Eller alternativt titta på och validera via förhandsversionen av webbadressen under livesessionen före steg 6)
8. Registrera tillgångs-ID: et, publicerad strömningsadress för livearkivet och de inställningar och den version som används från din live-kodare
9. Återställ kanaltillståndet när du har skapat varje exempel
10. Upprepa steg 3 till 9 för alla konfigurationer som stöds av kodaren (med och utan annonssignalering/bildtexter/olika kodningshastigheter)

Verifiering av livslängd
1. Skapa eller besöka ditt Azure Media Services-konto
2. Skapa och starta en **direktkanal**
3. Konfigurera kodaren så att den skickar en livestream med flera bitar.
4. Skapa en publicerad livehändelse
5. Kör din live-kodare i en vecka eller längre
6. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på livestreamingen då och då (eller arkiverad tillgång) för att säkerställa att uppspelningen inte har några synliga problem
7. Stoppa livehändelsen
8. Registrera tillgångs-ID: et, publicerad strömningsadress för livearkivet och de inställningar och den version som används från din live-kodare

Slutligen, skicka dina inspelade inställningar och live-arkivparametrar till Media Services genom att amsstreaming@microsoft.comskicka e-post . Vid mottagandet utför Media Services verifieringstester på proverna från din live-kodare. Du kan kontakta Media Services med frågor om denna process.
