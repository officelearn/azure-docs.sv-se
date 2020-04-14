---
title: Api för hämta åtgärder | Azure Marketplace
description: Hämtar alla åtgärder som erbjuds eller för att få en viss åtgärd för den angivna operationId.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255881"
---
# <a name="retrieve-operations"></a>Hämta åtgärder

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Hämtar alla åtgärder som erbjuds eller för att få en viss åtgärd för den angivna operationId. Klienten kan använda frågeparametrar för att filtrera på åtgärder som körs.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>URI-parametrar

|  **Namn**          |      **Beskrivning**                                                                                           | **Datatyp** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Publisher-identifierare, till exempel`Contoso`                                                                   |  Sträng       |
|  offerId (erbjudandeId)           |  Identifierare för erbjudande                                                                                              |  Sträng       |
|  operationId       |  GUID som unikt identifierar åtgärden på erbjudandet. OperationId kan hämtas med hjälp av det här API:et och returneras även i HTTP-huvudet för svaret för alla tidskrävande åtgärder, till exempel [API:et för publiceringserbjudande.](./cloud-partner-portal-api-publish-offer.md)  |   GUID   |
|  api-version       | Senaste versionen av API |    Date      |
|  |  |  |

## <a name="header"></a>Huvud


|  **Namn**          |  **Värde**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Auktorisering     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exempel på brödtext

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
            }
        }
    ]
```

### <a name="response-body-properties"></a>Egenskaper för svarstext

|  **Namn**                    |  **Beskrivning**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID som unikt identifierar åtgärden                                                       |
|  inlämningTyp              | Identifierar vilken typ av åtgärd som rapporteras för erbjudandet, till exempel`Publish/GoLive`      |
|  createdDateTime             | UTC-datumtid när åtgärden skapades                                                       |
|  lastActionDateTime          | UTC-datumtid när den senaste uppdateringen gjordes på åtgärden                                       |
|  status                      | Åtgärdens status, `not started` \| `running` \| `failed` \| `completed`antingen . Endast en åtgärd `running` kan ha status åt gången. |
|  fel                       | Felmeddelande för misslyckade åtgärder                                                               |
|  |  |

### <a name="response-step-properties"></a>Egenskaper för svarssteg

|  **Namn**                    |  **Beskrivning**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| uppskattadTimeFrame | Den beräknade varaktigheten av denna operation |
| id | Den unika identifieraren för stegprocessen |
| description | Beskrivning av steget |
| stepName (stegnamn) | Det egna namnet för steget |
| status | Status för steget, `notStarted` \| `running` \| `failed` \| antingen`completed` |
| meddelanden | Alla meddelanden eller varningar som påträffas under steget. Matris med strängar |
| framstegPercentage | Ett heltal från 0 till 100 som anger stegprogressionen |
| | |

### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  |   **Beskrivning**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- Begäran har bearbetats och de begärda operationerna returnerades.        |
|  400      | `Bad/Malformed request`- Felsvarstexten kan innehålla mer information.                    |
|  403      | `Forbidden`- Klienten har inte åtkomst till det angivna namnområdet.                          |
|  404      | `Not found`- Den angivna entiteten finns inte.                                                 |
|  |  |
