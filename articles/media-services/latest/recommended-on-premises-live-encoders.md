---
title: Livestreaming-kodare som rekommenderas av Media Services – Azure | Microsoft-dokument
description: Läs mer om direktuppspelning lokalt som rekommenderas av Media Services
services: media-services
keywords: kodning;kodare;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: bbd2929e245fa5fc01245e7bd02e537db8d5ef36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536343"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Testade lokala direktuppspelningsgivare

I Azure Media Services representerar en [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) en pipeline för bearbetning av live-strömmande innehåll. Live-händelsen tar emot live-indataströmmar på ett av två sätt.

* En lokal live-kodare skickar en RTMP- eller Smooth Streaming -ström (fragmenterad MP4) till livehändelsen som inte är aktiverad för att utföra livekodning med Media Services. De intövda strömmarna passerar genom livehändelser utan ytterligare bearbetning. Den här metoden kallas **genomströmning**. Vi rekommenderar att live-kodaren skickar flerbitrateströmmar i stället för en dataström med en bithastighet till en direktuppspelningshändelse för att möjliggöra adaptiv bithastighetsströmning till klienten. 

    Om du använder strömmar med flera bithastigheter för direktuppspelningshändelsen måste video-GOP-storleken och videofragmenten på olika bithastigheter synkroniseras för att undvika oväntat beteende på uppspelningssidan.

  > [!TIP]
  > Att använda en genomströmningsmetod är det mest ekonomiska sättet att göra livestreaming.
 
* En lokal live-kodare skickar en enbitrate-ström till Live Event som är aktiverad för live-kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live Event utför sedan live-kodning av den inkommande enbitrate-strömmen till en videoström med flera bithastigheter (adaptiv).

I den här artikeln beskrivs testade lokala direktuppspelningskodare. Instruktioner om hur du verifierar din lokala live-kodare finns i [verifiera din lokala kodare](become-on-premises-encoder-partner.md)

Detaljerad information om livekodning med Media Services finns i [Direktuppspelning med Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Krav på kodare

Kodare måste ha stöd för TLS 1.2 när https- eller RTMPS-protokoll används.

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som matar ut RTMP

Media Services rekommenderar att du använder någon av följande livekodare som har RTMP som utdata. Url-schemana `rtmp://` som `rtmps://`stöds är eller .

Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.<br/><br/>
Vid direktuppspelning via RTMPS ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 2935 och 2936 är öppna.

> [!NOTE]
> Kodare måste ha stöd för TLS 1.2 när RTMPS-protokoll används.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (version 2.14.15 och högre)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (version 13.0.2 eller senare på grund av TLS 1.2-kravet)
- Telestream Wirecast S (endast RTMP stöds)
- Teradek Slice 756
- TriCaster 8000
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro (på ett år)](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 och Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live-kodare som matar ut fragmenterad MP4

Media Services rekommenderar att du använder någon av följande live-kodare som har multibitrate Smooth Streaming (fragmenterad MP4) som utdata. Url-schemana `http://` som `https://`stöds är eller .

> [!NOTE]
> Kodare måste ha stöd för TLS 1.2 när HTTPS-protokoll används.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (version 2.14.15 och högre på grund av TLS 1.2-kravet)
- Envivio 4Caster C4 Gen III 
- Föreställ kommunikation Selenio MCP3
- Media Excel Hero Live och Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Om du streamar livehändelser på flera språk (till exempel ett engelskt ljudspår och ett spanskt ljudspår) kan du åstadkomma detta med Media Excel live-kodaren konfigurerad för att skicka liveflödet till en livehändelse.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurera inställningar för lokala live-kodare

Information om vilka inställningar som gäller för din livehändelsetyp finns i [Jämförelse med livehändelsetyper](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Uppspelningskrav

Om du vill spela upp innehåll måste både en ljud- och videoström finnas. Uppspelning av endast videoströmmen stöds inte.

### <a name="configuration-tips"></a>Konfigurationstips

- Använd en direktkopplad internetanslutning när det är möjligt.
- När du bestämmer bandbreddskraven dubblar du strömmande bithastigheter. Även om den inte är obligatorisk, bidrar den här enkla regeln till att minska effekten av överbelastning i nätverket.
- När du använder programvarubaserade kodare stänger du alla onödiga program.
- Ändra din kodare konfiguration efter det har börjat trycka har negativa effekter på händelsen. Konfigurationsändringar kan leda till att händelsen blir instabil. 
- Se till att du ger dig själv gott om tid att ställa in ditt evenemang. För händelser i hög skala rekommenderar vi att du startar installationen en timme före händelsen.
- Använd H.264-video- och AAC-ljudcodec-utgången.
- Se till att det finns nyckelbildruta eller GOP-tidsmässig justering över videokvaliteter.
- Kontrollera att det finns ett unikt strömnamn för varje videokvalitet.
- Använd strikt CBR-kodning rekommenderas för optimal adaptiv bithastighet prestanda.

> [!IMPORTANT]
> Titta på den fysiska konditionen på maskinen (CPU / Minne / etc) som laddar upp fragment till molnet innebär CPU och IO-operationer. Om du ändrar några inställningar i kodaren, vara säker återställa kanaler / live händelse för att ändringen ska träda i kraft.

## <a name="see-also"></a>Se även

[Livestreaming med Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Nästa steg

[Så här verifierar du kodaren](become-on-premises-encoder-partner.md)
