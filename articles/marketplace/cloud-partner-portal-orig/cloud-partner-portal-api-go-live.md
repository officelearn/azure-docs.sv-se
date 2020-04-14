---
title: Gå Live | Azure Marketplace
description: Go Live API initierar erbjudandet live notering processen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256322"
---
# <a name="go-live"></a>Gå Live

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Det här API:et startar processen för att driva en app till produktion. Den här åtgärden är vanligtvis tidskrävande. Det här anropet använder e-postlistan för meddelanden från åtgärden [Publicera](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Publisher-identifierare för erbjudandet att hämta, till exempel`contoso`       |  Sträng       |
| offerId (erbjudandeId)        | Erbjudandeidentifierare för erbjudandet att hämta                                   |  Sträng       |
| api-version    | Senaste versionen av API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exempel på brödtext

### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Location    |  Den relativa sökvägen för att hämta åtgärdens status            |
|  |  |

### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Begäran har accepterats. Svaret innehåller en plats för att spåra operationsstatusen. |
|  400     | `Bad/Malformed request`- Ytterligare felinformation finns i svarstexten. |
|  404     |  `Not found`- Den angivna entiteten finns inte.                                       |
|  |  |
