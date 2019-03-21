---
title: Skala slutpunkter med Azure portal för direktuppspelning | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att skala slutpunkter för direktuppspelning med Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 23eb51428dcf4961febfb592bf957bb8beeeda57
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257215"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skala direktuppspelande slutpunkter med Azure-portalen
## <a name="overview"></a>Översikt

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

**Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om att strömma slutpunktstyper och CDN-konfiguration finns i den [Strömningsslutpunkt översikt](media-services-streaming-endpoints-overview.md) avsnittet.
 
Det här avsnittet visar hur du skalar en slutpunkt för direktuppspelning.

Mer information om priser finns i [Prisuppgifter för Media Services](https://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Skala slutpunkter för strömning

Om du vill ändra antalet enheter för strömning gör du följande:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I den **inställningar** väljer **slutpunkter för direktuppspelning**.
3. Klicka på slutpunkten för direktuppspelning som du vill skala. 

    > [!NOTE] 
    > Du kan skala **Premium** slutpunkter för direktuppspelning.

4. Flytta skjutreglaget för att ange antalet strömningsenheter.

    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

