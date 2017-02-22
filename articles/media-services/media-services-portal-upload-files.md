---
title: " Överföra filer till ett Media Services-konto med Azure Portal | Microsoft Docss"
description: "Den här kursen vägleder dig igenom steg som överför filer till ett Media Services-konto med Azure-portalen"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ed8ea30b87c8086d41cab879acce82062f08b31c
ms.openlocfilehash: f27ab42ab3c7c704804b9a5493c8b3acd954decb


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Överför filer till ett Media Services-konto med Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


I Media Services överför du dina digitala filer till en tillgång. Tillgång kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning (samt metadata om dessa filer.) När filerna har överförts lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning.


## <a name="upload-files"></a>Överföra filer

>[!NOTE]
>Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [det här](media-services-quotas-and-limitations.md) avsnittet.
>

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I bladet **Inställningar** klickar du på **Tillgångar**.
   
    ![Överföra filer](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Klicka på knappen **Överför**.
   
    Fönstret **Överför en videotillgång** visas.
   
   > [!NOTE]
   > Det finns inga filstorleksbegränsningar.
   > 
   > 
4. Bläddra till den önskade videon på datorn, markera den och tryck på OK.  
   
    Överföringen startar och du kan följa förloppet under filnamnet.  

När överföringen är klar visas den nya tillgången i listan **Tillgångar**. 

## <a name="next-steps"></a>Nästa steg
Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade behållaren. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


