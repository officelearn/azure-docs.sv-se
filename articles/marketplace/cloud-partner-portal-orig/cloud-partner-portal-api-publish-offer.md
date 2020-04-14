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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255948"
---
# <a name="publish-an-offer"></a>Publicera ett erbjudande

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Startar publiceringsprocessen för det angivna erbjudandet. Det här anropet är en tidskrävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Publisher-identifierare, till exempel`contoso`      |   Sträng       |
|  offerId (erbjudandeId)       | Identifierare för erbjudande                                 |   Sträng       |
|  api-version   | Senaste versionen av API                        |   Date         |
|  |  |

## <a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exempel på brödtext
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

### <a name="request-body-properties"></a>Egenskaper för begäran om brödtext

|  **Namn**               |   **Beskrivning**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  anmälan-e-post    | Kommaavgränsad lista över e-postadresser som ska meddelas om förloppet för publiceringen. |
|  |  |


### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | Den relativa sökvägen för att hämta åtgärdens status     |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Begäran har accepterats. Svaret innehåller en plats som kan användas för att spåra åtgärden som startas. |
| 400   | `Bad/Malformed request`- Felsvarstexten kan ge mer information.                                                               |
| 422   | `Un-processable entity`- Anger att den enhet som ska publiceras misslyckades validering.                                                        |
| 404   | `Not found`- Entiteten som anges av klienten finns inte.                                                                              |
|  |  |
