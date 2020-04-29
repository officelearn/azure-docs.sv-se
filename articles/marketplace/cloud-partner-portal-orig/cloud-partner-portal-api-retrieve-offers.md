---
title: 'Hämta API: er för erbjudandet | Azure Marketplace'
description: API hämtar en sammanfattande lista med erbjudanden under ett utgivar namn område.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255965"
---
<a name="retrieve-offers"></a>Hämta erbjudanden
===============

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Hämtar en summerad lista med erbjudanden under ett utgivar namn område.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

| **Namn**         |  **Beskrivning**                         |  **Datatyp** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Utgivar identifierare, till exempel`contoso` |   Sträng    |
|  api-version     | Senaste versionen av API                    |    Date        |
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
|  definition     | Innehåller en summerad vy av den faktiska definitionen av arbets belastningen. Om du vill ha en detaljerad definition använder du API: et [Hämta särskilda erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | UTC-tid när erbjudandet senast ändrades                                                                              |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Kod**  |  **Beskrivning**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-Begäran har bearbetats och alla erbjudanden under utgivaren returnerades till klienten.  |
|  400      | `Bad/Malformed request`– Fel svars texten kan innehålla mer information.                                    |
|  403      | `Forbidden`-Klienten har inte åtkomst till den angivna namn rymden.                                          |
|  404      | `Not found`-Den angivna entiteten finns inte.                                                                 |
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
|  Misslyckades                      | Det gick inte att skicka erbjudandet.                         |
|  |  |
