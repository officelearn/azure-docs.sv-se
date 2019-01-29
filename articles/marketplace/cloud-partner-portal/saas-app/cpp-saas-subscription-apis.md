---
title: 'SaaS-sälj via Azure - API: er | Microsoft Docs'
description: 'Beskriver hur du skapar ett SaaS-erbjudande via marketplace API: er.'
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a778723093b226ee0e681c2a95ce4db597a310e5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199156"
---
# <a name="saas-sell-through-azure---apis"></a>SaaS-sälj via Azure - API: er

Den här artikeln förklarar hur du skapar ett SaaS-erbjudande med API: er. API: erna är nödvändiga för att tillåta prenumerationer till SaaS-erbjudande om du har sälj via Azure som valts.  Den här artikeln är uppdelad i två delar:

-   Tjänst-till-tjänst-autentisering mellan en SaaS-tjänsten och Azure Marketplace
-   API-metoder och slutpunkter

Följande API: er tillhandahålls för att hjälpa dig integrera SaaS-tjänsten med Azure:

-   Lös
-   Prenumerera
-   Konvertera
-   Avbryt prenumerationen

Följande diagram visar prenumeration flödet för en ny kund och när dessa API: er används:

![SaaS erbjuder API-flöde](./media/saas-offer-publish-api-flow.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Tjänst till tjänst-autentisering mellan SaaS-tjänsten och Azure marketplace

Azure medför inte några begränsningar på vilken autentisering som SaaS-tjänsten exponerar till sina slutanvändare. När det gäller SaaS tjänsten kommunicerar med Azure Marketplace-API: er, görs autentisering i kontexten för ett Azure Active Directory (Azure AD)-program.

I följande avsnitt beskrivs hur du skapar ett Azure AD-program.


### <a name="register-an-azure-ad-application"></a>Registrera ett Azure AD-program

Alla program som vill använda funktionerna i Azure AD måste först registreras i en Azure AD-klientorganisation. Registreringsprocessen innebär att du ger Azure AD-information om ditt program, till exempel URL: en där den finns, URL: en att skicka svar när en användare autentiseras den URI som identifierar appen och så vidare.

Utför följande steg för att registrera ett nytt program med Azure portal:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Om ditt konto ger dig tillgång till fler än en klickar du på ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3.  I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, och klicka på **ny programregistrering**.

    ![SaaS AD App-registreringar](./media/saas-offer-app-registration.png)

4.  Ange ditt program på sidan Skapa\'s registreringsinformation:
    -   **Namn**: Ange ett beskrivande namn
    -   **Programtyp**: 
        - Välj **Internt** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) som installeras lokalt på en enhet. Den här inställningen används för OAuth-offentliga [interna klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Välj **webbapp / API** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) och [resurs/API-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth-konfidentiella [webbklienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) och offentliga [användar-agent-baserade klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Samma program kan även visa både en klient och resurs/API.
    -   **Inloggnings-URL**: För Web app/API-program, anger du den grundläggande Webbadressen för din app. Till exempel **http://localhost:31544** kanske URL-Adressen för en webbapp som körs på den lokala datorn. Användare använder sedan denna URL för att logga in till ett webbprogram för klienten.
    -   **Omdirigerings-URI**: Ange den URI som används av Azure AD för att returnera tokensvar för interna program. Ange ett specifikt värde till ditt program, till exempel **http://MyFirstAADApp**.

        ![SaaS AD App-registreringar](./media/saas-offer-app-registration-2.png) för specifika exempel på webbprogram eller interna program, Kolla in snabbstarten guidad inställningar som är tillgängliga i avsnittet komma igång i den [Utvecklarguiden för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Klicka på **Skapa** när du är klar. Azure AD tilldelas ett unikt ID till ditt program, och du\'på nytt kommer du till ditt program\'s huvudsakliga registreringssidan. Beroende på om ditt program är ett webbprogram eller ett internt program ges olika alternativ för att lägga till ytterligare funktioner i programmet.

    **Obs:** som standard det nyligen registrerade programmet konfigureras så att endast användare från samma klient att logga in på ditt program.

<a name="api-methods-and-endpoints"></a>API-metoder och slutpunkter
-------------------------

I följande avsnitt beskrivs de API-metoder och slutpunkter som är tillgängliga för att aktivera prenumerationer för ett SaaS-erbjudande.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Hämta en token baserat på Azure AD-app

HTTP-metod

`GET`

*Request URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI-parameter*

|  **Parameternamn**  | **Krävs**  | **Beskrivning**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Klient-ID för det registrerade AAD-programmet   |
|  |  |  |


*Begärandehuvud*

|  **Rubriknamn**  | **Krävs** |  **Beskrivning**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Innehållstyp som är associerade med begäran. Standardvärdet är `application/x-www-form-urlencoded`.  |
|  |  |  |


*Brödtext i begäran*

| **Egenskapsnamn**   | **Krävs** |  **Beskrivning**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  _Typ av beviljande         | True         | Beviljandetyp. Standardvärdet är `client_credentials`.                    |
|  Client_id          | True         |  Client/app-ID som är associerade med Azure AD-app.                  |
|  client_secret      | True         |  Lösenordet som associeras med Azure AD-app.                               |
|  Resurs           | True         |  Målresurs som token begärs. Standardvärdet är `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Svar*

|  **Namn**  | **Typ**       |  **Beskrivning**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Förfrågan lyckades   |
|  |  |  |

*TokenResponse*

Exemplet svarstoken:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-slutpunkt och API-version

Slutpunkten för API för Azure Marketplace är `https://marketplaceapi.microsoft.com`.

Den aktuella API-versionen är `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Lösa prenumeration

Åtgärd efter på lösa slutpunkten tillåter användare att matcha en token med en beständig resurs-ID.

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
        “id”: “”, 
        “subscriptionName”: “”,
        “offerId”:””, 
         “planId”:””
    }     
```

| **Parameternamn** | **Datatyp** | **Beskrivning**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Sträng        | ID för SaaS-prenumerationen.          |
| subscriptionName| Sträng| Namnet på SaaS-prenumeration som anges av användaren i Azure när du prenumererar på SaaS-tjänsten.|
| OfferId            | Sträng        | Erbjudande-ID som du prenumererar. |
| planId             | Sträng        | Plan-ID som du prenumererar.  |
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
| x-ms-activityid    | Ja          | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning. |
| Retry-After        | Nej           | Det här värdet anges bara för en 429-svaret.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Prenumerera

Prenumerera-slutpunkten tillåter användare att starta en prenumeration på en SaaS-tjänst för en viss plan och aktivera fakturering i commerce-systemet.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternamn**  | **Beskrivning**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikt Id för saas-prenumeration som erhålls när du har löst token via lösa API: et.                              |
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
      “planId”:””
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

``` json
                { 
                    “planId”:””
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

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Parameternamn** | **Datatyp** | **Beskrivning**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Sträng        | ID för åtgärden.                                                                      |
| status             | Enum          | Åtgärdsstatus något av följande: `In Progress`, `Succeeded`, eller `Failed`.          |
| resourceLocation   | Sträng        | Länka till den prenumeration som skapades eller ändrades. Detta hjälper klienten att hämta uppdaterade tillståndet post-åtgärd. Det här värdet har inte angetts för `Unsubscribe` åtgärder. |
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

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “” 
  }
```
| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sträng        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | Sträng        | Erbjudande-ID som du prenumererar.         |
| planId                 | Sträng        | Plan-ID som du prenumererar.          |
| saasSubscriptionName   | Sträng        | Namnet på SaaS-prenumeration.                |
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

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “”
  }
```

| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Sträng        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | Sträng        | Erbjudande-ID som du prenumererar.         |
| planId                 | Sträng        | Plan-ID som du prenumererar.          |
| saasSubscriptionName   | Sträng        | Namnet på SaaS-prenumeration.                |
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
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **Parameternamn**     | **Datatyp** | **Beskrivning**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Sträng       | Unikt ID för den åtgärd som utlöste.                |
| Aktivitets-ID   | Sträng        | En unik sträng som värde för spårning av förfrågan från tjänsten. Det här används för alla avstämning.               |
| subscriptionId                     | Sträng        | ID för SaaS-prenumeration-resurs i Azure.    |
| offerId                | Sträng        | Erbjudande-ID som du prenumererar. Medföljer endast åtgärden ”Uppdatera”.        |
| publisherId                | Sträng        | Publicerings-ID för SaaS-erbjudande         |
| planId                 | Sträng        | Plan-ID som du prenumererar. Medföljer endast åtgärden ”Uppdatera”.          |
| åtgärd                 | Sträng        | Den åtgärd som utlösa det här meddelandet. Possible values - Activate, Delete, Suspend, Reinstate, Update          |
| timeStamp                 | Sträng        | Tidsstämpelvärde i UTC när den här aviseringen utlöstes.          |
|  |  |  |
