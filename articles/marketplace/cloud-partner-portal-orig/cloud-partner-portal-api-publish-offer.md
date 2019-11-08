---
title: Publicera ett erbjudande | Azure Marketplace
description: API för att publicera det angivna erbjudandet.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819640"
---
<a name="publish-an-offer"></a>Publicera ett erbjudande
================

Startar publicerings processen för det angivna erbjudandet. Det här anropet är en tids krävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Utgivar-ID, till exempel `contoso`      |   Sträng       |
|  OfferId       | Erbjudande-ID                                 |   Sträng       |
|  API-version   | Senaste versionen av API: et                        |   Date         |
|  |  |


<a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Body-exempel
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

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Åtgärds plats    | URL som kan användas för att avgöra åtgärdens aktuella status.    |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Rikt** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-begäran har godkänts. Svaret innehåller en plats som kan användas för att spåra den åtgärd som startas. |
| 400   | `Bad/Malformed request` – fel svars texten kan innehålla mer information.                                                               |
| 422   | `Un-processable entity`-anger att entiteten som ska publiceras inte kunde verifieras.                                                        |
| 404   | `Not found`-enheten som anges av klienten finns inte.                                                                              |
|  |  |
