---
title: Logik för omprövning i Media Services SDK för .NET | Microsoft Docs
description: Avsnittet ger en översikt över logik för omprövning i Media Services SDK för .NET.
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
ms.date: 10/24/2018
ms.author: juliako
ms.openlocfilehash: 0a4c9db8da046e901241bc383098013b2acc6bb2
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242270"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logik för omprövning i Media Services SDK för .NET
När du arbetar med Microsoft Azure-tjänster, kan tillfälliga fel uppstå. Om ett tillfälligt fel inträffar oftast när du har några få återförsök lyckas åtgärden. Media Services SDK för .NET implementerar logik för omprövning för att hantera tillfälliga fel som är associerade med undantag och fel som orsakas av webbegäranden, köra frågor, sparar ändringar och lagringsåtgärder.  Som standard körs Media Services SDK för .NET fyra återförsök innan nytt utlöste undantaget i ditt program. Koden i programmet måste sedan hantera det här undantaget korrekt.  

 Här följer en kort riktlinje webbegäran, lagring, fråga och SaveChanges principer:  

* Principen för lagring används för åtgärder för blob storage (överföringar eller hämtning av tillgångsfiler).  
* Princip för webb-begäran används för allmänna webbegäranden (till exempel för att hämta en token för autentisering och lösa klusterslutpunkten användare).  
* Fråga principen används för att fråga entiteter från REST (till exempel mediaContext.Assets.Where(...)).  
* SaveChanges principen används för att göra något som ändrar data i tjänsten (till exempel skapa en entitet som uppdaterar en entitet, anropa en tjänstfunktion för en åtgärd).  
  
  Det här avsnittet listar undantagstyper och logik för omprövning av felkoder som hanteras av Media Services SDK för .NET.  

## <a name="exception-types"></a>Undantagstyper
I följande tabell beskrivs undantag som Media Services SDK för .NET hanterar eller hanterar inte för vissa åtgärder som kan orsaka tillfälliga fel.  

| Undantag | Webbegäran | Storage | Söka i data | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Mer information finns i den [WebException statuskoder](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) avsnittet. |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceQueryException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceRequestException<br/> Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nej |Ja |Ja |Ja |
| DataServiceTransportException |Nej |Nej |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Nej |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Nej |Ja |Nej |Nej |
| IOException |Nej |Ja |Nej |Nej |

### <a name="WebExceptionStatus"></a> WebException statuskoder
I följande tabell visas vilka WebException felkoder omprövningslogiken tillämpas. Den [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) uppräkning definierar statuskoder.  

| Status | Webbegäran | Storage | Söka i data | SaveChanges |
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
| Protokollfel <br/>Återförsök på protokollfel styrs av HTTP-status kod hantering. Mer information finns i [statuskoder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ja |Ja |Ja |Ja |

### <a name="HTTPStatusCode"></a> Statuskoder för HTTP-fel
När frågan och SaveChanges kasta DataServiceClientException, DataServiceQueryException eller DataServiceQueryException, returneras HTTP-statuskoden för felet i egenskapen StatusCode.  I följande tabell visas vilka felkoder omprövningslogiken tillämpas.  

| Status | Webbegäran | Storage | Söka i data | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nej |Ja |Nej |Nej |
| 403 |Nej |Ja<br/>Hanterar återförsök med längre väntar. |Nej |Nej |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Nej |
| 502 |Ja |Ja |Ja |Nej |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Nej |

Om du vill ta en titt på den faktiska implementeringen av Media Services SDK för .NET logik för omprövning, se [azure-sdk-för-– medietjänster](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

