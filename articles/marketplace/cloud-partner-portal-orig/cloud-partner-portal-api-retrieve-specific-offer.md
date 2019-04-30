---
title: Hämta ett specifikt erbjudande-API | Microsoft Docs
description: 'API: et hämtar det angivna erbjudandet i publisher-namnområde.'
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61096071"
---
<a name="retrieve-a-specific-offer"></a>Hämta ett specifikt erbjudande
=========================

Hämtar det angivna erbjudandet i publisher-namnområde.  

Du kan också hämta en viss version av erbjudandet eller hämta erbjudandet i draft, vyer och produktionsplatser. Om en plats inte anges är standardvärdet `draft`. Försök att hämta ett erbjudande som inte förhandsgranskas eller publicerat resulterar i en `404 Not Found` fel.

> [!WARNING]
> Kommer inte att hämta de hemliga värdena för hemliga typfält av den här API: et.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parametrar
--------------


| **Namn**    | **Beskrivning**                                                                          | **Datatyp** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Exempel: Contoso                                                        | String        |
| offerId     | GUID som unikt identifierar erbjudandet.                                                 | String        |
| version     | Version av erbjudandet som hämtas. Som standard hämtas den senaste versionen av erbjudandet. | Integer       |
| slotId      | Facket som erbjudandet är att hämta kan vara något av:      <br/>  - `Draft` (standard) hämtar erbjudande-versionen för närvarande i utkastet.  <br/>  -  `Preview` hämtar erbjudande-version för närvarande i förhandsversion.     <br/>  -  `Production` hämtar erbjudande-version för närvarande i produktion.          |      Enum |
| API-versionen | Senaste versionen av API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**          |   **Värde**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Auktorisering     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Brödtext exempel
------------

### <a name="response"></a>Svar

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Egenskaper för rapportinnehåll i svaret

|  **Namn**       |   **Beskrivning**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifierar typ av erbjudande                                                                                                    |
|  publisherId    | Unik identifierare för utgivaren                                                                                              |
|  status         | Status för erbjudandet. Listan över möjliga värden, se [erbjuder status](#offer-status) nedan.                                  |
|  Id             | GUID som unikt identifierar erbjudandet                                                                                         |
|  version        | Aktuell version av erbjudandet. Att går inte ändra versionsegenskapen av klienten. Det ökar stegvis när varje publicering.    |
|  definition     | Faktiska definitionen av arbetsbelastning                                                                                               |
|  changedTime    | UTC-datum/tid när erbjudandet senast ändrades                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Svarsstatuskoder

| **Kod**  | **Beskrivning**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Begäran har bearbetats och alla erbjudanden under utgivaren har returnerats till klienten.               |
|  400      | `Bad/Malformed request` -Fel svarstexten kan innehålla mer information.                                                 |
|  403      | `Forbidden` -Klienten har inte åtkomst till det angivna namnområdet.                                                        |
|  404      | `Not found` -Den angivna entiteten finns inte. Klienten ska kontrollera publisherId, offerId och version (om det angetts).      |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                   |   **Beskrivning**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Erbjudandet har inte publicerats.               |
|  NotStarted                 | Erbjudandet är nytt, men har startats inte.              |
|  WaitingForPublisherReview  | Erbjudande väntar på godkännande av utgivaren.      |
|  Körs                    | Erbjud bidrag bearbetas.          |
|  Lyckades                  | Erbjudandet bidrag har bearbetat.    |
|  Avbrutna                   | Erbjudandet överföringen avbröts.                |
|  Misslyckad                     | Det gick inte att skicka för erbjudandet.                      |
|  |  |
