---
title: Registrera dig för Video Indexer och ladda upp din första video – Azure
titleSuffix: Azure Media Services
description: Lär dig hur du registrerar dig och laddar upp din första video med hjälp av Video Indexer-portalen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: 22b06ce99333750d48d1cb65d9f60779572723b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499628"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Snabbstart: Så här registrerar du dig och laddar upp din första video

Den här snabbstarten visar hur du loggar in på videoindexerarens webbplats och hur du laddar upp din första video.

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med betalningsalternativet skapar du ett videoindexerkonto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras samt kostnader relaterade till Azure Media Services-kontot. 

## <a name="sign-up-for-video-indexer"></a>Registrera dig för Video Indexer

När du vill börja utveckla med Video Indexer går du till [Video Indexer](https://www.videoindexer.com)-webbplatsen och registrerar dig.

> [!NOTE]
> När du börjar använda Video Indexer krypteras alla lagrade data och uppladdat innehåll i vila med en Hanterad Microsoft-nyckel.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Ladda upp en video med hjälp av Video Indexer-webbplatsen

### <a name="supported-file-formats-for-video-indexer"></a>Filformat som stöds för Video Indexer

Se artikeln [för indatabehållaren/filformaten](../latest/media-encoder-standard-formats.md#input-containerfile-formats) för en lista över filformat som du kan använda med Video Indexer.

### <a name="upload-a-video"></a>Ladda upp en video

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen.
2. Ladda upp en video genom att trycka på knappen eller länken **Ladda upp**.

    > [!NOTE]
    > Namnet på videon får inte vara större än 80 tecken.

    ![Ladda upp](./media/video-indexer-get-started/video-indexer-upload.png)

    När videon har laddats upp påbörjar Video Indexer indexering och analys av videon.

    ![Uppladdad](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    När Video Indexer är klar med analysen får du ett meddelande med en länk till videon och en kort beskrivning av vad som hittades i videon. Till exempel: personer, ämnen och OCR.

## <a name="see-also"></a>Se även

Mer information [finns i Ladda upp och indexera videor.](upload-index-videos.md)

När du har laddat upp och indexerat en video kan du börja använda [Video Indexer-webbplatsen](video-indexer-view-edit.md) eller [Video Indexer Developer Portal](video-indexer-use-apis.md) för att se videons insikter. 

[Börja använda API:er](video-indexer-use-apis.md)

## <a name="next-steps"></a>Nästa steg

För detaljerad introduktion besök vårt [introduktionslabb.](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) 

I slutet av workshopen kommer du att ha en god förståelse för vilken typ av information som kan extraheras från video- och ljudinnehåll, du kommer att vara mer förberedd för att identifiera möjligheter relaterade till innehållsinformation, pitch video AI på Azure och demo flera scenarier på Video Indexer.

