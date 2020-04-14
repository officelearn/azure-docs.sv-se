---
title: API för avbryt åtgärd | Azure Marketplace
description: Avbryt åtgärder .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256441"
---
# <a name="cancel-operation"></a>Avbryt åtgärd

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Det här API:et avbryter en pågående åtgärd för erbjudandet. Använd [API:et](./cloud-partner-portal-api-retrieve-operations.md) hämta `operationId` åtgärder för att få ett att skicka till det här API:et. Annullering är vanligtvis en synkron åtgärd, men i vissa komplexa scenarier kan en ny åtgärd krävas för att avbryta en befintlig. I det här fallet innehåller HTTP-svarstexten åtgärdens plats som ska användas för att fråga status.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar

--------------

|  **Namn**    |      **Beskrivning**                                  |    **Datatyp**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Publisher-identifierare, till exempel`contoso`         |   Sträng          |
| offerId (erbjudandeId)      |  Identifierare för erbjudande                                     |   Sträng          |
| api-version  |  Aktuell version av API                               |    Date           |
|  |  |  |

## <a name="header"></a>Huvud
------

|  **Namn**              |  **Värde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Auktorisering         |  Bärare din token |
|  |  |

## <a name="body-example"></a>Exempel på brödtext
------------

### <a name="request"></a>Förfrågan

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Egenskaper för begäran om brödtext

|  **Namn**                |  **Beskrivning**                                               |
|  --------                |  ---------------                                               |
|  anmälan-e-post     | Kommaavgränsad lista över e-post-ID:er som ska meddelas om förloppet för publiceringsåtgärden. |
|  |  |

### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                       |
|  ---------            |    ----------                      |
| Location    | Den relativa sökvägen för att hämta åtgärdens status. |
|  |  |

### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  |  **Beskrivning**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okej. Begäran har bearbetats och åtgärden avbryts synkront. |
|  202      | Accepterade. Begäran har bearbetats och åtgärden håller på att avbrytas. Platsen för annulleringsåtgärden returneras i svarshuvudet. |
|  400      | Felaktig/felaktig begäran. Felsvarstexten kan ge mer information.  |
|  403      | Tillträde Förbjudet. Klienten har inte åtkomst till det namnområde som anges i begäran. |
|  404      | Hittades inte. Den angivna entiteten finns inte. |
|  |  |
