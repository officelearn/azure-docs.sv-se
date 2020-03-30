---
title: Publicera ett erbjudande | Azure Marketplace
description: API för att publicera det angivna erbjudandet.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280532"
---
<a name="publish-an-offer"></a>Publicera ett erbjudande
================

Startar publiceringsprocessen för det angivna erbjudandet. Det här anropet är en tidskrävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Publisher-identifierare, till exempel`contoso`      |   String       |
|  offerId (erbjudandeId)       | Identifierare för erbjudande                                 |   String       |
|  api-version   | Senaste versionen av API                        |   Datum         |
|  |  |


<a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exempel på brödtext
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

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-plats    | URL som kan efterfrågas för att fastställa åtgärdens aktuella status.    |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Begäran har accepterats. Svaret innehåller en plats som kan användas för att spåra åtgärden som startas. |
| 400   | `Bad/Malformed request`- Felsvarstexten kan ge mer information.                                                               |
| 422   | `Un-processable entity`- Anger att den enhet som ska publiceras misslyckades validering.                                                        |
| 404   | `Not found`- Entiteten som anges av klienten finns inte.                                                                              |
|  |  |
