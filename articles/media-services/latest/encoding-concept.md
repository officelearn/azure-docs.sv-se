---
title: Kodning i molnet med Media Services – Azure | Microsoft Docs
description: Det här avsnittet beskriver hur du kodning med Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ccf298c99851dc2418da894431c5c86adafe59b3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959450"
---
# <a name="encoding-with-media-services"></a>Encoding med Media Services

Azure Media Services kan du koda din digitala mediefiler med hög kvalitet till MP4-filer så att ditt innehåll kan spelas upp på en mängd olika webbläsare och enheter. En lyckad kodningsjobb för Media Services skapar utdata tillgång med en uppsättning med anpassningsbar bithastighet MP4s och manifestfiler (server och klient). Nu kan du dra nytta av [dynamisk paketering](dynamic-packaging-overview.md).

Att göra videor i utdata tillgången som är tillgängliga för klienter för uppspelning, måste du skapa en **Strömningspositionerare** och sedan skapa strömmande URL: er. Sedan, baserat på formatet som anges i manifestet, klienterna får dataströmmen i protokollet som de har valt.

Följande diagram visar strömning på begäran med dynamisk paketering arbetsflöde.

![Dynamic Encoding](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Det här avsnittet ger vägledning om att koda ditt innehåll med Media Services v3.

## <a name="transforms-and-jobs"></a>Transformeringar och jobb

Om du vill koda med Media Services v3, måste du skapa en [transformera](https://docs.microsoft.com/rest/api/media/transforms) och en [jobbet](https://docs.microsoft.com/rest/api/media/jobs). En transformering definierar receptet för kodning inställningar och utdata och jobbet är en instans av receptet. Mer information finns i [transformeringar och jobb](transforms-jobs-concept.md)

När encoding med Media Services, använder du förinställningar som talar om kodaren hur inkommande mediefiler ska bearbetas. Du kan till exempel ange video upplösning och/eller antalet ljud kanaler som du vill i det kodade innehållet. 

Du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar baserat på branschens bästa praxis eller du kan välja att skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet. Mer information finns i [koda med en anpassad transformering](customize-encoder-presets-how-to.md). 

Från och med januari 2019 när encoding med Media Encoder Standard till producerar MP4-filer, en ny .mpi-fil och lagts till i utdata tillgången. Den här MPI-filen är avsedd att förbättra prestanda för [dynamisk paketering](dynamic-packaging-overview.md) och strömning av scenarier.

> [!NOTE]
> Du bör inte ändra eller ta bort filen MPI eller ta alla beroenden i din tjänst med befintliga (eller inte) av sådan fil.

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder för närvarande följande inbyggda förinställningar för kodning:  

### <a name="builtinstandardencoderpreset-preset"></a>BuiltInStandardEncoderPreset preset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) används för att ange en inbyggd förinställning för encoding indatavideon med Standard-kodare. 

Följande förinställningar stöds för närvarande:

- **EncoderNamedPreset.AdaptiveStreaming** (rekommenderas). Mer information finns i [autogenerering en bithastighetsstege](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** -producerar en enda MP4-fil som innehåller endast stereo ljud kodade med 192 kbit/s.
- **EncoderNamedPreset.H264MultipleBitrate1080p** -producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 6000 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas vid 1080p och ned 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -producerar en uppsättning 6 GOP-justerad MP4-filer, sträcker sig från 3400 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas vid 720p och ned 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -producerar en uppsättning 5 GOP-justerad MP4-filer, sträcker sig från 1600 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas på 480 pixlar och ned 360 p.<br/><br/>Mer information finns i [överför, koda och strömma filer](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset förinställning

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beskriver inställningar som ska användas när kodning indatavideon med Standard-kodare. Använd denna förinställning när du anpassar transformeringen förinställningar. 

#### <a name="custom-presets"></a>Anpassade förinställningar

Media Services stöder helt anpassa alla värden i förinställningar för att uppfylla dina specifika kodning behov och krav. Du använder den **StandardEncoderPreset** förinställda när du anpassar transformeringen förinställningar. För en detaljerade förklaringar och exempel finns i [hur du anpassar encoder förinställningar](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Skala kodning i v3

Om du vill skala mediebearbetning Se [skala med CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Nästa steg

* [Koda från en HTTPS-URL med hjälp av inbyggda förinställningar](job-input-from-http-how-to.md)
* [Koda en lokal fil med hjälp av inbyggda förinställningar](job-input-from-local-file-how-to.md)
* [Skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet](customize-encoder-presets-how-to.md)
* [Ladda upp, koda och strömma med Media Services](stream-files-tutorial-with-api.md)
