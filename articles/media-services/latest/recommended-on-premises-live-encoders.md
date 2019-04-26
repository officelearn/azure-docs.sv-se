---
title: Live direktuppspelning kodare som rekommenderas av Media Services – Azure | Microsoft Docs
description: Lär dig mer om live direktuppspelning lokala kodare som rekommenderas av Media Services
services: media-services
keywords: kodning; kodare; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c7d895cd87122374a79a520643580a179961fba3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405366"
---
# <a name="recommended-live-streaming-encoders"></a>Rekommenderade livekodare för direktuppspelning

I Azure Media Services, en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) representerar en pipeline för bearbetning av live-streaming-innehåll. Live-händelsen tar emot inkommande direktsändningar i något av två sätt.

* En lokal livekodare skickar en med flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) strömma Live-händelsen som inte är aktiverad för att utföra live encoding med Media Services. De infogade strömmarna passerar via Live händelser utan vidare bearbetning. Den här metoden anropas **direkt**. En livekodare kan skicka en dataström med enkel bithastighet till en genomströmningskanal. Vi rekommenderar inte den här konfigurationen eftersom den inte tillåter för direktuppspelning med anpassningsbar bithastighet till klienten.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning.

* En lokal livekodare skickar en dataström med enkel bithastighet Live-händelsen som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live-händelsen utför sedan live encoding av den inkommande dataströmmen med enkel bithastighet till en med flera bithastigheter (anpassningsbar) videoström.

Detaljerad information om live encoding med Media Services finns i [direktsänd strömning med Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Livekodare som RTMP

Media Services rekommenderar att du använder någon av följande livekodare som har RTMP som utdata. URL-scheman som stöds är `rtmp://` eller `rtmps://`.

> [!NOTE]
> Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
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
- Media Excel Hero Live och Hero 4K (UHD/HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurera en lokal livekodare inställningar

Information om vilka inställningar som är giltiga för din typ av live-händelse, finns i [direktsänd händelse skriver jämförelse](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Uppspelningskrav för

För att spela upp innehåll, måste både en ljud- och ström finnas. Uppspelning av videon endast dataströmmen stöds inte.

### <a name="configuration-tips"></a>Konfigurationstips

- När det är möjligt använda ett inbyggt internet-anslutning.
- När du bestämmer krav på bandbredd, dubbla strömmande bithastighet. Även om det är inte obligatoriska regeln enkel hjälper dig för att undvika påverkan av överbelastning på nätverket.
- När du använder programvarubaserad kodare kan du stänga alla onödiga program.
- Ändra din Kodarkonfiguration när det har startat push-överföring har negativ inverkan på händelsen. Konfigurationsändringar kan orsaka händelsen för att bli instabil. 
- Se till att du ger dig själv gott om tid att bygga upp din händelse. För hög skalbarhet händelser rekommenderar vi startar installationsprogrammet en timme innan evenemanget.

## <a name="becoming-an-on-premises-encoder-partner"></a>Bli en partner för den lokala kodare

Som ett Azure Media Services lokala kodare partner främjar medietjänster din produkt genom att rekommendera kodaren för företagskunder. Om du vill bli lokala kodare-partner måste du kontrollera kompatibiliteten för din lokala kodare med Media Services. Du gör detta genom att slutföra följande verifieringar.

### <a name="pass-through-live-event-verification"></a>Genomströmning direktsänd händelse verifiering

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** körs. 
2. Skapa och starta den **direkt** direktsänd händelse. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera din lokala kodare för att använda Webbadressen för att skicka en direktsänd dataström med multibithastighet till Media Services.
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.
7. Skapa en **Strömningspositionerare** med inbyggt **Streaming princip** typer.
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda.
9. Hämta värdnamnet för den **Strömningsslutpunkt** som du vill spela från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen. 
13. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem på alla servicenivåer. Eller titta på och validera via förhandsgransknings-URL i live-session.
14. Registrera tillgångs-ID, publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
15. Återställ direktsänd händelse tillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av din kodare (med och utan ad signalering, undertexter eller olika kodning hastigheter).

### <a name="live-encoding-live-event-verification"></a>Live encoding direktsänd händelse-verifiering

1. I ditt Media Services-konto, se till att den **Strömningsslutpunkt** körs. 
2. Skapa och starta den **direktsänd kodning** direktsänd händelse. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: er för inmatning och konfigurera kodaren för att skicka en direktsänd dataström med enkel bithastighet till Media Services.
4. Få förhandsgransknings-URL och använda den för att kontrollera att indata från kodaren faktiskt tas emot.
5. Skapa en ny **tillgången** objekt.
6. Skapa en **Live utdata** och använda Tillgångsnamn som du skapade.
7. Skapa en **Strömningspositionerare** med inbyggt **Streaming princip** typer.
8. Lista över sökvägar på den **Strömningspositionerare** att få tillbaka de URL: er för att använda.
9. Hämta värdnamnet för den **Strömningsslutpunkt** som du vill spela från.
10. Kombinera URL: en från steg 8 med värdnamnet i steg 9 för att hämta den fullständiga URL: en.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen.
13. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på den arkiverade tillgången för att säkerställa att uppspelningen har inga synliga problem för alla kvalitetsnivå. Eller titta på och validera via förhandsgransknings-URL i live-session.
14. Registrera tillgångs-ID, publicerade strömmande URL för live-arkivet, inställningar och version som används från din live-kodare.
15. Återställ direktsänd händelse tillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av din kodare (med och utan ad signalering, undertexter eller olika kodning hastigheter).

### <a name="longevity-verification"></a>Verifiering av livslängd

Följ samma steg som i [direkt direktsänd händelse verifiering](#pass-through-live-event-verification) förutom steg 11. <br/>I stället för 10 minuter att köra din live-kodare för en vecka eller längre. Använd en spelare som [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) att titta på live direktuppspelning från tid till tid (eller en arkiverade tillgången) för att säkerställa att uppspelningen har inga synliga problem.

### <a name="email-your-recorded-settings"></a>E-inställningarna inspelade

Slutligen e-inställningarna inspelade och live-arkivet parametrar till Azure Media Services på amsstreaming@microsoft.com som ett meddelande om att alla självhantering verifieringskontroller är uppfyllda. Dessutom innehåller din kontaktinformation för alla uppföljningar. Du kan kontakta Azure Media Services-teamet om du har frågor om den här processen.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)
