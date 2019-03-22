---
title: Lär dig mer om kodare som rekommenderas av Azure Media Services | Microsoft Docs
description: Lär dig mer om kodare som rekommenderas av media services
services: media-services
keywords: kodning; kodare; media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 373ce1d10af87603b1bdd6339c94891187c35d8c
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295376"
---
# <a name="recommended-on-premises-encoders"></a>Rekommenderade lokala kodare
När direktsänd strömning med Azure Media Services, kan du ange hur du vill att din kanal tar emot Indataströmmen. Om du vill använda en på lokala kodare med en livekanal med kodning ska kodaren skicka en dataström med hög kvalitet med enkel bithastighet som utdata. Om du vill använda en på lokala kodare med släpp igenom kanal bör kodaren skicka en dataström med multibithastighet som utdata med alla önskade output-egenskaper. Mer information finns i [direktsänd strömning med på lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services rekommenderar att du använder ett av följande livekodare som har RTMP som utdata:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X – HEVC
- Haivision KB
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Mellan aktiviteter Studio (iOS)

Azure Media Services rekommenderar att du använder något av följande livekodare som har flera bithastigheter fragmenterad-MP4 (Smooth Streaming) som utdata:
- Excel-Media Hero Live och Hero 4K (UHD / – HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> En livekodare kan skicka en dataström med enkel bithastighet till släppa igenom kanal, men den här konfigurationen rekommenderas inte eftersom det inte tillåter för strömning med anpassad bithastighet till klienten.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hur du blir partner på lokala kodare
Som ett Azure Media Services i lokala kodare partner främjar medietjänster din produkt genom att rekommendera kodaren för företagskunder. Om du vill bli partner kodare på plats måste du kontrollera kompatibiliteten för kodaren på plats med Media Services. Slutför följande verifieringar gör du:

Passera kanal verifiering
1. Skapa eller gå till ditt Azure Media Services-konto
2. Skapa och starta en **direkt** kanal
3. Konfigurera kodaren för att skicka en direktsänd dataström i multibithastighet.
4. Skapa en publicerade live-händelse
5. Kör din live-kodare i cirka 10 minuter
6. Stoppa direktsändningen
7. Skapa, starta slutpunkten för direktuppspelning, Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller du kan också se och validera via förhandsgransknings-URL under den direktsända sessionen innan steg 6)
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare
9. Återställs kanalen när du har skapat varje prov
10. Upprepa steg 3 till 9 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning)

Live encoding kanal-verifiering
1. Skapa eller gå till ditt Azure Media Services-konto
2. Skapa och starta en **direktsänd kodning** kanal
3. Konfigurera kodaren för att skicka en direktsänd dataström med enkel bithastighet.
4. Skapa en publicerade live-händelse
5. Kör din live-kodare i cirka 10 minuter
6. Stoppa direktsändningen
7. Skapa, starta slutpunkten för direktuppspelning, Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller du kan också se och validera via förhandsgransknings-URL under den direktsända sessionen innan steg 6)
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare
9. Återställs kanalen när du har skapat varje prov
10. Upprepa steg 3 till 9 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning)

Verifiering av livslängd
1. Skapa eller gå till ditt Azure Media Services-konto
2. Skapa och starta en **direkt** kanal
3. Konfigurera kodaren för att skicka en direktsänd dataström i multibithastighet.
4. Skapa en publicerade live-händelse
5. Kör din live-kodare för en vecka eller längre
6. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på live direktuppspelning från tid till tid (eller arkiverade tillgången) för att säkerställa att uppspelningen har inga synliga problem
7. Stoppa direktsändningen
8. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare

Till sist skicka dina inspelade inställningar och live-arkivet parametrar till Media Services genom att skicka e-post amsstreaming@microsoft.com. Mottagit utför medietjänster verifieringstest på exempel från din live-kodare. Du kan kontakta medietjänster med några frågor om den här processen.
