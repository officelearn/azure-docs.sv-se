---
title: SaaS betjäna API v2 | Azure Marketplace
description: 'Den här artikeln förklarar hur du skapar och hanterar ett SaaS-erbjudande på Azure Marketplace och AppSource med associerade uppfyllandet v2 API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ecee1669c29d7b298741f9e5521de03da6dd7e3b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331639"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS techtrends API: er, version 2 

Den här artikeln beskriver API: er som gör det möjligt för partner att sälja sina SaaS-program i AppSource-marknadsplatsen och Azure Marketplace. Dessa API: er är ett krav för transactable SaaS erbjuder på Azure Marketplace och AppSource.

## <a name="managing-the-saas-subscription-life-cycle"></a>Hantera livscykeln för SaaS-prenumeration

Azure SaaS hanterar hela livscykeln för ett SaaS-prenumerationsinköpet. Den använder uppfyllandet API: er som en mekanism för att driva faktiska uppfyllandet, ändringar i planer och borttagning av prenumerationen med partnern. Kundens faktura baseras på tillståndet för SaaS-prenumeration som Microsoft underhåller. Följande diagram visar status och vilka åtgärder som genomför ändringarna emellan.

![SaaS prenumerationstillstånd för livscykel](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

I följande tabell visas de etablering tillstånden för ett SaaS-prenumeration, inklusive en beskrivning och sekvens diagram för varje (om tillämpligt). 

#### <a name="provisioning"></a>Etablering

När en kund initierar ett köp, får partnern som den här informationen i en auktoriseringskod på en kund-webbsida som använder en URL-parameter. Ett exempel är `https://contoso.com/signup?token=..`, medan startsida sid-URL i Partnercenter är `https://contoso.com/signup`. Auktoriseringskoden kan verifieras och bytas ut mer information om etableringstjänsten genom att anropa API: et lösa.  När en SaaS-tjänst är klar etablering, skickar den ett aktivera anrop för att signalera att uppfyllandet är klar och kunden kan faktureras. 

I följande diagram visas de API-anrop för ett scenario med etablering.  

![API-anrop för att etablera en SaaS-tjänst](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Etablerat

Det här tillståndet är i stabilt tillstånd för en etablerad tjänst.

##### <a name="provisioning-for-update"></a>Etablering för uppdatering 

Det här tillståndet anger att en uppdatering av en befintlig tjänst väntande. Sådan uppdatering kan initieras av kunden, antingen från marketplace eller på SaaS-tjänsten (endast för direkt till kunden transaktioner).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Etablering för uppdatering (när den har initierats från marketplace)

Följande diagram visar sekvens med åtgärder när en uppdatering har initierats från marketplace.

![API-anrop när uppdateringen har initierats från marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Etablering för uppdatering (när den har initierats från SaaS tjänsten)

Följande diagram visar åtgärderna när en uppdatering initieras från SaaS-tjänsten. (Webhook-anropet ersätts av en uppdatering för den prenumeration som initieras av SaaS-tjänst.) 

![API-anrop när uppdateringen har initierats från SaaS-tjänst](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Uppehåll

Det här tillståndet anger att en kund betalning inte har tagits emot. Av Grupprincip får kunden en respitperiod innan du avbryter prenumerationen. När en prenumeration är i det här tillståndet: 

- Som partner kan du välja att försämras eller blockera åtkomst till tjänsten.
- Prenumerationen måste hållas i ett återställningsbara tillstånd som kan återställa samtliga funktioner utan att förlora data eller inställningar. 
- Förvänta dig att hämta en begäran om återställa för den här prenumerationen via uppfyllandet API: er eller ta bort etableringsbegäran i slutet av respitperioden. 

#### <a name="unsubscribed"></a>Prenumeration avslutad 

Prenumerationer nå det här tillståndet som svar på en explicit kundbegäran eller utebliven betalning av avgifter. Från partnern väntas att kundens data bevaras för återställning på begäran för ett visst antal dagar och tas sedan bort. 


## <a name="api-reference"></a>API-referens

Det här avsnittet beskrivs SaaS *prenumeration API* och *Operations API*.  Värdet för den `api-version` parametern för version 2 API: er är `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Parametern-och entiteten

I följande tabell visas definitionerna för gemensamma parametrar och entiteter som används av techtrends API: er.

|     Entity/Parameter     |     Definition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | GUID-identifierare för en SaaS-resurs.  |
| `name`                   | Ett eget namn som angetts för den här resursen av kunden. |
| `publisherId`            | En unik sträng som identifierare för varje utgivare (till exempel: ”contoso”). |
| `offerId`                | En unik sträng som identifierare för varje erbjudande (till exempel: ”offer1”).  |
| `planId`                 | En unik sträng som identifierare för varje plan/SKU (till exempel: ”silver”). |
| `operationId`            | GUID-identifierare för en viss åtgärd.  |
|  `action`                | Den åtgärd som utförs på en resurs, antingen `subscribe`, `unsubscribe`, `suspend`, `reinstate`, eller `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Globalt unika identifierare ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) är 128-bitars (32-hexadecimalt) värden som genereras vanligtvis automatiskt. 

#### <a name="resolve-a-subscription"></a>Lösa en prenumeration 

Lös slutpunkten gör det möjligt för utgivare att matcha en marketplace-token med en beständig resurs-ID. Resurs-ID är den unika identifieraren för en SaaS-prenumeration. När en användare omdirigeras till partnerns webbplats, innehåller URL: en en token i Frågeparametrar. Partnern förväntas använder denna token och göra en begäran att lösa detta. Svaret innehåller den unika SaaS prenumerations-ID, namn, erbjudande-ID och planera för resursen. Denna token är giltig i en timme. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Token Frågeparametern i URL: en när användaren omdirigeras till SaaS-partnerns webbplats från Azure (till exempel: `https://contoso.com/signup?token=..`). *Obs:* URL: en avkodar token-värde från webbläsaren innan du använder den.  |

*Svarskoder:*

Kod: 200<br>
Löser täckande token till en SaaS-prenumeration. Svarstext:
 

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
Felaktig begäran. x-ms-marketplace-token är felaktigt, saknas eller har upphört att gälla.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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

### <a name="subscription-api"></a>Prenumeration API

Prenumeration API stöder HTTPS följande åtgärder: **Hämta**, **Post**, **Patch**, och **ta bort**.


#### <a name="list-subscriptions"></a>Lista över prenumerationer

Visar alla SaaS-prenumerationer för en utgivare.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Hämta<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
| x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
| authorization      |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Svarskoder:*

Kod: 200 <br/>
Hämtar utgivare och motsvarande prenumerationer för utgivarens alla erbjudanden, baserat på autentiseringstoken.
Svarsnyttolasten:<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Fortsättningstoken är tillgänglig endast om det finns ytterligare ”sidor” i planer för att hämta. 

Kod: 403 <br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren. 

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

Hämtar den angivna SaaS-prenumerationen. Använd det här anropet att hämta licensinformationen och planera information.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token via lösa API: et.   |
|  ApiVersion        |   Versionen av åtgärden för den här begäran.   |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Svarskoder:*

Kod: 200<br>
Hämtar SaaS-prenumerationen från identifierare. Svarsnyttolasten:<br>

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
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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

Använd det här anropet för att ta reda på om det finns några gemensamma eller privata erbjudanden för aktuella utgivaren.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  x-ms-correlationid  | En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Svarskoder:*

Kod: 200<br>
Hämtar en lista över tillgängliga planer för en kund. Svarstext:

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
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren. <br> 

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
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  | En unik sträng som värde för åtgärden på klienten. Den här strängen kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Nyttolasten i begäran:*

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
Felaktig begäran: verifieringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.    |
| authorization      |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Nyttolasten i begäran:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Länk till en resurs att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
Förfrågan om att ändra plan har accepterats. Partnern förväntas att avsöka åtgärden-platsen för att fastställa en lyckad eller misslyckad. <br>

Kod: 400<br>
Felaktig begäran: verifieringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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
>Endast en plan eller kvantitet kan vara uppdaterad i taget, inte båda. Redigerar på en prenumeration med **uppdatering** finns inte i `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Ändra antalet i prenumerationen

Uppdatera kvantitet för prenumerationen.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.    |
| authorization      |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Nyttolasten i begäran:*

```json
Request Body:
{
    "quantity": 5
}
```

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Länka till en resurs att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
Begäran att ändra antalet har accepterats. Partnern förväntas att avsöka åtgärden-platsen för att fastställa en lyckad eller misslyckad. <br>

Kod: 400<br>
Felaktig begäran: verifieringsfel.


Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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
>Endast en plan eller kvantitet kan vara uppdaterad i taget, inte båda. Redigerar på en prenumeration med **uppdatering** finns inte i `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Tar bort en prenumeration

Avbryta prenumerationen och ta bort den angivna prenumerationen.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Ta bort<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.   |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.   |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Svarskoder:*

Kod: 202<br>
Partnern skickar ett anrop för att avbryta prenumerationen en SaaS-prenumeration.<br>

Kod: 400<br>
Ta bort en prenumeration med **ta bort** inte i `allowedCustomerOperations`.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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


### <a name="operations-api"></a>Åtgärdens API

Operations-API: et stöder följande korrigerings- och Get-åtgärder.

#### <a name="list-outstanding-operations"></a>Utestående Liståtgärder 

Visar en lista över väntande åtgärder för den aktuella utgivaren. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Frågeparametrar:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Versionen av åtgärden för den här begäran.                |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Svarskoder:*

Kod: 200<br> Hämtar listan över väntande åtgärder på en prenumeration. Svarsnyttolasten:

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
Felaktig begäran: verifieringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

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

#### <a name="get-operation-status"></a>Hämta Åtgärdsstatus

Gör att utgivaren för att spåra statusen för den angivna utlösta asynkron åtgärden (till exempel `subscribe`, `unsubscribe`, `changePlan`, eller `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Hämta<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Svarskoder:*<br>

Kod: 200<br> Hämtar den angivna väntande SaaS-åtgärd. Svarsnyttolasten:

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
Felaktig begäran: verifieringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.
 
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

Uppdatera status för en åtgärd för att ange lyckad eller misslyckad med angivna värden.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | En unik identifierare för SaaS-prenumerationen som erhålls när du har löst token med hjälp av lösa API.  |
|  operationId       | Åtgärden som utförs. |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  authorization     |  [Hämta JSON web token (JWT) ägarautentisering token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Nyttolasten i begäran:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Svarskoder:*

Kod: 200<br> Ett anrop till informera av slutförandet av en åtgärd på partner-sida. Svaret kan till exempel skulle kunna signalera platser eller planer ändras.

Kod: 400<br>
Felaktig begäran: verifieringsfel.

Kod: 403<br>
Obehörig. Autentiseringstoken har inte angetts eller är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 404<br>
Kunde inte hittas.

Kod: 409<br>
En konflikt uppstod. Till exempel uppfyllde en nyare transaktion redan.

Kod: 500<br> Internt serverfel.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementera en webhook i SaaS-tjänst

Utgivare måste implementera en webhook i den här SaaS tjänsten för att proaktivt meddela användare om ändringar i sin tjänst. SaaS-tjänsten förväntas du anropar API för att validera och auktorisera innan du vidtar en åtgärd på webhook-meddelandet.

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
- `subscribe` (när resursen har aktiverats)
- `unsubscribe` (när resursen har tagits bort)
- `changePlan` (när ändringen plan åtgärden har slutförts)
- `changeQuantity` (när ändringen kvantitet åtgärden har slutförts)
- `suspend` (när resursen har pausats)
- `reinstate` (när resursen har tagits giltigt först när inaktivering)

Där status kan vara något av följande: 
- **NotStarted** <br>
 - **Pågår** <br>
- **Lyckades** <br>
- **Misslyckades** <br>
- **Konflikt** <br>

I ett webhook-meddelande, användbara statusen är antingen **lyckades** och **misslyckades**. Livscykel för en åtgärd är från **NotStarted** till ett avslutat tillstånd som **lyckades**, **misslyckades**, eller **konflikt**. Om du får **NotStarted** eller **InProgress**, fortsätter att begära status för via få API tills åtgärden har nått avslutat tillstånd innan åtgärd vidtas. 

## <a name="mock-apis"></a>Fingera API: er

Du kan använda våra fingerad API: er för att komma igång med utveckling, särskilt prototyper samt som testar projekt. 

Vara värd för slutpunkten: `https://marketplaceapi.microsoft.com/api` (ingen autentisering krävs)<br/>
API-version: `2018-09-15`<br/>
URI-exempel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Sökvägar för API-slutpunkten är gemensamma för både fingerad och verkliga API: er, men API-versioner är olika. Versionen är `2018-09-15` för fingerad versionen och `2018-08-31` för programmets produktionsversion. 

API-anrop i den här artikeln kan göras till fingerad värd-slutpunkten. I allmänhet förväntar sig att få fingerade data tillbaka som svar. Anrop till uppdateringsmetoder för prenumerationen på fingerad API: et returnerar alltid 500. 

## <a name="next-steps"></a>Nästa steg

Utvecklare kan även programmässigt hämta och ändra arbetsbelastningar, erbjudanden och utgivare profiler med hjälp av den [Cloud Partner Portal REST API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
