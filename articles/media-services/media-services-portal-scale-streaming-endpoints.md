---
title: "Skala strömningsslutpunkter med Azure-portalen | Microsoft Docs"
description: "Den här självstudiekursen vägleder dig genom stegen för skalning strömningsslutpunkter med Azure-portalen."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: 156c5d0b007e33a9181d2847fc7b517c6f816494
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skala direktuppspelande slutpunkter med Azure-portalen
## <a name="overview"></a>Översikt

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

**Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om typer av slutpunkter och CDN konfiguration finns i [Strömningsslutpunkt översikt](media-services-streaming-endpoints-overview.md) avsnittet.
 
Det här avsnittet visar hur du skalar en strömmande slutpunkt.

Mer information om priser finns i [Prisuppgifter för Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Skala slutpunkter för strömning

Om du vill ändra antalet enheter för strömning gör du följande:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I den **inställningar** väljer **Strömningsslutpunkter**.
3. Klicka på den strömningsslutpunkt som du vill skala. 

    > [!NOTE] 
    > Du kan skala **Premium** strömningsslutpunkter.

4. Flytta skjutreglaget för att ange antalet strömningsenheter.

    ![Strömmande slutpunkt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

