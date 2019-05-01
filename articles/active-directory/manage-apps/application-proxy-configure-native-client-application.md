---
title: Publicera appar med inbyggd klient - Azure AD | Microsoft Docs
description: Beskriver hur du aktiverar inbyggd klient apps att kommunicera med Azure AD Application Proxy Connector att tillhandahålla säker fjärråtkomst till lokala appar.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4614d8190436ad89faa200f83b1a71bde10a8acb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684944"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Så här aktiverar du interna klientprogram att interagera med proxy-program

Du kan använda Azure Active Directory (Azure AD) Application Proxy för att publicera webbappar, men det kan också användas till att publicera interna klientprogram som är konfigurerade med Azure AD Authentication Library (ADAL). Interna klientprogram skiljer sig från web apps eftersom de är installerade på en enhet, medan webbappar som kan nås via en webbläsare.

För att stödja interna klientprogram, accepterar Application Proxy Azure AD-utfärdade token som skickas i rubriken. Application Proxy-tjänsten stöder autentisering för användare. Den här lösningen använder inte programmet token för autentisering.

![Förhållandet mellan användare och Azure Active Directory publicerade program](./media/application-proxy-configure-native-client-application/richclientflow.png)

Om du vill publicera interna program, använder du den Azure AD Authentication Library, som tar hand om autentisering och har stöd för många klientmiljöer. Programproxy passar in i den [internt program för webb-API-scenario](../develop/native-app.md).

Den här artikeln vägleder dig igenom fyra stegen för att publicera ett internt program med Application Proxy och Azure AD-Autentiseringsbiblioteket.

## <a name="step-1-publish-your-proxy-application"></a>Steg 1: Publicera dina proxy-program

Publicera dina proxy-program, precis som alla andra program och tilldela användare åtkomst till ditt program. Mer information finns i [publicera program med Application Proxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Steg 2: Registrera ditt interna program

Nu måste du registrera ditt program i Azure AD finns på följande sätt:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/). Den **instrumentpanelen** för den **Azure Active Directory Administrationscenter** visas.
2. Välj i sidopanelen, **Azure Active Directory**. Den **Azure Active Directory** översiktssidan visas.
3. I Azure AD översikt sidopanelen, Välj **appregistreringar**. Lista över alla appregistreringar visas.
4. Välj **ny registrering**. Den **registrera ett program** visas.

   ![Skapa en ny appregistrering](./media/application-proxy-configure-native-client-application/create.png)
5. I den **namn** rubrik, ange ett visningsnamn för användarinriktade för ditt program.
6. Under den **stöds kontotyper** väljer du en åtkomstnivå med dessa riktlinjer:
   - Om du vill rikta bara de konton som är interna för din organisation, Välj **konton i den här organisationens katalogen**.
   - Om du vill rikta endast företag eller högskolor kunder, Välj **konton i alla organisationskatalog**.
   - Om du vill anpassa den största uppsättningen Microsoft identiteter, Välj **konton alla organisationskatalog och personliga Microsoft-konton**.
7. I den **omdirigerings-URI** väljer **offentlig klient (mobila och stationära)**, och skriv sedan omdirigeringen-URI för ditt program.
8. Välj och läsa den **principer för Microsoft-plattformen**, och välj sedan **registrera**. En översiktssida för ny programregistrering skapas och visas.

Mer information om hur du skapar en ny programregistrering finns [integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Steg 3: Bevilja åtkomst till programmets proxy

Nu när du har registrerat ditt interna program, kan du ge det åtkomst till andra program i din katalog i det här fallet kan komma åt proxy-program. Så här aktiverar programspecifika exponeras proxy-program:

1. I sidopanelen på registreringssidan för nya program, Välj **API-behörigheter**. Den **API-behörigheter** sidan för ny programregistrering visas.
2. Välj **lägga till en behörighet**. Den **begär API-behörigheter** visas.
3. Under den **Välj en API** inställningen, Välj **API: er som min organisation använder**. En lista visas med program i din katalog som exponerar API: er.
4. Skriv i sökrutan eller Bläddra för att hitta det proxy-program som du publicerade i [steg 1: Publicera proxyprogrammet](#step-1-publish-your-proxy-application), och välj sedan proxy-program.
5. I den **vilken typ av behörigheter för ditt program kräver?** väljer du behörighetstyp av. Om ditt interna program behöver åtkomst till API: et för proxy-program som den inloggade användaren, Välj **delegerade behörigheter**. Om ditt interna program körs som en bakgrundstjänst eller daemon utan en inloggad användare, välja **programbehörigheter**.
6. I den **Välj behörigheter** rubrik, Välj önskade behörighet och välj **Lägg till behörigheter**. Den **API-behörigheter** sidan för ditt interna program nu visar proxyn program och behörigheten API som du har lagt till.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Steg 4: Redigera Active Directory Authentication Library

Redigera koden internt program i kontexten för autentisering av den Active Directory Authentication Library (ADAL) att inkludera följande text:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Informationen som krävs i exempelkoden kan hittas i Azure AD-portalen på följande sätt:

| Information krävs | Så här hittar du i Azure AD-portalen |
| --- | --- |
| \<Klient-ID > | **Azure Active Directory** > **egenskaper** > **katalog-ID** |
| \<Extern Url för Proxy-App > | **Företagsprogram** > *programmets proxy* > **programproxy** > **externa URL: en** |
| \<App-ID för den inbyggda appen > | **Företagsprogram** > *ditt interna program* > **egenskaper** > **program-ID** |
| \<Omdirigerings-URI för den inbyggda appen > | **Azure Active Directory** > **appregistreringar** > *ditt interna program* > **omdirigerings-URI: er** |
| \<Proxy App API-Url > | **Azure Active Directory** > **appregistreringar** > *ditt interna program* > **API-behörigheter**  >  **API / NAME BEHÖRIGHETER** |

När du har redigerat ADAL med följande parametrar kan användarna autentisera till interna klientprogram även när de är utanför företagets nätverk.

## <a name="next-steps"></a>Nästa steg

Mer information om flödet internt program finns i [inbyggda appar i Azure Active Directory](../develop/native-app.md).

Läs om hur du konfigurerar [enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
