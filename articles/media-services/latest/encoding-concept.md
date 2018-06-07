---
title: Kodning i molnet med Azure Media Services | Microsoft Docs
description: Det här avsnittet beskrivs kodningen när du använder Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655628"
---
# <a name="encoding-with-azure-media-services"></a>Encoding med Azure Media Services

Azure Media Services kan du koda dina digitala mediefiler med hög kvalitet till format som kan titta på en mängd olika webbläsare och enheter. Exempelvis kanske du vill dataströmmen ditt innehåll i Apples HLS eller MPEG DASH-format. Media Services kan du analysera innehållet video eller ljud. Det här avsnittet ger vägledning om att koda ditt innehåll med Media Services v3.

Du måste skapa en transformering och ett jobb för att koda med Media Services v3. En transformering definierar recept för kodning inställningar och utdata och jobbet är en instans av recept. Mer information finns i [omvandlar och jobb](transform-concept.md)

När encoding med Azure Media Services kan du använda förinställda för att berätta kodaren hur mediefilerna ska bearbetas. Du kan till exempel ange video upplösning och/eller antalet ljud kanaler som du vill använda i det kodade innehållet. 

Du kan komma igång snabbt med något av de rekommenderade inbyggda förinställda baserat på bästa praxis från branschen eller kan du skapa en egen förinställning om du vill anpassa dina specifika krav för scenario eller enhet. 

Hitta information om kodaren i den [OpenAPI specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder för närvarande följande inbyggda kodning förinställningar:  

|**Förinställda namnet**|**Scenario**|**Detaljer**|
|---|---|---|
|**AudioAnalyzerPreset**|Analysera ljud|Förinställningen gäller en fördefinierad uppsättning AI-baserade analys åtgärder, inklusive tal skrivfel. Förinställningen stöder för närvarande, bearbetning av innehåll med ett enda ljud spår.<br/>Du kan ange språk för ljud nyttolasten i indata med BCP 47 format 'språk tagg-område' (till exempel ”sv-se”). Lista över språk som stöds är en-US, en-GB, es-ES, 'es MX', fr-FR, it-IT, ja-JP, pt-BR, zh-CN.|
|**VideoAnalyzerPreset**|Analysera ljud och video|Extraherar insikter (omfattande metadata) från både ljud och video och matar ut en fil i JSON-format. Du kan ange om du bara vill extrahera ljud insikter vid bearbetning av en videofil. Mer information finns i [analysera video](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Strömning|Används för att ange en inbyggd förinställningen för kodning indatavideo med Standard-kodare. <br/>Följande förinställningar stöds:<br/>**EncoderNamedPreset.AdaptiveStreaming** (rekommenderas). Mer information finns i [autogenerering av en bithastighet stege](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -producerar en enda MP4-fil som innehåller endast stereoljud kodade 192 kbit/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** -producerar en uppsättning 8 GOP-justerad MP4-filer, allt från 6000 kbit/s till 400 kbit/s och AAC stereoljud. Lösning startas vid 1080p och nedåt 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -producerar en uppsättning 6 GOP-justerad MP4-filer, allt från 3400 kbit/s till 400 kbit/s och AAC stereoljud. Lösning startas på 720p och nedåt 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** -producerar en uppsättning 5 GOP-justerad MP4-filer, allt från 1600 kbit/s till 400 kbit/s och AAC stereoljud. Lösning startas vid 480p och nedåt 360 p.<br/><br/>Mer information finns i [Uploading kodning och direktöverföring av filer](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Strömning|Beskrivning av inställningar som ska användas när kodningen indatavideo med Standard-kodare. <br/>Använd denna förinställning när du anpassar transformeringen förinställningar. Mer information finns i [hur du anpassar transformeringen förinställningar](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Anpassade förinställningar

Media Services stöder anpassning av alla värden i förinställningar för att uppfylla dina kodning behov och krav. Du använder den **StandardEncoderPreset** förinställningen när du anpassar transformeringen förinställningar. För en detaljerade förklaringar och exempel finns [hur du anpassar kodare förinställningar](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Skalning kodning i v3

Kunder har för närvarande, om du använder Azure-portalen eller AMS v2 API: er för att ange RUs (enligt beskrivningen i [skalning media bearbetning](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Nästa steg

### <a name="tutorials"></a>Självstudier

Följande tutorals visar hur att koda ditt innehåll med Media Services:

* [Överföra, koda och strömma med Azure Media Services](stream-files-tutorial-with-api.md)
* [Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Kodexempel

I följande kodexempel innehåller kod som visar hur du kodar med Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK:er

Du kan använda någon av följande stöds Media Services v3 SDK: erna för att koda ditt innehåll.

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

