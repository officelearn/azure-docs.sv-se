---
title: Registrera ett SaaS-program – Azure Marketplace | Microsoft Docs
description: Beskriver hur du registrerar ett SaaS-program med Azure portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
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
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101599"
---
# <a name="register-a-saas-application"></a>Registrera ett SaaS-program

Den här artikeln beskrivs hur du registrerar ett SaaS-program med hjälp av Microsofts [Azure-portalen](https://portal.azure.com/).  När en lyckad registrering visas en säkerhetstoken för Azure Active Directory (Azure AD) som du kan använda för att få åtkomst till SaaS betjäna API: er.  Läs mer om Azure AD, [vad är authentication?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Autentiseringsflödet för tjänst-till-tjänst

Följande diagram visar prenumeration flödet för en ny kund och när dessa API: er används:

![SaaS erbjuder API-flöde](./media/saas-offer-publish-api-flow-v1.png)

Azure medför inte några begränsningar på vilken autentisering som SaaS-tjänsten exponerar till sina slutanvändare. Dock utförs autentiseringen med SaaS Techtrends API: er med en Azure AD-säkerhetstoken som vanligtvis hämtas genom att registrera SaaS-app via Azure portal. 


## <a name="register-an-azure-ad-secured-app"></a>Registrera en Azure AD-skyddad app

Alla program som vill använda funktionerna i Azure AD måste först registreras i en Azure AD-klientorganisation. Registreringsprocessen innebär att du ger Azure AD-information om ditt program, till exempel URL: en där den finns, URL: en att skicka svar när en användare autentiseras den URI som identifierar appen och så vidare.  Utför följande steg för att registrera ett nytt program med Azure portal:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Om ditt konto ger dig tillgång till fler än en klickar du på ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3.  I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, och klicka på **ny programregistrering**.

    ![SaaS AD App-registreringar](./media/saas-offer-app-registration-v1.png)

4.  Ange ditt program på sidan Skapa\'s registreringsinformation:
    -   **Namn**: Ange ett beskrivande programnamn
    -   **Programtyp**: 
        - Välj **Internt** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) som installeras lokalt på en enhet. Den här inställningen används för OAuth-offentliga [interna klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Välj **webbapp / API** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) och [resurs/API-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth-konfidentiella [webbklienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) och offentliga [användar-agent-baserade klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Samma program kan även visa både en klient och resurs/API.
    -   **Inloggnings-URL**: För Web app/API-program, anger du den grundläggande Webbadressen för din app. Till exempel **http://localhost:31544** kanske URL-Adressen för en webbapp som körs på den lokala datorn. Användare använder sedan denna URL för att logga in till ett webbprogram för klienten.
    -   **Omdirigerings-URI**: Ange den URI som används av Azure AD för att returnera tokensvar för interna program. Ange ett specifikt värde till ditt program, till exempel **http://MyFirstAADApp**.

        ![SaaS AD App-registreringar](./media/saas-offer-app-registration-v1-2.png)

        För specifika exempel på webbprogram eller interna program, Kolla in snabbstarten guidad inställningar som är tillgängliga i den *börjar* delen av den [Utvecklarguiden för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Klicka på **Skapa** när du är klar. Azure AD tilldelar ett unikt *program-ID* till ditt program och du\'på nytt kommer du till ditt program\'s huvudsakliga registreringssidan. Beroende på om ditt program är ett webbprogram eller ett internt program ges olika alternativ för att lägga till ytterligare funktioner i programmet.

>[!Note]
>Som standard konfigureras det nyligen registrerade programmet så att endast användare från samma klient att logga in på ditt program.


## <a name="using-the-azure-ad-security-token"></a>Med hjälp av Azure AD-säkerhetstoken

När du har registrerat ditt program, kan du begära en Azure AD-säkerhetstoken programmässigt.  Utgivaren förväntas Använd denna token och gör en begäran att lösa detta.  När du använder API: er för olika betjäna är token Frågeparametern i URL: en när användaren omdirigeras till SaaS-webbplats från Azure.  Den här variabeln är endast giltig under en timme.  Dessutom bör du URL: en avkoda token-värde från webbläsaren innan du använder den.

Läs mer om dessa token [åtkomsttoken för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


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


## <a name="next-steps"></a>Nästa steg

Din Azure AD-skyddad app kan nu använda den [SaaS Techtrends API Version 2](./cpp-saas-fulfillment-api-v2.md).
