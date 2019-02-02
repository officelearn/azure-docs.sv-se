---
title: Lär dig mer om live direktuppspelning lokala kodare som rekommenderas av Media Services – Azure | Microsoft Docs
description: Lär dig mer om live direktuppspelning lokala kodare som rekommenderas av Media Services
services: media-services
keywords: kodning; kodare; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c3e42ba9fe84ded8c60fc71f19de785945852116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656676"
---
# <a name="recommended-live-streaming-encoders"></a>Rekommenderade livekodare för direktuppspelning

I Media Services, en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) representerar en pipeline för bearbetning av live-streaming-innehåll. Live-händelsen tar emot inkommande direktsändningar i ett av två sätt:

* En lokal livekodare skickar en med flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) strömma Live-händelsen som inte är aktiverad för att utföra live encoding med Media Services. De infogade strömmarna passerar via Live händelser utan vidare bearbetning. Den här metoden anropas **direkt**. En livekodare kan skicka en dataström med enkel bithastighet till släppa igenom kanal, men den här konfigurationen rekommenderas inte eftersom det inte tillåter för strömning med anpassad bithastighet till klienten.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning.

* En lokal livekodare skickar en dataström med enkel bithastighet Live-händelsen som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live-händelsen utför sedan live encoding av den inkommande dataströmmen med enkel bithastighet till en med flera bithastigheter (anpassningsbar) videoström.

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

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurera en lokal livekodare inställningar

Information om vilka inställningar som är giltiga för din typ av live-händelse, finns i [direktsänd händelse skriver jämförelse](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Uppspelningskrav för

Både en ljud- och ström måste finnas i ordning för uppspelning av innehåll. Uppspelning av videon endast dataströmmen stöds inte.

### <a name="configuration-tips"></a>Konfigurationstips

- När det är möjligt använda ett inbyggt internet-anslutning.
- En bra tumregel när du fastställer kraven på nätverksbandbredd är att dubbla strömmande bithastighet. Detta är inte ett obligatoriskt krav, kan minimera effekten av överbelastning på nätverket.
- När du använder programvara baserat kodare, Stäng ut alla onödiga program.
- Ändra inte din konfiguration för kodare har startat push-överföring. Den har negativ inverkan på händelsen och kan orsaka händelsen ska vara instabil. 
- Se till att ge dig själv gott om tid att konfigurera din händelse. För storskaliga händelser, vi rekommenderar att starta installationsprogrammet en timme innan evenemanget.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hur du blir partner lokala kodare

Som ett Azure Media Services lokala kodare partner främjar medietjänster din produkt genom att rekommendera kodaren för företagskunder. Om du vill bli lokala kodare-partner måste du kontrollera kompatibiliteten för din lokala kodare med Media Services. Slutför följande verifieringar gör du:

### <a name="pass-through-live-event-verification"></a>Genomströmning direktsänd händelse verifiering

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** körs. 
2. Skapa och starta den **direkt** direktsänd händelse. <br/> Mer information finns i [direktsänd händelse tillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera din lokala kodare för att använda Webbadressen för att skicka den en direktsänd dataström med multibithastighet till Media Services.
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.
7. Skapa en **Strömningspositionerare** med inbyggt **Streaming princip** typer.
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda.
9. Hämta värdnamnet för den **Strömningsslutpunkt** du vill strömma från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa direktsändningen. 
13. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller, du kan också se och validera via förhandsgransknings-URL i live-session).
14. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
15. Återställ direktsänd händelse tillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning).

### <a name="live-encoding-live-event-verification"></a>Live encoding direktsänd händelse-verifiering

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** körs. 
2. Skapa och starta den **direktsänd kodning** direktsänd händelse. <br/> Mer information finns i [direktsänd händelse tillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera kodaren för att skicka en direktsänd dataström med enkel bithastighet till Media Services.
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.
7. Skapa en **Strömningspositionerare** med inbyggt **Streaming princip** typer.
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda.
9. Hämta värdnamnet för den **Strömningsslutpunkt** du vill strömma från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa direktsändningen.
13. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå (eller, du kan också se och validera via förhandsgransknings-URL i live-session).
14. Registrera tillgångs-ID publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
15. Återställ direktsänd händelse tillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av din kodare (med och utan ad signalering/bildtexter/olika hastigheter-kodning).

### <a name="longevity-verification"></a>Verifiering av livslängd

Samma steg som i [direkt direktsänd händelse verifiering](#pass-through-live-event-verification) förutom steg 11. <br/>I stället för 10 minuter att köra din live-kodare för en vecka eller längre. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på live direktuppspelning från tid till tid (eller arkiverade tillgången) för att säkerställa att uppspelningen har inga synliga problem.

### <a name="email-your-recorded-settings"></a>E-inställningarna inspelade

Till sist e inspelade inställningarna och live-arkivet parametrar till Azure Media Services på amsstreaming@microsoft.com som ett meddelande om att alla självhantering verifieringskontroller är uppfyllda. Dessutom innehåller din kontaktinformation för alla Följ ups. Du kan kontakta Azure Media Services-teamet med några frågor om den här processen.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)
