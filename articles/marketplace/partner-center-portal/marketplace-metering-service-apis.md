---
title: 'API: er för avläsning av tjänst – Microsoft Commercial Marketplace'
description: Med API för användnings händelser kan du generera användnings händelser för SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 159d2c60fc1fc5ad1f21f2b948208eaae0d06208
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857861"
---
# <a name="marketplace-metering-service-apis"></a>API:er för Marketplace Metering Service

Med API: et för användnings händelser kan du generera användnings händelser för en bestämd inköpt enhet. Begäran om användnings händelse hänvisar till den mätnings tjänst dimension som definieras av utgivaren när du publicerar erbjudandet.

## <a name="usage-event"></a>Användnings händelse

**Publicera**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Den version av åtgärden som ska användas för den här begäran. Den senaste API-versionen är 2018-08-31. |

*Begärandehuvuden:*

| Innehålls typ       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikt sträng värde för spårning av begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` | Unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`   | [Hämta JSON Web token (JWT) Bearer-token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Obs: när du gör en HTTP-begäran `Bearer` , används prefixet till den token som hämtades från den refererade länken. |

*Anmoda*

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
Felaktig begäran, data som saknas eller är ogiltiga eller har upphört att gälla

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
Felaktig begäran, data som saknas eller är ogiltiga eller har upphört att gälla

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kod: 409<br>
När vi tar emot användnings anropet för användnings resurs-ID: t och den effektiva användningen som redan finns. Svaret innehåller `additionalInfo` fält som innehåller information om det accepterade meddelandet.

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

## <a name="batch-usage-event"></a>Batch-användning-händelse

Med event API för batch-användning kan du generera användnings händelser för fler än en inköpt enhet samtidigt. Händelse förfrågningen för batch-användning hänvisar till den mätnings tjänst dimension som definieras av utgivaren när du publicerar erbjudandet.

>[!Note]
>Du kan registrera flera SaaS-erbjudanden på Microsofts kommersiella marknads plats. Varje registrerat SaaS-erbjudande har ett unikt Azure AD-program som har registrerats för autentiserings-och behörighets syfte. De händelser som genereras i batch ska tillhöra erbjudanden med samma Azure AD-program vid tidpunkten för registreringen av erbjudandet.

**Publicera:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Den version av åtgärden som ska användas för den här begäran. Den senaste API-versionen är 2018-08-31. |

*Begärandehuvuden:*

| Innehålls typ       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikt sträng värde för spårning av begäran från klienten, helst en GUID. Om det här värdet inte anges genereras ett och anges i svarshuvuden. |
| `x-ms-correlationid` | Unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras ett och anges i svarshuvuden. |
| `authorization`      | [Hämta JSON Web token (JWT) Bearer-token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Obs: när du gör en HTTP-begäran `Bearer` , används prefixet till den token som hämtades från den refererade länken.  |

*Anmoda*
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

Beskrivning av status kod som refereras `BatchUsageEvent` i API-svar:

| Statuskod  | Description |
| ---------- | -------------------- |
| `Accepted` | Godkänd kod. |
| `Expired` | Användning har gått ut. |
| `Duplicate` | Duplicerad användning har angetts. |
| `Error` | Felkod. |
| `ResourceNotFound` | Den tillhandahållna användnings resursen är ogiltig. |
| `ResourceNotAuthorized` | Du har inte behörighet att tillhandahålla användning för den här resursen. |
| `InvalidDimension` | Dimensionen för vilken användningen skickas är ogiltig för det här erbjudandet/den här prenumerationen. |
| `InvalidQuantity` | Den överförda kvantiteten är < 0. |
| `BadArgument` | Inmatad information saknas eller är felaktig. |

Kod: 400<br>
Felaktig begäran, data som saknas eller är ogiltiga eller har upphört att gälla

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
Användaren har inte behörighet att göra detta samtal

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [SaaS-mätare för fakturering](./saas-metered-billing.md).
