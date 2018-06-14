---
title: Skydda en API med OAuth 2.0 med Azure Active Directory och API-hantering | Microsoft Docs
description: Lär dig hur du skyddar en web API-serverdel med Azure Active Directory och API-hantering.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928044"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Skydda en API med OAuth 2.0 med Azure Active Directory och API-hantering

Den här guiden visar hur du konfigurerar din Azure API Management-instans för att skydda en API med OAuth 2.0-protokollet med Azure Active Directory (AD Azure). 

## <a name="prerequisites"></a>Förutsättningar
Om du vill följa stegen i den här artikeln, måste du ha:
* En instans för API Management
* En API som publiceras som använder API Management-instans
* En Azure AD-klient

## <a name="overview"></a>Översikt

Här är en snabb överblick över stegen:

1. Registrera ett program (backend-app) i Azure AD för att representera API: et.
2. Registrera ett annat program (klientappen) i Azure AD för att representera ett klientprogram som måste anropa API: et.
3. Bevilja behörigheter för att tillåta klientappen att anropa backend-app i Azure AD.
4. Konfigurera Developer-konsolen för att använda OAuth 2.0 användarautentiseringen.
5. Lägg till den **Validera jwt** princip för att validera OAuth-token för varje inkommande begäran.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrera ett program i Azure AD för att representera API: et

Det första steget är att registrera ett program i Azure AD som representerar API: et för att skydda en API med Azure AD. 

1. Bläddra till Azure AD-klienten och bläddra sedan till **App registreringar**.

2. Välj **Ny programregistrering**. 

3. Ange ett namn för programmet. (Det här exemplet är namn `backend-app`.)  

4. Välj **webbapp / API** som den **programtyp**. 

5. För **inloggnings-URL**, kan du använda `https://localhost` som platshållare.

6. Välj **Skapa**.

När programmet har skapats, notera den **program-ID**, för användning i ett senare skede. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrera ett annat program i Azure AD för att representera ett klientprogram

Alla klientprogram som anropar API: N måste vara registrerad som ett program i Azure AD. I det här exemplet är klienten exempelprogrammet Developer-konsolen i API Management developer-portalen. Här är att registrera ett annat program i Azure AD för att representera Developer-konsolen.

1. Välj **Ny programregistrering**. 

2. Ange ett namn för programmet. (Det här exemplet är namn `client-app`.)

3. Välj **webbapp / API** som den **programtyp**.  

4. För **inloggnings-URL**, kan du använda `https://localhost` som platshållare eller Använd logga in URL: en för API Management-instans. (Det här exemplet är URL: en `https://contoso5.portal.azure-api.net/signin`.)

5. Välj **Skapa**.

När programmet har skapats, notera den **program-ID**, för användning i ett senare skede. 

Nu ska du skapa en klienthemlighet för det här programmet för användning i ett senare skede.

1. Välj **inställningar** igen och gå till **nycklar**.

2. Under **lösenord**, ange en **nyckeln beskrivning**. Välj när nyckeln ska upphöra att gälla, och välj **spara**.

Anteckna värdet för nyckeln. 

## <a name="grant-permissions-in-azure-ad"></a>Bevilja behörighet i Azure AD

Nu när du har registrerat två program för att representera API: et och Developer-konsolen, måste du bevilja behörigheter för att tillåta klientappen att anropa backend-app.  

1. Bläddra till **programmet registreringar**. 

2. Välj `client-app`, och gå till **inställningar**.

3. Välj **nödvändiga behörigheter** > **Lägg till**.

4. Välj **väljer en API**, och Sök efter `backend-app`.

5. Under **delegerade behörigheter**väljer `Access backend-app`. 

6. Välj **Välj**, och välj sedan **klar**. 

> [!NOTE]
> Om **Azure Active Directory** visas under behörigheter för andra program, Välj **Lägg till** att lägga till den i listan.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivera OAuth 2.0 användarautentiseringen i Developer-konsolen

Nu är du har skapat dina program i Azure AD och har korrekt behörighet att tillåta klientappen att anropa backend-app. 

I det här exemplet är Developer Console klient-app. Följande steg beskriver hur du aktiverar OAuth 2.0 användarautentiseringen i Utvecklarkonsolen. 

1. Bläddra till din API Management-instans.

2. Välj **OAuth 2.0** > **lägga till**.

3. Ange en **visningsnamn** och **beskrivning**.

4. För den **klienten URL för registrering**, ange en platshållarvärde som `http://localhost`. Den **klienten URL för registrering** pekar på den sida som användarna kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-providers som stöder den här. I det här exemplet användare inte skapa och konfigurera sina egna konton, så att du använder en platshållare i stället.

5. För **auktorisering bevilja typer**väljer **Auktoriseringskoden**.

6. Ange den **slutpunkts-URL-auktorisering** och **Token slutpunkts-URL**. Hämtar värdena från den **slutpunkter** sida i Azure AD-klienten. Bläddra till den **App registreringar** igen och väljer **slutpunkter**.

7. Kopiera den **OAuth 2.0-Autentiseringsslutpunkt**, och klistrar in det i den **slutpunkts-URL-auktorisering** textruta.

8. Kopiera den **OAuth 2.0-Token för slutpunkt**, och klistrar in det i den **Token slutpunkts-URL** textruta. Förutom klistra in tokenslutpunkten, lägga till en brödtext parameter med namnet **resurs**. Värdet på parametern använder den **program-ID** för backend-app.

9. Därefter anger du klientautentiseringsuppgifterna. Det är dessa autentiseringsuppgifter för klient-app.

10. För **klient-ID**, använda den **program-ID** för klient-app.

11. För **klienthemlighet**, med den nyckel som du skapade tidigare för klient-app. 

12. Omedelbart efter klienthemligheten är den **redirect_url** kod bevilja typ för tillståndet. Anteckna denna URL.

13. Välj **Skapa**.

14. Gå tillbaka till den **inställningar** sidan i din klientapp.

15. Välj **Reply URL: er**, och klistra in den **redirect_url** på den första raden. I det här exemplet ersätts `https://localhost` med URL-Adressen på den första raden.  

Nu när du har konfigurerat en server för OAuth 2.0-auktorisering, kan Developer-konsolen hämta åtkomsttoken från Azure AD. 

Nästa steg är att aktivera användarautentiseringen för OAuth 2.0 för din API. Detta gör att Utvecklarkonsolen om du vill veta att de måste få en åtkomsttoken på uppdrag av användaren innan anrop till ditt API.

1. Bläddra till din API Management-instans och gå till **API: er**.

2. Välj API som du vill skydda. I det här exemplet använder du den `Echo API`.

3. Gå till **inställningar**.

4. Under **säkerhet**, Välj **OAuth 2.0**, och välj den OAuth 2.0-server som du tidigare har konfigurerat. 

5. Välj **Spara**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Anropa API: et från developer-portalen

Nu när användarautentiseringen OAuth 2.0 är aktiverat på den `Echo API`, Developer-konsolen hämtar en åtkomst-token för användarens räkning, innan du anropar API: et.

1. Bläddra till alla åtgärder under den `Echo API` i developer portal och välj **prova**. Detta ger dig till Developer-konsolen.

2. Lägg märke till ett nytt objekt i den **auktorisering** avsnittet motsvarar auktoriserings-server som du just lagt till.

3. Välj **Auktoriseringskoden** från tillståndet listrutan, och du uppmanas att logga in på Azure AD-klient. Om du har redan loggat in med kontot, kan du inte ombedd.

4. Efter lyckad inloggning, en `Authorization` huvudet har lagts till på begäran med en åtkomst-token från Azure AD. Följande är ett exempel token (Base64-kodade):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Välj **skicka**, och du kan anropa API: N har.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en princip för verifiering av JWT för att godkänna begäranden före

Nu uppmanas användaren att logga in när en användare försöker ringa ett samtal från Developer-konsolen. Developer-konsolen hämtar en åtkomst-token för användarens räkning.

Men vad händer om någon ringer ditt API utan en token eller med en ogiltig token? Exempelvis kan du fortfarande anropa API: et även om du tar bort den `Authorization` rubrik. Anledningen är att API-hantering inte kan valideras åtkomsttoken nu. Helt enkelt passerar den `Authorization` sidhuvud till backend-API: et.

Du kan använda den [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) princip för att godkänna begäranden i API Management före genom att verifiera åtkomsttoken för varje inkommande begäran. Om en begäran inte har en giltig token blockerar den API-hantering. Exempelvis kan du lägga till följande principen så att den `<inbound>` principavsnitt i den `Echo API`. Den kontrollerar målgruppen anspråk i en åtkomst-token och returnerar ett felmeddelande om token inte är giltig. Mer information om hur du konfigurerar principer finns [ange eller redigera principer](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Skapa ett program för att anropa API: et

I den här guiden du använde Utvecklarkonsolen i API-hantering som klienten exempelprogrammet för att anropa den `Echo API` skyddas av OAuth 2.0. Läs mer om hur du skapar ett program och implementera OAuth 2.0 i [kodexempel för Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azure Active Directory och OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Checka ut mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API-hantering.
* Andra sätt att skydda din backend-tjänst finns [ömsesidig autentisering](api-management-howto-mutual-certificates.md).

* [Skapa en instans för API Management-tjänsten](get-started-create-service-instance.md).

* [Hantera din första API](import-and-publish.md).
