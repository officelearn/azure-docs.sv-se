---
title: Registrera ett SaaS-program | Azure Marketplace
description: Förklarar hur du registrerar ett SaaS-program med hjälp av Azure Portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: b2e02e42ab63f893574ca5217fd2f36c7481aabd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827944"
---
# <a name="register-a-saas-application"></a>Registrera ett SaaS-program

Den här artikeln förklarar hur du registrerar ett SaaS-program med hjälp av Microsoft [Azure Portal](https://portal.azure.com/).  Efter en lyckad registrering får du en Azure Active Directory (Azure AD) säkerhetstoken som du kan använda för att få åtkomst till API: er för SaaS-utförande.  Mer information om Azure AD finns i [Vad är autentisering?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Flöde för tjänst-till-tjänst-autentisering

Följande diagram visar prenumerations flödet för en ny kund och när dessa API: er används:

![API-flöde för SaaS-erbjudande](./media/saas-offer-publish-api-flow-v1.png)

Azure tillhandahåller inte några begränsningar för den autentisering som SaaS-tjänsten exponerar för slutanvändarna. Autentisering med SaaS-API: er utförs dock med en Azure AD-säkerhetstoken, som vanligt vis hämtas genom att registrera SaaS-appen via Azure Portal. 


## <a name="register-an-azure-ad-secured-app"></a>Registrera en Azure AD-skyddad app

Alla program som vill använda funktionerna i Azure AD måste först registreras i en Azure AD-klientorganisation. Den här registrerings processen innebär att ge Azure AD information om ditt program, till exempel URL: en där den finns, URL: en för att skicka svar när en användare har autentiserats, URI: n som identifierar appen och så vidare.  Gör så här för att registrera ett nytt program med hjälp av Azure Portal:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Om ditt konto ger dig åtkomst till mer än ett, klickar du på ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
3.  I det vänstra navigerings fönstret klickar du på **Azure Active Directory** tjänst, klickar på **Appregistreringar**och klickar på **ny program registrering**.

    ![SaaS AD App-registreringar](./media/saas-offer-app-registration-v1.png)

4.  På sidan Skapa anger du ditt program\'s registrerings information:
    -   **Namn**: Ange ett meningsfullt program namn
    -   **Program typ**: 
        - Välj **Internt** för [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) som installeras lokalt på en enhet. Den här inställningen används för OAuth-offentliga [interna klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client).
        - Välj **webbapp/API** för [klient program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) och [resurs-/API-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth-konfidentiella [webb klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) och offentliga [användar agentbaserade klienter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Samma program kan även visa både en klient och resurs/API.
    -   **Inloggnings-URL**: för WEBBAPP/API-program, anger du webbappens bas-URL. **http://localhost:31544** kan till exempel vara URL: en för en webbapp som körs på den lokala datorn. Användarna använder sedan denna URL för att logga in på ett webb klient program.
    -   **Omdirigerings-URI**: för interna program, anger du den URI som används av Azure AD för att returnera svar på token. Ange ett värde som är specifik för ditt program, till exempel **http://MyFirstAADApp** .

        ![SaaS AD App-registreringar](./media/saas-offer-app-registration-v1-2.png)

        Specifika exempel för webb program och interna program finns i de guidade installations guiderna för snabb start som är tillgängliga i avsnittet *komma igång* i [Azure AD-utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Klicka på **Skapa** när du är klar. Azure AD tilldelar ett unikt *program-ID* till ditt program och du\'till din program\'s huvud registrerings sida. Beroende på om ditt program är ett webbprogram eller ett internt program ges olika alternativ för att lägga till ytterligare funktioner i programmet.

>[!Note]
>Som standard är det nyligen registrerade programmet konfigurerat att bara tillåta användare från samma klient organisation att logga in i ditt program.


## <a name="using-the-azure-ad-security-token"></a>Använda Azure AD-säkerhetstoken

När du har registrerat ditt program kan du program mässigt begära en Azure AD-säkerhetstoken.  Utgivaren förväntas använda denna token och göra en begäran om att lösa det.  När du använder de olika API: erna för utförande, finns token-frågeparametern i URL: en när användaren omdirigeras till SaaS webbplats från Azure.  Denna token är endast giltig i en timme.  Dessutom bör URL: en avkoda värdet för token från webbläsaren innan du använder det.

Mer information om dessa tokens finns [Azure Active Directory åtkomsttoken](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Hämta en token baserat på Azure AD-appen

HTTP-metod

`POST`

*Request URL*

**https://login.microsoftonline.com/ *{tenantId}* /OAuth2/token**

*URI-parameter*

|  **Parameternamn**  | **Kunna**  | **Beskrivning**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| TenantId             | True          | Klient-ID för det registrerade AAD-programmet   |
|  |  |  |


*Begär ande huvud*

|  **Rubrik namn**  | **Kunna** |  **Beskrivning**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Innehålls typ som är associerad med begäran. Standardvärdet är `application/x-www-form-urlencoded`.  |
|  |  |  |


*Brödtext i begäran*

| **Egenskapsnamn**   | **Kunna** |  **Beskrivning**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ av beviljande. Standardvärdet är `client_credentials`.                    |
|  Client_id          | True         |  Klient/app-identifierare som är associerad med Azure AD-appen.                  |
|  client_secret      | True         |  Lösen ordet som är associerat med Azure AD-appen.                               |
|  Resurs           | True         |  Mål resurs för vilken token begärs. Standardvärdet är `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Svar*

|  **Namn**  | **Typ**       |  **Beskrivning**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Begäran lyckades   |
|  |  |  |

*TokenResponse*

Token för samplings svar:

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

Din Azure AD-säkrade app kan nu använda [API-version 2 för SaaS-Uppfyllaning](./pc-saas-fulfillment-api-v2.md).
