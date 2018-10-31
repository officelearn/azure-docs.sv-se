---
title: Översikt över och jämförelse av Azure på begäran media kodare | Microsoft Docs
description: Det här avsnittet ger en översikt och jämförelse av Azure på begäran media kodare.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 6695e17ec3dd9bf9ebeeb44b34e9688d3e5a1625
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249755"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Översikt över och jämförelse av Azure på begäran media kodare
## <a name="encoding-overview"></a>Kodning – översikt
Azure Media Services erbjuder flera alternativ för kodning av media i molnet.

När du börjar med Media Services, är det viktigt att förstå skillnaden mellan codec- och filformat.
Codec är program som implementerar komprimering/dekomprimering algoritmer filformat är behållare som innehåller komprimerade videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera innehåll med anpassad bithastighet MP4 eller Smooth Streaming-kodade i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver packa om till dessa strömningsformat.

> [!NOTE]
> När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Media Services stöder följande på begäran-kodare som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Den här artikeln ger en kort översikt över på begäran media kodare och innehåller länkar till artiklar som ger mer detaljerad information. Avsnittet innehåller också jämförelse av kodarna.

>[!NOTE]
>Som standard kan varje Media Services-konto ha en aktiv kodning aktivitet i taget. Du kan reservera kodningsenheter så att du kan ha flera kodningsuppgifter körs samtidigt, en för varje kodning reserverad enhet som du köper. Mer information finns i [skalning kodningsenheter](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Hur du ska använda detta
[Koda med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Format
[Format och -codec](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Förinställningar
Media Encoder Standard konfigureras med hjälp av en kodare-förinställningar beskrivs [här](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Inkommande och utgående metadata
Kodare indatametadata beskrivs [här](media-services-input-metadata-schema.md).

Kodare utgående metadata beskrivs [här](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generera miniatyrer
Mer information finns i [generera miniatyrer med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Beskär videor (urklippet)
Mer information finns i [hur du beskär videoklipp med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Skapa överlägg
Mer information finns i [så här skapar du överlägg med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Se också
[Media Services-blogg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Arbetsflöde för Media Encoder Premium
### <a name="overview"></a>Översikt
[Vi presenterar Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Hur du ska använda detta
Media Encoder Premium Workflow har konfigurerats med hjälp av komplexa arbetsflöden. Arbetsflödesfiler kunde skapas och uppdateras med den [Arbetsflödesdesigner](media-services-workflow-designer.md) verktyget.

[Hur du använder Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Kända problem
Om din indatavideo inte innehåller innehåller textning, utdata tillgången fortfarande en tom TTML-fil.


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utför avancerad kodningsuppgifter genom att anpassa förinställningar för Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
