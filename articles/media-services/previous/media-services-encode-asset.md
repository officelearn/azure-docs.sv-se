---
title: Översikt över Azure on-demand media-kodare | Microsoft-dokument
description: Azure Media Services innehåller flera alternativ för kodning av media i molnet. Den här artikeln innehåller en översikt över Azure-mediakodrar på begäran.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251107"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Översikt över Azure on-demand media-kodare 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services innehåller flera alternativ för kodning av media i molnet.

När du börjar med Media Services är det viktigt att förstå skillnaden mellan codec-enheter och filformat.
Codecs är den programvara som implementerar komprimerings-/dekompressionsalgoritmerna medan filformat är behållare som innehåller den komprimerade videon.

Media Services tillhandahåller dynamiska förpackningar som gör att du kan leverera ditt adaptiva bithastighets-MP4- eller Smooth Streaming-kodat innehåll i strömmande format som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver paketera om till dessa strömmande format.

När ditt Media Services-konto skapas läggs en **standardslutpunkt** för direktuppspelning till ditt konto i tillståndet **Stoppad.** Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. Fakturering för slutpunkter för direktuppspelning sker **Running** när slutpunkten är i körläge.

Media Services stöder följande kodare på begäran som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Den här artikeln ger en kort översikt över mediakodrar på begäran och innehåller länkar till artiklar som ger mer detaljerad information. Ämnet ger också jämförelse av kodarna.

Som standard kan varje Media Services-konto ha en aktiv kodningsuppgift åt gången. Du kan reservera kodningsenheter som gör att du kan ha flera kodningsuppgifter som körs samtidigt, en för varje kodningsreserverad enhet som du köper. Mer information finns i [Skala kodningsenheter](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Hur du ska använda detta
[Så här kodar du med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Format
[Format och codec-enheter](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Förinställningar
Media Encoder Standard är konfigurerad med hjälp av en av kodarförinställningarna som beskrivs [här](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadata för indata och utdata
Kodarna indatametadata beskrivs [här](media-services-input-metadata-schema.md).

Kodarna utdatametadata beskrivs [här](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Skapa miniatyrbilder
Information finns i [Så här skapar du miniatyrer med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trimma videor (urklipp)
Mer information finns i [Så här trimmar du videor med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Skapa överlägg
Mer information finns i [Så här skapar du överlägg med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Se även
[Media Services blogg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Arbetsflöde för Media Encoder Premium
### <a name="overview"></a>Översikt
[Introduktion till Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Hur du ska använda detta
Media Encoder Premium Workflow är konfigurerat med hjälp av komplexa arbetsflöden. Arbetsflödesfiler kan skapas och uppdateras med verktyget [Arbetsflödesdesigner.](media-services-workflow-designer.md)

[Så här använder du Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Kända problem
Om indatavideon inte innehåller dold textning innehåller utdatatillgången fortfarande en tom TTML-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodningsuppgifter genom att anpassa standardförinställningar för mediekodare](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
