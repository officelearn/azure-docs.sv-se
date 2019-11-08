---
title: Go Live | Azure Marketplace
description: 'Go Live API: t initierar bearbetnings processen för Live-erbjudandet.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819685"
---
<a name="go-live"></a>Gå live
=======

Detta API startar processen för att skicka en app till produktion. Den här åtgärden körs vanligt vis längre. Det här anropet använder e-postlistan för meddelanden från [publicerings](./cloud-partner-portal-api-publish-offer.md) -API-åtgärden.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Utgivarens identifierare för erbjudandet att hämta, till exempel `contoso`       |  Sträng       |
| OfferId        | Erbjudande-ID för erbjudandet som ska hämtas                                   |  Sträng       |
| API-version    | Senaste versionen av API: et                                                   |  Date         |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Body-exempel
------------

### <a name="response"></a>Svar

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Åtgärds plats    |  URL att fråga för att fastställa aktuell status för åtgärden            |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Rikt** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-begäran har godkänts. Svaret innehåller en plats för att spåra åtgärdens status. |
|  400     | `Bad/Malformed request` – ytterligare fel information finns i svars texten. |
|  404     |  `Not found`-den angivna entiteten finns inte.                                       |
|  |  |
