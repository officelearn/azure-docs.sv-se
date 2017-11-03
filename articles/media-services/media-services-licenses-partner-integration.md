---
title: "Med hjälp av partner för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRMs. PlayReady-licens kommer från licensserver för Media Services PlayReady och Widevine-licens levereras med castLabs licensservern."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 6867e4f910970121df3858516c6bab3114c3c6f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd partners för att leverera Widevine-licenser till Azure Media Services
## <a name="overview"></a>Översikt
Microsoft Azure Media Services kan du leverera MPEG-DASH skyddas med Widevine DRM som krypteras enligt specifikationen vanliga kryptering (CENC).

Från och med Media Services .NET SDK version 3.5.2 kan Media Services du konfigurera Widevine-licensmall och hämta Widevine-licenser. Du kan också använda följande AMS-partner för hjälp med att leverera Widevine-licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) och [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Du kan använda [castLabs](http://castlabs.com/company/partners/azure/) att leverera Widevine-licenser. Mer information finns i [med castLabs för att leverera DRM licenser till Azure Media Services](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
Du kan använda [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) att leverera Widevine-licenser. Mer information finns i [med Axinom att leverera DRM-licenser till Azure Media Services](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Använda PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-drm.md)

[Mingfeis blogg](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

