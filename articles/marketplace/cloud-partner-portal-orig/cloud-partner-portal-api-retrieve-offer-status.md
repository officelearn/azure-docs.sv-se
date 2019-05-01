---
title: Hämta status för erbjudande | Azure Marketplace
description: 'API: et hämtar aktuell status för erbjudandet.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 26ee8b5b1879c505f8200671558fe065ace068a3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935459"
---
<a name="retrieve-offer-status"></a>Hämta erbjudandestatus 
=====================

Hämtar den aktuella statusen för erbjudandet.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**       |   **Beskrivning**                            |  **Datatyp** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identifierare för utgivare, till exempel `Contoso`  |     String     |
|  offerId        | GUID som unikt identifierar erbjudandet      |     String     |
|  API-versionen    | Senaste versionen av API                        |     Date       |
|  |  |


<a name="header"></a>Huvud
------

|  Namn           |  Värde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Auktorisering  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Brödtext exempel
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


### <a name="response-body-properties"></a>Egenskaper för rapportinnehåll i svaret

|  **Namn**             |    **Beskrivning**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Status för erbjudandet. Listan över möjliga värden, se [erbjuder status](#offer-status) nedan. |
|  meddelanden             | Matris med meddelanden som är associerade med erbjudandet                                                    |
|  steg                | Matris med de steg som erbjudandet går igenom vid ett erbjudande-publicering                      |
|  estimatedTimeFrame   | Uppskattning av tid det tar för att slutföra det här steget i egna format                       |
|  id                   | Identifierare för steget                                                                         |
|  stepName             | Namn på steget                                                                               |
|  description          | Beskrivning av steget                                                                        |
|  status               | Status för steget. Listan över möjliga värden, se [steg status](#step-status) nedan.    |
|  meddelanden             | Matris med meddelanden som är relaterade till steg                                                          |
|  processPercentage    | Procentsats slutförandet av steget                                                              |
|  previewLinks         | *Inte implementerat*                                                                    |
|  liveLinks            | *Inte implementerat*                                                                    |
|  notificationEmails   | Kommaavgränsad lista över e-postadresser för att aviseras om förloppet för åtgärden        |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod** |   **Beskrivning**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -Begäran har bearbetats och aktuell status för erbjudandet returnerades. |
|  400     | `Bad/Malformed request` -Fel svarstexten kan innehålla mer information.                 |
|  404     | `Not found` -Den angivna entiteten finns inte.                                                |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                    |    **Beskrivning**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Erbjudandet har inte publicerats.                          |
|  NotStarted                  | Erbjudandet är nytt och inte startad.                            |
|  WaitingForPublisherReview   | Erbjudande väntar på godkännande av utgivaren.                 |
|  Körs                     | Erbjud bidrag bearbetas.                     |
|  Lyckades                   | Erbjudandet bidrag har bearbetat.               |
|  Avbrutna                    | Erbjudandet överföringen avbröts.                           |
|  Misslyckad                      | Det gick inte att skicka för erbjudandet.                                 |
|  |  |


### <a name="step-status"></a>Steg-Status

|  **Namn**                    |    **Beskrivning**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Steg har inte startat.                        |
|  Pågår                  | Steget körs.                             |
|  WaitingForPublisherReview   | Steget väntar på godkännande av utgivaren.      |
|  WaitingForApproval          | Steget väntar på godkännande av processen.        |
|  Blockerad                     | Steg blockeras.                             |
|  Avvisad                    | Steg avvisas.                            |
|  Slutför                    | Steg har slutförts.                            |
|  Avbrutna                    | Steg avbröts.                           |
|  |  |
