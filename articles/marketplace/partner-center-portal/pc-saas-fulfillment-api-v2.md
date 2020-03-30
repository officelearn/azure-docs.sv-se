---
title: SaaS uppfyllelse API v2 | Azure Marketplace
description: I den här artikeln beskrivs hur du skapar och hanterar ett SaaS-erbjudande på AppSource och Azure Marketplace med hjälp av tillhörande uppfyllelse v2 API:er.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275772"
---
# <a name="saas-fulfillment-apis-version-2"></a>API:er för uppfyllnad av SaaS, v2 

I den här artikeln beskrivs API:er som gör det möjligt för partner att sälja sina SaaS-program på AppSource-marknadsplatsen och Azure Marketplace. Dessa API:er är ett krav för transactable SaaS-erbjudanden på AppSource och Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Hantera SaaS-prenumerationens livscykel

Azure SaaS hanterar hela livscykeln för ett SaaS-prenumerationsköp. Den använder uppfyllelse API:er som en mekanism för att driva den faktiska uppfyllelsen, ändringar i planer och radering av prenumerationen med partnern. Kundens faktura baseras på tillståndet för Den SaaS-prenumeration som Microsoft underhåller. I följande diagram visas lägena och åtgärderna som driver ändringarna mellan lägena.

![Livscykeltillstånd för SaaS-abonnemang](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>stater med saas-abonnemang

I följande tabell visas etableringstillstånden för en SaaS-prenumeration, inklusive en beskrivning och ett sekvensdiagram för varje (om tillämpligt). 

#### <a name="provisioning"></a>Etablering

När en kund initierar ett köp får partnern den här informationen i en auktoriseringskod på en kund interaktiv webbsida som använder en URL-parameter. Ett exempel `https://contoso.com/signup?token=..`är , medan målsidans `https://contoso.com/signup`URL i Partnercenter är . Auktoriseringskoden kan valideras och bytas ut mot information om etableringstjänsten genom att anropa Resolve API.  När en SaaS-tjänst är klar skickar den ett aktivera anrop för att signalera att uppfyllelsen är klar och att kunden kan faktureras. 

Följande diagram visar sekvensen av API-anrop för ett etableringsscenario.  

![API-anrop för etablering av en SaaS-tjänst](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Avstämmt

Det här tillståndet är det fasta tillståndet för en etablerad tjänst.

##### <a name="provisioning-for-update"></a>Etablering för uppdatering 

Det här tillståndet innebär att en uppdatering av en befintlig tjänst väntar. En sådan uppdatering kan initieras av kunden, antingen från marknadsplatsen eller på SaaS-tjänsten (endast för direkt-till-kund-transaktioner).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Etablering för uppdatering (när den initieras från marknadsplatsen)

Följande diagram visar sekvensen av åtgärder när en uppdatering initieras från marknadsplatsen.

![API-anrop när uppdateringen initieras från marknadsplatsen](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Etablering för uppdatering (när den initieras från SaaS-tjänsten)

I följande diagram visas åtgärderna när en uppdatering initieras från SaaS-tjänsten. (Webhook-anropet ersätts av en uppdatering av prenumerationen som initierats av SaaS-tjänsten.) 

![API-anrop när uppdateringen initieras från SaaS-tjänsten](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Inaktiverad

Det här tillståndet anger att en kunds betalning inte har tagits emot. Enligt policy ger vi kunden en respitperiod innan vi avbryter prenumerationen. När en prenumeration är i det här tillståndet: 

- Som partner kan du välja att försämra eller blockera användarens åtkomst till tjänsten.
- Prenumerationen måste hållas i ett återställningsbart tillstånd som kan återställa alla funktioner utan förlust av data eller inställningar. 
- Räkna med att få en återinsättande begäran om den här prenumerationen via uppfyllelse API:er eller en begäran om avetablering i slutet av respitperioden. 

#### <a name="unsubscribed"></a>Avsluta prenumerationen 

Prenumerationer når detta tillstånd som svar på antingen en uttrycklig kundbegäran eller utebliven betalning av avgifter. Förhoppningen från partnern är att kundens data behålls för återställning på begäran under ett visst antal dagar och sedan raderas. 


## <a name="api-reference"></a>API-referens

Det här avsnittet dokumenterar *SaaS-prenumerations-API:et* och *operations-API:et*.  Värdet för `api-version` parametern för API:er för version 2 är `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Parameter- och entitetsdefinitioner

I följande tabell visas definitionerna för vanliga parametrar och entiteter som används av uppfyllelse-API:er.

|     Entitet/parameter     |     Definition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | GUID-identifieraren för en SaaS-resurs.  |
| `name`                   | Ett eget namn som tillhandahålls för den här resursen av kunden. |
| `publisherId`            | En unik strängidentifierare för varje utgivare (till exempel: "contoso"). |
| `offerId`                | En unik strängidentifierare för varje erbjudande (till exempel: "offer1").  |
| `planId`                 | En unik strängidentifierare för varje plan/SKU (till exempel: "silver"). |
| `operationId`            | GUID-identifieraren för en viss åtgärd.  |
|  `action`                | Åtgärden som utförs på en `Unsubscribe`resurs, `Reinstate`antingen `ChangePlan` `ChangeQuantity`, `Transfer` `Suspend`, eller , , . |
|   |   |

Globalt unika identifierare[(GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) är 128-bitars (32-hexadecimala) tal som vanligtvis genereras automatiskt. 

#### <a name="resolve-a-subscription"></a>Lösa en prenumeration 

Lösningsslutpunkten gör det möjligt för utgivaren att matcha en marketplace-token till ett beständigt resurs-ID. Resurs-ID:et är den unika identifieraren för en SaaS-prenumeration. När en användare omdirigeras till en partners webbplats innehåller URL:en token i frågeparametrarna. Partnern förväntas använda den här token och göra en begäran om att lösa den. Svaret innehåller det unika SaaS-prenumerations-ID:t, namn, erbjudande-ID och abonnemang för resursen. Den här token är endast giltig i en timme. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |

*Begär rubriker:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Till exempel:`Bearer <access_token>`" ". |
|  x-ms-marketplace-token  |  Parametern tokenfrågefråga i URL:en när användaren omdirigeras till SaaS-partnerns webbplats från Azure (till exempel: `https://contoso.com/signup?token=..`). *Anm.:* URL:en avkodar tokenvärdet från webbläsaren innan du använder det.  |

*Svarskoder:*

Kod: 200<br>
Artikeln innehÃ¥ller den ogenomskinliga token till en SaaS-prenumeration. Svarsorgan:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 400<br>
Dålig begäran. x-ms-marketplace-token saknas, har felformaterats eller upphört att gälla.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API för prenumeration

Prenumerations-API:et stöder följande HTTPS-åtgärder: **Hämta**, **Bokför**, **Korrigeringsfil**och **Ta bort**.


#### <a name="list-subscriptions"></a>Lista prenumerationer

Visar en lista över alla SaaS-prenumerationer för en utgivare.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Hämta<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion (ApiVersion)  |  Den version av åtgärden som ska användas för den här begäran.  |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
| auktorisering      |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Till exempel:`Bearer <access_token>`" ".  |

*Svarskoder:*

Kod: 200 <br/>
Hämtar utgivaren och motsvarande prenumerationer för alla utgivarens erbjudanden, baserat på autentiseringstoken.

>[!Note]
>[Falska API:er](#mock-apis) används när du först utvecklar erbjudandet, medan verkliga API:er måste användas när du faktiskt publicerar erbjudandet.  Verkliga API:er och Falska API:er skiljer sig åt med den första raden i koden.  I det verkliga API:et `subscription` finns avsnittet, medan det här avsnittet inte finns för utkast API.

Svarsnyttolast för utkast API:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
Och för riktiga API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Fortsättningstoken kommer endast att finnas om det finns ytterligare "sidor" med planer att hämta. 

Kod: 403 <br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren. 

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Skaffa prenumeration

Hämtar den angivna SaaS-prenumerationen. Använd det här samtalet för att få licensinformation och planera information.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token via Resolve API.   |
|  ApiVersion (ApiVersion)        |   Den version av åtgärden som ska användas för den här begäran.   |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Till exempel:`Bearer <access_token>`" ".  |

*Svarskoder:*

Kod: 200<br>
Hämtar SaaS-prenumerationen från identifieraren. Nyttolast för svar:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.<br> 

Kod: 500<br>
Internt serverfel.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista tillgängliga planer

Använd det här samtalet för att ta reda på om det finns några privata eller offentliga erbjudanden för den aktuella utgivaren.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |   Den version av åtgärden som ska användas för den här begäran.  |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  x-ms-correlationid  | Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ". |

*Svarskoder:*

Kod: 200<br>
Hämtar en lista över tillgängliga planer för en kund. Svarsorgan:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kod: 404<br>
Hittades inte.<br> 

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren. <br> 

Kod: 500<br>
Internt serverfel.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktivera en prenumeration

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |

*Begär rubriker:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  x-ms-correlationid  | Ett unikt strängvärde för åtgärder på klienten. Den här strängen korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ". |

*Begär nyttolast:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Svarskoder:*

Kod: 200<br>
Aktiverar prenumerationen.<br>

Kod: 400<br>
Felaktig begäran: valideringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Ändra planen för prenumerationen

Uppdatera planen för prenumerationen.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  x-ms-correlationid  |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.    |
| auktorisering      |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ".  |

*Begär nyttolast:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-plats | Länken till en resurs för att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
Begäran om ändringsplan har accepterats. Partnern förväntas avsöka operation-plats för att avgöra en framgång eller ett misslyckande. <br>

Kod: 400<br>
Felaktig begäran: valideringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Endast en plan eller kvantitet kan korrigeras samtidigt, inte båda. Redigeringar på en **Update** prenumeration med `allowedCustomerOperations`Update finns inte i .

#### <a name="change-the-quantity-on-the-subscription"></a>Ändra kvantiteten på prenumerationen

Uppdatera kvantiteten på prenumerationen.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  x-ms-correlationid  |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.    |
| auktorisering      |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ".  |

*Begär nyttolast:*

```json
Request Body:
{
    "quantity": 5
}
```

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-plats | Länka till en resurs för att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
Begäran om ändring av kvantitet har accepterats. Partnern förväntas avsöka operation-plats för att avgöra en framgång eller ett misslyckande. <br>

Kod: 400<br>
Felaktig begäran: valideringsfel.


Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Endast en plan eller kvantitet kan korrigeras samtidigt, inte båda. Redigeringar på en **Update** prenumeration med `allowedCustomerOperations`Update finns inte i .

#### <a name="delete-a-subscription"></a>Ta bort en prenumeration

Avsluta prenumerationen och ta bort den angivna prenumerationen.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ta bort<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |

*Begär rubriker:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.   |
|  x-ms-correlationid  |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.   |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ".  |

*Svarskoder:*

Kod: 202<br>
Partnern initierade ett samtal för att avsluta prenumerationen på en SaaS-prenumeration.<br>

Kod: 400<br>
Ta bort på en `allowedCustomerOperations`prenumeration med Ta **bort** inte i .

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API för åtgärder

Operations-API:et stöder följande korrigeringsåtgärder och hämta åtgärder.

#### <a name="list-outstanding-operations"></a>Lista utestående operationer 

Visar en lista över utestående åtgärder för den aktuella utgivaren. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion (ApiVersion)                |   Den version av åtgärden som ska användas för den här begäran.                |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |

*Begär rubriker:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ".  |

*Svarskoder:*

Kod: 200<br> Hämtar listan över väntande åtgärder på en prenumeration. Nyttolast för svar:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kod: 400<br>
Felaktig begäran: valideringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 500<br>
Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Hämta åtgärdsstatus

Gör det möjligt för utgivaren att spåra status för den `Subscribe` `Unsubscribe`angivna `ChangePlan`utlösta asynkronåtgärden (till exempel , , eller `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (ApiVersion)        |  Den version av åtgärden som ska användas för den här begäran.  |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Till exempel:`Bearer <access_token>`" ".  |

*Svarskoder:*<br>

Kod: 200<br> Hämtar den angivna väntande SaaS-åtgärden. Nyttolast för svar:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kod: 400<br>
Felaktig begäran: valideringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.
 
Kod: 404<br>
Hittades inte.

Kod: 500<br> Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Uppdatera status för en åtgärd

Uppdatera status för en åtgärd för att ange lyckade eller misslyckade med de angivna värdena.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion (ApiVersion)       |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls efter att ha löst token med hjälp av Resolve API.  |
|  operationId       | Operationen som håller på att slutföras. |

*Begär rubriker:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  x-ms-correlationid |  Ett unikt strängvärde för åtgärder på klienten. Den här parametern korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
|  auktorisering     |  [Hämta JSON-webbtoken (JWT) bärare token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Till exempel:`Bearer <access_token>`" ".  |

*Begär nyttolast:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Svarskoder:*

Kod: 200<br> Ett samtal för att informera om slutförandet av en operation på partnersidan. Det här svaret kan till exempel signalera byte av platser eller planer.

Kod: 400<br>
Felaktig begäran: valideringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken angavs inte eller är ogiltig, eller så försöker begäran komma åt en anskaffning som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Hittades inte.

Kod: 409<br>
Konflikt. En nyare transaktion har till exempel redan uppfyllts.

Kod: 500<br> Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementera en webhook på SaaS-tjänsten

Utgivaren måste implementera en webhook i den här SaaS-tjänsten för att proaktivt meddela användarna om ändringar i tjänsten. SaaS-tjänsten förväntas anropa åtgärds-API:et för att validera och auktorisera innan du vidtar en åtgärd på webhook-meddelandet.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Om åtgärden kan vara något av följande: 
- `Unsubscribe`(när resursen har tagits bort)
- `ChangePlan`(när ändringsplanen har slutförts)
- `ChangeQuantity`(när ändringskvantiteten har slutförts)
- `Suspend`(när resursen har pausats)
- `Reinstate`(när resursen har återinförts efter avstängning)

Om statusen kan vara något av följande: 
- **Inte startad** <br>
 - **InProgress (InProgress)** <br>
- **Lyckades** <br>
- **Misslyckades** <br>
- **Konflikt** <br>

I ett webhook-meddelande är åtgärderbara statusar antingen **lyckades** och **misslyckades**. En operationss livscykel är från **NotStarted** till ett terminaltillstånd som **Lyckades,** **Misslyckades**eller **Konflikt**. Om du får **NotStarted** eller **InProgress**fortsätter du att begära status via GET API tills åtgärden når ett terminaltillstånd innan du vidtar åtgärder. 

## <a name="mock-apis"></a>Håna API:er

Du kan använda våra falska API:er för att komma igång med utveckling, särskilt prototyper, samt testa projekt. 

Värdslutpunkt: `https://marketplaceapi.microsoft.com/api` (ingen autentisering krävs)<br/>
API-version:`2018-09-15`<br/>
Exempel på URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API-slutpunktssökvägarna är desamma i både falska och verkliga API:er, men API-versionerna är olika. Versionen är `2018-09-15` för mock-versionen och `2018-08-31` för produktionsversionen. 

Alla API-anrop i den här artikeln kan göras till slutpunkten för den falska värden. I allmänhet, räkna med att få håna data tillbaka som ett svar. Anrop till uppdateringsprenumerationsmetoderna på det falska API:et returnerar alltid 500. 

## <a name="next-steps"></a>Nästa steg

Utvecklare kan också programmässigt hämta och manipulera arbetsbelastningar, erbjudanden och utgivarprofiler med hjälp av [CLOUD Partner Portal REST API:er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
