---
title: Publicera ett erbjudande – Azure Marketplace
description: API för att publicera det angivna erbjudandet.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: e3bc420a60c514e704a6caa38acee155b4981552
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115595"
---
# <a name="publish-an-offer"></a>Publicera ett erbjudande

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Startar publicerings processen för det angivna erbjudandet. Det här anropet är en tids krävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Utgivar identifierare, till exempel`contoso`      |   Sträng       |
|  offerId       | Erbjudande-ID                                 |   Sträng       |
|  api-version   | Senaste versionen av API: et                        |   Datum         |
|  |  |

## <a name="header"></a>Sidhuvud
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
| Location    | Den relativa sökvägen för att hämta den här åtgärdens status     |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-Begäran har godkänts. Svaret innehåller en plats som kan användas för att spåra den åtgärd som startas. |
| 400   | `Bad/Malformed request`– Fel svars texten kan innehålla mer information.                                                               |
| 422   | `Un-processable entity`-Anger att entiteten som ska publiceras misslyckades valideringen.                                                        |
| 404   | `Not found`-Den entitet som anges av klienten finns inte.                                                                              |
|  |  |
