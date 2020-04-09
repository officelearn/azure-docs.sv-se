---
title: Skala slutpunkter för direktuppspelning med Azure-portalen | Microsoft-dokument
description: Den här självstudien går igenom stegen för att skala slutpunkter för direktuppspelning med Azure-portalen.
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
ms.openlocfilehash: 40820170bae275f090c5f898387698fc562e59ae
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985549"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skala direktuppspelande slutpunkter med Azure-portalen
## <a name="overview"></a>Översikt

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

**Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om slutpunktstyper och CDN-konfiguration finns i översiktsavsnittet för slutpunkt för [direktuppspelning.](media-services-streaming-endpoints-overview.md)
 
Det här avsnittet visar hur du skalar en slutpunkt för direktuppspelning.

Mer information om priser finns i [Prisuppgifter för Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Skala slutpunkter för direktuppspelning

Så här ändrar du antalet strömningsenheter:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Slutpunkter för direktuppspelning**i fönstret **Inställningar** .
3. Klicka på den slutpunkt för direktuppspelning som du vill skala. 

    > [!NOTE] 
    > Du kan bara skala **Slutpunkter** för premiumstreaming.

4. Flytta skjutreglaget för att ange antalet strömningsenheter.

    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

