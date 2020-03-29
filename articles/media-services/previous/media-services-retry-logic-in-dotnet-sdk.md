---
title: Återförsökslogik i Media Services SDK för .NET | Microsoft-dokument
description: Avsnittet innehåller en översikt över logiken för återförsök i Media Services SDK för .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094667"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Återförsökslogik i Media Services SDK för .NET  

När du arbetar med Microsoft Azure-tjänster kan tillfälliga fel uppstå. Om ett tillfälligt fel inträffar, i de flesta fall, efter några försök åtgärden lyckas. Media Services SDK för .NET implementerar logiken för återförsök för att hantera tillfälliga fel som är associerade med undantag och fel som orsakas av webbbegäranden, kör frågor, spara ändringar och lagringsåtgärder.  Som standard kör Media Services SDK för .NET fyra försök innan du återavstämer undantaget från ditt program. Koden i ditt program måste sedan hantera det här undantaget på rätt sätt.  

 Följande är en kort riktlinje för principer för webbbegäran, lagring, fråga och SaveChanges:  

* Lagringsprincipen används för blob-lagringsåtgärder (uppladdningar eller nedladdning av tillgångsfiler).  
* Principen för webbbegäran används för allmänna webbbegäranden (till exempel för att hämta en autentiseringstoken och lösa användarklusterslutpunkten).  
* Frågeprincipen används för att fråga entiteter från REST (till exempel mediaContext.Assets.Where(...)).  
* SaveChanges-principen används för att göra allt som ändrar data inom tjänsten (till exempel skapa en entitet som uppdaterar en entitet och anropar en tjänstfunktion för en operation).  
  
  I det här avsnittet visas undantagstyper och felkoder som hanteras av medietjänst-SDK för .NET-återförsökslogik.  

## <a name="exception-types"></a>Undantagstyper
I följande tabell beskrivs undantag som Media Services SDK för .NET hanterar eller inte hanterar för vissa åtgärder som kan orsaka tillfälliga fel.  

| Undantag | Webbbegäran | Lagring | Söka i data | SparaChanges |
| --- | --- | --- | --- | --- |
| WebException (WebException)<br/>Mer information finns i avsnittet [WebException-statuskoder.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Mer information finns i [HTTP-felstatuskoder](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceQueryException<br/> Mer information finns i [HTTP-felstatuskoder](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceRequestException<br/> Mer information finns i [HTTP-felstatuskoder](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceTransportUtställning |Inga |Inga |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Inga |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Inga |Ja |Inga |Inga |
| IOException |Inga |Ja |Inga |Inga |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Statuskoder för WebException
I följande tabell visas för vilka WebException-felkoder logiken för återförsök implementeras. [WebExceptionStatus-uppräkningen](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) definierar statuskoderna.  

| Status | Webbbegäran | Lagring | Söka i data | SparaChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NamnResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| SendFailure |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Inga |
| Anslutning Stängd |Ja |Ja |Ja |Inga |
| KeepAliveFailure |Ja |Ja |Ja |Inga |
| Okändare |Ja |Ja |Ja |Inga |
| ReceiveFailure |Ja |Ja |Ja |Inga |
| BegäranCanceled |Ja |Ja |Ja |Inga |
| Timeout |Ja |Ja |Ja |Inga |
| Protokollerror <br/>Återförsöket på ProtocolError styrs av http-statuskodhanteringen. Mer information finns i [HTTP-felstatuskoder](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ja |Ja |Ja |Ja |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>HTTP-felstatuskoder
När åtgärder för Query och SaveChanges genererar DataServiceClientException, DataServiceQueryException eller DataServiceQueryException returneras HTTP-felstatuskoden i egenskapen Statuskod för StatusCode.  I följande tabell visas för vilka felkoder logiken för återförsök implementeras.  

| Status | Webbbegäran | Lagring | Söka i data | SparaChanges |
| --- | --- | --- | --- | --- |
| 401 |Inga |Ja |Inga |Inga |
| 403 |Inga |Ja<br/>Hantera återförsök med längre väntetider. |Inga |Inga |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Inga |
| 502 |Ja |Ja |Ja |Inga |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Inga |

Om du vill ta en titt på den faktiska implementeringen av medietjänst-SDK för .NET-återförsökslogik läser du [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

