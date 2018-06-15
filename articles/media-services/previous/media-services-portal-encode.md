---
title: Koda en tillgång med Media Encoder Standard i Azure portal | Microsoft Docs
description: Den här självstudiekursen vägleder dig genom stegen för att koda en tillgång med Media Encoder Standard i Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 470eb8613416f441c1becee628acf3c898591c84
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790225"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Koda en tillgång med Media Encoder Standard i Azure-portalen

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

En av de vanligaste scenarierna arbetar med Azure Media Services levererar strömning till dina klienter med anpassningsbar bithastighet. Media Services stöder följande anpassningsbar bithastighet tekniker för strömning: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och dynamiska anpassningsbar strömning via HTTP (TANKSTRECK, kallas även MPEG-DASH). För att förbereda dina videor för strömning med anpassad bithastighet, koda videon källa som filer med multibithastighet. Du kan använda Azure Media Encoder Standard för att koda dina videor.  

Media Services ger dig dynamisk paketering. Med dynamisk paketering behöver leverera du din multibithastighet MP4s i HLS, Smooth Streaming och MPEG-DASH utan ompaketering i dessa strömningsformat. När du använder dynamisk paketering, kan du lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämplig respons baserat på en klientbegäran.

Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. De kodningsstegen senare i den här artikeln.

Om du vill lära dig mer om att skala media bearbetning, se [skala media bearbetning med hjälp av Azure portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koda i Azure-portalen

Att koda ditt innehåll med Media Encoder Standard:

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
* [Övervaka förloppet för din kodningsjobbet](media-services-portal-check-job-progress.md) i Azure-portalen.  

