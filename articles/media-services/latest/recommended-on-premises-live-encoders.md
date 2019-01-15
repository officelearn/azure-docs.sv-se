---
title: Lär dig mer om live direktuppspelning lokala kodare som rekommenderas av Media Services – Azure | Microsoft Docs
description: Lär dig mer om live direktuppspelning lokala kodare som rekommenderas av Media Services
services: media-services
keywords: kodning; kodare; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4d25e4cd94fec35f31594544b619aa054a35d58d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302348"
---
# <a name="recommended-live-streaming-encoders"></a>Rekommenderade livekodare för direktuppspelning

I Media Services, en [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) representerar en pipeline för bearbetning av live-streaming-innehåll. En LiveEvent tar emot inkommande direktsändningar i ett av två sätt:

* En lokal livekodare skickar en med flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) strömma till LiveEvent som inte är aktiverad att utföra live encoding med Media Services. Infogade strömmarna passerar genom LiveEvents utan vidare bearbetning. Den här metoden anropas **direkt**. En livekodare kan skicka en dataström med enkel bithastighet till släppa igenom kanal, men den här konfigurationen rekommenderas inte eftersom det inte tillåter för strömning med anpassad bithastighet till klienten.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning.

* En lokal livekodare skickar en dataström med enkel bithastighet till LiveEvent som är aktiverade för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). LiveEvent utför sedan live encoding av den inkommande dataströmmen med enkel bithastighet till en med flera bithastigheter (anpassningsbar) videoström.

Detaljerad information om live encoding med Media Services finns i [direktsänd strömning med Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Livekodare som RTMP

Media Services rekommenderar att du använder något av följande livekodare som har RTMP som utdata. URL-scheman som stöds är `rtmp://` eller `rtmps://`.

> [!NOTE]
 > När strömmande via RTMP, kontrollerar du inställningarna för brandväggen och/eller proxyn för att bekräfta att utgående TCP-portarna 1935 och 1936 är öppna.<br/>
 När strömmande via RTMPS, kontrollerar du inställningarna för brandväggen och/eller proxyn för att bekräfta att utgående TCP-portarna 2935 och 2936 är öppna.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X – HEVC
- OBS Studio
- Mellan aktiviteter Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Livekodare som fragmenterad MP4

Media Services rekommenderar att du använder något av följande livekodare som har flera bithastigheter Smooth Streaming (fragmenterad MP4) som utdata. URL-scheman som stöds är `http://` eller `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Excel-Media Hero Live och Hero 4K (UHD / – HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hur du blir partner lokala kodare

Som ett Azure Media Services lokala kodare partner främjar medietjänster din produkt genom att rekommendera kodaren för företagskunder. Om du vill bli lokala kodare-partner måste du kontrollera kompatibiliteten för din lokala kodare med Media Services. Slutför följande verifieringar gör du:

### <a name="pass-through-liveevent-verification"></a>Direkt LiveEvent verifiering

1. Skapa eller gå till Azure Media Services-kontot.
2. Skapa och starta en **direkt** LiveEvent.
3. Konfigurera kodaren för att skicka en direktsänd dataström i multibithastighet.
4. Skapa en publicerade live-händelse.
5. Kör din live-kodare i cirka 10 minuter.
6. Stoppa direktsändningen.
7. Skapa, starta slutpunkten för direktuppspelning, Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller du kan också se och validera via förhandsgransknings-URL under den direktsända sessionen innan steg 6).
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
9. Återställ LiveEvent tillståndet när du har skapat varje exempel.
10. Upprepa steg 3 till och med 9 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning).

### <a name="live-encoding-liveevent-verification"></a>Direktsänd kodning LiveEvent verifiering

1. Skapa eller gå till Azure Media Services-kontot.
2. Skapa och starta en **direktsänd kodning** LiveEvent.
3. Konfigurera kodaren för att skicka en direktsänd dataström med enkel bithastighet.
4. Skapa en publicerade live-händelse.
5. Kör din live-kodare i cirka 10 minuter.
6. Stoppa direktsändningen.
7. Skapa, starta slutpunkten för direktuppspelning, Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller du kan också se och validera via förhandsgransknings-URL under den direktsända sessionen innan steg 6).
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
9. Återställ LiveEvent tillståndet när du har skapat varje exempel.
10. Upprepa steg 3 till och med 9 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning).

### <a name="longevity-verification"></a>Verifiering av livslängd

1. Skapa eller gå till Azure Media Services-kontot.
2. Skapa och starta en **direkt** kanal.
3. Konfigurera kodaren för att skicka en direktsänd dataström i multibithastighet.
4. Skapa en publicerade live-händelse.
5. Kör din live-kodare för en vecka eller längre.
6. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på live direktuppspelning från tid till tid (eller arkiverade tillgången) för att säkerställa att uppspelningen har inga synliga problem.
7. Stoppa direktsändningen.
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.

Till sist e inspelade inställningarna och live-arkivet parametrar till Azure Media Services på amsstreaming@microsoft.com som ett meddelande om att alla självhantering verifieringskontroller är uppfyllda. Dessutom innehåller din kontaktinformation för alla Följ ups. Du kan kontakta Azure Media Services-teamet med några frågor om den här processen.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)
