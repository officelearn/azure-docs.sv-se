---
title: 'SaaS uppfyllelse API: er v1 | Azure Marketplace'
description: Förklarar hur du skapar och hanterar ett SaaS-erbjudande på Azure Marketplace med hjälp av tillhörande Uppfyllelse v1 API:er.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288350"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS uppfyllelse API:er version 1 (föråldrad)

I den här artikeln beskrivs hur du skapar ett SaaS-erbjudande med API:er. API:erna, som består av REST-metoder och slutpunkter, är nödvändiga för att tillåta prenumerationer på ditt SaaS-erbjudande om du har valt Sälja via Azure.  

> [!WARNING]
> Den här första versionen av SaaS Uppfyllelse API är föråldrad; Använd i stället [SaaS Uppfyllelse API V2](./pc-saas-fulfillment-api-v2.md).  Den här första versionen av API:et underhålls för närvarande endast för att betjäna befintliga utgivare. 

Följande API:er tillhandahålls för att hjälpa dig att integrera din SaaS-tjänst med Azure:

-   Lös
-   Prenumerera
-   Konvertera
-   Avbryt prenumeration


## <a name="api-methods-and-endpoints"></a>API-metoder och slutpunkter

I följande avsnitt beskrivs API-metoder och slutpunkter som är tillgängliga för att aktivera prenumerationer för ett SaaS-erbjudande.


### <a name="marketplace-api-endpoint-and-api-version"></a>Slutpunkt och API-version för Marketplace API

Slutpunkten för Azure Marketplace `https://marketplaceapi.microsoft.com`API är .

Den aktuella `api-version=2017-04-15`API-versionen är .


### <a name="resolve-subscription"></a>Lös prenumeration

POST-åtgärd för att lösa slutpunkten gör det möjligt för användare att matcha en marketplace-token till ett beständigt resurs-ID.  Resurs-ID är den unika identifieraren för SAAS-prenumeration. 

När en användare omdirigeras till en ISV:webbplats innehåller URL:en token i frågeparametrarna. ISV förväntas använda den här token och göra en begäran om att lösa den. Svaret innehåller det unika SAAS-prenumerations-ID: n, namn, erbjudande-ID och abonnemang för resursen. Den här token är endast giltig i en timme.

*Förfrågan*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameternamn** |     **Beskrivning**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  Den version av åtgärden som ska användas för den här begäran.   |
|  |  |


*Rubriker*

| **Huvudnyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Inga           | Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
| x-ms-correlationid | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här fältet korrelerar alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| Innehållstyp       | Ja          | `application/json`                                        |
| auktorisering      | Ja          | JSON-webbtokentoken (JWT) bärare token.                    |
| x-ms-marketplace-token| Ja| Parametern tokenfrågefråga i URL:en när användaren omdirigeras till SaaS ISV:s webbplats från Azure. **Anm.:** Denna token är endast giltig i 1 timme. Dessutom avkodar URL tokenvärdet från webbläsaren innan du använder det.|
|  |  |  |
  

*Svarskropp*

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
| id                 | String        | ID för SaaS-prenumerationen.          |
| subscriptionName| String| Namnet på SaaS-prenumerationen som angetts av användaren i Azure när du prenumererar på SaaS-tjänsten.|
| OfferId (erbjudande)            | String        | Erbjudande-ID som användaren prenumererar på. |
| planId (på)             | String        | Plan-ID som användaren prenumererar på.  |
|  |  |  |


*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token har lösts.                                                            |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så har en ogiltig api-version angetts. Det gick inte att lösa token eftersom token antingen är felaktig eller har upphört att gälla (token är endast giltig i 1 timme när den har genererats). |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                                 |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                                |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                                        |
|  |  |  |


*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta ID används för alla avstämningar. |
| Försök igen efter        | Inga           | Det här värdet anges endast för ett 429-svar.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Prenumerera

Med slutpunkten för prenumerera kan användare starta en prenumeration på en SaaS-tjänst för en viss plan och aktivera fakturering i handelssystemet.

**Sätta**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikt ID för SaaS-prenumeration som erhålls efter att ha löst token via Resolve API.                              |
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

|  **Huvudnyckel**        | **Obligatoriskt** |  **Beskrivning**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Inga         | Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om detta inte tillhandahålls genereras en och anges i svarsrubrikerna. |
| x-ms-correlationid     |   Inga         | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte tillhandahålls genereras en och anges i svarsrubrikerna. |
| Om-matcha/om-ingen-matchning |   Inga         |   Starkt validerare ETag-värde.                                                          |
| innehållstyp           |   Ja        |    `application/json`                                                                   |
|  auktorisering         |   Ja        |    JSON-webbtokentoken (JWT) bärare token.                                               |
| x-ms-marketplace-session-läge| Inga | Flagga för att aktivera torrkörningsläge när du prenumererar på ett SaaS-erbjudande. Om den är inställd debiteras inte prenumerationen. Detta är användbart för ISV-testningsscenarier. Ställ in den på **"dryrun"**|
|  |  |  |

*Brödtext*

``` json
{
    "lanId": "",
}
```

| **Elementnamn** | **Datatyp** | **Beskrivning**                      |
|------------------|---------------|--------------------------------------|
| planId (på)           | (Obligatoriskt) Sträng        | Plan-ID för SaaS-tjänstanvändaren prenumererar på.  |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-prenumerationsaktivering har mottagits för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så är JSON-brödtexten felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                          |
|  |  |  |

För ett 202-svar följer du upp statusen för begäranden vid rubriken "Operation-plats". Autentiseringen är samma som andra Marketplace-API:er.

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Det här värdet används för alla avstämningar. |
| Försök igen efter        | Ja          | Intervall med vilket klienten kan kontrollera statusen.                                                       |
| Operation-plats | Ja          | Länk till en resurs för att hämta operationsstatus.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Slutpunkt för ändringsplan

Ändringsslutpunkten gör det möjligt för användaren att konvertera sin för närvarande tecknade plan till ett nytt abonnemang.

**Patch**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

| **Huvudnyckel**          | **Obligatoriskt** | **Beskrivning**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Inga           | Ett unikt strängvärde för att spåra begäran från klienten. Rekommendera en GUID. Om detta inte tillhandahålls genereras en och anges i svarsrubrikerna.   |
| x-ms-correlationid      | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte tillhandahålls genereras en och anges i svarsrubrikerna. |
| Om-matcha /Om-ingen-matchning | Inga           | Starkt validerare ETag-värde.                              |
| innehållstyp            | Ja          | `application/json`                                        |
| auktorisering           | Ja          | JSON-webbtokentoken (JWT) bärare token.                    |
|  |  |  |

*Brödtext*

```json
{
    "planId": ""
}
```

|  **Elementnamn** |  **Datatyp**  | **Beskrivning**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId (på)           |  (Obligatoriskt) Sträng         | Plan-ID för SaaS-tjänstanvändaren prenumererar på.          |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-prenumerationsaktivering har mottagits för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så är JSON-brödtexten felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                          |
|  |  |  |

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är det här värdet serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Det här värdet används för alla avstämningar. |
| Försök igen efter        | Ja          | Intervall med vilket klienten kan kontrollera statusen.                                                       |
| Operation-plats | Ja          | Länk till en resurs för att hämta operationsstatus.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Ta bort prenumeration

Med åtgärden Ta bort på slutpunkten prenumerera kan en användare ta bort en prenumeration med ett visst ID.

*Förfrågan*

**Ta bort**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

| **Huvudnyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Inga           | Ett unikt strängvärde för att spåra begäran från klienten. Rekommendera en GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.                                                           |
| x-ms-correlationid | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte tillhandahålls genereras en och anges i svarsrubrikerna. |
| auktorisering      | Ja          | JSON-webbtokentoken (JWT) bärare token.                    |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-prenumerationsaktivering har mottagits för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så är JSON-brödtexten felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et                                  |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt nere. Försök igen senare.                          |
|  |  |  |

För ett 202-svar följer du upp statusen för begäranden vid rubriken "Operation-plats". Autentiseringen är samma som andra Marketplace-API:er.

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är detta serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen efter        | Ja          | Intervall med vilket klienten kan kontrollera statusen.                                                       |
| Operation-plats | Ja          | Länk till en resurs för att hämta operationsstatus.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Hämta åtgärdsstatus

Med den här slutpunkten kan användaren spåra status för en utlöst asynkron åtgärd (abonnemang för prenumeration/ändring).

*Förfrågan*

**Få**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikt ID för åtgärden som utlöses.                |
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

| **Huvudnyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Inga           | Ett unikt strängvärde för att spåra begäran från klienten. Rekommendera en GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.   |
| x-ms-correlationid | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.  |
| auktorisering      | Ja          | JSON-webbtokentoken (JWT) bärare token.                    |
|  |  |  | 

*Svarskropp*

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
| id                 | String        | ID för operationen.                                                                      |
| status             | Enum          | Åtgärdsstatus, något av `In Progress` `Succeeded`följande: `Failed`, eller .          |
| resourceLocation   | String        | Länk till prenumerationen som har skapats eller ändrats. Detta hjälper klienten att få uppdaterade tillståndspoståtgärd. Det här värdet `Unsubscribe` har inte angetts för operationer. |
| Skapad            | DateTime      | Operation skapa tid i UTC.                                                           |
| lastModified       | DateTime      | Senaste uppdatering om åtgärden i UTC.                                                      |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Löst begäran om erbehåll och brödtexten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så har en ogiltig api-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et.                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                             |
|  |  |  |

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är detta serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen efter        | Ja          | Intervall med vilket klienten kan kontrollera statusen.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Få prenumeration

Med åtgärden Hämta på prenumerationsslutpunkten kan en användare hämta en prenumeration med en viss resursidentifierare.

*Förfrågan*

**Få**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

| **Huvudnyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Inga           | Ett unikt strängvärde för att spåra begäran från klienten, helst ett GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.                                                           |
| x-ms-correlationid | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| auktorisering      | Ja          | JSON-webbtokentoken (JWT) bärare token.                                                                    |
|  |  |  |

*Svarskropp*

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
| id                     | String        | ID för SaaS-prenumerationsresurs i Azure.    |
| offerId (erbjudandeId)                | String        | Erbjudande-ID som användaren prenumererar på.         |
| planId (på)                 | String        | Plan-ID som användaren prenumererar på.          |
| saasSubscriptionName   | String        | Namnet på SaaS-prenumerationen.                |
| saasSubscriptionStatus | Enum          | Status för åtgärden.  Något av följande:  <br/> - `Subscribed`: Prenumerationen är aktiv.  <br/> - `Pending`: Användaren skapar resursen men den aktiveras inte av ISV.   <br/> - `Unsubscribed`: Användaren har avslutat prenumerationen.   <br/> - `Suspended`: Användaren har avbrutit prenumerationen.   <br/> - `Deactivated`: Azure-prenumerationen är avstängd.  |
| Skapad                | DateTime      | Tidsstämpelvärde för skapande av prenumeration i UTC. |
| lastModified           | DateTime      | Tidsstämpelvärde för prenumeration i UTC. |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Löst begäran om erbehåll och brödtexten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så har en ogiltig api-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                             |
|  |  |  |

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är detta serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen efter        | Inga           | Intervall med vilket klienten kan kontrollera statusen.                                                       |
| Etag               | Ja          | Länk till en resurs för att hämta operationsstatus.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Få prenumerationer

Med slutpunkten Hämta på prenumerationer kan en användare hämta alla prenumerationer för alla erbjudanden från ISV.

*Förfrågan*

**Få**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | Den version av åtgärden som ska användas för den här begäran. |
|  |  |

*Rubriker*

| **Huvudnyckel**     | **Obligatoriskt** | **Beskrivning**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Inga           | Ett unikt strängvärde för att spåra begäran från klienten. Rekommendera en GUID. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna.             |
| x-ms-correlationid | Inga           | Ett unikt strängvärde för åtgärder på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges genereras ett och anges i svarsrubrikerna. |
| auktorisering      | Ja          | JSON-webbtokentoken (JWT) bärare token.                    |
|  |  |  |

*Svarskropp*

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
| id                     | String        | ID för SaaS-prenumerationsresurs i Azure    |
| offerId (erbjudandeId)                | String        | Erbjudande-ID som användaren prenumererar på         |
| planId (på)                 | String        | Plan-ID som användaren prenumererar på          |
| saasSubscriptionName   | String        | Namnet på SaaS-prenumerationen                |
| saasSubscriptionStatus | Enum          | Status för åtgärden.  Något av följande:  <br/> - `Subscribed`: Prenumerationen är aktiv.  <br/> - `Pending`: Användaren skapar resursen men den aktiveras inte av ISV.   <br/> - `Unsubscribed`: Användaren har avslutat prenumerationen.   <br/> - `Suspended`: Användaren har avbrutit prenumerationen.   <br/> - `Deactivated`: Azure-prenumerationen är avstängd.  |
| Skapad                | DateTime      | Tidsstämpelvärde för skapande av prenumeration i UTC |
| lastModified           | DateTime      | Tidsstämpelvärde för prenumeration i UTC |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Löst begäran om erbehåll och brödtexten innehåller svaret.    |
| 400                  | `BadRequest`         | Antingen saknas obligatoriska rubriker eller så har en ogiltig api-version angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med det angivna ID:et                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen med att bearbeta begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är tillfälligt nere. Försök igen senare.                             |
|  |  |  |

*Svarsrubriker*

| **Rubriknyckel**     | **Obligatoriskt** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Begärande-ID som tagits emot från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID om det skickas av klienten, annars är detta serverns korrelations-ID.                   |
| x-ms-activityid    | Ja          | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta används för alla avstämningar. |
| Försök igen efter        | Inga           | Intervall med vilket klienten kan kontrollera statusen.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

En SaaS webhook används för att meddela ändringar proaktivt till SaaS-tjänsten. Detta POST API förväntas vara oautentiserade och kommer att anropas av Microsoft-tjänsten. SaaS-tjänsten förväntas anropa åtgärds-API:et för att validera och auktorisera innan du vidtar åtgärder på webhook-meddelandet. 

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
| id  | String       | Unikt ID för åtgärden som utlöses.                |
| activityId (på)   | String        | Ett unikt strängvärde för att spåra begäran från tjänsten. Detta används för alla avstämningar.               |
| subscriptionId                     | String        | ID för SaaS-prenumerationsresurs i Azure.    |
| offerId (erbjudandeId)                | String        | Erbjudande-ID som användaren prenumererar på. Tillhandahålls endast med åtgärden "Uppdatera".        |
| publisherId                | String        | Publisher-ID för SaaS-erbjudandet         |
| planId (på)                 | String        | Plan-ID som användaren prenumererar på. Tillhandahålls endast med åtgärden "Uppdatera".          |
| åtgärd                 | String        | Åtgärden som utlöser det här meddelandet. Möjliga värden - Aktivera, Ta bort, pausa, återställa, uppdatera          |
| Tidsstämpel                 | String        | TImestamp-värde i UTC när det här meddelandet utlöstes.          |
|  |  |  |


## <a name="next-steps"></a>Nästa steg

Utvecklare kan också programmässigt hämta och manipulera arbetsbelastningar, erbjudanden och utgivarprofiler med hjälp av [CLOUD Partner Portal REST API:er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
