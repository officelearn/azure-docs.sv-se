---
title: Skydda ett API med hjälp av OAuth 2.0 med Azure Active Directory och API Management | Microsoft Docs
description: Lär dig hur du skyddar ett webb-API-serverdelen med Azure Active Directory och API Management.
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
ms.openlocfilehash: cfe2620801f743831f77fb76f344c156676966d3
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635075"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Skydda ett API med hjälp av OAuth 2.0 med Azure Active Directory och API Management

Den här guiden visar hur du konfigurerar din Azure API Management-instans för att skydda ett API med hjälp av OAuth 2.0-protokollet med Azure Active Directory (AD Azure). 

## <a name="prerequisites"></a>Förutsättningar
Om du vill följa stegen i den här artikeln, måste du ha:
* En API Management-instans
* Ett API som publiceras som använder API Management-instans
* En Azure AD-klient

## <a name="overview"></a>Översikt

Här är en snabb översikt över stegen:

1. Registrera ett program (backend-app) i Azure AD för att representera API: et.
2. Registrera ett annat program (klientappen) i Azure AD för att representera ett klientprogram som måste anropa API: et.
3. Bevilja behörigheter för att tillåta klientappen att anropa backend-app i Azure AD.
4. Konfigurera Developer-konsolen om du vill använda OAuth 2.0-användarautentisering.
5. Lägg till den **verifiera jwt** princip för att verifiera OAuth-token för varje inkommande begäran.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrera ett program i Azure AD för att representera API: et

Det första steget är att registrera ett program i Azure AD som representerar API: et för att skydda ett API med Azure AD. 

1. Bläddra till din Azure AD-klient och bläddra sedan till **appregistreringar**.

2. Välj **Ny programregistrering**. 

3. Ange ett namn för programmet. (I det här exemplet heter `backend-app`.)  

4. Välj **webbapp / API** som den **programtyp**. 

5. För **inloggnings-URL**, du kan använda `https://localhost` som platshållare.

6. Välj **Skapa**.

När programmet har skapats, notera den **program-ID**, för användning i ett senare skede. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrera ett annat program i Azure AD för att representera ett klientprogram

Alla klientprogram som anropar API: et måste registreras som ett program i Azure AD. I det här exemplet är klienten exempelprogrammet Utvecklarkonsolen i developer-portalen för API Management. Här är att registrera ett annat program i Azure AD för att representera Developer-konsolen.

1. Välj **Ny programregistrering**. 

2. Ange ett namn för programmet. (I det här exemplet heter `client-app`.)

3. Välj **webbapp / API** som den **programtyp**.  

4. För **inloggnings-URL**, du kan använda `https://localhost` som platshållare, eller Använd inloggningen URL: en för din API Management-instans. (Det här exemplet är URL: en `https://contoso5.portal.azure-api.net/signin`.)

5. Välj **Skapa**.

När programmet har skapats, notera den **program-ID**, för användning i ett senare skede. 

Skapa nu en klienthemlighet för det här programmet för användning i ett senare skede.

1. Välj **inställningar** igen, och gå till **nycklar**.

2. Under **lösenord**, ange en **nyckelbeskrivning**. Välj när nyckeln ska upphöra att gälla och **spara**.

Anteckna värdet för nyckeln. 

## <a name="grant-permissions-in-azure-ad"></a>Bevilja behörigheter i Azure AD

Nu när du har registrerat två program som motsvarar API: et och Developer-konsolen, måste du bevilja behörigheter för att tillåta klientappen att anropa backend-app.  

1. Bläddra till **programregistreringar**. 

2. Välj `client-app`, och gå till **inställningar**.

3. Välj **nödvändiga behörigheter** > **Lägg till**.

4. Välj **Välj en API**, och Sök efter `backend-app`.

5. Under **delegerade behörigheter**väljer `Access backend-app`. 

6. Välj **Välj**, och välj sedan **klar**. 

> [!NOTE]
> Om **Azure Active Directory** visas under behörigheter för andra program, Välj **Lägg till** att lägga till den i listan.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivera OAuth 2.0-användarautentisering i Developer-konsolen

Nu kan du har skapat dina program i Azure AD och har beviljat rätt behörigheter för att tillåta klientappen att anropa backend-app. 

I det här exemplet är Developer Console-klientappen. Följande steg beskriver hur du aktiverar OAuth 2.0-användarautentisering i Developer-konsolen. 

1. Bläddra till din API Management-instans i Azure-portalen.

2. Välj **OAuth 2.0** > **lägga till**.

3. Ange en **visningsnamn** och **beskrivning**.

4. För den **klientregistreringssidans URL**, ange ett platshållarvärde som `http://localhost`. Den **klientregistreringssidans URL** pekar på den sida som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder detta. I det här exemplet kan användare inte skapa och konfigurera sina egna konton, så att du använder en platshållare i stället.

5. För **typer av auktoriseringsbeviljanden**väljer **auktoriseringskod**.

6. Ange den **URL för Auktoriseringsslutpunkt** och **Token slutpunkts-URL**. Hämtar dessa värden från den **slutpunkter** sida i Azure AD-klienten. Bläddra till den **appregistreringar** igen och välj **slutpunkter**.

    >[!NOTE]
    > Använd den **v1** här slutpunkter

7. Kopiera den **slutpunkt för OAuth 2.0-auktorisering**, och klistra in den i den **URL för Auktoriseringsslutpunkt** textrutan.

8. Kopiera den **OAuth 2.0-Tokenslutpunkten**, och klistra in den i den **Token slutpunkts-URL** textrutan. Utöver klistra in tokenslutpunkten, lägger du till en brödtextparameter med namnet **resource**. Värdet för den här parametern använder den **program-ID** för backend-appen.

9. Därefter anger du autentiseringsuppgifterna för klienten. Det är dessa autentiseringsuppgifter för klient-appen.

10. För **klient-ID**, använda den **program-ID** för klient-appen.

11. För **klienthemlighet**, använda den nyckel som du skapade för klientappen-tidigare. 

12. Omedelbart efter klienthemlighet är den **redirect_url** beviljandetyp i koden för auktorisering. Anteckna denna URL.

13. Välj **Skapa**.

14. Gå tillbaka till den **inställningar** för din klientapp.

15. Välj **Svarswebbadresser**, och klistra in den **redirect_url** på den första raden. I det här exemplet du ersatte `https://localhost` med URL-Adressen på den första raden.  

Nu när du har konfigurerat en OAuth 2.0-auktoriseringsservern, kan Developer Console hämta åtkomsttoken från Azure AD. 

Nästa steg är att aktivera OAuth 2.0-användarautentisering för ditt API. På så sätt kan Utvecklarkonsolen veta att den behöver att hämta en åtkomsttoken för användaren, innan du gör din API-anrop.

1. Bläddra till din API Management-instans och gå till **API: er**.

2. Välj det API som du vill skydda. I det här exemplet använder du den `Echo API`.

3. Gå till **inställningar**.

4. Under **Security**, Välj **OAuth 2.0**, och välj den OAuth 2.0-server som du konfigurerade tidigare. 

5. Välj **Spara**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Anropa API: et från developer-portalen

> [!NOTE]
> Det här avsnittet gäller inte för den **förbrukning** nivå, som inte stöder developer-portalen.

Nu när OAuth 2.0-användarautentisering är aktiverat på den `Echo API`, Developer Console hämtar en åtkomsttoken för användaren, innan du anropar API: et.

1. Bläddra till alla åtgärder under den `Echo API` i developer portalen och välj **prova**. Detta öppnar du Developer-konsolen.

2. Observera ett nytt objekt i den **auktorisering** avsnittet motsvarar auktoriseringsservern som du just lade till.

3. Välj **auktoriseringskod** från auktoriseringen nedrullningsbar listruta, och du uppmanas att logga in på Azure AD-klient. Om du redan har loggat in med kontot, kan du inte uppmanas.

4. Efter inloggningen lyckas, en `Authorization` huvud läggs till på begäran med en åtkomsttoken från Azure AD. Följande är en exempel-token (Base64-kodad):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Välj **skicka**, och du kan anropa API: et har.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en princip för verifiering av JWT för att förauktorisera begäranden

Nu uppmanas användaren att logga in när en användare försöker anropa från Developer-konsolen. Developer Console hämtar en åtkomsttoken för användaren.

Men vad händer om någon anropar ditt API utan en token eller med en ogiltig token? Exempelvis kan du fortfarande anropa API: et även om du tar bort den `Authorization` rubrik. Anledningen är att API Management kan inte valideras åtkomsttoken i det här läget. Det bara skickar de `Authorization` sidhuvud till serverdels-API.

Du kan använda den [verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) princip att förauktorisera begäranden i API Management genom att verifiera åtkomsttoken för varje inkommande begäran. Om en begäran inte har en giltig token, blockerar den API Management. Exempel: du kan lägga till följande princip till den `<inbound>` principavdelningen i det `Echo API`. Den kontrollerar målgrupp anspråk i en åtkomsttoken och returnerar ett felmeddelande om token inte är giltig. Information om hur du konfigurerar principer finns i [ange eller redigera principer](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Skapa ett program för att anropa API

I den här guiden du använde Utvecklarkonsolen i API Management som exempelklientprogram för att anropa den `Echo API` skyddas av OAuth 2.0. Läs mer om hur du skapar ett program och implementera OAuth 2.0 i [kodexempel för Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Active Directory och OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Kolla in mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda ditt backend-tjänst, se [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md).

* [Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).

* [Hantera ditt första API](import-and-publish.md).
