---
title: "Försök logiken i Media Services SDK för .NET | Microsoft Docs"
description: "Avsnittet ger en översikt över logik i Media Services SDK för .NET."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 859dd76db4ba06196a853469a1385703d835fa22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Försök logiken i Media Services SDK för .NET
När du arbetar med Microsoft Azure-tjänster, kan tillfälliga problem uppstå. Om ett tillfälligt fel uppstår i de flesta fall efter några försök slutförs åtgärden. Media Services SDK för .NET implementerar logik för omprövning för att hantera tillfälliga problem som är associerade med undantag och fel som orsakas av webbegäranden, köra frågor, sparar ändringar och lagringsåtgärder.  Som standard kör Media Services SDK för .NET fyra återförsök innan nytt undantaget i tillämpningsprogrammet. Koden i ditt program måste sedan hantera undantaget korrekt.  

 Följande är en kort riktlinje webbegäran, lagring, fråga och SaveChanges principer:  

* Storage-principen används för blob storage-åtgärder (överföring eller hämtning av tillgångsinformation filer).  
* Webbegäran principen används för allmänna webbegäranden (till exempel för att hämta någon autentiseringstoken och lösa klusterslutpunkten användare).  
* Frågan principen används för att fråga entiteter från VILOLÄGE (till exempel mediaContext.Assets.Where(...)).  
* SaveChanges principen används för att göra något som ändrar data i tjänsten (till exempel skapa en entitet som uppdaterar en entitet som anropar en tjänst för en åtgärd).  
  
  Det här avsnittet beskriver typer av undantag och felkoder som hanteras av Media Services SDK för .NET försök logik.  

## <a name="exception-types"></a>Typer av undantag
I följande tabell beskrivs undantag som Media Services SDK för .NET hanterar eller hanteras inte för vissa åtgärder som kan orsaka tillfälliga problem.  

| Undantag | Webbegäran | Lagring | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Mer information finns i [WebException statuskoder](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) avsnitt. |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceQueryException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceRequestException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceTransportException |Nej |Nej |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Nej |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Nej |Ja |Nej |Nej |
| IOException |Nej |Ja |Nej |Nej |

### <a name="WebExceptionStatus"></a>Statuskoder för WebException
Följande tabell visar för vilka WebException felkoder logik för omprövning implementeras. Den [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) uppräkning definierar statuskoder.  

| Status | Webbegäran | Lagring | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NameResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| SendFailure |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Nej |
| ConnectionClosed |Ja |Ja |Ja |Nej |
| KeepAliveFailure |Ja |Ja |Ja |Nej |
| UnknownError |Ja |Ja |Ja |Nej |
| ReceiveFailure |Ja |Ja |Ja |Nej |
| RequestCanceled |Ja |Ja |Ja |Nej |
| Timeout |Ja |Ja |Ja |Nej |
| Protokollfel <br/>Försök igen på protokollfel styrs av HTTP-status kod hantering. Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ja |Ja |Ja |Ja |

### <a name="HTTPStatusCode"></a>Statuskoder för HTTP-fel
När frågan och SaveChanges kasta DataServiceClientException, DataServiceQueryException eller DataServiceQueryException, returneras HTTP-statuskoden för felet i egenskapen StatusCode.  Följande tabell visar för vilka felkoder logik för omprövning implementeras.  

| Status | Webbegäran | Lagring | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nej |Ja |Nej |Nej |
| 403 |Nej |Ja<br/>Hanterar återförsök med längre väntar. |Nej |Nej |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Nej |
| 502 |Ja |Ja |Ja |Nej |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Nej |

Om du vill ta en titt på den faktiska implementeringen av Media Services SDK för .NET logik finns [azure-sdk-för-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

