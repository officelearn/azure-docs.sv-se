---
title: Koda en tillgång med hjälp av Media Encoder Standard i Azure-portalen | Microsoft-dokument
description: Den här självstudien går igenom stegen för kodning av en tillgång med hjälp av Media Encoder Standard i Azure-portalen.
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
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69542608"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Koda en tillgång med hjälp av Media Encoder Standard i Azure-portalen

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)av Azure . 
> 
> 

Ett av de vanligaste scenarierna i arbetet med Azure Media Services är att leverera adaptiv bithastighetsstreaming till dina klienter. Media Services stöder följande adaptiva bithastighetsstreamingteknik: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och Dynamic Adaptive Streaming over HTTP (DASH, även kallad MPEG-DASH). Om du vill förbereda dina videor för adaptiv bithastighetsstreaming kodar du först källvideon som multibitrate-filer. Du kan använda Media Encoder Standard för att koda dina videor.  

Media Services ger dig dynamisk paketering. Med dynamiska förpackningar kan du leverera dina multibitrate MP4-enheter i HLS, Smooth Streaming och MPEG-DASH, utan att paketera om i dessa streamingformat. När du använder dynamisk förpackning kan du lagra och betala för filerna i ett lagringsformat. Media Services skapar och betjänar rätt svar baserat på en klients begäran.

Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. Kodningsstegen visas senare i den här artikeln.

Mer information om hur du skalar mediebearbetning finns i [Skala mediebearbetning med hjälp av Azure-portalen](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koda i Azure-portalen

Så här kodar du innehållet med Media Encoder Standard:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningars** > **tillgångar**. Välj den tillgång som du vill koda.
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

