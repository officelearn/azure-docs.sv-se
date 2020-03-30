---
title: Skicka autentiseringstoken till Azure Media Services | Microsoft-dokument
description: Lär dig hur du skickar autentiseringstoken från klienten till Azure Media Services nyckelleveranstjänst
services: media-services
keywords: innehållsskydd, DRM, tokenautentisering
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684937"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Lär dig hur klienter skickar token till Azure Media Services nyckelleveranstjänst
Kunder frågar ofta hur en spelare kan skicka token till Azure Media Services nyckelleveranstjänst för verifiering så att spelaren kan hämta nyckeln. Media Services stöder de enkla webbtokenformaten (SWT) och JSON Web Token (JWT). Tokenautentisering tillämpas på alla typer av nycklar, oavsett om du använder vanlig kryptering eller AES-kuvertkryptering (Advanced Encryption Standard) i systemet.

 Beroende på vilken spelare och plattform du riktar in dig på kan du skicka token med din spelare på följande sätt:

- Via HTTP-auktoriseringshuvudet.
    > [!NOTE]
    > Prefixet "Bärare" förväntas enligt OAuth 2.0-specifikationerna. En exempelspelare med tokenkonfigurationen finns på [demosidan](https://ampdemo.azureedge.net/)för Azure Media Player . Om du vill ställa in videokällan väljer du **AES (JWT Token)** eller **AES (SWT Token)**. Token skickas via auktoriseringshuvudet.

- Via tillägg av en URL-frågeparameter med "token=tokenvalue".  
    > [!NOTE]
    > Prefixet "Bärare" förväntas inte. Eftersom token skickas via en URL måste du pansar tokensträngen. Här är en C# exempelkod som visar hur man gör det:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Genom fältet CustomData.
Det här alternativet används endast för anskaffning av PlayReady-licenser via fältet CustomData i PlayReady License Acquisition Challenge. I det här fallet måste token finnas i XML-dokumentet enligt beskrivningen här:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Placera din autentiseringstoken i tokenelementet.

- Via en alternativ HLS-spellista (HTTP Live Streaming). Om du behöver konfigurera tokenautentisering för AES + HLS-uppspelning på iOS/Safari finns det inget sätt att skicka in token direkt. Mer information om hur du växlar spellistan för att aktivera det här scenariot finns i det här [blogginlägget](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
