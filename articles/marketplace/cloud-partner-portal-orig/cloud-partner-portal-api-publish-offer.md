---
title: Publicera ett erbjudande | Azure Marketplace
description: API för att publicera det angivna erbjudandet.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255948"
---
# <a name="publish-an-offer"></a>Publicera ett erbjudande

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Startar publicerings processen för det angivna erbjudandet. Det här anropet är en tids krävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Utgivar identifierare, till exempel`contoso`      |   Sträng       |
|  offerId       | Erbjudande-ID                                 |   Sträng       |
|  api-version   | Senaste versionen av API: et                        |   Date         |
|  |  |

## <a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Body-exempel
------------

### <a name="request"></a>Förfrågan

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Egenskaper för begär ande brödtext

|  **Namn**               |   **Beskrivning**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  meddelande – e-post    | Kommaavgränsad lista med e-postadresser som ska meddelas om förloppet för publicerings åtgärden. |
|  |  |


### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Plats    | Den relativa sökvägen för att hämta den här åtgärdens status     |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-Begäran har godkänts. Svaret innehåller en plats som kan användas för att spåra den åtgärd som startas. |
| 400   | `Bad/Malformed request`– Fel svars texten kan innehålla mer information.                                                               |
| 422   | `Un-processable entity`-Anger att entiteten som ska publiceras misslyckades valideringen.                                                        |
| 404   | `Not found`-Den entitet som anges av klienten finns inte.                                                                              |
|  |  |
