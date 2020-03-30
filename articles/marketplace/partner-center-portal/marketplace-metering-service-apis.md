---
title: API:er för marknadstjänster för marknadsplatsmätning | Azure Marketplace
description: Användningshändelse för SaaS-erbjudanden på Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275755"
---
# <a name="marketplace-metering-service-apis"></a>API:er för Marketplace Metering Service

Med API:et för användningshändelse kan du avge användningshändelser för en viss inköpt entitet. Användningshändelsebegäran refererar till den mätartjänster dimension som definierats av utgivaren när erbjudandet publiceras.

## <a name="usage-event"></a>Användningshändelse

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Den version av åtgärden som ska användas för den här begäran. Senaste API-versionen är 2018-08-31. |

*Begär rubriker:*

| Innehållstyp       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| `x-ms-correlationid` | Unikt strängvärde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| `authorization`   | [Hämta JSON-webbtoken (JWT) innehavartoken.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) När HTTP-begäran skapas prefixet `Bearer` till den token som hämtats från den refererade länken. |

*Begäran:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Svar

Kod: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kod: 400 <br>
Felaktiga uppgifter, saknade eller ogiltiga data som lämnats eller har upphört att gälla

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>
Felaktiga uppgifter, saknade eller ogiltiga data som lämnats eller har upphört att gälla

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kod: 409<br>
Konflikt, när vi får användningsanropet för användningsresurs-ID och effektiv användning, som redan finns. Svaret innehåller `additionalInfo` ett fält som innehåller information om det accepterade meddelandet.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Händelse för batchanvändning

Med api:et för batchanvändningshändelse kan du avge användningshändelser för mer än en köpt entitet samtidigt. Händelsebegäran för batchanvändning refererar till den mätartjänster dimension som definierats av utgivaren när erbjudandet publiceras.

>[!Note]
>Du kan registrera flera SaaS-erbjudanden på Microsofts kommersiella marknadsplats. Varje registrerat SaaS-erbjudande har ett unikt Azure AD-program som är registrerat för autentisering och auktorisering. De händelser som skickas ut i batch bör tillhöra erbjudanden med samma Azure AD-program vid tidpunkten för registrering av erbjudandet.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Den version av åtgärden som ska användas för den här begäran. Senaste API-versionen är 2018-08-31. |

*Begär rubriker:*

| Innehållstyp       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| `x-ms-correlationid` | Unikt strängvärde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| `authorization`      | [Hämta JSON-webbtoken (JWT) innehavartoken.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) När HTTP-begäran skapas prefixet `Bearer` till den token som hämtats från den refererade länken.  |

*Begäran:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Svar

Kod: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Beskrivning av statuskod `BatchUsageEvent` som refereras i API-svar:

| Statuskod  | Beskrivning |
| ---------- | -------------------- |
| `Accepted` | Godkänd kod. |
| `Expired` | Utgångna användning. |
| `Duplicate` | Duplicerad användning tillhandahålls. |
| `Error` | Felkod. |
| `ResourceNotFound` | Den angivna användningsresursen är ogiltig. |
| `ResourceNotAuthorized` | Du har inte behörighet att tillhandahålla användning för den här resursen. |
| `InvalidDimension` | Den dimension som användningen skickas till är ogiltig för det här erbjudandet/planen. |
| `InvalidQuantity` | Den passerade kvantiteten är < 0. |
| `BadArgument` | Ingången saknas eller är felaktig. |

Kod: 400<br>
Felaktig begäran, saknade eller ogiltiga data som lämnats eller Utgånget

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kod: 403<br>
Användaren är obehörig att ringa det här samtalet

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Fakturering med saaS-mätning](./saas-metered-billing.md).
