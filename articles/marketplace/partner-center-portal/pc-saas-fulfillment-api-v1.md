---
title: 'API: er för SaaS-utförande v1 | Azure Marketplace'
description: 'Förklarar hur du skapar och hanterar ett SaaS-erbjudande på Azure Marketplace med associerade v1-API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: 99dd6db7003e0358ddde2438f6897cd767932227
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816573"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>API: er för SaaS-uppfyllelse, version 1 (inaktuell)

Den här artikeln förklarar hur du skapar ett SaaS-erbjudande med API: er. API: erna, som består av REST-metoder och slut punkter, är nödvändiga för att tillåta prenumerationer på ditt SaaS-erbjudande om du har valt att sälja via Azure.  

> [!WARNING]
> Den här inledande versionen av API för SaaS-uppfyllaning är föråldrad. Använd i stället [SaaS uppfyllelse API v2](./pc-saas-fulfillment-api-v2.md).  Den här initiala-versionen av API: et behålls för närvarande endast för att betjäna befintliga utgivare. 

Följande API: er tillhandahålls för att hjälpa dig att integrera din SaaS-tjänst med Azure:

-   Lös
-   Prenumerera
-   Konvertera
-   avbryta prenumerationen


## <a name="api-methods-and-endpoints"></a>API-metoder och slut punkter

I följande avsnitt beskrivs de API-metoder och slut punkter som är tillgängliga för att aktivera prenumerationer för ett SaaS-erbjudande.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-slutpunkt och API-version

Slut punkten för Azure Marketplace-API: et är `https://marketplaceapi.microsoft.com`.

Den aktuella API-versionen är `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Lös prenumeration

POST åtgärd vid åtgärd för att lösa slut punkter låter användarna matcha en Marketplace-token till ett beständigt resurs-ID.  Resurs-ID: t är den unika identifieraren för SAAS-prenumerationen. 

När en användare omdirigeras till en ISV-webbplats innehåller URL: en token i frågeparametrar. ISV förväntas använda denna token och gör en begäran om att lösa det. Svaret innehåller det unika prenumerations-ID: t, namn, erbjudande-ID och plan för resursen. Denna token är endast giltig för en timme.

*Förfrågan*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameter namn** |     **Beskrivning**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-version        |  Den version av åtgärden som ska användas för den här begäran.   |
|  |  |


*Headers*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Nej           | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
| x-MS-correlationId | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här fältet korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| innehålls typ       | Ja          | `application/json`                                        |
| auktoriseringsregeln      | Ja          | JSON Web token (JWT) Bearer-token.                    |
| x-MS-Marketplace-token| Ja| Parametern för token-frågan i URL: en när användaren omdirigeras till SaaS ISV ' s webbplats från Azure. **Obs:** Denna token är endast giltig i 1 timme. Dessutom avkodar URL-värdet för token från webbläsaren innan det används.|
|  |  |  |
  

*Svars text*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Parameternamn** | **Datatyp** | **Beskrivning**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Sträng        | ID för SaaS-prenumerationen.          |
| subscriptionName| Sträng| Namnet på SaaS-prenumerationen som har angetts av användaren i Azure och som prenumererar på SaaS-tjänsten.|
| OfferId            | Sträng        | Erbjudande-ID som användaren prenumererar på. |
| planId             | Sträng        | Plans-ID som användaren prenumererar på.  |
|  |  |  |


*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token har lösts.                                                            |
| 400                  | `BadRequest`         | Antingen saknas nödvändiga huvuden eller så har en ogiltig API-version angetts. Det gick inte att matcha token eftersom antingen token är felaktig eller har upphört att gälla (token är endast giltig i 1 timme när den har genererats). |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                                 |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                                |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd, försök igen senare.                                        |
|  |  |  |


*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta ID används för alla avstämningar. |
| Försök igen        | Nej           | Det här värdet anges bara för 429-svar.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Prenumerera

Prenumerations slut punkten låter användare starta en prenumeration på en SaaS-tjänst för en viss plan och aktivera fakturering i Commerce-systemet.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-version = 2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikt ID för SaaS-prenumeration som erhålls efter att ha löst token via lösnings-API.                              |
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

|  **Rubrik nyckel**        | **Kunna** |  **Beskrivning**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-RequestId         |   Nej         | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om detta inte anges genereras en och anges i svarshuvuden. |
| x-MS-correlationId     |   Nej         | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om detta inte anges genereras en och anges i svarshuvuden. |
| If-Match/If-None-Match |   Nej         |   Starkt validator ETag-värde.                                                          |
| innehålls typ           |   Ja        |    `application/json`                                                                   |
|  auktoriseringsregeln         |   Ja        |    JSON Web token (JWT) Bearer-token.                                               |
| x-MS-Marketplace-session-mode| Nej | Flagga för att aktivera torrt körnings läge när du prenumererar på ett SaaS-erbjudande. Om den är inställd debiteras inte prenumerationen. Detta är användbart för testnings scenarier i ISV. Ställ in det på **"blobbar"**|
|  |  |  |

*Brödtext*

``` json
{
    "lanId": "",
}
```

| **Element namn** | **Datatyp** | **Beskrivning**                      |
|------------------|---------------|--------------------------------------|
| planId           | Kunna Nollängd        | Plan-ID för SaaS-tjänstens användare prenumererar på.  |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS prenumerations aktivering togs emot för en specifik plan.                   |
| 400                  | `BadRequest`         | De obligatoriska huvudena saknas eller så är texten i JSON felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd, försök igen senare.                          |
|  |  |  |

För ett 202-svar följer du anvisningarna i begärans åtgärds status i rubriken åtgärds plats. Autentiseringen är samma som andra Marketplace-API: er.

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Det här värdet används för alla avstämningar. |
| Försök igen        | Ja          | Intervallet som klienten kan kontrol lera status för.                                                       |
| Åtgärds plats | Ja          | Länka till en resurs för att hämta åtgärds statusen.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ändra plan slut punkt

Med ändrings slut punkten kan användaren konvertera sin prenumeration till en ny plan.

**9.0a**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-version = 2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

| **Rubrik nyckel**          | **Kunna** | **Beskrivning**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId          | Nej           | Ett unikt sträng värde för att spåra begäran från klienten. Rekommendera ett GUID. Om detta inte anges genereras en och anges i svarshuvuden.   |
| x-MS-correlationId      | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om detta inte anges genereras en och anges i svarshuvuden. |
| If-Match/If-None-Match | Nej           | Starkt validator ETag-värde.                              |
| innehålls typ            | Ja          | `application/json`                                        |
| auktoriseringsregeln           | Ja          | JSON Web token (JWT) Bearer-token.                    |
|  |  |  |

*Brödtext*

```json
{
    "planId": ""
}
```

|  **Element namn** |  **Datatyp**  | **Beskrivning**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Kunna Nollängd         | Plan-ID för SaaS-tjänstens användare prenumererar på.          |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS prenumerations aktivering togs emot för en specifik plan.                   |
| 400                  | `BadRequest`         | De obligatoriska huvudena saknas eller så är texten i JSON felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd, försök igen senare.                          |
|  |  |  |

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Det här värdet används för alla avstämningar. |
| Försök igen        | Ja          | Intervallet som klienten kan kontrol lera status för.                                                       |
| Åtgärds plats | Ja          | Länka till en resurs för att hämta åtgärds statusen.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Ta bort prenumeration

Åtgärden ta bort i prenumerations slut punkten gör att användaren kan ta bort en prenumeration med ett angivet ID.

*Förfrågan*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-version = 2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-RequestId     | Nej           | Ett unikt sträng värde för att spåra begäran från klienten. Rekommendera ett GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.                                                           |
| x-MS-correlationId | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om detta inte anges genereras en och anges i svarshuvuden. |
| auktoriseringsregeln      | Ja          | JSON Web token (JWT) Bearer-token.                    |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS prenumerations aktivering togs emot för en specifik plan.                   |
| 400                  | `BadRequest`         | De obligatoriska huvudena saknas eller så är texten i JSON felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd. Försök igen senare.                          |
|  |  |  |

För ett 202-svar följer du anvisningarna i begärans åtgärds status i rubriken åtgärds plats. Autentiseringen är samma som andra Marketplace-API: er.

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är detta Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen        | Ja          | Intervallet som klienten kan kontrol lera status för.                                                       |
| Åtgärds plats | Ja          | Länka till en resurs för att hämta åtgärds statusen.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Hämta åtgärds status

Den här slut punkten låter användaren spåra statusen för en utlöst asynkron åtgärd (prenumeration/Avsluta prenumeration/ändra plan).

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ? API-version = 2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikt ID för åtgärden som har utlösts.                |
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Nej           | Ett unikt sträng värde för att spåra begäran från klienten. Rekommendera ett GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.   |
| x-MS-correlationId | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
| auktoriseringsregeln      | Ja          | JSON Web token (JWT) Bearer-token.                    |
|  |  |  | 

*Svars text*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternamn** | **Datatyp** | **Beskrivning**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Sträng        | Åtgärdens ID.                                                                      |
| status             | Enum          | Åtgärds status, något av följande: `In Progress`, `Succeeded`eller `Failed`.          |
| resourceLocation   | Sträng        | Länk till prenumerationen som skapades eller ändrades. Detta hjälper klienten att hämta uppdaterad tillstånds post åtgärd. Det här värdet är inte angivet för `Unsubscribe` åtgärder. |
| Create            | DateTime      | Skapande tid för åtgärden i UTC.                                                           |
| lastModified       | DateTime      | Senaste uppdatering av åtgärden i UTC.                                                      |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | GET-begäran har lösts och texten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas nödvändiga huvuden eller också har en ogiltig API-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Det gick inte att hitta prenumerationen med angivet ID.                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd, försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är detta Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen        | Ja          | Intervallet som klienten kan kontrol lera status för.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Hämta prenumeration

Med åtgärden Hämta i prenumerations slut punkten kan en användare hämta en prenumeration med en viss resurs-ID.

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ? API-version = 2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Nej           | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.                                                           |
| x-MS-correlationId | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| auktoriseringsregeln      | Ja          | JSON Web token (JWT) Bearer-token.                                                                    |
|  |  |  |

*Svars text*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sträng        | ID för SaaS-prenumerations resurs i Azure.    |
| OfferId                | Sträng        | Erbjudande-ID som användaren prenumererar på.         |
| planId                 | Sträng        | Plans-ID som användaren prenumererar på.          |
| saasSubscriptionName   | Sträng        | Namnet på SaaS-prenumerationen.                |
| saasSubscriptionStatus | Enum          | Åtgärds status.  Något av följande:  <br/> - `Subscribed`: prenumerationen är aktiv.  <br/> - `Pending`: användaren skapar resursen, men den aktive ras inte av ISV.   <br/> - `Unsubscribed`: användaren har avbrutit prenumerationen.   <br/> - `Suspended`: användaren har inaktiverat prenumerationen.   <br/> - `Deactivated`: Azure-prenumerationen har pausats.  |
| Create                | DateTime      | Tids stämplings värde för skapande av prenumeration i UTC. |
| lastModified           | DateTime      | Tids stämplings värde för prenumeration har ändrats i UTC. |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | GET-begäran har lösts och texten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas nödvändiga huvuden eller också har en ogiltig API-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd, försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är detta Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen        | Nej           | Intervallet som klienten kan kontrol lera status för.                                                       |
| eTag               | Ja          | Länka till en resurs för att hämta åtgärds statusen.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Hämta prenumerationer

Med slut punkten Hämta åtgärd för prenumerationer kan en användare hämta alla prenumerationer för alla erbjudanden från ISV.

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameter namn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| API-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Headers*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Nej           | Ett unikt sträng värde för att spåra begäran från klienten. Rekommendera ett GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.             |
| x-MS-correlationId | Nej           | Ett unikt sträng värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klient åtgärd med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| auktoriseringsregeln      | Ja          | JSON Web token (JWT) Bearer-token.                    |
|  |  |  |

*Svars text*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sträng        | ID för prenumerations resurs för SaaS i Azure    |
| OfferId                | Sträng        | Erbjudande-ID som användaren prenumererar på         |
| planId                 | Sträng        | Plan-ID som användaren prenumererar på          |
| saasSubscriptionName   | Sträng        | Namnet på SaaS-prenumerationen                |
| saasSubscriptionStatus | Enum          | Åtgärds status.  Något av följande:  <br/> - `Subscribed`: prenumerationen är aktiv.  <br/> - `Pending`: användaren skapar resursen, men den aktive ras inte av ISV.   <br/> - `Unsubscribed`: användaren har avbrutit prenumerationen.   <br/> - `Suspended`: användaren har inaktiverat prenumerationen.   <br/> - `Deactivated`: Azure-prenumerationen har pausats.  |
| Create                | DateTime      | Tids stämplings värde för skapande av prenumeration i UTC |
| lastModified           | DateTime      | Tids stämplings värde för prenumeration har ändrats i UTC |
|  |  |  |

*Svars koder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | GET-begäran har lösts och texten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas nödvändiga huvuden eller också har en ogiltig API-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begär Anden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt avstängd. Försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Rubrik nyckel**     | **Kunna** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ja          | Begärande-ID mottaget från klienten.                                                                   |
| x-MS-correlationId | Ja          | Korrelations-ID om det skickas av klienten, annars är detta Server korrelations-ID.                   |
| x-MS-ActivityId    | Ja          | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen        | Nej           | Intervallet som klienten kan kontrol lera status för.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS-webhook

En SaaS-webhook används för att meddela ändringar proaktivt till SaaS-tjänsten. Detta POST-API förväntas vara oautentiserat och kommer att anropas av Microsoft-tjänsten. SaaS-tjänsten förväntas anropa API: et för åtgärder för att verifiera och auktorisera innan åtgärden för webhook-aviseringen kan utföras. 

*Brödtext*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Sträng       | Unikt ID för åtgärden som har utlösts.                |
| ActivityId   | Sträng        | Ett unikt sträng värde för spårning av begäran från tjänsten. Detta används för alla avstämningar.               |
| subscriptionId                     | Sträng        | ID för SaaS-prenumerations resurs i Azure.    |
| OfferId                | Sträng        | Erbjudande-ID som användaren prenumererar på. Endast tillhandahållits med åtgärden Uppdatera.        |
| publisherId                | Sträng        | Utgivar-ID för SaaS-erbjudandet         |
| planId                 | Sträng        | Plans-ID som användaren prenumererar på. Endast tillhandahållits med åtgärden Uppdatera.          |
| åtgärd                 | Sträng        | Den åtgärd som utlöser det här meddelandet. Möjliga värden-aktivera, ta bort, pausa, återställa, uppdatera          |
| Tidsstämpel                 | Sträng        | Tidsstämpel-värde i UTC när det här meddelandet utlöstes.          |
|  |  |  |


## <a name="next-steps"></a>Nästa steg

Utvecklare kan också program mässigt hämta och ändra arbets belastningar, erbjudanden och utgivar profiler med hjälp av [Cloud Partner Portal REST-API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
