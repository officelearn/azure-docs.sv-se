---
title: Registrera en SaaS-ansökan | Azure Marketplace
description: Förklarar hur du registrerar ett SaaS-program med Azure-portalen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275721"
---
# <a name="register-a-saas-application"></a>Registrera ett SaaS-program

I den här artikeln beskrivs hur du registrerar ett SaaS-program med Microsoft [Azure-portalen](https://portal.azure.com/).  Efter en lyckad registrering får du en Azure Active Directory -säkerhetstoken (Azure AD) som du kan använda för att komma åt SaaS Fulfillment API:er.  Mer information om Azure AD finns i [Vad är autentisering?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Autentiseringsflöde för tjänst

I följande diagram visas prenumerationsflödet för en ny kund och när dessa API:er används:

![SaaS erbjuder API-flöde](./media/saas-offer-publish-api-flow-v1.png)

Azure medför inga begränsningar för autentiseringen som SaaS-tjänsten exponerar för sina slutanvändare. Autentisering med SaaS Fulfillment API:er utförs dock med en Azure AD-säkerhetstoken, som vanligtvis erhålls genom att registrera SaaS-appen via Azure-portalen. 


## <a name="register-an-azure-ad-secured-app"></a>Registrera en Azure AD-säker app

Alla program som vill använda funktionerna i Azure AD måste först registreras i en Azure AD-klientorganisation. Den här registreringsprocessen innebär att ge Azure AD-information om ditt program, till exempel webbadressen där det finns, url:en för att skicka svar efter att en användare har autentiserats, URI:n som identifierar appen och så vidare.  Så här registrerar du ett nytt program med Azure-portalen:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Om ditt konto ger dig åtkomst till mer än ett klickar du på ditt konto i det övre högra hörnet och ställer in portalsessionen på önskad Azure AD-klientorganisation.
3.  Klicka på **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret, klicka på **Appregistreringar**och klicka på **Ny programregistrering**.

    ![Registreringar av SaaS AD-appar](./media/saas-offer-app-registration-v1.png)

4.  På sidan Skapa anger\'du registreringsinformationen för din ansökan:
    -   **Namn**: Ange ett meningsfullt programnamn
    -   **Typ av program:** 
        - Välj **Internt** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) som installeras lokalt på en enhet. Den här inställningen används för OAuth-offentliga [interna klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Välj **Webbapp/API** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) och [resurs-/API-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth konfidentiella [webbklienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) och offentliga [användaragent-baserade klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Samma program kan även visa både en klient och resurs/API.
    -   **Inloggnings-URL:** För webbapp/API-program anger du appens grundläggande URL. Det kan **http://localhost:31544** till exempel vara WEBBADRESSEN till en webbapp som körs på den lokala datorn. Användarna skulle sedan använda den här URL:en för att logga in på ett webbklientprogram.
    -   **Omdirigera URI:** För native-program, ange URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är **http://MyFirstAADApp**specifikt för ditt program, till exempel .

        ![Registreringar av SaaS AD-appar](./media/saas-offer-app-registration-v1-2.png)

        Om du vill ha specifika exempel för webbprogram eller inbyggda program finns i snabbstartsstyrda inställningar som är tillgängliga i avsnittet *Kom igång* i Azure AD [Developers Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Klicka på **Skapa** när du är klar. Azure AD tilldelar ett unikt *program-ID* till ditt program och du\'tas till programmets\'huvudregistreringssida. Beroende på om ditt program är ett webbprogram eller ett internt program ges olika alternativ för att lägga till ytterligare funktioner i programmet.

>[!Note]
>Som standard är det nyligen registrerade programmet konfigurerat så att endast användare från samma klient kan logga in på ditt program.


## <a name="using-the-azure-ad-security-token"></a>Använda azure AD-säkerhetstoken

När du har registrerat ditt program kan du programmässigt begära en Azure AD-säkerhetstoken.  Utgivaren förväntas använda den här token och göra en begäran om att lösa den.  När du använder de olika uppfyllelse-API:erna finns parametern tokenfrågefråga i URL:en när användaren omdirigeras till SaaS-webbplatsen från Azure.  Den här token är endast giltig i en timme.  Dessutom bör du URL avkoda tokenvärdet från webbläsaren innan du använder det.

Mer information om dessa token finns i [Azure Active Directory access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Hämta en token baserat på Azure AD-appen

HTTP-metod

`POST`

*Request URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI-parameter*

|  **Parameternamn**  | **Obligatoriskt**  | **Beskrivning**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId (hyresgäst)             | True          | Klient-ID för den registrerade AAD-programmet   |
|  |  |  |


*Begär huvud*

|  **Huvudnamn**  | **Obligatoriskt** |  **Beskrivning**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Innehållstyp som är associerad med begäran. Standardvärdet är `application/x-www-form-urlencoded`.  |
|  |  |  |


*Begäran kropp*

| **Egenskapsnamn**   | **Obligatoriskt** |  **Beskrivning**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ av bidrag. Standardvärdet är `client_credentials`.                    |
|  Client_id          | True         |  Klient-/appidentifierare som är associerad med Azure AD-appen.                  |
|  client_secret      | True         |  Lösenord som är associerat med Azure AD-appen.                               |
|  Resurs           | True         |  Målresurs som token begärs för. Standardvärdet är `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Svar*

|  **Namn**  | **Typ**       |  **Beskrivning**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Begäran lyckades   |
|  |  |  |

*TokenResponse*

Exempel på svarstoken:

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


## <a name="next-steps"></a>Nästa steg

Din Azure AD-säkrad app kan nu använda [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md).
