---
title: Gå Live | Azure Marketplace
description: Go Live API initierar erbjudandet live notering processen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288588"
---
<a name="go-live"></a>Gå Live
=======

Det här API:et startar processen för att driva en app till produktion. Den här åtgärden är vanligtvis tidskrävande. Det här anropet använder e-postlistan för meddelanden från åtgärden [Publicera](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Publisher-identifierare för erbjudandet att hämta, till exempel`contoso`       |  String       |
| offerId (erbjudandeId)        | Erbjudandeidentifierare för erbjudandet att hämta                                   |  String       |
| api-version    | Senaste versionen av API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exempel på brödtext
------------

### <a name="response"></a>Svar

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Operation-plats    |  URL till fråga för att fastställa åtgärdens aktuella status            |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Begäran har accepterats. Svaret innehåller en plats för att spåra operationsstatusen. |
|  400     | `Bad/Malformed request`- Ytterligare felinformation finns i svarstexten. |
|  404     |  `Not found`- Den angivna entiteten finns inte.                                       |
|  |  |
