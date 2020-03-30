---
title: Skydda ett API med OAuth 2.0 med AAD och API Management
titleSuffix: Azure API Management
description: Lär dig hur du skyddar en webb-API-serverdel med Azure Active Directory och API Management.
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
ms.openlocfilehash: 8b396b782c1254b3229aeeb8e51b61cc744d6318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190367"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Skydda ett API med hjälp av OAuth 2.0 med Azure Active Directory och API Management

Den här guiden visar hur du konfigurerar din Azure API Management-instans för att skydda ett API med OAuth 2.0-protokollet med Azure Active Directory (Azure AD). 

> [!NOTE]
> Den här funktionen är tillgänglig i **nivåerna Utvecklare,** **Standard** och **Premium** i API Management.

## <a name="prerequisites"></a>Krav
Om du vill följa stegen i den här artikeln måste du ha:
* En API-hanteringsinstans
* Ett API som publiceras som använder API Management-instansen
* En Azure AD-klient

## <a name="overview"></a>Översikt

Här är en snabb översikt över stegen:

1. Registrera ett program (backend-app) i Azure AD för att representera API:et.
2. Registrera ett annat program (klient-app) i Azure AD för att representera ett klientprogram som måste anropa API:et.
3. I Azure AD beviljar du behörigheter så att klientappen kan anropa backend-appen.
4. Konfigurera utvecklarkonsolen så att API:et anropas med hjälp av OAuth 2.0-användarauktorisering.
5. Lägg till **principen validate-jwt** för att validera OAuth-token för varje inkommande begäran.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrera ett program i Azure AD för att representera API:et

För att skydda ett API med Azure AD är det första steget att registrera ett program i Azure AD som representerar API:et. 

1. Gå till [Azure-portalen](https://portal.azure.com) för att registrera ditt program. Sök efter och välj **APP-registreringar**.

1. Välj **Ny registrering**. 

1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan: 
    - I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen, till exempel *backend-app*. 
    - I avsnittet **Konton som stöds** väljer du ett alternativ som passar ditt scenario. 

1. Lämna **avsnittet Omdirigera URI** tomt.

1. Välj **Registrera** för att skapa programmet. 

1. Leta reda på värdet **program (klient) och** registrera det för senare på sidan App **översikt.**

1. Välj **Exponera ett API** och ange **program-ID-URI** med standardvärdet. Registrera det här värdet för senare.

1. Välj knappen **Lägg till ett scope** om du vill visa sidan Lägg till ett **scope.** Skapa sedan ett nytt scope som stöds av `Files.Read`API :et (till exempel ). Slutligen väljer du knappen **Lägg till omfång** för att skapa scopet. Upprepa det här steget om du vill lägga till alla scope som stöds av ditt API.

1. När scope skapas, anteckna dem för användning i ett efterföljande steg. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrera ett annat program i Azure AD för att representera ett klientprogram

Varje klientprogram som anropar API:et måste också registreras som ett program i Azure AD. I det här exemplet är klientprogrammet utvecklarkonsolen i utvecklarportalen för API Management. Så här registrerar du ett annat program i Azure AD för att representera utvecklarkonsolen.

1. Gå till [Azure-portalen](https://portal.azure.com) för att registrera ditt program. Sök efter och välj **APP-registreringar**.

1. Välj **Ny registrering**.

1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan: 
    - I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen, till exempel *klientapp*. 
    - I avsnittet **Konton som stöds** väljer du Konton i valfri **organisationskatalog (Valfri Azure AD-katalog - Multitenant)**. 

1. Markera `Web` och ange URL:en `https://contoso5.portal.azure-api.net/signin`i avsnittet Omdirigera **URI** .

1. Välj **Registrera** för att skapa programmet. 

1. Leta reda på värdet **program (klient) och** registrera det för senare på sidan App **översikt.**

Nu skapar du en klienthemlighet för det här programmet som ska användas i ett efterföljande steg.

1. Välj **Certifikat & hemligheter**i listan över sidor för klientappen och välj Ny **klienthemlighet**.

1. Under **Lägg till en klienthemlighet**anger du en **beskrivning**. Välj när nyckeln ska upphöra att gälla och välj **Lägg till**.

När hemligheten skapas noterar du nyckelvärdet för användning i ett efterföljande steg. 

## <a name="grant-permissions-in-azure-ad"></a>Bevilja behörigheter i Azure AD

Nu när du har registrerat två program för att representera API:et och utvecklarkonsolen måste du bevilja behörigheter så att klientappen kan anropa backend-appen.  

1. Gå till [Azure-portalen](https://portal.azure.com) för att bevilja behörigheter till klientprogrammet. Sök efter och välj **APP-registreringar**.

1. Välj din klientapp. Välj sedan **API-behörigheter**i listan över sidor för appen .

1. Välj **Lägg till en behörighet**.

1. Under **Välj ett API**väljer du Mina **API:er**och hittar och väljer sedan din serverdelsapp.

1. Under **Delegerade behörigheter**väljer du lämpliga behörigheter till serverdelsappen och väljer sedan **Lägg till behörigheter**.

1. På sidan **API-behörigheter** väljer du eventuellt **Bevilja administratörsgodkännande för \<att ditt klientnamn ska beviljas>** att bevilja medgivande för alla användare i den här katalogen. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivera OAuth 2.0-användarauktorisering i utvecklarkonsolen

Nu har du skapat dina program i Azure AD och har gett rätt behörighet för att tillåta klientappen att anropa backend-appen. 

I det här exemplet är Utvecklarkonsolen klientappen. I följande steg beskrivs hur du aktiverar OAuth 2.0-användarauktorisering i Utvecklarkonsolen. 

1. I Azure-portalen bläddrar du till din API Management-instans.

1. Välj **OAuth 2.0** > **Lägg till**.

1. Ange ett **visningsnamn** och **en beskrivning**.

1. För **url:en för sidan Klientregistrering**anger `http://localhost`du ett platshållarvärde, till exempel . **Url:en för klientregistreringssidan** pekar på en sida som användarna kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder detta. I det här exemplet skapar och konfigurerar inte användare sina egna konton, så du använder en platshållare i stället.

1. För **auktoriseringsbidragstyper**väljer du **Auktoriseringskod**.

1. Ange **URL:en för auktoriseringsslutpunkt** och **tokenslutpunkts-URL**. Hämta dessa värden från sidan **Slutpunkter** i din Azure AD-klientorganisation. Bläddra till sidan **Appregistreringar** igen och välj **Slutpunkter**.


1. Kopiera **OAuth 2.0 Auktoriseringsslutpunkten**och klistra in den i textrutan **URL för auktoriseringsslutpunkt.** Välj **POST** under metoden Auktoriseringsbegäran.

1. Kopiera **slutpunkten OAuth 2.0 Token**och klistra in den i textrutan **URL för tokenslutpunkt.** 

    >[!IMPORTANT]
    > Du kan använda antingen **v1-** eller **v2-slutpunkter.** Beroende på vilken version du väljer kommer dock nedanstående steg att vara annorlunda. Vi rekommenderar att du använder v2-slutpunkter. 

1. Om du använder **v1-slutpunkter** lägger du till en brödtextparameter med namnet **resource**. Använd **program-ID för** backend-appen för värdet för den här parametern. 

1. Om du använder **v2-slutpunkter** använder du det scope som du skapade för backend-appen i fältet **Standardomfattning.**

1. Ange sedan klientautentiseringsuppgifterna. Dessa är autentiseringsuppgifterna för klientappen.

1. För **klient-ID**använder du **program-ID:et** för klientappen.

1. Använd nyckeln som du skapade för klientappen tidigare för **klienthemligheten.** 

1. Omedelbart efter klienthemligheten är **redirect_url** för tilldelningskodens bidragstyp. Anteckna den här webbadressen.

1. Välj **Skapa**.

1. Gå tillbaka till klientappen och välj **Autentisering**.

1. Under **Omdirigera URI väljer**du typen som **webb,** klistrar in **redirect_url** under **Omdirigera URI**och sparar sedan.

Nu när du har konfigurerat en OAuth 2.0-auktoriseringsserver kan utvecklarkonsolen hämta åtkomsttoken från Azure AD. 

Nästa steg är att aktivera OAuth 2.0-användarauktorisering för ditt API. Detta gör det möjligt för Utvecklarkonsolen att veta att den måste hämta en åtkomsttoken för användarens räkning, innan du ringer till ditt API.

1. Bläddra till din API Management-instans och gå till **API:er**.

2. Välj det API som du vill skydda. Du kan till exempel `Echo API`använda .

3. Gå till **Inställningar**.

4. Under **Säkerhet**väljer du **OAuth 2.0**och väljer den OAuth 2.0-server som du konfigurerade tidigare. 

5. Välj **Spara**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Anropa API:et från utvecklarportalen

> [!NOTE]
> Det här avsnittet gäller inte för **förbrukningsnivån,** som inte stöder utvecklarportalen.

Nu när OAuth 2.0-användarauktoriseringen är aktiverad på ditt API får utvecklarkonsolen en åtkomsttoken för användarens räkning innan API:et anropas.

1. Bläddra till alla åtgärder under API:et i utvecklarportalen och välj **Prova den**. Detta tar dig till utvecklarkonsolen.

2. Observera ett nytt objekt i avsnittet **Auktorisering,** som motsvarar den auktoriseringsserver som du just har lagt till.

3. Välj **Auktoriseringskod** i listrutan auktorisering och du uppmanas att logga in på Azure AD-klienten. Om du redan är inloggad med kontot kanske du inte blir tillfrågad.

4. Efter lyckad inloggning `Authorization` läggs ett huvud till i begäran, med en åtkomsttoken från Azure AD. Följande är en exempeltoken (Base64 kodad):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Välj **Skicka**och du kan anropa API:et.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en JWT-valideringsprincip för att förauktorisera begäranden

När en användare försöker ringa ett samtal från utvecklarkonsolen uppmanas användaren att logga in. Utvecklarkonsolen hämtar en åtkomsttoken för användarens räkning och inkluderar token i begäran till API:et.

Men vad händer om någon anropar ditt API utan en token eller med en ogiltig token? Försök till exempel att anropa `Authorization` API:et utan huvudet, samtalet går fortfarande igenom. Anledningen är att API Management inte validerar åtkomsttoken just nu. Det skickar `Authorization` helt enkelt huvudet till backend-API: et.

Du kan använda [principen Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) för att förauktorisera begäranden i API Management genom att validera åtkomsttoken för varje inkommande begäran. Om en begäran inte har en giltig token blockerar API Management den. Lägg till exempel till följande `<inbound>` princip i `Echo API`principavsnittet i . Den kontrollerar målgruppsanspråket i en åtkomsttoken och returnerar ett felmeddelande om token inte är giltig. Information om hur du konfigurerar principer finns i [Ange eller redigera principer](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Den `openid-config` här URL:en motsvarar v1-slutpunkten. Använd `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`för `openid-config`v2-ändpunkten .

## <a name="build-an-application-to-call-the-api"></a>Skapa ett program för att anropa API:et

I den här guiden använde du utvecklarkonsolen i API `Echo API` Management som exempelklientprogram för att anropa skyddad av OAuth 2.0. Mer information om hur du skapar ett program och implementerar OAuth 2.0 finns i [Azure Active Directory-kodexempel](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Active Directory och OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Kolla in fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda backend-tjänsten finns i [Autentisering av ömsesidigt certifikat](api-management-howto-mutual-certificates.md).

* [Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).

* [Hantera ditt första API](import-and-publish.md).
