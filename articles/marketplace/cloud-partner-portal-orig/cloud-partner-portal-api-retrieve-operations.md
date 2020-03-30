---
title: Api för hämta åtgärder | Azure Marketplace
description: Hämtar alla åtgärder som erbjuds eller för att få en viss åtgärd för den angivna operationId.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 4fc77407ae1c5854d3fe977da5a81f4226bf5305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280481"
---
<a name="retrieve-operations"></a>Hämta åtgärder
===================

Hämtar alla åtgärder som erbjuds eller för att få en viss åtgärd för den angivna operationId. Klienten kan använda frågeparametrar för att filtrera på åtgärder som körs.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**          |      **Beskrivning**                                                                                           | **Datatyp** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Publisher-identifierare, till exempel`Contoso`                                                                   |  String       |
|  offerId (erbjudandeId)           |  Identifierare för erbjudande                                                                                              |  String       |
|  operationId       |  GUID som unikt identifierar åtgärden på erbjudandet. OperationId kan hämtas med hjälp av det här API:et och returneras även i HTTP-huvudet för svaret för alla tidskrävande åtgärder, till exempel [API:et för publiceringserbjudande.](./cloud-partner-portal-api-publish-offer.md)  |   GUID   |
|  filtreradStatus    | Valfri frågeparameter som används för `running`att filtrera efter status (till exempel ) i samlingen som returneras av det här API:et.  |   String |
|  api-version       | Senaste versionen av API                                                                                           |    Datum      |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**          |  **Värde**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Auktorisering     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exempel på brödtext
------------

### <a name="response"></a>Svar

#### <a name="get-operations"></a>GET-åtgärder

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET-åtgärd

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Egenskaper för svarstext

|  **Namn**                    |  **Beskrivning**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID som unikt identifierar åtgärden                                                       |
|  inlämningTyp              | Identifierar vilken typ av åtgärd som rapporteras för erbjudandet, till exempel`Publish/GGoLive`      |
|  createdDateTime             | UTC-datumtid när åtgärden skapades                                                       |
|  lastActionDateTime          | UTC-datumtid när den senaste uppdateringen gjordes på åtgärden                                       |
|  status                      | Åtgärdens status, `not started` \| `running` \| `failed` \| `completed`antingen . Endast en åtgärd `running` kan ha status åt gången. |
|  fel                       | Felmeddelande för misslyckade åtgärder                                                               |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  |   **Beskrivning**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- Begäran har bearbetats och de begärda operationerna returnerades.        |
|  400      | `Bad/Malformed request`- Felsvarstexten kan innehålla mer information.                    |
|  403      | `Forbidden`- Klienten har inte åtkomst till det angivna namnområdet.                          |
|  404      | `Not found`- Den angivna entiteten finns inte.                                                 |
|  |  |
