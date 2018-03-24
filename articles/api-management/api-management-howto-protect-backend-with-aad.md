---
title: Skydda en API med OAuth 2.0 med Azure Active Directory och API-hantering | Microsoft Docs
description: Lär dig hur du skyddar en webb-API-serverdel med Azure Active Directory och API-hantering.
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
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Hur du skyddar ett API med OAuth 2.0 med Azure Active Directory och API-hantering

Den här guiden visar hur du konfigurerar din API Management (APIM)-instans för att skydda en API med OAuth 2.0-protokollet med Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Krav
Om du vill följa stegen i den här artikeln, måste du ha:
* En APIM-instans
* En API som publiceras med APIM-instansen
* En Azure AD-klient

## <a name="overview"></a>Översikt

Den här guiden visar hur du skyddar ett API med OAuth 2.0 i APIM. I den här artikeln används Azure AD som auktorisering Server (OAuth-Server). Nedan finns en snabb överblick över stegen:

1. Registrera ett program (backend-app) i Azure AD för att representera API: et
2. Registrera ett annat program (klientappen) i Azure AD för att representera ett klientprogram som måste anropa API: et
3. Bevilja behörigheter för att tillåta klientappen att anropa backend-app i Azure AD
4. Konfigurera Developer-konsolen för att använda OAuth 2.0 användarautentiseringen
5. Lägg till princip för att verifiera jwt för att validera OAuth-token för varje inkommande begäran

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrera ett program i Azure AD för att representera API: et

Det första steget är att registrera ett program i Azure AD som representerar API: et för att skydda en API med Azure AD. 

Navigera till din Azure AD-klient och navigera till **App registreringar**.

Välj **nya appregistrering**. 

Ange ett namn för programmet. I det här exemplet `backend-app` används.  

Välj **webbapp / API** som den **programtyp**. 

För **inloggnings-URL**, kan du använda `https://localhost` som platshållare.

Klicka på **skapa**.

När programmet har skapats, notera den **program-ID** för användning i ett senare skede. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrera ett annat program i Azure AD för att representera ett klientprogram

Alla klientprogram som behöver anropa API: N måste vara registrerad som ett program i Azure AD. I den här guiden använder vi Developer-konsolen i APIM Developer-portalen som klienten exempelprogrammet. 

Vi behöver registrera ett annat program i Azure AD för att representera Developer-konsolen.

Klicka på **nya appregistrering** igen. 

Ange ett namn för programmet och välj **webbapp / API** som den **programtyp**. I det här exemplet `client-app` används.  

För **inloggnings-URL**, kan du använda `https://localhost` som en platshållare eller använda inloggning URL-Adressen för din APIM-instans. I det här exemplet `https://contoso5.portal.azure-api.net/signin` används.

Klicka på **skapa**.

När programmet har skapats, notera den **program-ID** för användning i ett senare skede. 

Nu ska vi skapa en klienthemlighet för det här programmet för användning i ett senare steg.

Klicka på **inställningar** igen och gå till **nycklar**.

Under **lösenord**, ange en **nyckeln beskrivning**, Välj när nyckeln ska upphöra att gälla och klicka på **spara**.

Anteckna värdet för nyckeln. 

## <a name="grant-permissions-in-aad"></a>Bevilja behörighet i AAD

Vi har nu registrerats två program för att representera API (backend-app) och Developer-konsolen (klient-app), måste vi du bevilja behörigheter för att tillåta klientappen att anropa backend-app.  

Gå till **programmet registreringar** igen. 

Klicka på `client-app` och gå till **inställningar**.

Klicka på **nödvändiga behörigheter** och sedan **Lägg till**.

Klicka på **väljer en API** och Sök efter `backend-app`.

Kontrollera `Access backend-app` under **delegerad behörighet**. 

Klicka på **Välj** och sedan **klar**. 

> [!NOTE]
> Om **Windows** **Azure Active Directory** är inte i listan under behörigheter för andra program, klickar du på **Lägg till** och lägga till den i listan.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivera OAuth 2.0 användarautentiseringen i Developer-konsolen

Nu vi har skapat våra program i Azure AD och har korrekt behörighet att tillåta klientappen att anropa backend-app. 

I den här guiden använder vi Developer-konsolen som klienten-app. Nedanstående steg Beskriv hur du aktiverar OAuth 2.0-användarautentiseringen i Utvecklarkonsolen 

Gå till din APIM-instans.

Klicka på **OAuth 2.0** och sedan **lägga till**.

Ange en **visningsnamn** och **beskrivning**.

Sidan-URL för klientregistrering ** ange ett platshållarvärde som `http://localhost`.  Den **klienten URL för registrering** pekar på den sida som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder Användarhantering av konton. I det här exemplet användare inte skapa och konfigurera sina egna konton, så att en platshållare används.

Kontrollera **Auktoriseringskoden** som den **auktorisering bevilja typer**.

Ange sedan **slutpunkts-URL-auktorisering** och **Token slutpunkts-URL**.

Dessa värden kan hämtas från den **slutpunkter** sida i Azure AD-klienten. Om du vill komma åt slutpunkterna, navigera till den **App registreringar** sidan igen och klicka på **slutpunkter**.

Kopiera den **OAuth 2.0-Autentiseringsslutpunkt** och klistrar in det i den **slutpunkts-URL-auktorisering** textruta.

Kopiera den **OAuth 2.0-Token för slutpunkt** och klistrar in det i den **Token slutpunkts-URL** textruta.

Förutom klistra in tokenslutpunkten, lägga till en ytterligare brödtext parameter med namnet **resurs** och använda värdet i **program-ID** för backend-app.

Därefter anger du klientautentiseringsuppgifterna. Det är dessa autentiseringsuppgifter för klient-app.

För **klient-Id**, använda den **program-ID** för klient-app.

För **klienthemlighet**, med den nyckel som du skapade tidigare för klient-app. 

Omedelbart efter klienthemligheten är den **redirect_url** kod bevilja typ för tillståndet.

Anteckna denna URL.

Klicka på **skapa**.

Gå tillbaka till den **inställningar** sidan i din klientapp.

Klicka på **Reply URL: er** och klistra in den **redirect_url** på den första raden. I det här exemplet ersätts vi `https://localhost` med URL-Adressen på den första raden.  

Nu vi har konfigurerat en OAuth 2.0-auktorisering Server ska Developer-konsolen kunna hämta åtkomsttoken från Azure AD. 

Nästa steg är att aktivera användarautentiseringen för OAuth 2.0 för vårt API, så att den Utvecklarkonsolen vet det måste få en åtkomsttoken för användarens räkning innan anrop till vårt API.

Navigera till din APIM-instans, gå till **API: er**.

Klicka på det API som du vill skydda. I det här exemplet ska vi använda den `Echo API`.

Gå till **inställningar**.

Under **säkerhet**, Välj **OAuth 2.0** och välja en OAuth 2.0-server som vi har konfigurerat tidigare. 

Klicka på **Spara**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Anropa API: et från developer-portalen

Nu när användarautentiseringen OAuth 2.0 är aktiverat på den `Echo API`, Developer-konsolen ska få en åtkomsttoken för användarens räkning innan du anropar API: et.

Navigera till alla åtgärder under den `Echo API` i Developer-portalen och klicka på **prova**, som kommer att få oss till Developer-konsolen.

Lägg märke till ett nytt objekt i den **auktorisering** avsnittet motsvarar auktoriserings-server som du just lagt till.

Välj **Auktoriseringskoden** från tillståndet nedrullningsbara listan och du uppmanas att logga in på Azure AD-klient. Om du redan har loggat in med kontot, kan du inte ombedd.

Efter lyckad inloggning, en `Authorization` sidhuvud ska läggas till på begäran med en åtkomst-token från Azure AD. 

En exempel-token ser ut som nedan, det är Base64-kodad.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Klicka på **skicka** och du ska kunna anropa API: N har.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en princip för verifiering av JWT för att godkänna begäranden före

Nu när en användare försöker att ringa ett samtal från Developer-konsolen, användaren uppmanas att logga in och Developer-konsolen ska få en åtkomst-Token för användarens räkning. Allt fungerar som förväntat. Men vad händer om någon ringer vårt API utan en token eller med en ogiltig token? Exempelvis kan du ta bort den `Authorization` sidhuvud och hittar du kan fortfarande att anropa API: et. Det beror på eftersom APIM inte validerar åtkomst-Token i det här läget. Passerar den `Auhtorization` sidhuvud till API-serverdelen.

Vi kan använda den [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) princip för att godkänna begäranden i APIM före genom att verifiera åtkomsttoken för varje inkommande begäran. Om en begäran inte har en giltig token, blockeras av API-hantering och skickas inte vidare till serverdelen. Vi kan lägga till den nedan för att `Echo API`. 

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

## <a name="next-steps"></a>Nästa steg
* Checka ut mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API-hantering.
* Andra sätt att skydda din backend-tjänst finns [ömsesidig autentisering](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
