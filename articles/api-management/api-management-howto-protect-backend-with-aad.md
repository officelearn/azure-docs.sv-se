---
title: Skydda ett API med hjälp av OAuth 2,0 med AAD och API Management
titleSuffix: Azure API Management
description: Lär dig hur du skyddar en webb-API-backend med Azure Active Directory och API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 213e5523c0c99309c3244e19a406a7b82297188f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161808"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Skydda ett API med hjälp av OAuth 2,0 med Azure Active Directory och API Management

Den här guiden visar hur du konfigurerar Azure API Management-instansen för att skydda ett API genom att använda OAuth 2,0-protokollet med Azure Active Directory (Azure AD). 

> [!NOTE]
> Den här funktionen är tillgänglig i API Managements **utvecklings**-, **standard** -och **Premium** -nivå.

## <a name="prerequisites"></a>Förutsättningar
För att följa stegen i den här artikeln måste du ha:
* En API Management-instans
* Ett API som publiceras som använder API Management-instansen
* En Azure AD-klient

## <a name="overview"></a>Översikt

Här är en snabb översikt över stegen:

1. Registrera ett program (backend-app) i Azure AD för att representera API: et.
2. Registrera ett annat program (klient-app) i Azure AD för att representera ett klient program som behöver anropa API: et.
3. I Azure AD beviljar du behörighet att tillåta klient-app att anropa backend-appen.
4. Konfigurera Developer-konsolen för att anropa API: et med OAuth 2,0-användarauktorisering.
5. Lägg till **validate-JWT-** principen för att verifiera OAuth-token för varje inkommande begäran.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrera ett program i Azure AD för att representera API: et

Det första steget är att registrera ett program i Azure AD som representerar API för att skydda ett API med Azure AD. 

1. Gå till [Azure Portal](https://portal.azure.com) för att registrera ditt program. Sök efter och välj **app-registreringar**.

1. Välj **ny registrering**. 

1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation: 
    - I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användarna av appen, till exempel *backend-app*. 
    - I avsnittet **konto typer som stöds** väljer du ett alternativ som passar ditt scenario. 

1. Lämna avsnittet **omdirigerings-URI** tomt.

1. Välj **Registrera** för att skapa programmet. 

1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare.

1. Välj **exponera ett API** och ange **program-ID-URI** med standardvärdet. Registrera det här värdet för senare.

1. Välj knappen **Lägg till ett omfång** för att visa sidan **Lägg till ett omfång** . Skapa sedan en ny omfattning som stöds av API: t (till exempel `Files.Read`). Slutligen väljer du knappen **Lägg till omfång** för att skapa omfånget. Upprepa det här steget om du vill lägga till alla omfattningar som stöds av ditt API.

1. När du har skapat omfattningarna noterar du dem för användning i ett senare steg. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrera ett annat program i Azure AD för att representera ett klient program

Varje klient program som anropar API: et måste registreras som ett program i Azure AD. I det här exemplet är klient programmet Developer-konsolen i API Management Developer-portalen. Så här registrerar du ett annat program i Azure AD för att representera Developer-konsolen.

1. Gå till [Azure Portal](https://portal.azure.com) för att registrera ditt program. Sök efter och välj **API-registreringar**.

1. Välj **ny registrering**.

1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation: 
    - I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användarna av appen, t. ex. *klient-app*. 
    - I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog (alla Azure AD-kataloger – flera klienter)** . 

1. I avsnittet **omdirigerings-URI** väljer du `Web` och anger URL: en `https://contoso5.portal.azure-api.net/signin`.

1. Välj **Registrera** för att skapa programmet. 

1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare.

Skapa nu en klient hemlighet för det här programmet som ska användas i ett senare steg.

1. I listan med sidor för din klient app väljer du **certifikat & hemligheter**och väljer **ny klient hemlighet**.

1. Under **Lägg till en klient hemlighet**anger du en **Beskrivning**. Välj när nyckeln ska förfalla och välj **Lägg till**.

När hemligheten skapas noterar du nyckel värdet för användning i ett senare steg. 

## <a name="grant-permissions-in-azure-ad"></a>Bevilja behörigheter i Azure AD

Nu när du har registrerat två program som ska representera API: et och Developer-konsolen måste du bevilja behörighet att tillåta klient-app att anropa backend-appen.  

1. Gå till [Azure Portal](https://portal.azure.com) om du vill bevilja behörighet till klient programmet. Sök efter och välj **API-registreringar**.

1. Välj klient programmet. Välj sedan **API-behörigheter**i listan över sidor för appen.

1. Välj **Lägg till en behörighet**.

1. Under **Välj ett API**väljer du **Mina API: er**och letar sedan upp och väljer din backend-app.

1. Under **delegerade behörigheter**väljer du lämpliga behörigheter till Server delen – appen och väljer sedan **Lägg till behörigheter**.

1. Om du vill kan du på sidan **API-behörigheter** välja **bevilja administrativt medgivande för \<ditt klient namn >** för att bevilja medgivande åt alla användare i den här katalogen. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivera OAuth 2,0-användarauktorisering i Developer-konsolen

Nu har du skapat dina program i Azure AD och har beviljat rätt behörighet att tillåta klient-app att anropa backend-appen. 

I det här exemplet är Developer-konsolen klient-app. I följande steg beskrivs hur du aktiverar OAuth 2,0-användarauktorisering i Developer-konsolen. 

1. I Azure Portal bläddrar du till API Management-instansen.

1. Välj **OAuth 2,0** > **Lägg till**.

1. Ange ett **visnings namn** och en **Beskrivning**.

1. Ange ett värde för plats hållare för **klient registrerings sidans URL**, t. ex. `http://localhost`. **URL: en för klient registrerings sidan** pekar på en sida som användarna kan använda för att skapa och konfigurera sina egna konton för OAuth 2,0-leverantörer som stöder detta. I det här exemplet skapar och konfigurerar användarna inte sina egna konton, så du kan använda en plats hållare i stället.

1. För **godkännande typer för auktorisering**väljer du **auktoriseringskod**.

1. Ange URL för **Auktoriseringens slut punkt** och **token slut punkt**. Hämta de här värdena från sidan **slut punkter** i din Azure AD-klient. Bläddra till **Appregistreringar** sidan igen och välj **slut punkter**.


1. Kopiera **OAuth 2,0 Authorization-slutpunkten**och klistra in den i text rutan **URL för behörighets slut punkt** . Välj **post** under metod för begäran om godkännande.

1. Kopiera **OAuth 2,0-token-slutpunkten**och klistra in den i text rutan **URL för token-slutpunkt** . 

    >[!IMPORTANT]
    > Du kan använda antingen **v1** -eller **v2** -slutpunkter. Men beroende på vilken version du väljer är steget nedan olika. Vi rekommenderar att du använder v2-slutpunkter. 

1. Om du använder **v1** -slutpunkter lägger du till en Body-parameter med namnet **Resource**. Använd **program-ID: t** för backend-appen för värdet för den här parametern. 

1. Om du använder **v2** -slutpunkter använder du den omfattning som du skapade för backend-appen i fältet **standard omfång** .

1. Ange sedan klientens autentiseringsuppgifter. Detta är autentiseringsuppgifterna för klient-app.

1. För **klient-ID**använder du **program-ID: t** för klient-app.

1. För **klient hemlighet**använder du nyckeln som du skapade för klient-app tidigare. 

1. Direkt efter klient hemligheten är **redirect_url** för typen av auktoriseringskod. Anteckna denna URL.

1. Välj **Skapa**.

1. Gå tillbaka till din klient-app och välj **autentisering**.

1. Under **omdirigerings-URI: er**väljer du typ som **webb**, klistrar in **redirect_url** under **omdirigerings-URI**och sparar sedan.

Nu när du har konfigurerat en OAuth 2,0-Authorization-Server kan Developer-konsolen Hämta åtkomsttoken från Azure AD. 

Nästa steg är att aktivera OAuth 2,0-användarauktorisering för ditt API. På så sätt kan Developer-konsolen veta att den behöver skaffa en åtkomsttoken åt användaren, innan anrop till ditt API.

1. Bläddra till API Management-instansen och gå till **API: er**.

2. Välj det API som du vill skydda. Du kan till exempel använda `Echo API`.

3. Gå till **Inställningar**.

4. Under **säkerhet**väljer du **OAuth 2,0**och väljer den OAuth 2,0-server som du konfigurerade tidigare. 

5. Välj **Spara**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Anropa API: t från Developer-portalen

> [!NOTE]
> Det här avsnittet gäller inte för **förbruknings** nivån, som inte stöder Developer-portalen.

Nu när OAuth 2,0-användarauktorisering har Aktiver ATS på ditt API får Developer-konsolen en åtkomsttoken åt användaren, innan API: et anropas.

1. Bläddra till en åtgärd under API: et i Developer-portalen och välj **testa**. Det leder dig till Developer-konsolen.

2. Observera ett nytt objekt i avsnittet **auktorisering** , som motsvarar den auktoriseringsservern som du nyss lade till.

3. Välj **auktoriseringskod** i list rutan auktorisering så uppmanas du att logga in på Azure AD-klienten. Om du redan har loggat in med kontot kanske du inte uppmanas att göra det.

4. Efter lyckad inloggning läggs ett `Authorization`-huvud till i begäran med en åtkomsttoken från Azure AD. Följande är en exempel-token (Base64-kodad):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Välj **Skicka**och du kan anropa API: et.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en JWT-validerings princip för förauktoriserade begär Anden

Vid det här tillfället uppmanas användaren att logga in när en användare försöker göra ett anrop från Developer-konsolen. Developer-konsolen hämtar en åtkomsttoken åt användaren och inkluderar token i begäran till API: et.

Men vad händer om någon anropar ditt API utan token eller med en ogiltig token? Om du till exempel försöker anropa API: et utan `Authorization`s huvudet går det fortfarande att ringa. Anledningen är att API Management inte validerar åtkomsttoken i det här läget. Det skickar bara `Authorization`-rubriken till Server dels-API: et.

Du kan använda [validate JWT](api-management-access-restriction-policies.md#ValidateJWT) -principen för att förauktorisera begär anden i API Management, genom att verifiera åtkomsttoken för varje inkommande begäran. Om en begäran inte har en giltig token, API Management blockerar den. Lägg till exempel till följande princip i avsnittet `<inbound>` princip i `Echo API`. Den kontrollerar mål grupps anspråket i en åtkomsttoken och returnerar ett fel meddelande om token inte är giltig. Information om hur du konfigurerar principer finns i [Ange eller redigera principer](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID URI of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Den här `openid-config` URL: en motsvarar v1-slutpunkten. Använd `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`för v2 `openid-config`-slutpunkten.

## <a name="build-an-application-to-call-the-api"></a>Bygg ett program för att anropa API: et

I den här guiden använde du Developer-konsolen i API Management som exempel klient programmet för att anropa `Echo API` som skyddas av OAuth 2,0. Mer information om hur du skapar ett program och implementerar OAuth 2,0 finns i [Azure Active Directory kod exempel](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Active Directory och OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Se fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda Server dels tjänsten finns i [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md).

* [Skapa en API Management tjänst instans](get-started-create-service-instance.md).

* [Hantera ditt första API](import-and-publish.md).
