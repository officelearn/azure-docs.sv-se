---
title: Referens – Azure API Management resurs logg
description: Schema referens för resurs loggen för Azure API Management GatewayLogs
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380484"
---
# <a name="reference-api-management-resource-log-schema"></a>Referens: API Management resurs logg schema

Den här artikeln innehåller en schema referens för resurs loggen för Azure API Management GatewayLogs. Logg poster innehåller också fält i det [gemensamma schemat på den översta nivån](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema).

Information om hur du aktiverar insamling av resurs loggen i API Management finns i [övervaka publicerade API: er](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>GatewayLogs-schema

Följande egenskaper loggas för varje API-begäran.

| Egenskap  | Typ | Description |
| ------------- | ------------- | ------------- |
| metod | sträng | HTTP-metod för inkommande begäran |
| url | sträng | URL för inkommande begäran |
| responseCode | heltal | Statuskod för HTTP-svar som skickas till klient |
| responseSize | heltal | Antalet byte som har skickats till en klient under bearbetning av begäran | 
| cache | sträng | Status för API Management cache-inblandning i begär ande bearbetning (träff, missar, ingen) | 
| apiId | sträng | API-entitetsidentifierare för aktuell begäran | 
| operationId | sträng | Åtgärdsentitetsidentifierare för aktuell begäran | 
| clientProtocol | sträng | HTTP-protokollversion för inkommande begäran |
| clientTime | heltal | Antal millisekunder som ägnats åt övergripande klient-I/O (ansluta, skicka och ta emot byte) | 
| apiRevision | sträng | API-revision för aktuell begäran | 
| clientTlsVersion| sträng | TLS-version som används av klienten som skickar begäran |
| lastError | objekt | För en misslyckad begäran, information om det senaste fel vid begär ande bearbetning | 
| backendMethod | sträng | HTTP-metod för begäran som skickats till en serverdel |
| backendUrl | sträng | URL för begäran som skickats till en serverdel |
| backendResponseCode | heltal | Kod för HTTP-svaret togs emot av en serverdel |
| backedProtocol | sträng | HTTP-protokollversion för begäran som skickats till en serverdel |
| backendTime | heltal | Antal millisekunder som ägnats åt övergripande Server dels-IO (ansluta, skicka och ta emot byte) | 


## <a name="next-steps"></a>Nästa steg

* Information om övervakning av API: er i API Management finns i [övervaka publicerade API: er](api-management-howto-use-azure-monitor.md)
* Lär dig mer om [vanliga och tjänstspecifika scheman för Azures resurs loggar](../azure-monitor/platform/resource-logs-schema.md)

