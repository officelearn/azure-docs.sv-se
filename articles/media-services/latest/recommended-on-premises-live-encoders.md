---
title: Direkt uppspelnings kodare rekommenderas av Media Services – Azure | Microsoft Docs
description: Lär dig mer om direkt uppspelning av lokala kodare som rekommenderas av Media Services
services: media-services
keywords: Encoding; encoders; Media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6b9cb325f2bb7419e32efd5bde4705786c5dbeb5
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934935"
---
# <a name="recommended-live-streaming-encoders"></a>Rekommenderade kodare för direkt uppspelning

I Azure Media Services representerar en [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) en pipeline för bearbetning av direktuppspelat innehåll. Live-händelsen tar emot direktsända indata strömmar på ett av två sätt.

* En lokal Live-kodare skickar en RTMP-eller Smooth Streaming-ström (fragmenterad MP4) med flera bit hastigheter till den direktsända händelse som inte är aktive rad för att utföra direktsänd kodning med Media Services. De inmatade strömmarna passerar Live-händelser utan ytterligare bearbetning. Den här metoden kallas **genom strömning**. En Live-kodare kan skicka en data ström med en bit hastighet till en direkt kanal. Vi rekommenderar inte den här konfigurationen eftersom den inte tillåter strömning med anpassad bit hastighet till klienten.

  > [!NOTE]
  > Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning.
 
* En lokal Live-kodare skickar en data ström med en bit hastighet till den direktsända händelse som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live-händelsen utför sedan direktsänd kodning av den inkommande data strömmen med en bit hastighet till en anpassningsbar video ström med flera bit hastigheter.

Detaljerad information om Live encoding med Media Services finns i [direkt uppspelning med Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som utdata av RTMP

Media Services rekommenderar att du använder någon av följande livekodare som har RTMP som utdata. URL-scheman som stöds `rtmp://` är `rtmps://`eller.

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

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live-kodare som utdata fragmenterade MP4

Media Services rekommenderar att du använder någon av följande Live-kodare med multi-bit-Smooth Streaming (fragmenterad MP4) som utdata. URL-scheman som stöds `http://` är `https://`eller.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Föreställ dig Selenio-MCP3
- Media Excel Hero Live och Hero 4K (UHD/HEVC)

> [!TIP]
>  Om du strömmar Live-händelser på flera språk (till exempel ett engelskt ljud spår och ett spanskt ljud spår) kan du göra detta med mediet för Live-kodare i media som kon figurer ATS för att skicka Live-flödet till en direkt sändnings händelse.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurera lokala inställningar för Live-kodare

Information om vilka inställningar som är giltiga för din live event-typ finns i [jämförelse av aktiva händelse typer](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Uppspelnings krav

Om du vill spela upp innehåll måste både ljud-och video strömmar finnas. Det finns inte stöd för uppspelning av data strömmen med video.

### <a name="configuration-tips"></a>Konfigurations tips

- När det är möjligt använda ett inbyggt internet-anslutning.
- När du bestämmer bandbredds kraven kan du dubblera bit hastigheterna för strömmande data. Även om detta inte är obligatoriskt bidrar den här enkla regeln till att minimera påverkan av nätverks belastning.
- När du använder programvarubaserad kodare kan du stänga alla onödiga program.
- Om du ändrar konfigurationen för konfigurationen efter det att sändningen har startat, har det negativa effekter på evenemanget. Konfigurations ändringar kan orsaka att händelsen blir instabil. 
- Se till att du ger dig tid att konfigurera evenemanget. För storskaliga händelser rekommenderar vi att du startar installationen en timme före evenemanget.

## <a name="becoming-an-on-premises-encoder-partner"></a>Bli en lokal Encoder-partner

Som en Azure Media Services lokal Encoder-partner Media Services befordrar din produkt genom att rekommendera din kodare till företags kunder. För att bli en lokal Encoder-partner måste du kontrol lera kompatibiliteten för din lokala kodare med Media Services. Det gör du genom att utföra följande verifieringar.

### <a name="pass-through-live-event-verification"></a>Direkt händelse verifiering

1. Kontrol lera att **slut punkten för direkt uppspelning** körs i ditt Media Services konto. 
2. Skapa och starta direkt sändnings evenemanget. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: en för inmatning och konfigurera din lokala kodare för att använda URL: en för att skicka en Live-dataström med flera bit hastigheter till Media Services.
4. Hämta för hands versionen av URL: en och Använd den för att kontrol lera att inmatarna faktiskt tas emot.
5. Skapa ett nytt **till gångs** objekt.
6. Skapa en **Live-utdata** och Använd namnet på den till gång som du skapade.
7. Skapa en **strömmande lokaliserare** med de inbyggda typer av **strömmande principer** .
8. Visa en lista över Sök vägarna för den strömmande lokaliseraren för att få tillbaka de webb adresser som ska användas.
9. Hämta värd namnet för den **slut punkt för direkt uppspelning** som du vill strömma från.
10. Kombinera URL: en från steg 8 med värd namnet i steg 9 för att få den fullständiga URL: en.
11. Kör din Live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen. 
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel på alla kvalitets nivåer. Du kan också titta på och validera via URL: en för för hands versionen under Live-sessionen.
14. Registrera till gångs-ID, den publicerade streaming-URL: en för Live-arkivet och de inställningar och den version som används i din Live Encoder.
15. Återställ Live händelse tillstånd när du har skapat varje exempel.
16. Upprepa steg 5 till och med 15 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering, under texter eller olika kodnings hastigheter).

### <a name="live-encoding-live-event-verification"></a>Live-kodning av Live-händelse

1. Kontrol lera att **slut punkten för direkt uppspelning** körs i ditt Media Services konto. 
2. Skapa och starta Live **encoding** Live-händelsen. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta URL: en för inmatning och konfigurera kodaren för att skicka en Live-dataström med en bit hastighet till Media Services.
4. Hämta för hands versionen av URL: en och Använd den för att kontrol lera att inmatarna faktiskt tas emot.
5. Skapa ett nytt **till gångs** objekt.
6. Skapa en **Live-utdata** och Använd namnet på den till gång som du skapade.
7. Skapa en **strömmande lokaliserare** med de inbyggda typer av **strömmande principer** .
8. Visa en lista över Sök vägarna för den strömmande lokaliseraren för att få tillbaka de webb adresser som ska användas.
9. Hämta värd namnet för den **slut punkt för direkt uppspelning** som du vill strömma från.
10. Kombinera URL: en från steg 8 med värd namnet i steg 9 för att få den fullständiga URL: en.
11. Kör din Live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen.
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel för alla kvalitets nivåer. Du kan också titta på och validera via URL: en för för hands versionen under Live-sessionen.
14. Registrera till gångs-ID, den publicerade streaming-URL: en för Live-arkivet och de inställningar och den version som används i din Live Encoder.
15. Återställ Live händelse tillstånd när du har skapat varje exempel.
16. Upprepa steg 5 till och med 15 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering, under texter eller olika kodnings hastigheter).

### <a name="longevity-verification"></a>Longevity-verifiering

Följ samma steg som i direkt [sändnings händelse verifiering](#pass-through-live-event-verification) , förutom steg 11. <br/>I stället för 10 minuter kan du köra din Live-kodare i en vecka eller längre. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se direkt uppspelningen från tid till gång (eller till en arkiverad till gång) för att säkerställa att uppspelningen inte har några synliga fel.

### <a name="email-your-recorded-settings"></a>E-posta dina inspelade inställningar

Skicka sedan dina inspelade inställningar och Live Archive-parametrarna till amshelp@microsoft.com Azure Media Services vid ett meddelande om att alla själv verifierings kontroller har slutförts. Ta även med din kontakt information för alla följare. Du kan kontakta Azure Media Servicess teamet och få frågor om den här processen.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)
