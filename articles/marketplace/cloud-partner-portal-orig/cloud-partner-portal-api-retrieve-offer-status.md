---
title: Hämta erbjudande status | Azure Marketplace
description: API hämtar aktuell status för erbjudandet.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819668"
---
<a name="retrieve-offer-status"></a>Hämta erbjudandestatus 
=====================

Hämtar aktuell status för erbjudandet.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**       |   **Beskrivning**                            |  **Datatyp** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Utgivar-ID, till exempel `Contoso`  |     Sträng     |
|  OfferId        | GUID som unikt identifierar erbjudandet      |     Sträng     |
|  API-version    | Senaste versionen av API                        |     Date       |
|  |  |


<a name="header"></a>Huvud
------

|  Namn           |  Värde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Auktorisering  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Body-exempel
------------

### <a name="response"></a>Svar

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Egenskaper för svars text

|  **Namn**             |    **Beskrivning**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Status för erbjudandet. En lista över möjliga värden finns i [erbjudande status](#offer-status) nedan. |
|  meddelanden             | Matris med meddelanden som är associerade med erbjudandet                                                    |
|  steg                | Matris med de steg som erbjudandet går igenom under publiceringen av erbjudandet                      |
|  estimatedTimeFrame   | Uppskattning av hur lång tid det tar att slutföra det här steget, i eget format                       |
|  id                   | Identifierare för steget                                                                         |
|  stepName             | Namn på steget                                                                               |
|  description          | Beskrivning av steget                                                                        |
|  status               | Status för steget. En lista över möjliga värden finns i [steg status](#step-status) nedan.    |
|  meddelanden             | Matris med meddelanden som är relaterade till steget                                                          |
|  processPercentage    | Procent slutfört för steget                                                              |
|  previewLinks         | *Inte implementerad för närvarande*                                                                    |
|  liveLinks            | *Inte implementerad för närvarande*                                                                    |
|  notificationEmails   | Kommaavgränsad lista över e-postadresser som ska meddelas om åtgärdens förlopp        |
|  |  |


### <a name="response-status-codes"></a>Svars status koder

| **Rikt** |   **Beskrivning**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`-begäran har bearbetats och aktuell status för erbjudandet returnerades. |
|  400     | `Bad/Malformed request` – fel svars texten kan innehålla mer information.                 |
|  404     | `Not found`-den angivna entiteten finns inte.                                                |
|  |  |


### <a name="offer-status"></a>Erbjudande status

|  **Namn**                    |    **Beskrivning**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Erbjudandet har aldrig publicerats.                          |
|  NotStarted                  | Erbjudandet är nytt och har inte startats.                            |
|  WaitingForPublisherReview   | Erbjudandet väntar på godkännande av utgivare.                 |
|  Körs                     | Överföring av erbjudande bearbetas.                     |
|  Lyckades                   | Bearbetningen av erbjudandet har slutförts.               |
|  Avbrutna                    | Överföring av erbjudande avbröts.                           |
|  Misslyckad                      | Det gick inte att skicka erbjudandet.                                 |
|  |  |


### <a name="step-status"></a>Steg status

|  **Namn**                    |    **Beskrivning**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Steget har inte startats.                        |
|  Pågår                  | Steget körs.                             |
|  WaitingForPublisherReview   | Steget väntar på godkännande av utgivare.      |
|  WaitingForApproval          | Steget väntar på process godkännande.        |
|  Blockeras                     | Steget är blockerat.                             |
|  Slagit                    | Steget avvisades.                            |
|  Slutför                    | Steget har slutförts.                            |
|  Avbrutna                    | Steget avbröts.                           |
|  |  |
