---
title: Go Live | Azure Marketplace
description: 'Go Live API: t initierar bearbetnings processen för Live-erbjudandet.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256322"
---
# <a name="go-live"></a>Gå live

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Detta API startar processen för att skicka en app till produktion. Den här åtgärden körs vanligt vis längre. Det här anropet använder e-postlistan för meddelanden från [publicerings](./cloud-partner-portal-api-publish-offer.md) -API-åtgärden.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Utgivarens identifierare för erbjudandet att hämta, till exempel`contoso`       |  Sträng       |
| offerId        | Erbjudande-ID för erbjudandet som ska hämtas                                   |  Sträng       |
| api-version    | Senaste versionen av API: et                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Body-exempel

### <a name="response"></a>Svar

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Plats    |  Den relativa sökvägen för att hämta den här åtgärdens status            |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-Begäran har godkänts. Svaret innehåller en plats för att spåra åtgärdens status. |
|  400     | `Bad/Malformed request`-Ytterligare fel information finns i svars texten. |
|  404     |  `Not found`-Den angivna entiteten finns inte.                                       |
|  |  |
