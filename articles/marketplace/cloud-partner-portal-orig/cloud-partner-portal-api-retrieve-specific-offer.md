---
title: Hämta ett specifikt erbjudande-API | Azure Marketplace
description: API hämtar det angivna erbjudandet inom utgivarens namnområde.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f2182ed2377a392f55af2c1f723be325bd518349
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255930"
---
<a name="retrieve-a-specific-offer"></a>Hämta ett specifikt erbjudande
=========================

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Hämtar det angivna erbjudandet inom utgivarens namnområde.  

Du kan också hämta en viss version av erbjudandet eller hämta erbjudandet i utkast-, vy- eller produktionsplatser. Om en plats inte har angetts är `draft`standardvärdet . Om du försöker hämta ett erbjudande som inte har `404 Not Found` förhandsgranskats eller publicerats uppstår ett fel.

> [!WARNING]
> De hemliga värdena för hemliga typfält hämtas inte av det här API:et.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parametrar
--------------


| **Namn**    | **Beskrivning**                                                                          | **Datatyp** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Till exempel, Contoso                                                        | Sträng        |
| offerId (erbjudandeId)     | Guid som unikt identifierar erbjudandet.                                                 | Sträng        |
| version     | Version av erbjudandet som hämtas. Som standard hämtas den senaste erbjudandeversionen. | Integer       |
| slotId (kortplats)      | Den plats från vilken erbjudandet ska hämtas, kan vara en av:      <br/>  - `Draft`(standard) hämtar erbjudandeversionen som för närvarande är i utkast.  <br/>  -  `Preview`hämtar erbjudandeversionen som för närvarande förhandsgranskas.     <br/>  -  `Production`hämtar erbjudandeversionen som för närvarande är i produktion.          |      Enum |
| api-version | Senaste versionen av API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Huvud
------

|  **Namn**          |   **Värde**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Auktorisering     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Exempel på brödtext
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


### <a name="response-body-properties"></a>Egenskaper för svarstext

|  **Namn**       |   **Beskrivning**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifierar typen av erbjudande                                                                                                    |
|  publisherId    | Utgivarens unika identifierare                                                                                              |
|  status         | Erbjudandets status. Information om listan över möjliga värden finns i [Erbjudandestatus](#offer-status) nedan.                                  |
|  Id             | GUID som unikt identifierar erbjudandet                                                                                         |
|  version        | Aktuell version av erbjudandet. Versionsegenskapen kan inte ändras av klienten. Det ökas efter varje publicering.    |
|  definition     | Faktisk definition av arbetsbelastningen                                                                                               |
|  ändradTid    | UTC-datumtid när erbjudandet senast ändrades                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Statuskoder för svar

| **Kod**  | **Beskrivning**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Begäran har behandlats och alla erbjudanden under utgivaren returnerades till klienten.               |
|  400      | `Bad/Malformed request`- Felsvarstexten kan innehålla mer information.                                                 |
|  403      | `Forbidden`- Klienten har inte åtkomst till det angivna namnområdet.                                                        |
|  404      | `Not found`- Den angivna entiteten finns inte. Klienten bör kontrollera publisherId, offerId och version (om det anges).      |
|  |  |


### <a name="offer-status"></a>Status för erbjudande

|  **Namn**                   |   **Beskrivning**                             |
| --------------------------- |  -------------------------------------------- |
|  Aldrigpublicerat             | Erbjudandet har aldrig publicerats.               |
|  Inte startad                 | Erbjudandet är nytt men startas inte.              |
|  Väntar påpublicerAreView  | Erbjudandet väntar på utgivarens godkännande.      |
|  Körs                    | Inlämning av erbjudande bearbetas.          |
|  Lyckades                  | Erbjudandet inlämning har slutfört behandlingen.    |
|  Avbrutna                   | Inlämningen av erbjudandet avbröts.                |
|  Misslyckades                     | Erbjudandet misslyckades.                      |
|  |  |
