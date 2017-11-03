---
title: "Översikt över och jämförelse av Azure på begäran mediet kodare | Microsoft Docs"
description: "Det här avsnittet ger en översikt och jämförelse av Azure på begäran mediet kodare."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 538a6ab60168735c2626a93cdeedd8d4999a6efc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Översikt över och jämförelse av Azure på begäran mediet kodare
## <a name="encoding-overview"></a>Kodning: översikt
Azure Media Services erbjuder flera alternativ för kodning av media i molnet.

När börjat med Media Services är det viktigt att förstå skillnaden mellan codec och filformat.
Codec-rutiner är programvara som implementerar komprimering/dekomprimering algoritmer filformat är behållare som innehåller den komprimerad videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt innehåll MP4 eller Smooth Streaming-kodade med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver packa om till dessa strömningsformat.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. Dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md), måste du göra följande:
>
>Dessutom koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet eller Smooth Streaming-filer (de kodningsstegen senare i den här självstudiekursen).

Media Services stöder följande på begäran kodare som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Den här artikeln ger en kort översikt över på begäran mediet kodare och innehåller länkar till artiklar som ger mer detaljerad information. Avsnittet innehåller också jämförelse av kodare.

>[!NOTE]
>Som standard har varje Media Services-konto en aktiv kodning aktivitet i taget. Du kan reservera kodning enheter som du kan ha flera kodning aktiviteter som körs samtidigt, ett för varje kodning reserverad enhet som du köper. Mer information finns i [skalning kodning enheter](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Hur du ska använda detta
[Koda med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Format
[Format och codec](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Förinställningar
Media Encoder Standard konfigureras med hjälp av en kodare förinställningar beskrivs [här](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Indata och utdata metadata
Inkommande metadata kodare beskrivs [här](media-services-input-metadata-schema.md).

Kodare utdata metadata beskrivs [här](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generera miniatyrbilder
Mer information finns i [så att generera miniatyrbilder med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trim videor (urklippet)
Mer information finns i [så trim videor med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Skapa överlägg
Mer information finns i [hur du skapar överlägg med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Se även
[Media Services-blogg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Arbetsflöde för Media Encoder Premium
### <a name="overview"></a>Översikt
[Introduktion till Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Hur du ska använda detta
Arbetsflöde för Media Encoder Premium konfigureras med hjälp av komplexa arbetsflöden. Arbetsflödesfiler kunde skapas och uppdateras med den [Arbetsflödesdesignern](media-services-workflow-designer.md) verktyget.

[Hur du använder Premium kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Kända problem
Om din indatavideo inte innehåller innehåller dold textning utdata tillgången fortfarande en tom TTML-fil.


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodning uppgifter genom att anpassa Media Encoder Standard förinställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
