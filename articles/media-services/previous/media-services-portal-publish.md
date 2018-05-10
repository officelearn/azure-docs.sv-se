---
title: Publicera innehåll i Azure portal | Microsoft Docs
description: Den här självstudiekursen vägleder dig genom stegen för att publicera ditt innehåll i Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: d93bfc548134f730f4fad49a37593c861d6b6cbb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="publish-content-in-the-azure-portal"></a>Publicera innehåll i Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Översikt
> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

För att ge användaren en URL som kan användas för att strömma eller hämta innehållet måste du först publicera tillgången genom att skapa en lokaliserare. Lokaliserare ger åtkomst till tillgångsfiler. Azure Media Services stöder två typer av lokaliserare: 

* **Strömningslokaliserare (OnDemandOrigin)**. Strömningslokaliserare används för anpassad strömning. Exempel på anpassningsbar strömning är Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och dynamiska anpassningsbar strömning via HTTP (TANKSTRECK, kallas även MPEG-DASH). Om du vill skapa en strömningslokaliserare måste tillgången innehålla en .ism-fil. Till exempel http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Progressiv lokaliserare (signatur för delad åtkomst)**. Progressiva lokaliserare används för att leverera video via progressiv nedladdning.

Lägg till *(format = m3u8 aapl)* till URL:en för att skapa en HLS-strömnings-URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Om du vill skapa en strömnings-URL som ska spela upp Smooth Streaming-tillgångar använder du följande URL-format:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Om du vill skapa en strömnings-URL för MPEG-DASH lägger du till *(format=mpd-time-csf)* i URL:en:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

En URL för signatur för delad åtkomst har följande format:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Mer information finns i [leverera Innehållsöversikt](media-services-deliver-content-overview.md).

> [!NOTE]
> Lokaliserare som har skapats i Azure-portalen före mars 2015 har ett utgångsdatum på två år.  
> 
> 

Om du vill uppdatera ett utgångsdatum för en positionerare kan använda en [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) eller en [.NET API](http://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> URL:en ändras när du uppdaterar utgångsdatumet för en SAS-lokaliserare.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Använda portalen för att publicera en tillgång
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar** > **Tillgångar**. Välj den tillgång som du vill publicera.
3. Välj sedan knappen **Publicera**.
4. Välj typ av lokaliserare.
5. Välj **Lägg till**.
   
    ![Publicera videon](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL:en läggs till i listan över **publicerade URL:er**.

## <a name="play-content-in-the-portal"></a>Spela upp innehåll i portalen
Du kan testa videon på en innehållsspelare i Azure-portalen.

Välj videon och välj sedan knappen **Spela upp**.

![Spela upp videon i Azure-portalen](./media/media-services-portal-vod-get-started/media-services-play.png)

Vissa förutsättningar gäller:

* Kontrollera att videon har publicerats.
* Azure-portalens mediaspelare spelar upp från den slutpunkt för direktuppspelning som är standard. Klicka för att kopiera URL:en och klistra in den i en annan spelare om du vill spela upp från en slutpunkt för direktuppspelning som inte är standard. Du kan till exempel testa videon i [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* Den strömningsslutpunkt från vilken du strömning måste köras.  

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

