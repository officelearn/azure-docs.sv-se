---
title: SaaS betjäna API V2 | Azure Marketplace
description: 'Beskriver hur du skapar ett SaaS-erbjudande på Azure Marketplace med associerade uppfyllandet V2 API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: e1715c2cb66398ff7ca55c0ccdbfe50685fae76e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941986"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS Techtrends API: er för Version 2 

Den här artikeln beskriver API som gör det möjligt för oberoende programvaruleverantörer (ISV) för att integrera sina SaaS-program med Azure Marketplace. Detta API kan ISV-program att delta i alla commerce aktiverat kanaler: direct partnerledd (återförsäljare) ledde till fältet.  Detta API är ett krav för lista transactable SaaS-erbjudanden på Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>Hanterar livscykeln för SaaS-prenumeration

Microsoft SaaS Service hanterar hela livscykeln för ett SaaS-prenumerationsinköpet och använder betjäna API som en mekanism för att driva den faktiska betjäna ändringar i planer och borttagning av prenumerationen med ISV: er. Kunden faktureras baserat på status för SaaS-prenumeration som Microsoft underhåller. Följande diagram visar status och vilka åtgärder som genomför ändringarna emellan.

![Livscykelstadier för SaaS-prenumeration](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

I följande tabell visas de etablering tillstånden för ett SaaS-prenumeration, inklusive en beskrivning och sekvens diagram för varje (om tillämpligt). 

#### <a name="provisioning"></a>Etablering

När en kund initierar ett köp, får ISV: er den här informationen i en Auth-kod i en kund interaktiva webbsida med en URL-parameter. Till exempel: `https://contoso.com/signup?token=..`, där startsida sidan URL: en provider i partnercenter är `https://contoso.com/signup`. Auth-kod kan verifieras och bytas ut mer information om vad som ska etableras genom att anropa API: et lösa.  När SaaS tjänsten har slutfört etablering, skickar den ett aktivera anrop för att signalera att uppfyllandet är klar och kunden kan faktureras.  I följande diagram visas de API-anrop för ett scenario med etablering.  

![API-anrop för att etablera en SaaS-tjänst.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Etablerat

Det här tillståndet är i stabilt tillstånd för en etablerad tjänst.

#### <a name="provisioning-for-update"></a>Etablering för uppdatering
(från marketplace) 

Det här tillståndet motsvarar en uppdatering till en befintlig service väntar. Sådan uppdatering kan initieras av kunden på marketplace eller på SaaS-tjänsten (endast för direkt till kundtransaktioner). Följande diagram visar åtgärderna när en uppdatering har initierats från marketplace.

![API-anrop när uppdateringen har initierats från marketplace.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Etablering för uppdatering  
(från SaaS tjänsten) 

Följande diagram visar åtgärderna när en uppdatering initieras av SaaS-tjänsten. (Webhook-anropet ersätts av en uppdatering till den prenumeration som initieras av SaaS Service. 

![API-anrop när uppdateringen har initierats av SaaS-tjänsten.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Tillfälligt avbruten

Det här tillståndet anger att en kund betalning inte har tagits emot. I princip, kommer vi ger kunden en respitperiod innan unfulfilling prenumerationen. När en prenumeration är i det här tillståndet: 

- Som en ISV kan du välja att försämras eller blockera åtkomst till tjänsten. 
- Prenumerationen måste hållas i ett återställningsbara tillstånd som kan återställa samtliga funktioner utan att förlora data eller inställningar. 
- Du kan förvänta dig att hämta en återställa-begäran för den här prenumerationen via uppfyllandet API eller ta bort etableringsbegäran i slutet av respitperioden. 

#### <a name="unsubscribed"></a>Borttagen 

Prenumerationer nå det här tillståndet som svar på en explicit kundfråga eller som svar på utebliven betalning av avgifter. Förväntar sig från ISV: er är att kundens data bevaras för återställning på begäran för en period av minst X dagar och tas sedan bort. 


## <a name="api-reference"></a>API-referens

Det här avsnittet beskrivs SaaS *prenumeration API* och *Operations API*.  Värdet för den `api-version` parametern för version 2 API: er är `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Parametern-och entiteten

Tabellen nedan innehåller definitioner för gemensamma parametrar och entiteter som används av Techtrends API: er.

|     Entity/Parameter     |     Definition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | GUID-identifierare för en SaaS-resurs  |
| `name`                   | Eget namn för den här resursen från kunden |
| `publisherId`            | Unik sträng som identifierare för varje utgivare, till exempel ”contoso” |
| `offerId`                | Unik sträng som identifierare för varje erbjudande, till exempel ”offer1”  |
| `planId`                 | Unik sträng som identifierare för varje plan/sku, till exempel ”silver” |
| `operationId`            | GUID-identifierare för en viss åtgärd  |
|  `action`                | Den åtgärd som utförs på en resurs, antingen `subscribe`, `unsubscribe`, `suspend`, `reinstate`, eller `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Globalt unika identifierare ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) är 128-bitars (32 hexadecimala) värden som genereras vanligtvis automatiskt. 

#### <a name="resolve-a-subscription"></a>Lösa en prenumeration 

Lös slutpunkten gör det möjligt för utgivare att matcha en marketplace-token med en beständig resurs-ID. Resurs-ID är den unika identifieraren för SAAS-prenumeration.  När en användare omdirigeras till en ISV-webbplats, innehåller URL: en en token i Frågeparametrar. ISV förväntas Använd denna token och gör en begäran att lösa detta. Svaret innehåller den unika SAAS prenumerations-ID, namn, erbjudande-ID och planera för resursen. Denna token är giltig i endast en timme. 

**Post:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version av åtgärden för den här begäran  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  Unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Token frågeparameter i URL: en när användaren omdirigeras till SaaS-ISV-webbplats från Azure (för t.ex.: `https://contoso.com/signup?token=..`). *Obs!* URL: en avkodar token-värde från webbläsaren innan du använder den.  |

*Svarskoder:*

Kod: 200<br>
Löser täckande token till en SaaS-prenumeration.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran. x-ms-marketplace-token är saknas, är felaktig eller har upphört att gälla.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

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


#### <a name="list-subscriptions"></a>Lista prenumerationer

Visar alla SaaS-prenumerationer för en utgivare.

**Hämta:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Frågeparametrar:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
| x-ms-correlationid |  Unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
| Auktorisering      |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Svarskoder:*

Kod: 200<br>
Baserat på auktoriseringstoken, hämta utgivare och motsvarande prenumerationer för erbjudanden som alla utgivare.<br> Svarsnyttolasten:<br>

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
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Fortsättningstoken är endast tillgänglig om det finns ytterligare ”sidor” i planer för att hämta. 


Kod: 403 <br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren. 

Kod: 500 Internt serverfel

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

**Hämta:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Unik identifierare för SaaS-prenumeration som erhålls när du har löst token via lösa API   |
|  ApiVersion        |   Version av åtgärden för den här begäran   |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  Unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Svarskoder:*

Kod: 200<br>
Hämtar SaaS-prenumeration från identifierare<br> Svarsnyttolasten:<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kod: 404<br>
Kunde inte hittas<br> 

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista tillgängliga planer

Använd det här anropet för att ta reda på om det finns några privata/offentliga erbjudanden för aktuella utgivaren.

**Hämta:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Version av åtgärden för den här begäran  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
|  x-ms-correlationid  | Unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Svarskoder:*

Kod: 200<br>
Hämta en lista över tillgängliga planer för en kund.<br>

Svarstext:

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
Kunde inte hittas<br> 

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren. <br> 

Kod: 500<br>
Internt serverfel<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktivera en prenumeration

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version av åtgärden för den här begäran  |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  | Unik sträng som värde för åtgärden på klienten. Den här strängen kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Begäran:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Svarskoder:*

Kod: 202<br>
Aktiverar prenumerationen.<br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

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

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.    |
| Auktorisering      |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
| Operation-Location | Länka till en resurs att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
Förfrågan om att ändra plan har accepterats. ISV förväntas att avsöka åtgärden-platsen för att fastställa en lyckades/misslyckades. <br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel.

>[!Note]
>Endast en plan eller kvantitet kan vara uppdaterad i taget, inte båda. Redigerar på en prenumeration med **uppdatering** finns inte i `allowedCustomerOperations`.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Ändra antalet i prenumerationen

Uppdatera kvantitet för prenumerationen.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.    |
| Auktorisering      |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
Accepteras. Begäran att ändra antalet har accepterats. ISV förväntas att avsöka åtgärden-platsen för att fastställa en lyckades/misslyckades. <br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel.

>[!Note]
>Endast en plan eller kvantitet kan vara uppdaterad i taget, inte båda. Redigerar på en prenumeration med **uppdatering** finns inte i `allowedCustomerOperations`.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Ta bort en prenumeration

Avbryta prenumerationen och ta bort den angivna prenumerationen.

**Ta bort:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.   |
|  x-ms-correlationid  |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.   |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Svarskoder:*

Kod: 200<br>
ISV initierade anrop till indikerar avbryta prenumerationen på en SaaS-prenumeration.<br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Ta bort en prenumeration med **ta bort** inte i `allowedCustomerOperations`.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

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

**Hämta:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Frågeparametrar:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Versionen av åtgärden för den här begäran.                |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API.  |

*Rubriker i begäran:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Svarskoder:*

Kod: 200<br> Hämtar listan över väntande åtgärder på en prenumeration.<br>
Svarsnyttolasten:

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

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 500<br>
Internt serverfel

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Hämta Åtgärdsstatus

Gör att utgivaren för att spåra statusen för den angivna utlösta asynkron åtgärden (prenumerera / Avbryt prenumeration / ändra planera / ändra kvantitet).

**Hämta:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     |[Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Svarskoder:* Kod: 200<br> Hämtar den angivna väntande åtgärd för SaaS<br>
Svarsnyttolasten:

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

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.
 
Kod: 500<br> Internt serverfel

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Uppdatera status för en åtgärd

Uppdatera status för en åtgärd för att indikera lyckades/misslyckades med angivna värden.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Versionen av åtgärden för den här begäran.  |
| subscriptionId     | Unik identifierare för SaaS-prenumeration som erhålls när du har löst token med hjälp av lösa API.  |
|  operationId       | Åtgärden som utförs. |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden. |
|  Auktorisering     |  [Hämta JSON web token (JWT) ägar-token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Nyttolasten i begäran:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Svarskoder:*

Kod: 200<br> Anropa för att informera av slutförandet av en åtgärd på ISV-sida. Svaret kan till exempel skulle kunna signalera ändringar av platser/planer.

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella utgivaren.

Kod: 409<br>
En konflikt uppstod. Till exempel har en nyare transaktion redan uppfyllts

Kod: 500<br> Internt serverfel

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook på SaaS-tjänst

Utgivare måste implementera en webhook i den här SaaS tjänsten för att proaktivt meddela användare om ändringar i sin tjänst. API: et förväntas vara icke-autentiserade och kommer att anropas av Microsoft SaaS-tjänsten. SaaS-tjänsten förväntas du anropar API för att validera och auktorisera innan du vidtar en åtgärd på webhook-meddelandet.

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}

Where action can be one of these: 
       Subscribe, (When the resource has been activated)
       Unsubscribe, (When the resource has been deleted)
       ChangePlan, (When the change plan operation has completed)
       ChangeQuantity, (When the change quantity operation has completed),
       Suspend, (When resource has been suspended)
       Reinstate, (When resource has been reinstated after suspension)
```


## <a name="mock-api"></a>Fingera API

Du kan använda våra fingerad API: er för att komma igång med utveckling, särskilt prototyper och testa projekt. 

Värd-slutpunkt: `https://marketplaceapi.microsoft.com/api` API-Version: `2018-09-15` Ingen autentisering krävs exempel Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15`

API-anrop i den här artikeln kan göras till fingerad värd-slutpunkten. Du kan förvänta dig att få fingerade data tillbaka som svar.


## <a name="next-steps"></a>Nästa steg

Utvecklare kan även kan hämta och ändra arbetsbelastningar, erbjudanden och utgivare profiler med hjälp av den [Cloud Partner Portal REST API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
