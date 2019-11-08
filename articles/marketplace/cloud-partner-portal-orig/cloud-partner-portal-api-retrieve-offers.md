---
title: 'Hämta API: er för erbjudandet | Azure Marketplace'
description: API hämtar en sammanfattande lista med erbjudanden under ett utgivar namn område.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819630"
---
<a name="retrieve-offers"></a>Hämta erbjudanden
===============

Hämtar en summerad lista med erbjudanden under ett utgivar namn område.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-parametrar
--------------

| **Namn**         |  **Beskrivning**                         |  **Datatyp** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Utgivar-ID, till exempel `contoso` |   Sträng    |
|  API-version     | Senaste versionen av API                    |    Date        |
|  |  |


<a name="header"></a>Huvud
------

|  **Namn**        |         **Värde**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Auktorisering   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Body-exempel
------------

### <a name="response"></a>Svar

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Egenskaper för svars text

|  **Namn**       |       **Beskrivning**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifierar typen av erbjudande                                                                                           |
|  publisherId    | Identifierare som unikt identifierar utgivaren                                                                      |
|  status         | Status för erbjudandet. En lista över möjliga värden finns i [erbjudande status](#offer-status) nedan.                         |
|  id             | GUID som unikt identifierar erbjudandet i utgivarens namnrymd.                                                    |
|  version        | Aktuell version av erbjudandet. Det går inte att ändra versions egenskapen av klienten. Den ökar efter varje publicering. |
|  Definition     | Innehåller en summerad vy av den faktiska definitionen av arbets belastningen. Om du vill ha en detaljerad definition använder du API: et [Hämta särskilda erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | UTC-tid när erbjudandet senast ändrades                                                                              |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Rikt**  |  **Beskrivning**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-begäran har bearbetats och alla erbjudanden under utgivaren returnerades till klienten.  |
|  400      | `Bad/Malformed request` – fel svars texten kan innehålla mer information.                                    |
|  403      | `Forbidden`-klienten har inte åtkomst till den angivna namn rymden.                                          |
|  404      | `Not found`-den angivna entiteten finns inte.                                                                 |
|  |  |


### <a name="offer-status"></a>Erbjudande status

|  **Namn**                    | **Beskrivning**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Erbjudandet har aldrig publicerats.                  |
|  NotStarted                  | Erbjudandet är nytt men har inte startats.                 |
|  WaitingForPublisherReview   | Erbjudandet väntar på godkännande av utgivare.         |
|  Körs                     | Överföring av erbjudande bearbetas.             |
|  Lyckades                   | Bearbetningen av erbjudandet har slutförts.       |
|  Avbrutna                    | Överföring av erbjudande avbröts.                   |
|  Misslyckad                      | Det gick inte att skicka erbjudandet.                         |
|  |  |
