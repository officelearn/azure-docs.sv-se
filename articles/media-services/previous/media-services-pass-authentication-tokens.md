---
title: Skicka autentiseringstoken till Azure Media Services | Microsoft Docs
description: Lär dig hur du skickar autentiseringstoken från klienten till Azure Media Services viktiga tjänsten
services: media-services
keywords: innehållsskydd DRM, token-autentisering
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: b6aca2928465b73e35ac15f01bb776b1f69add0b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Lär dig hur klienter skickar token till Azure Media Services viktiga tjänsten
Kunder be ofta hur en spelare kan skicka token till Azure Media Services viktiga tjänsten för verifiering så spelaren kan hämta nyckeln. Media Services stöder enkel web token (SWT) och JSON-Webbtoken (JWT)-format. Token-autentisering används på någon typ av nyckel, oavsett om du använder vanlig kryptering eller Advanced Encryption Standard (AES) kuvert kryptering i systemet.

 Beroende på player och plattform som du riktar kan skicka du token med spelaren på följande sätt:

- Via HTTP Authorization-huvud.
    > [!NOTE]
    > Prefixet ”ägar” förväntas per OAuth 2.0-specifikationerna. En exempel-spelare med token konfiguration finns på Azure Media Player [demo sidan](http://ampdemo.azureedge.net/). Om du vill ange videokällan väljer **AES (JWT-Token)** eller **AES (SWT Token)**. Token som skickas via Authorization-huvud.

- Fråga genom att lägga till en URL-parametern med ”token = tokenvalue”.  
    > [!NOTE]
    > Prefixet ”ägar” förväntades inte. Du måste skydda token strängen eftersom token som skickas via en URL. Här är en C# exempelkod som visar hur du gör det:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Fältet CustomData.
Det här alternativet används för endast PlayReady-licenser via fältet CustomData för PlayReady licens förvärv utmaning. I det här fallet måste token vara i XML-dokumentet som beskrivs här:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Placera din autentiseringstoken i elementet Token.

- Via en annan HTTP Live Streaming (HLS) spelningslista. Om du behöver konfigurera tokenautentisering för AES + HLS uppspelning på iOS/Safari är ett sätt som du kan skicka direkt i token. Mer information om hur du alternativ spelningslista om du vill aktivera det här scenariot finns [blogginlägget](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]