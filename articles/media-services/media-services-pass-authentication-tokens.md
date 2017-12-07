---
title: Skicka autentiseringstoken till Azure Media Services | Microsoft Docs
description: "Lär dig att skicka autentiseringstoken från klienten till tjänsten för Azure Media Services viktiga leverans"
services: media-services
keywords: "innehållsskydd DRM, token-autentisering"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Hur klienter skickar token till Azure Media Services viktiga delivery service
Vi får frågor kring hur en spelare kunde skicka token till våra leveranstjänster av nyckel som kommer få verifieras hela tiden och media player erhåller nyckeln. Vi stöder enkel Web Token (SWT) och JSON-Webbtoken (JWT) dessa två token format. Tokenautentisering kunde tillämpas på alla typer av nyckeln – oavsett du gör vanlig kryptering eller AES envelope kryptering i systemet.

Dessa finns på följande sätt som du kan skicka token med spelaren, är beroende av player och plattform som du utvecklar för:
- Via HTTP Authorization-huvud.
> [!NOTE]
> Observera att prefixet ”ägar” förväntas per OAuth 2.0-specifikationerna. Det finns en exempel-spelare med Token konfiguration finns i Azure Media Player [demo sidan](http://ampdemo.azureedge.net/). Välj AES (JWT-Token) eller AES (SWT Token) för att ange videokällan. Token har skickats via Authorization-huvud.

- Genom att lägga till en Url-frågan parameter med ”token = tokenvalue”.  
> [!NOTE]
> Observera att inget prefix ”ägar” förväntas. Eftersom token som skickas via en URL, behöver du skydda token strängen. Här är ett C#-kodexempel om hur du gör det:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- Via CustomData fält.
För PlayReady licens förvärv, via fältet CustomData för PlayReady licens förvärv utmaning. I det här fallet måste token vara i xml-dokumentet som beskrivs nedan.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Placera din autentiseringstoken i den <Token> element.

- Via en annan HLS spelningslista. Om du behöver konfigurera Tokenautentisering för AES + HLS uppspelning på iOS/Safari är ett sätt som du kan skicka direkt i token. Finns [blogginlägget](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) om hur du alternativ spelningslista om du vill aktivera det här scenariot.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]