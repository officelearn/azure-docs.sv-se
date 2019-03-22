---
title: Koda en tillgång med Media Encoder Standard i Azure portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att koda en tillgång med Media Encoder Standard i Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258487"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Koda en tillgång med Media Encoder Standard i Azure portal

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ett av de vanligaste scenarierna i att arbeta med Azure Media Services leverera strömning med anpassad bithastighet till dina klienter. Media Services stöder de följande strömningstekniker med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och Dynamic Adaptive Streaming över HTTP (DASH, även kallad MPEG-DASH). För att förbereda dina videor för strömning med anpassad bithastighet, koda källvideon som filer med flera bithastigheter. Du kan använda Azure Media Encoder Standard för att koda dina videor.  

Media Services ger dig dynamisk paketering. Med dynamisk paketering kan du leverera dina MP4 i multibithastighet MP4s i HLS, Smooth Streaming och MPEG-DASH, utan ompaketering i dessa strömningsformat. När du använder dynamisk paketering, kan du lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och ger lämplig respons baserat på en klientbegäran.

Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. De kodningsstegen senare i den här artikeln.

Om du vill lära dig mer om att skala mediebearbetning, se [skala mediebearbetning med hjälp av Azure portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koda i Azure portal

Att koda ditt innehåll med hjälp av Media Encoder Standard:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar** > **Tillgångar**. Välj den tillgång som du vill koda.
3. Välj knappen **Koda**.
4. I fönstret **Koda en tillgång** väljer du processorn **Media Encoder Standard** och en förinställning. Information om förinställningar finns i [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Autogenerera en bithastighetsstege) och [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Uppgiftsförinställningar för Media Encoder Standard). Det är viktigt att välja den förinställning som fungerar bäst för indatavideon. Om du till exempel vet att indatavideon har en upplösning på 1 920 &#215; 1 080 bildpunkter kan du välja förinställningen **H264 multibithastighet 1080p**. Om du har en video med låg upplösning (640 &#215; 360) ska du inte använda förinställningen **H264 multibithastighet 1080p**.
   
   Du kan underlätta hanteringen av resurserna genom att redigera namnet på utdatatillgången och namnet på jobbet.
   
   ![Koda tillgångar](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Välj **Skapa**.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Övervaka förloppet för ditt kodningsjobb](media-services-portal-check-job-progress.md) i Azure-portalen.  

