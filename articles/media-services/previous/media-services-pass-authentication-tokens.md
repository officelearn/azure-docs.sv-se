---
title: Skicka autentiseringstoken till Azure Media Services | Microsoft Docs
description: Lär dig hur du skickar autentiseringstoken från klienten till Azure Media Services-nyckelleveranstjänst
services: media-services
keywords: Content protection DRM, tokenautentisering
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
ms.date: 03/19/2019
ms.author: dwgeo
ms.openlocfilehash: 71925a1ee67956df45901950b2a59fa4c1b458a7
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260095"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Lär dig hur klienter skickar token till Azure Media Services-nyckelleveranstjänst
Kunder frågar ofta hur en spelare kan skicka token till Azure Media Services-nyckelleveranstjänst för verifiering så spelaren kan hämta nyckeln. Media Services stöder simple webbtoken (SWT) och JSON Web Token (JWT)-format. Tokenautentisering tillämpas på alla typer av nyckel, oavsett om du använder vanlig kryptering eller Advanced Encryption Standard (AES) kuvert kryptering i systemet.

 Beroende på player och plattform som du riktar kan skicka du token med din player på följande sätt:

- Via HTTP auktoriseringsrubriken.
    > [!NOTE]
    > Prefixet ”ägar” förväntas per OAuth 2.0-specifikationer. En exempel-spelare med konfigurationen finns på Azure Media Player [Demonstrationssida](https://ampdemo.azureedge.net/). Om du vill ange videokällan väljer **AES (JWT-Token)** eller **AES (SWT Token)**. Token skickas via auktoriseringsrubriken.

- Via tillägg av en URL-frågeparameter med ”token = tokenvalue”.  
    > [!NOTE]
    > Prefixet ”ägar” är inte förväntades. Du måste skydda token strängen eftersom token som skickas via en URL. Här är en C# exempelkod som visar hur du gör det:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via CustomData-fält.
Det här alternativet används för PlayReady-licenser, via fältet CustomData för PlayReady licens förvärv utmaning. I det här fallet måste token vara i XML-dokumentet enligt nedan:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Placera din autentiseringstoken i Token-elementet.

- Via en annan spelningslista HTTP Live Streaming (HLS). Om du behöver konfigurera autentisering med enhetstoken för AES + HLS uppspelning på iOS/Safari, inte ett sätt som du kan skicka direkt i token. Mer information om hur du alternativ till spellistan för att aktivera det här scenariot finns i den här [blogginlägget](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]