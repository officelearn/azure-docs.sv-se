---
title: SaaS betjäna API V2 – Azure Marketplace | Microsoft Docs
description: 'Beskriver hur du skapar ett SaaS-erbjudande på Azure Marketplace med associerade uppfyllandet V2 API: er.'
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
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 81213d1f7cfeb7ea10cdadfb124047ecb76aa7d4
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352093"
---
# <a name="saas-fulfillment-api"></a>SaaS betjäna API

Den här artikeln beskriver API som gör det möjligt för oberoende programvaruleverantörer (ISV) för att integrera sina SaaS-program med Azure Marketplace. Detta API kan ISV-program att delta i alla commerce aktiverat kanaler: direct partnerledd (återförsäljare) ledde till fältet.  Detta API är ett krav för lista transactable SaaS-erbjudanden på Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>Hanterar livscykeln för SaaS-prenumeration

Microsoft SaaS Service hanterar hela livscykeln för ett SaaS-prenumerationsinköpet och använder betjäna API som en mekanism för att driva den faktiska betjäna ändringar i planer och borttagning av prenumerationen med ISV: er. Kunden faktureras baserat på status för SaaS-prenumeration som Microsoft underhåller. Följande diagram visar status och vilka åtgärder som genomför ändringarna emellan.

![Livscykelstadier för SaaS-prenumeration](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

I följande tabell visas de etablering tillstånden för ett SaaS-prenumeration, inklusive en beskrivning och sekvens diagram för varje (om tillämpligt). 

#### <a name="provisioning"></a>Etablering

När en kund initierar ett köp, får ISV: er den här informationen i en AuthCode på en kund interaktiva webbsida med en URL-parameter. AuthCode kan verifieras och bytas ut mer information om vad som ska etableras.  När SaaS tjänsten har slutfört etablering, skickar den ett aktivera anrop för att signalera att uppfyllandet är klar och kunden kan faktureras.  I följande diagram visas de API-anrop för ett scenario med etablering.  

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

Det här avsnittet beskrivs SaaS *prenumeration API* och *Operations API*.


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
| Auktorisering      |  JSON web token (JWT) ägartoken.  |

*Svarskoder:*

Kod: 200<br>
Baserat på auth token get utgivare och motsvarande prenumerationer för erbjudanden som alla utgivare.<br> Svarsnyttolasten:<br>

```json
{
  "subscriptions": [
      {
          "id": "",
          "name": "CloudEndure for Production use",
          "publisherId": "cloudendure",
          "offerId": "ce-dr-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "status": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Kod: 403 <br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren. 

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
|  Auktorisering     |  JSON web token (JWT) ägartoken  |

*Svarskoder:*

Kod: 200<br>
Hämtar saas-prenumeration från identifierare<br> Svarsnyttolasten:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"CloudEndure for Production use",
        "publisherId": "cloudendure",
        "offerId": "ce-dr-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kod: 404<br>
Kunde inte hittas<br> 

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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

Använd det här anropet för att ta reda på om det finns några privata/offentliga erbjudanden för den aktuella användaren.

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
|  Auktorisering     |  JSON web token (JWT) ägartoken |

*Svarskoder:*

Kod: 200<br>
Hämta en lista över tillgängliga planer för en kund.<br>

```json
Response Body:
[{
    "planId": "silver",
    "displayName": "Silver",
    "isPrivate": false
},
{
    "planId": "silver-private",
    "displayName": "Silver-private",
    "isPrivate": true
}]
```

Kod: 404<br>
Kunde inte hittas<br> 

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren. <br> 

Kod: 500<br>
Internt serverfel<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Lösa en prenumeration 

Lös slutpunkten gör det möjligt för användare att matcha en marketplace-token med en beständig resurs-ID. Resurs-ID är den unika identifieraren för SAAS-prenumeration.  När en användare omdirigeras till en ISV-webbplats, innehåller URL: en en token i Frågeparametrar. ISV förväntas Använd denna token och gör en begäran att lösa detta. Svaret innehåller den unika SAAS prenumerations-ID, namn, erbjudande-ID och planera för resursen. Denna token är giltig i endast en timme. 

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

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
|  Auktorisering     |  JSON web token (JWT) ägartoken  |
|  x-ms-marketplace-token  |  Token frågeparameter i URL: en när användaren omdirigeras till SaaS-ISV-webbplats från Azure. *Obs!* URL: en avkodar token-värde från webbläsaren innan du använder den. |

*Svarskoder:*

Kod: 200<br>
Löser täckande token till en SaaS-prenumeration.<br>

```json
Response body:
{
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",  
    "subscriptionName": "My Saas application",
    "offerId": "ce-dr-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": " be750acb-00aa-4a02-86bc-476cbe66d7fa"  
}
```

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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
|  x-ms-correlationid  | Unik sträng som värde för åtgärden på klienten. Detta kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     |  JSON web token (JWT) ägartoken |

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
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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

#### <a name="update-a-subscription"></a>Uppdatera en prenumeration

Uppdatera eller ändra ett prenumerationsavtal med angivna värden.

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
| Auktorisering      |  JSON web token (JWT) ägartoken.  |

*Nyttolasten i begäran:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Länka till en resurs att hämta åtgärdens status.   |

*Svarskoder:*

Kod: 202<br>
ISV initierar en ändra plan eller en ändring kvantitet. <br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel.

>[!Note]
>Endast en plan eller kvantitet kan vara uppdaterad i taget, inte båda. Redigerar på en prenumeration med **uppdatering** finns inte i `allowedCustomerOperations`.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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
|  Auktorisering     |  JSON web token (JWT) ägartoken.   |

*Svarskoder:*

Kod: 200<br>
ISV initierade anrop till indikerar avbryta prenumerationen på en SaaS-prenumeration.<br>

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Ta bort en prenumeration med **ta bort** inte i `allowedCustomerOperations`.

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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


#### <a name="update-a-subscription"></a>Uppdatera en prenumeration

Uppdatera en prenumeration med angivna värden.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operation/<operationId>?api-version=<ApiVersion>`**

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
|  Auktorisering     |  JSON web token (JWT) ägartoken.  |

*Nyttolasten i begäran:*

```json
{
    "planId": "",
    "quantity": "",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Svarskoder:*

Kod: 200<br> Anropa för att informera av slutförandet av en åtgärd på ISV-sida. Det kan till exempel ändring av platser/planer.

Kod: 404<br>
Kunde inte hittas

Kod: 400<br>
Felaktig begäran-verifieringsfel

Kod: 403<br>
Ej auktoriserad. Auth-token har inte angetts är ogiltig eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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

#### <a name="list-outstanding-operations"></a>Utestående Liståtgärder 

Visar en lista över väntande åtgärder för den aktuella användaren. 

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
|  Auktorisering     |  JSON web token (JWT) ägartoken.  |

*Svarskoder:*

Kod: 200<br> Hämtar listan över väntande åtgärder på en prenumeration.<br>
Svarsnyttolasten:

```json
[{
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",  
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.

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

Gör att användaren kan spåra statusen för en utlösta async-åtgärd (prenumerera/Unsubscribe/ändra plan).

**Hämta:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Frågeparametrar:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versionen av åtgärden för den här begäran.  |

*Rubriker i begäran:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  ` application/json`   |
|  x-ms-requestid    |   En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
|  x-ms-correlationid |  En unik sträng som värde för åtgärden på klienten. Den här parametern är kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges så kommer en genereras och anges i svarshuvuden.  |
|  Auktorisering     | JSON web token (JWT) ägartoken.  |

*Svarskoder:* Kod: 200<br> Hämtar listan över alla väntande SaaS-åtgärder<br>
Svarsnyttolasten:

```json
Response body:
[{
    "id  ": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Ej auktoriserad. Auth-token har inte angetts är ogiltig, eller begäran försöker komma åt ett företagsförvärv som inte tillhör den aktuella användaren.
 
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
    "operationId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```

<!-- Review following, might not be needed when this publishes -->


## <a name="mock-api"></a>Fingera API

Du kan använda våra fingerad API: er för att komma igång med utveckling, särskilt prototyper och testa projekt. 

Värd-slutpunkt: https://marketplaceapi.microsoft.com/api API-Version: 2018-09-15 ingen autentisering krävs exempel Uri: https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15

API-anrop i den här artikeln kan göras till fingerad värd-slutpunkten. Du kan förvänta dig att få fingerade data tillbaka som svar.


## <a name="next-steps"></a>Nästa steg

Utvecklare kan även kan hämta och ändra arbetsbelastningar, erbjudanden och utgivare profiler med hjälp av den [Cloud Partner Portal REST API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
