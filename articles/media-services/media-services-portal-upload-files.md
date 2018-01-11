---
title: "Ladda upp filer till ett Media Services-konto på Azure-portalen | Microsoft Docs"
description: "Den här självstudiekursen beskriver steg för steg hur du laddar upp filer till ett Media Services-konto på Azure-portalen."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 7ddfe44918b358a1749640d1c93dba490855cc5a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Ladda upp filer till ett Media Services-konto på Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

I Azure Media Services laddar du upp digitala filer till en tillgång. Tillgången kan innehålla video, ljud, bilder, samlingar med miniatyrbilder, textspår och filer med dold textning (samt metadata för dessa filer). När filerna har laddats upp lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

> [!NOTE]
> Media Services har en maximal filstorlek för bearbetning av filer. Mer information om storleksgränser för filer finns i avsnittet om [kvoter och begränsningar för Media Services](media-services-quotas-and-limitations.md).
>

## <a name="upload-files"></a>Överföra filer
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar** > **Tillgångar**. Välj sedan knappen **Ladda upp**.
   
    ![Överföra filer](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Fönstret **Överför en videotillgång** visas.
   
   > [!NOTE]
   > Media Services begränsar inte filstorleken för uppladdning av videor.
 
3. På datorn går du till den video som du vill ladda upp. Välj videon och klicka på **OK**.  
   
    Uppladdningen startar. Du kan följa förloppet under filnamnet.  

När uppladdningen är klar visas den nya tillgången i fönstret **Tillgångar**. 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [kodar dina uppladdade tillgångar](media-services-portal-encode.md).

* Du kan också använda Azure Functions om du vill att ett kodningsjobb ska köras automatiskt när den konfigurerade behållaren tar emot en fil. Mer information finns i exemplet i [Media Services: Integrera Azure Media Services med Azure Functions och Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).


