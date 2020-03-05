---
title: Direkt uppspelnings kodare rekommenderas av Media Services – Azure | Microsoft Docs
description: Lär dig mer om direkt uppspelning av lokala kodare som rekommenderas av Media Services
services: media-services
keywords: Encoding; encoders; Media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268200"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>Rekommenderade lokala Live streaming-kodare

I Azure Media Services representerar en [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) en pipeline för bearbetning av direktuppspelat innehåll. Live-händelsen tar emot direktsända indata strömmar på ett av två sätt.

* En lokal Live-kodare skickar en RTMP-eller Smooth Streaming-ström (fragmenterad MP4) med flera bit hastigheter till den direktsända händelse som inte är aktive rad för att utföra direktsänd kodning med Media Services. De inmatade strömmarna passerar Live-händelser utan ytterligare bearbetning. Den här metoden kallas **genom strömning**. Vi rekommenderar att Live Encoder skickar strömmar med flera bit hastigheter i stället för en data ström med en bit hastighet till en direkt sändnings händelse för att möjliggöra direkt uppspelning av bit hastighet till klienten. 

    Om du använder data strömmar med flera bit hastigheter för direkt sändnings händelse måste videons GOP storlek och videofragmenten på olika bit hastigheter synkroniseras för att undvika oväntad beteende på uppspelnings sidan.

  > [!NOTE]
  > Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning.
 
* En lokal Live-kodare skickar en data ström med en bit hastighet till den direktsända händelse som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live-händelsen utför sedan direktsänd kodning av den inkommande data strömmen med en bit hastighet till en anpassningsbar video ström med flera bit hastigheter.

Detaljerad information om Live encoding med Media Services finns i [direkt uppspelning med Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Kodarens krav

Kodare måste ha stöd för TLS 1,2 när du använder HTTPS-eller RTMP-protokoll.

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som utdata av RTMP

Media Services rekommenderar att du använder någon av följande livekodare som har RTMP som utdata. URL-scheman som stöds är `rtmp://` eller `rtmps://`.

Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.<br/><br/>
Vid direktuppspelning via RTMPS ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 2935 och 2936 är öppna.

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när du använder RTMP-protokoll.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Grundämne Live (version 2.14.15 och senare)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Wirecast för multistream (version 13.0.2 eller högre på grund av TLS 1,2-krav)
- Multistream Wirecast S (endast RTMP stöds)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hjälte 7 och hjälte 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live-kodare som utdata fragmenterade MP4

Media Services rekommenderar att du använder någon av följande Live-kodare med multi-bit-Smooth Streaming (fragmenterad MP4) som utdata. URL-scheman som stöds är `http://` eller `https://`.

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när de använder HTTPS-protokoll.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Grundämne Live (version 2.14.15 och högre på grund av kraven för TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Föreställ dig Selenio-MCP3
- Media Excel Hero Live och Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

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

## <a name="see-also"></a>Se även

[Bli en lokal Encoder-partner](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)
