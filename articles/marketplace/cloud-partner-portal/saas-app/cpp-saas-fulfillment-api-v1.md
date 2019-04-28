---
title: SaaS betjäna API V1 - Azure Marketplace | Microsoft Docs
description: 'Beskriver hur du skapar ett SaaS-erbjudande på Azure Marketplace med associerade uppfyllandet V1 API: er.'
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
ms.date: 03/28/2019
ms.author: pbutlerm
ROBOTS: NOINDEX
ms.openlocfilehash: 4908233280c69a37ea470eed2ef077cb220a7930
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101106"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>SaaS Techtrends API: er Version 1 (inaktuell)

Den här artikeln förklarar hur du skapar ett SaaS-erbjudande med API: er. API: erna, består av REST-metoder och slutpunkter är nödvändiga för att tillåta prenumerationer till SaaS-erbjudande om du har säljer via Azure som valts.  

> [!WARNING]
> Det här den första versionen av API: et för SaaS betjäna är föråldrad; Använd i stället [SaaS Techtrends API V2](./cpp-saas-fulfillment-api-v2.md).  Detta API underhålls för närvarande endast för att hantera befintliga utgivare. 

Följande API: er tillhandahålls för att hjälpa dig integrera SaaS-tjänsten med Azure:

-   Lös
-   Prenumerera
-   Konvertera
-   Avbryt prenumerationen


## <a name="api-methods-and-endpoints"></a>API-metoder och slutpunkter

I följande avsnitt beskrivs de API-metoder och slutpunkter som är tillgängliga för att aktivera prenumerationer för ett SaaS-erbjudande.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-slutpunkt och API-version

Slutpunkten för API för Azure Marketplace är `https://marketplaceapi.microsoft.com`.

Den aktuella API-versionen är `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Lösa prenumeration

Åtgärd efter på lösa slutpunkten tillåter användare att matcha en marketplace-token med en beständig resurs-ID.  Resurs-ID är den unika identifieraren för SAAS-prenumeration. 

När en användare omdirigeras till en ISV-webbplats, innehåller URL: en en token i Frågeparametrar. ISV förväntas Använd denna token och gör en begäran att lösa detta. Svaret innehåller den unika SAAS prenumerations-ID, namn, erbjudande-ID och planera för resursen. Denna token är giltig i endast en timme.

*Förfrågan*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameternamn** |     **Beskrivning**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-versionen        |  Versionen av åtgärden för den här begäran.   |
|  |  |


*Headers*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nej           | En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
| x-ms-correlationid | Nej           | En unik sträng som värde för åtgärden på klienten. Detta kopplat till alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
| innehållstyp       | Ja          | `application/json`                                        |
| Auktorisering      | Ja          | JSON web token (JWT) ägartoken.                    |
| x-ms-marketplace-token| Ja| Token Frågeparametern i URL: en när användaren omdirigeras till SaaS ISV-webbplats från Azure. **Obs!** Den här variabeln är endast giltig för 1 timme. Dessutom kan avkoda URL: en token-värde från webbläsaren innan du använder den.|
|  |  |  |
  

*Svarstext*

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
| subscriptionName| String| Namnet på SaaS-prenumeration som anges av användaren i Azure när du prenumererar på SaaS-tjänsten.|
| OfferId            | String        | Erbjudande-ID som du prenumererar. |
| planId             | String        | Plan-ID som du prenumererar.  |
|  |  |  |


*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token som lösta.                                                            |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller en ogiltig api-version anges. Det gick inte att matcha token eftersom antingen token är felaktigt eller har upphört att gälla (token är endast giltig för 1 timme när åtkomstprinciepn har skapats). |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                                 |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                                |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                                        |
|  |  |  |


*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars det här värdet server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Detta ID används för alla avstämning. |
| Retry-After        | Nej           | Det här värdet anges bara för en 429-svaret.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Prenumerera

Prenumerera-slutpunkten tillåter användare att starta en prenumeration på en SaaS-tjänst för en viss plan och aktivera fakturering i commerce-systemet.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikt ID för SaaS-prenumeration som hämtas när du har löst token via lösa API: et.                              |
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

|  **Huvud-nyckel**        | **Krävs** |  **Beskrivning**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nej         | En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om detta inte anges så kommer en genereras och anges i svarshuvuden. |
| x-ms-correlationid     |   Nej         | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte anges så kommer en genereras och anges i svarshuvuden. |
| If-Match/If-None-Match |   Nej         |   Stark verifieraren ETag-värdet.                                                          |
| innehållstyp           |   Ja        |    `application/json`                                                                   |
|  Auktorisering         |   Ja        |    JSON web token (JWT) ägartoken.                                               |
| x-ms-marketplace-session-mode| Nej | Flagga för att aktivera kontrolläge när du prenumererar på ett SaaS-erbjudande. Om har angetts prenumerationen inte debiteras. Detta är användbart för ISV testscenarier. Ange det **'blobbar i kontrolläge har ”**|
|  |  |  |

*Brödtext*

``` json
{
    "lanId": "",
}
```

| **Elementnamn** | **Datatyp** | **Beskrivning**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Krävs) Sträng        | Plan-Id för SaaS tjänsten användare prenumererar på.  |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aktiveringen av prenumerationen togs emot för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller innehållet i JSON är felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                          |
|  |  |  |

Följ upp på begäran-åtgärdens status på åtgärd-location-rubriken för en 202-svaret. Autentiseringen är samma som andra Marketplace-API: er.

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars det här värdet server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här värdet används för alla avstämning. |
| Retry-After        | Ja          | Intervall med vilken klient kan kontrollera status.                                                       |
| Operation-Location | Ja          | Länka till en resurs att hämta Åtgärdsstatus.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ändra plan slutpunkt

Ändra slutpunkten används att omvandla sina för närvarande prenumererade plan till en ny plan.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

| **Huvud-nyckel**          | **Krävs** | **Beskrivning**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nej           | En unik sträng som värde för att spåra begäran från klienten. Rekommenderar att ett GUID. Om detta inte anges så kommer en genereras och anges i svarshuvuden.   |
| x-ms-correlationid      | Nej           | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte anges så kommer en genereras och anges i svarshuvuden. |
| If-Match /If-None-Match | Nej           | Stark verifieraren ETag-värdet.                              |
| innehållstyp            | Ja          | `application/json`                                        |
| Auktorisering           | Ja          | JSON web token (JWT) ägartoken.                    |
|  |  |  |

*Brödtext*

```json
{
    "planId": ""
}
```

|  **Elementnamn** |  **Datatyp**  | **Beskrivning**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Krävs) Sträng         | Plan-Id för SaaS tjänsten användare prenumererar på.          |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aktiveringen av prenumerationen togs emot för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller innehållet i JSON är felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 409                  | `Conflict`           | En annan åtgärd pågår för prenumerationen.                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                          |
|  |  |  |

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars det här värdet server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här värdet används för alla avstämning. |
| Retry-After        | Ja          | Intervall med vilken klient kan kontrollera status.                                                       |
| Operation-Location | Ja          | Länka till en resurs att hämta Åtgärdsstatus.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Ta bort prenumeration

Borttagningsåtgärden på slutpunkten för prenumerera låter en användare tar bort en prenumeration med angivet ID.

*Förfrågan*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nej           | En unik sträng som värde för att spåra begäran från klienten. Rekommenderar att ett GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.                                                           |
| x-ms-correlationid | Nej           | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om detta inte anges så kommer en genereras och anges i svarshuvuden. |
| Auktorisering      | Ja          | JSON web token (JWT) ägartoken.                    |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aktiveringen av prenumerationen togs emot för en viss plan.                   |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller innehållet i JSON är felaktig. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                   |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                  |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                  |
| 503                  | `ServiceUnavailable` | Tjänsten har stoppats tillfälligt. Försök igen senare.                          |
|  |  |  |

Följ upp på begäran-åtgärdens status på åtgärd-location-rubriken för en 202-svaret. Autentiseringen är samma som andra Marketplace-API: er.

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars detta server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning. |
| Retry-After        | Ja          | Intervall med vilken klient kan kontrollera status.                                                       |
| Operation-Location | Ja          | Länka till en resurs att hämta Åtgärdsstatus.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Hämta Åtgärdsstatus

Den här slutpunkten tillåter användare att spåra status för utlösta async-åtgärd (prenumerera/Unsubscribe/ändra plan).

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{Åtgärds-ID}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikt ID för den åtgärd som utlöste.                |
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nej           | En unik sträng som värde för att spåra begäran från klienten. Rekommenderar att ett GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.   |
| x-ms-correlationid | Nej           | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden.  |
| Auktorisering      | Ja          | JSON web token (JWT) ägartoken.                    |
|  |  |  | 

*Svarstext*

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
| id                 | String        | ID för åtgärden.                                                                      |
| status             | Enum          | Åtgärdsstatus något av följande: `In Progress`, `Succeeded`, eller `Failed`.          |
| resourceLocation   | String        | Länka till den prenumeration som skapades eller ändrades. Detta hjälper klienten att hämta uppdaterade tillståndet post-åtgärd. Det här värdet har inte angetts för `Unsubscribe` åtgärder. |
| skapad            | DateTime      | Åtgärden Skapandetid i UTC.                                                           |
| lastModified       | DateTime      | Senaste uppdateringen på åtgärden i UTC.                                                      |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Lösta get-begäran och svaret innehåller.    |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller en ogiltig api-version har angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars detta server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning. |
| Retry-After        | Ja          | Intervall med vilken klient kan kontrollera status.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Hämta prenumeration

Get-åtgärd på prenumerera på slutpunkten låter en användare att hämta en prenumeration med en viss resurs-identifieraren.

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID för SaaS-prenumeration.                              |
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nej           | En unik sträng som värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.                                                           |
| x-ms-correlationid | Nej           | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
| Auktorisering      | Ja          | JSON web token (JWT) ägartoken.                                                                    |
|  |  |  |

*Svarstext*

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
| id                     | String        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | String        | Erbjudande-ID som du prenumererar.         |
| planId                 | String        | Plan-ID som du prenumererar.          |
| saasSubscriptionName   | String        | Namnet på SaaS-prenumeration.                |
| saasSubscriptionStatus | Enum          | Åtgärdsstatus.  Något av följande:  <br/> - `Subscribed`: Prenumerationen är aktiv.  <br/> - `Pending`: Användaren skapa resursen, men den inte är aktiverad av Programvaruutvecklaren.   <br/> - `Unsubscribed`: Användaren har avbrutit prenumerationen.   <br/> - `Suspended`: Användaren har avbrutit prenumerationen.   <br/> - `Deactivated`:  Azure-prenumeration har inaktiverats.  |
| skapad                | DateTime      | Prenumerationen skapas tidsstämpelvärde i UTC. |
| lastModified           | DateTime      | Prenumeration ändrade tidsstämpelvärde i UTC. |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Lösta get-begäran och svaret innehåller.    |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller en ogiltig api-version har angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten är ned tillfälligt, försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars detta server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning. |
| Retry-After        | Nej           | Intervall med vilken klient kan kontrollera status.                                                       |
| eTag               | Ja          | Länka till en resurs att hämta Åtgärdsstatus.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Hämta prenumerationer

Get-åtgärd på prenumerationer slutpunkt kan användaren att hämta alla prenumerationer för alla erbjudanden från Utvecklaren.

*Förfrågan*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| API-versionen         | Versionen av åtgärden för den här begäran. |
|  |  |

*Headers*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nej           | En unik sträng som värde för att spåra begäran från klienten. Rekommenderar att ett GUID. Om det här värdet inte anges något genereras och anges i svarshuvuden.             |
| x-ms-correlationid | Nej           | En unik sträng som värde för åtgärden på klienten. Det här värdet är för att korrelera alla händelser från klientåtgärden med händelser på serversidan. Om det här värdet inte anges något genereras och anges i svarshuvuden. |
| Auktorisering      | Ja          | JSON web token (JWT) ägartoken.                    |
|  |  |  |

*Svarstext*

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
| id                     | String        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | String        | Erbjudande-ID som du prenumererar.         |
| planId                 | String        | Plan-ID som du prenumererar.          |
| saasSubscriptionName   | String        | Namnet på SaaS-prenumeration.                |
| saasSubscriptionStatus | Enum          | Åtgärdsstatus.  Något av följande:  <br/> - `Subscribed`: Prenumerationen är aktiv.  <br/> - `Pending`: Användaren skapa resursen, men den inte är aktiverad av Programvaruutvecklaren.   <br/> - `Unsubscribed`: Användaren har avbrutit prenumerationen.   <br/> - `Suspended`: Användaren har avbrutit prenumerationen.   <br/> - `Deactivated`:  Azure-prenumeration har inaktiverats.  |
| skapad                | DateTime      | Prenumerationen skapas tidsstämpelvärde i UTC. |
| lastModified           | DateTime      | Prenumeration ändrade tidsstämpelvärde i UTC. |
|  |  |  |

*Svarskoder*

| **HTTP-statuskod** | **Felkod**     | **Beskrivning**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Lösta get-begäran och svaret innehåller.    |
| 400                  | `BadRequest`         | Antingen måste rubriker saknas eller en ogiltig api-version har angetts. |
| 403                  | `Forbidden`          | Anroparen har inte behörighet att utföra den här åtgärden.                      |
| 404                  | `NotFound`           | Prenumerationen hittades inte med angivet ID                                     |
| 429                  | `RequestThrottleId`  | Tjänsten är upptagen begäranden, försök igen senare.                     |
| 503                  | `ServiceUnavailable` | Tjänsten har stoppats tillfälligt. Försök igen senare.                             |
|  |  |  |

*Svarshuvuden*

| **Huvud-nyckel**     | **Krävs** | **Beskrivning**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Fråge-ID som mottogs från klienten.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID är om skickades av klienten, annars detta server Korrelations-ID.                   |
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning. |
| Retry-After        | Nej           | Intervall med vilken klient kan kontrollera status.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS-Webhook

En SaaS-webhook används för att Avisera ändringar proaktivt SaaS tjänsten. Det här INLÄGGET API förväntas vara icke-autentiserade och kommer att anropas av tjänsten Microsoft. SaaS-tjänsten förväntas du anropar API för att validera och auktorisera innan åtgärd vidtas på webhook-meddelandet. 

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
| id  | String       | Unikt ID för den åtgärd som utlöste.                |
| Aktivitets-ID   | String        | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning.               |
| subscriptionId                     | String        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | String        | Erbjudande-ID som du prenumererar. Medföljer endast åtgärden ”Uppdatera”.        |
| publisherId                | String        | Publicerings-ID för SaaS-erbjudande         |
| planId                 | String        | Plan-ID som du prenumererar. Medföljer endast åtgärden ”Uppdatera”.          |
| åtgärd                 | String        | Den åtgärd som utlösa det här meddelandet. Possible values - Activate, Delete, Suspend, Reinstate, Update          |
| timeStamp                 | String        | Tidsstämpelvärde i UTC när den här aviseringen utlöstes.          |
|  |  |  |


## <a name="next-steps"></a>Nästa steg

Utvecklare kan även kan hämta och ändra arbetsbelastningar, erbjudanden och utgivare profiler med hjälp av den [Cloud Partner Portal REST API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
