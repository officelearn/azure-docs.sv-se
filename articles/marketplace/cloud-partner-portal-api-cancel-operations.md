---
title: Avbryt åtgärds-API – Microsoft Commercial Marketplace
description: 'API: et för att avbryta en åtgärd som pågår för erbjudandet'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: anbene
ms.author: mingshen
ms.date: 06/16/2020
ms.openlocfilehash: e65f0a8ee0a5dfafab681010006fe190cb5bad70
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102760"
---
# <a name="cancel-operation"></a>Avbryt åtgärd

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Detta API avbryter en åtgärd som pågår för erbjudandet. Använd [API: er](./cloud-partner-portal-api-retrieve-operations.md) för att hämta för att `operationId` Skicka till det här API: et. Annullering är vanligt vis en synkron åtgärd, men i vissa komplexa scenarier kan det krävas en ny åtgärd för att avbryta en befintlig åtgärd. I det här fallet innehåller HTTP-svars texten den åtgärds plats som ska användas för att fråga om status.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar

--------------

|  **Namn**    |      **Beskrivning**                                  |    **Datatyp**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Utgivar identifierare, till exempel`contoso`         |   Sträng          |
| offerId      |  Erbjudande-ID                                     |   Sträng          |
| api-version  |  Aktuell version av API                               |    Datum           |
|  |  |  |

## <a name="header"></a>Sidhuvud
------

|  **Namn**              |  **Värde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Auktorisering         |  Förser din TOKEn |
|  |  |

## <a name="body-example"></a>Body-exempel
------------

### <a name="request"></a>Förfrågan

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Egenskaper för begär ande brödtext

|  **Namn**                |  **Beskrivning**                                               |
|  --------                |  ---------------                                               |
|  meddelande – e-post     | Kommaavgränsad lista med e-post-ID: n för att få ett meddelande om förloppet för publicerings åtgärden. |
|  |  |

### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                       |
|  ---------            |    ----------                      |
| Location    | Den relativa sökvägen för att hämta den här åtgärdens status. |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod**  |  **Beskrivning**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okej. Begäran har bearbetats och åtgärden avbröts synkront. |
|  202      | Accept. Begäran har bearbetats och åtgärden håller på att avbrytas. Platsen för avbrotts åtgärden returneras i svars huvudet. |
|  400      | Felaktig/felaktig begäran. Fel svars texten kan innehålla mer information.  |
|  403      | Åtkomst förbjuden. Klienten har inte åtkomst till det namn område som anges i begäran. |
|  404      | Hittades inte. Den angivna entiteten finns inte. |
|  |  |
