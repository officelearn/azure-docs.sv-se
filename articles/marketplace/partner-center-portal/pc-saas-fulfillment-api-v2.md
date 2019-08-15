---
title: API v2 för SaaS-utförande | Azure Marketplace
description: 'Den här artikeln förklarar hur du skapar och hanterar ett SaaS-erbjudande på AppSource och Azure Marketplace genom att använda de associerade API: erna för uppfyllande v2.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 276699b9316a0c4fd428038f2c967bdf934f449c
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016044"
---
# <a name="saas-fulfillment-apis-version-2"></a>API: er för SaaS-uppfyllelse, version 2 

Den här artikeln beskriver de API: er som gör det möjligt för partner att sälja sina SaaS-program på AppSource Marketplace och på Azure Marketplace. Dessa API: er är ett krav för SaaS-erbjudanden på AppSource och Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Hantera livs cykeln för SaaS-prenumeration

Azure SaaS hanterar hela livs cykeln för ett inköp av SaaS-prenumerationer. Den använder API: er för utförande som en mekanism för att driva faktisk uppfyllelse, ändringar av planer och borttagning av prenumerationen med partnern. Kundens faktura baseras på statusen för den SaaS-prenumeration som Microsoft underhåller. Följande diagram visar tillstånden och de åtgärder som styr ändringarna mellan tillstånden.

![Livs cykel tillstånd för SaaS-prenumeration](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

I följande tabell visas etablerings tillstånden för en SaaS-prenumeration, inklusive en beskrivning och ett sekventiellt diagram för varje (om tillämpligt). 

#### <a name="provisioning"></a>Etableras

När en kund initierar ett köp, tar partneren emot denna information i en auktoriseringskod på en kund interaktiv webb sida som använder en URL-parameter. Ett exempel är `https://contoso.com/signup?token=..`, medan URL: en för landnings sidan i `https://contoso.com/signup`Partner Center är. Auktoriseringskod kan verifieras och utbyts för information om etablerings tjänsten genom att anropa lösnings-API: et.  När en SaaS-tjänst har slutfört etableringen, skickar den ett aktiverings anrop till den signal att uppfyllandet är klart och kunden kan faktureras. 

Följande diagram visar sekvensen med API-anrop för ett etablerings scenario.  

![API-anrop för etablering av en SaaS-tjänst](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Etablerat

Det här läget är det stabila läget för en etablerad tjänst.

##### <a name="provisioning-for-update"></a>Etablering för uppdatering 

Det här läget indikerar att en uppdatering av en befintlig tjänst väntar. En sådan uppdatering kan initieras av kunden, antingen från Marketplace eller på SaaS-tjänsten (endast för direkta till kund transaktioner).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Etablering för uppdatering (när den startas från Marketplace)

Följande diagram visar en sekvens med åtgärder när en uppdatering initieras från Marketplace.

![API-anrop när uppdateringen initieras från Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Etablering för uppdatering (när det initieras från SaaS-tjänsten)

Följande diagram visar de åtgärder som vidtas när en uppdatering initieras från SaaS-tjänsten. (Webhook-anropet ersätts av en uppdatering av prenumerationen som initierats av SaaS-tjänsten.) 

![API-anrop när uppdateringen initieras från SaaS-tjänsten](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Uppehåll

Det här läget anger att kundens betalning inte har tagits emot. Enligt princip ger vi kunden en respitperiod innan prenumerationen avbryts. När en prenumeration är i det här läget: 

- Som partner kan du välja att försämra eller blockera användarens åtkomst till tjänsten.
- Prenumerationen måste behållas i ett återställnings Bart tillstånd som kan återställa fullständiga funktioner utan att data eller inställningar går förlorade. 
- Förväntar dig att få en återskapande begäran för den här prenumerationen via API: er för uppfyllande eller en avetablerings förfrågan i slutet av grace-perioden. 

#### <a name="unsubscribed"></a>Borttagen 

Prenumerationerna når det här läget som svar på antingen en explicit kund förfrågan eller ej betalnings avgift. Förväntan från partnern är att kundens data bevaras för återställning på begäran under ett visst antal dagar och sedan raderas. 


## <a name="api-reference"></a>API-referens

I det här avsnittet dokumenteras API för SaaS- *prenumeration* och *åtgärder*.  Värdet för `api-version` parametern för API: er för version 2 `2018-08-31`är.  


### <a name="parameter-and-entity-definitions"></a>Parameter-och enhets definitioner

I följande tabell visas definitionerna för vanliga parametrar och entiteter som används av API: er för att uppfylla kraven.

|     Entitet/parameter     |     Definition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | GUID-identifieraren för en SaaS-resurs.  |
| `name`                   | Ett eget namn som har angetts för den här resursen av kunden. |
| `publisherId`            | En unik sträng identifierare för varje utgivare (till exempel: "contoso"). |
| `offerId`                | En unik sträng identifierare för varje erbjudande (till exempel: "offer1").  |
| `planId`                 | En unik sträng identifierare för varje plan/SKU (till exempel: "Silver"). |
| `operationId`            | GUID-identifieraren för en viss åtgärd.  |
|  `action`                | Åtgärden som utförs på en resurs `unsubscribe`, antingen `suspend` `reinstate`,, eller `changePlan`, `changeQuantity` `transfer`,.  |
|   |   |

Globalt unika identifierare ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) är 128-bitars (32-hexadecimala) siffror som vanligt vis genereras automatiskt. 

#### <a name="resolve-a-subscription"></a>Lösa en prenumeration 

Med hjälp av matchnings slut punkten kan utgivaren matcha en Marketplace-token till ett beständigt resurs-ID. Resurs-ID: t är den unika identifieraren för en SaaS-prenumeration. När en användare omdirigeras till en partners webbplats innehåller URL: en token i frågeparametrar. Partnern förväntas använda denna token och göra en begäran om att lösa den. Svaret innehåller det unika prenumerations-ID: t, namn, erbjudande-ID och plan för resursen. Denna token är endast giltig för en timme. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Inlägg<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |

*Begärandehuvuden:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      | `application/json` |
|  x-MS-RequestId    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token. Till exempel: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Parametern för token-frågan i URL: en när användaren omdirigeras till SaaS-partnerns webbplats från Azure (till `https://contoso.com/signup?token=..`exempel:). *Obs:* URL: en avkodar värdet för token från webbläsaren innan det används.  |

*Svars koder:*

Kod: 200<br>
Löser in ogenomskinlig token till en SaaS-prenumeration. Svars text:
 

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
Felaktig begäran. x-MS-Marketplace-token saknas, är felaktig eller har upphört att gälla.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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

### <a name="subscription-api"></a>Prenumerations-API

Prenumerations-API: et stöder följande HTTPS-åtgärder: **Hämta**, **Skicka**, **korrigera**och **ta bort**.


#### <a name="list-subscriptions"></a>Lista prenumerationer

Visar en lista över alla SaaS-prenumerationer för en utgivare.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Hämta<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Den version av åtgärden som ska användas för den här begäran.  |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
| Innehållstyp       |  `application/json`  |
| x-MS-RequestId     |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
| authorization      |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token. Till exempel: "`Bearer <access_token>`".  |

*Svars koder:*

Kod: 200 <br/>
Hämtar utgivaren och motsvarande prenumerationer för alla utgivares erbjudanden, baserat på autentiseringstoken.
Nytto last för svar:<br>

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
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Fortsättnings-token finns bara om det finns ytterligare "sidor" av planer att hämta. 

Kod: 403 <br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren. 

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

#### <a name="get-subscription"></a>Hämta prenumeration

Hämtar den angivna SaaS-prenumerationen. Använd det här anropet för att hämta licens information och planera information.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   En unik identifierare för den SaaS-prenumeration som hämtades när token matchas via lösnings-API.   |
|  ApiVersion        |   Den version av åtgärden som ska användas för den här begäran.   |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      |  `application/json`  |
|  x-MS-RequestId    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token. Till exempel: "`Bearer <access_token>`".  |

*Svars koder:*

Kod: 200<br>
Hämtar SaaS-prenumerationen från identifierare. Nytto last för svar:<br>

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
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.<br> 

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

Använd det här anropet för att ta reda på om det finns privata eller offentliga erbjudanden för den aktuella utgivaren.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Den version av åtgärden som ska användas för den här begäran.  |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Innehållstyp     |  `application/json` |
|   x-MS-RequestId   |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  x-MS-correlationId  | Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`". |

*Svars koder:*

Kod: 200<br>
Hämtar en lista över tillgängliga planer för en kund. Svars text:

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
Kunde inte hittas.<br> 

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren. <br> 

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

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Inlägg<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      | `application/json`  |
|  x-MS-RequestId    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  x-MS-correlationId  | Ett unikt sträng värde för åtgärden på klienten. Den här strängen korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`". |

*Nytto last för begäran:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Svars koder:*

Kod: 200<br>
Aktiverar prenumerationen.<br>

Kod: 400<br>
Felaktig begäran: verifierings fel.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Korrigering<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      | `application/json` |
|  x-MS-RequestId    |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  x-MS-correlationId  |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.    |
| authorization      |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`".  |

*Nytto last för begäran:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Länken till en resurs för att hämta åtgärdens status.   |

*Svars koder:*

Kod: 202<br>
Begäran om att ändra planen har accepterats. Partnern förväntas avsöka åtgärds platsen för att avgöra om det lyckades eller inte. <br>

Kod: 400<br>
Felaktig begäran: verifierings fel.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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
>Det går bara att korrigera en plan eller kvantitet i taget, inte båda. Redigeringar av en prenumeration med **uppdatering** är inte `allowedCustomerOperations`i.

#### <a name="change-the-quantity-on-the-subscription"></a>Ändra antalet för prenumerationen

Uppdatera antalet i prenumerationen.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>9\.0a<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      | `application/json` |
|  x-MS-RequestId    |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  x-MS-correlationId  |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.    |
| authorization      |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`".  |

*Nytto last för begäran:*

```json
Request Body:
{
    "quantity": 5
}
```

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Länka till en resurs för att få åtgärdens status.   |

*Svars koder:*

Kod: 202<br>
Begäran om att ändra kvantitet har accepterats. Partnern förväntas avsöka åtgärds platsen för att avgöra om det lyckades eller inte. <br>

Kod: 400<br>
Felaktig begäran: verifierings fel.


Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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
>Det går bara att korrigera en plan eller kvantitet i taget, inte båda. Redigeringar av en prenumeration med **uppdatering** är inte `allowedCustomerOperations`i.

#### <a name="delete-a-subscription"></a>Ta bort en prenumeration

Avbryt prenumerationen och ta bort den angivna prenumerationen.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Ta bort<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Innehållstyp     |  `application/json` |
|  x-MS-RequestId    |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.   |
|  x-MS-correlationId  |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.   |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`".  |

*Svars koder:*

Kod: 202<br>
Partnern initierade ett anrop för att avbryta prenumerationen på en SaaS-prenumeration.<br>

Kod: 400<br>
Ta bort en prenumeration med **ta bort** inte `allowedCustomerOperations`i.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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

API: erna för åtgärder stöder följande korrigerings-och get-åtgärder.

#### <a name="list-outstanding-operations"></a>Lista över utestående åtgärder 

Visar en lista över utestående åtgärder för den aktuella utgivaren. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Den version av åtgärden som ska användas för den här begäran.                |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Innehållstyp     |  `application/json` |
|  x-MS-RequestId    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`".  |

*Svars koder:*

Kod: 200<br> Hämtar listan över väntande åtgärder för en prenumeration. Nytto last för svar:

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
Felaktig begäran: verifierings fel.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

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

#### <a name="get-operation-status"></a>Hämta åtgärds status

Gör att utgivaren kan spåra statusen för den angivna utlösta asynkrona åtgärden ( `subscribe`till `unsubscribe`exempel `changePlan`,, `changeQuantity`eller).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Den version av åtgärden som ska användas för den här begäran.  |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Innehållstyp      |  `application/json`   |
|  x-MS-RequestId    |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token. Till exempel: "`Bearer <access_token>`".  |

*Svars koder:*<br>

Kod: 200<br> Hämtar den angivna väntande SaaS-åtgärden. Nytto last för svar:

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
Felaktig begäran: verifierings fel.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.
 
Kod: 404<br>
Kunde inte hittas.

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

Uppdatera status för en åtgärd för att indikera att det lyckades eller misslyckades med de angivna värdena.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Korrigering<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Den version av åtgärden som ska användas för den här begäran.  |
| subscriptionId     | En unik identifierare för den SaaS-prenumeration som hämtades när token matchas med hjälp av lösnings-API: et.  |
|  operationId       | Åtgärden som slutförs. |

*Begärandehuvuden:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Innehållstyp     | `application/json`   |
|   x-MS-RequestId   |   Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  x-MS-correlationId |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  authorization     |  [Hämta JSON Web token (JWT) Bearer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)-token.  Till exempel: "`Bearer <access_token>`".  |

*Nytto last för begäran:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Svars koder:*

Kod: 200<br> Ett anrop för att informera om att en åtgärd slutförts på partner sidan. Svaret kan till exempel signalera ändringen av platser eller planer.

Kod: 400<br>
Felaktig begäran: verifierings fel.

Kod: 403<br>
Ej auktoriserad. Autentiseringstoken angavs inte eller är ogiltig eller så försöker begäran att komma åt ett förvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

Kod: 409<br>
Uppstod. Till exempel är en nyare transaktion redan uppfylld.

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

Utgivaren måste implementera en webhook i den här SaaS-tjänsten för att proaktivt meddela användarnas ändringar i sin tjänst. SaaS-tjänsten förväntas anropa API: et för åtgärder för att verifiera och auktorisera innan åtgärden för webhook-aviseringen utförs.

För att säkerställa säker kommunikation inkluderar Microsoft Azure Active Directory JWT-token i Authorization-huvudet som en del av anropet. SaaS-leverantörer uppmanas att validera JWT-token enligt beskrivningen i artikeln [Microsoft Identity Platform Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) för att säkerställa att endast giltiga anrop accepteras.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Där åtgärden kan vara något av följande: 
- `unsubscribe`(när resursen har tagits bort)
- `changePlan`(när åtgärden ändra plan har slutförts)
- `changeQuantity`(när åtgärden ändra kvantitet har slutförts)
- `suspend`(när resursen har pausats)
- `reinstate`(när resursen har återställts efter SUS pensionen)

Om statusen kan vara något av följande: 
- **NotStarted** <br>
 - **Pågår** <br>
- **Lyckades** <br>
- **Misslyckades** <br>
- **Uppstod** <br>

I ett webhook-meddelande har de åtgärds bara statusarna **lyckats** och **misslyckats**. En åtgärds livs cykel är från **NotStarted** till ett Terminal-tillstånd som **lyckades**, **misslyckades**eller **står i konflikt**. Om du får **NotStarted** eller **pågår**fortsätter du att begära STATUSEN via get-API tills åtgärden når ett Terminal-tillstånd innan åtgärden utförs. 

## <a name="mock-apis"></a>Modellera API: er

Du kan använda våra modeller för att hjälpa dig att komma igång med utveckling, särskilt prototyper, samt testa projekt. 

Värd slut punkt `https://marketplaceapi.microsoft.com/api` : (ingen autentisering krävs)<br/>
API-version:`2018-09-15`<br/>
Exempel-URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API-slutpunktens sökvägar är desamma för både modellerade och verkliga API: er, men API-versionerna skiljer sig åt. Versionen är `2018-09-15` för den blå versionen och `2018-08-31` för produktions versionen. 

Något av API-anropen i den här artikeln kan göras till värdens slut punkt för modeller. I allmänhet förväntar dig att få fram ett bakgrunds data som svar. Anrop till prenumerations metoderna för uppdatering i en modell-API returnerar alltid 500. 

## <a name="next-steps"></a>Nästa steg

Utvecklare kan också program mässigt hämta och manipulera arbets belastningar, erbjudanden och utgivar profiler med hjälp av [Cloud Partner Portal REST-API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
