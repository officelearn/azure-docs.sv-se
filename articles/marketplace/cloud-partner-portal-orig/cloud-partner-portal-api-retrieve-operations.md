---
title: Hämta åtgärdens API | Microsoft Docs
description: Hämtar alla åtgärder på erbjudandet, eller för att hämta en viss åtgärd för den angivna åtgärds-ID.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a7666ada6c4535010297415eac8b0bd9e5226d9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094235"
---
<a name="retrieve-operations"></a>Hämta åtgärder
===================

Hämtar alla åtgärder på erbjudandet, eller för att hämta en viss åtgärd för den angivna åtgärds-ID. Klienten kan använda frågeparametrar för att filtrera på åtgärder.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**          |      **Beskrivning**                                                                                           | **Datatyp** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identifierare för utgivare, till exempel `Contoso`                                                                   |  String       |
|  offerId           |  Erbjudande-ID                                                                                              |  String       |
|  operationId       |  GUID som unikt identifierar åtgärden på erbjudandet. Åtgärds-ID kan hämtas med hjälp av den här API: et och även returneras i HTTP-huvud i svaret för långvariga åtgärder, till exempel den [publicera erbjudandet](./cloud-partner-portal-api-publish-offer.md) API.  |   Guid   |
|  filteredStatus    | Valfritt frågeparameter som används för att filtrera efter status (till exempel `running`) på den samling som returneras av den här API: et.  |   String |
|  API-versionen       | Senaste versionen av API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**          |  **Värde**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Auktorisering     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="response"></a>Svar

#### <a name="get-operations"></a>Hämta åtgärder

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

#### <a name="get-operation"></a>Åtgärd för hämtning

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


### <a name="response-body-properties"></a>Egenskaper för rapportinnehåll i svaret

|  **Namn**                    |  **Beskrivning**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID som unikt identifierar igen                                                       |
|  submissionType              | Identifierar typ av åtgärd som har rapporterats för erbjudandet, till exempel `Publish/GGoLive`      |
|  createdDateTime             | UTC-datum/tid när åtgärden har skapats                                                       |
|  lastActionDateTime          | UTC-datum/tid när den senaste uppdateringen gjordes på åtgärden                                       |
|  status                      | Status för åtgärden, antingen `not started` \| `running` \| `failed` \| `completed`. Endast en åtgärd kan ha status `running` i taget. |
|  fel                       | Felmeddelande för misslyckade åtgärder                                                               |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod**  |   **Beskrivning**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -Begäran har bearbetats och åtgärderna som begärt returnerades.        |
|  400      | `Bad/Malformed request` -Fel svarstexten kan innehålla mer information.                    |
|  403      | `Forbidden` -Klienten har inte åtkomst till det angivna namnområdet.                          |
|  404      | `Not found` -Den angivna entiteten finns inte.                                                 |
|  |  |
