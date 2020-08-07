---
title: Felsök Azure Cosmos DBs tjänsten otillgängligt undantag
description: Så här diagnostiserar och åtgärdar du Cosmos DB tjänst ej tillgängligt undantag
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987383"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostisera och Felsök Azure Cosmos DB tjänsten är inte tillgänglig
SDK kunde inte ansluta till Azure Cosmos DB tjänsten.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för otillgängliga tjänst undantag.

### <a name="1-the-required-ports-are-being-blocked"></a>1. de portar som krävs blockeras
Kontrol lera att alla [nödvändiga portar](performance-tips-dotnet-sdk-v3-sql.md#networking) är aktiverade.

### <a name="2-client-side-transient-connectivity-issues"></a>2. problem med tillfälliga anslutningar på klient Sidan
Den otillgängliga undantags ytan för tjänsten kan användas när det finns tillfälliga anslutnings problem som orsakar tids gränser. Ofta innehåller stack spårningen som är relaterad till det här scenariot en `TransportException` , till exempel:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Följ [fel söknings tids gränsen för begäran](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) för att lösa problemet.

### <a name="3-service-outage"></a>3. avbrott i tjänsten
Kontrol lera [Azure-statusen](https://status.azure.com/status) för att se om det finns ett pågående problem.


## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)