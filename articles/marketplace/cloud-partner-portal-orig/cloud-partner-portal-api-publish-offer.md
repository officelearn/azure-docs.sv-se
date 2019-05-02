---
title: Publicera ett erbjudande | Azure Marketplace
description: API för att publicera det angivna erbjudandet.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934982"
---
<a name="publish-an-offer"></a>Publicera ett erbjudande
================

Startar publiceringsprocessen för det angivna erbjudanden. Det här anropet är en tidskrävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifierare för utgivare, till exempel `contoso`      |   String       |
|  offerId       | Erbjudande-ID                                 |   String       |
|  API-versionen   | Senaste versionen av API: et                        |   Date         |
|  |  |


<a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Brödtext exempel
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

### <a name="request-body-properties"></a>Begära egenskaper för rapportinnehåll

|  **Namn**               |   **Beskrivning**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  e-postaviseringar    | Kommaavgränsad lista över e-postadresser för att aviseras om förloppet för publiceringsåtgärden. |
|  |  |


### <a name="response"></a>Svar

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL som kan efterfrågas för att fastställa den aktuella statusen för åtgärden.    |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Begäran har accepterats. Svaret innehåller en plats som kan användas för att spåra den åtgärd som har startats. |
| 400   | `Bad/Malformed request` -Fel svarstexten kan ge mer information.                                                               |
| 422   | `Un-processable entity` -Anger att entitet som ska publiceras misslyckades verifieringen.                                                        |
| 404   | `Not found` -Enheten som anges av klienten finns inte.                                                                              |
|  |  |
