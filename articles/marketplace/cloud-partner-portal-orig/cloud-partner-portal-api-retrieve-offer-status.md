---
title: Hämta erbjudandestatus | Azure Marketplace
description: API hämtar aktuell status för erbjudandet.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2f5211716145d6c05bbfb0132c4a6ba2f9cceabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280515"
---
<a name="retrieve-offer-status"></a>Hämta erbjudandestatus 
=====================

Hämtar erbjudandets aktuella status.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**       |   **Beskrivning**                            |  **Datatyp** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Publisher-identifierare, till exempel`Contoso`  |     String     |
|  offerId (erbjudandeId)        | GUID som unikt identifierar erbjudandet      |     String     |
|  api-version    | Senaste versionen av API                        |     Datum       |
|  |  |


<a name="header"></a>Huvud
------

|  Namn           |  Värde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Auktorisering  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exempel på brödtext
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


### <a name="response-body-properties"></a>Egenskaper för svarstext

|  **Namn**             |    **Beskrivning**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Erbjudandets status. Information om listan över möjliga värden finns i [Erbjudandestatus](#offer-status) nedan. |
|  meddelanden             | Matris med meddelanden som är associerade med erbjudandet                                                    |
|  steg                | Matris över de steg som erbjudandet går igenom under en erbjudandepublicering                      |
|  uppskattadTimeFrame   | Uppskattning av den tid det skulle ta att slutföra detta steg, i vänligt format                       |
|  id                   | Identifierare för steget                                                                         |
|  stepName (stegnamn)             | Stegets namn                                                                               |
|  description          | Beskrivning av steget                                                                        |
|  status               | Stegets status. En lista över möjliga värden finns i [Stegstatus](#step-status) nedan.    |
|  meddelanden             | Matris med meddelanden relaterade till steget                                                          |
|  processPercentage    | Procentuellt slutförande av steget                                                              |
|  previewLinks         | *Inte implementerad för tillfället*                                                                    |
|  liveLinks            | *Inte implementerad för tillfället*                                                                    |
|  anmälanMeddelanden   | Kommaavgränsad lista över e-postadresser som ska meddelas om hur åtgärden fortskrider        |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod** |   **Beskrivning**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- Begäran har bearbetats och erbjudandets aktuella status returnerades. |
|  400     | `Bad/Malformed request`- Felsvarstexten kan innehålla mer information.                 |
|  404     | `Not found`- Den angivna entiteten finns inte.                                                |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                    |    **Beskrivning**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Aldrigpublicerat              | Erbjudandet har aldrig publicerats.                          |
|  Inte startad                  | Erbjudandet är nytt och inte startat.                            |
|  Väntar påpublicerAreView   | Erbjudandet väntar på utgivarens godkännande.                 |
|  Körs                     | Inlämning av erbjudande bearbetas.                     |
|  Lyckades                   | Erbjudandet inlämning har slutfört behandlingen.               |
|  Avbrutna                    | Inlämningen av erbjudandet avbröts.                           |
|  Misslyckades                      | Erbjudandet misslyckades.                                 |
|  |  |


### <a name="step-status"></a>Stegstatus

|  **Namn**                    |    **Beskrivning**                           |
|  -------------------------   |  ------------------------------------------  |
|  Inte startad                  | Steget har inte startat.                        |
|  InProgress (InProgress)                  | Steget är igång.                             |
|  Väntar påpublicerAreView   | Steget väntar på utgivarens godkännande.      |
|  Väntar På Väntar På          | Steget väntar på processgodkännande.        |
|  Blockerad                     | Steget är blockerat.                             |
|  Avvisad                    | Steget avvisas.                            |
|  Slutför                    | Steget är klart.                            |
|  Avbrutna                    | Steget avbröts.                           |
|  |  |
