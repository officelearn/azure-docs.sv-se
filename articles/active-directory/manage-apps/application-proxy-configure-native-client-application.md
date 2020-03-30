---
title: Publicera inbyggda klientappar – Azure AD | Microsoft-dokument
description: Beskriver hur du aktiverar inbyggda klientappar för att kommunicera med Azure AD Application Proxy Connector för att ge säker fjärråtkomst till dina lokala appar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159292"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Så här aktiverar du inbyggda klientprogram för att interagera med proxyprogram

Du kan använda Azure Active Directory (Azure AD) Application Proxy för att publicera webbappar, men den kan också användas för att publicera inbyggda klientprogram som är konfigurerade med Azure AD Authentication Library (ADAL). Inbyggda klientprogram skiljer sig från webbappar eftersom de är installerade på en enhet, medan webbappar nås via en webbläsare.

För att stödja inbyggda klientprogram accepterar programproxy Azure AD-utfärdade token som skickas i huvudet. Tjänsten Application Proxy gör autentiseringen för användarna. Den här lösningen använder inte programtoken för autentisering.

![Relation mellan slutanvändare, Azure AD och publicerade program](./media/application-proxy-configure-native-client-application/richclientflow.png)

Om du vill publicera inbyggda program använder du Azure AD Authentication Library, som tar hand om autentisering och stöder många klientmiljöer. Programproxy passar in i [scenariot Native Application to Web API](../azuread-dev/native-app.md).

I den här artikeln får du lära dig de fyra stegen för att publicera ett inbyggt program med Programproxy och Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Steg 1: Publicera ditt proxyprogram

Publicera proxyprogrammet på samma sätt som alla andra program och tilldela användare åtkomst till ditt program. Mer information finns i [Publicera program med Programproxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Steg 2: Registrera din inbyggda ansökan

Nu måste du registrera ditt program i Azure AD enligt följande:

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/). **Instrumentpanelen** för **administrationscentret** för Azure Active Directory visas.
1. Välj Azure Active **Directory**i sidofältet . Översiktssidan för **Azure Active Directory** visas.
1. Välj **Appregistreringar**i sidofältet i Azure AD översikt . Listan över alla appregistreringar visas.
1. Välj **Ny registrering**. Sidan **Registrera ett program** visas.

   ![Skapa en ny appregistrering i Azure-portalen](./media/application-proxy-configure-native-client-application/create.png)

1. Ange ett användarvänt visningsnamn för programmet i rubriken **Namn.**
1. Under rubriken **Konton som stöds** väljer du en åtkomstnivå med hjälp av följande riktlinjer:

   - Om du bara vill rikta in dig på konton som är interna i organisationen väljer du **Konton i den här organisationskatalogen**.
   - Om du bara vill rikta in dig på företags- eller utbildningskunder väljer du **Konton i en organisationskatalog**.
   - Om du vill rikta in dig på den bredaste uppsättningen Microsoft-identiteter väljer du **Konton i en organisationskatalog och personliga Microsoft-konton**.

1. I rubriken **Omdirigera URI** väljer du **Offentlig klient (mobilt &-skrivbordet)** och skriver sedan omdirigerings-URI:n för ditt program.
1. Välj och läs **Microsofts plattformsprinciper**och välj sedan **Registrera**. En översiktssida för den nya programregistreringen skapas och visas.

Mer detaljerad information om hur du skapar en ny programregistrering finns i [Integrera program med Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Steg 3: Bevilja åtkomst till ditt proxyprogram

Nu när du har registrerat ditt inbyggda program kan du ge det åtkomst till andra program i katalogen, i det här fallet för att komma åt proxyprogrammet. Så här aktiverar du det inbyggda programmet för proxyprogrammet:

1. Välj **API-behörighet i**sidofältet på den nya programregistreringssidan . **Api-behörighetssidan** för den nya programregistreringen visas.
1. Välj **Lägg till en behörighet**. Sidan **Begär API-behörigheter** visas.
1. Under inställningen **Välj ett API** väljer du **API:er som min organisation använder**. En lista visas som innehåller programmen i katalogen som exponerar API:er.
1. Skriv i sökrutan eller bläddra för att hitta proxyprogrammet som du publicerade i [steg 1: Publicera proxyprogrammet](#step-1-publish-your-proxy-application)och välj sedan proxyprogrammet.
1. I rubriken **Vilken typ av behörigheter kräver programmet?** Om ditt inbyggda program behöver komma åt proxyprogrammets API som inloggad användare väljer du **Delegerade behörigheter**.
1. I rubriken **Välj behörigheter** markerar du önskad behörighet och väljer **Lägg till behörigheter**. **Api-behörighetssidan** för ditt inbyggda program visar nu proxyprogrammet och behörighets-API:et som du har lagt till.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Steg 4: Redigera Active Directory-autentiseringsbiblioteket

Redigera den inbyggda programkoden i autentiseringskontexten för Active Directory Authentication Library (ADAL) så att följande text ingår:

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

Den information som krävs i exempelkoden finns i Azure AD-portalen enligt följande:

| Information krävs | Så här hittar du den i Azure AD-portalen |
| --- | --- |
| \<Klient-ID> | **Azure Active Directory** > **Katalog-ID för** Azure Active**Directory-egenskaper** >  |
| \<Extern url till proxyapp> | **Enterprise-program** > *din proxy program* > **Application proxy** > **extern url** |
| \<App-ID för den inbyggda appen> | **Företagsprogram** > *ditt inbyggda* > **programegenskaper** > **Application ID** |
| \<Omdirigera URI för den inbyggda app-> | **Azure Active Directory** > **App registrerar** > *dina inbyggda* > **programomdirigerings-URI:er** |
| \<Url till API-> för proxyapp | **Azure Active Directory** > **App registrerar** > *ditt inbyggda* > **program-API-behörigheter** > **API / PERMISSIONS NAME** |

När du har redigerat ADAL med dessa parametrar kan användarna autentisera till inbyggda klientprogram även när de befinner sig utanför företagsnätverket.

## <a name="next-steps"></a>Nästa steg

Mer information om det inbyggda programflödet finns [i Inbyggda appar i Azure Active Directory](../azuread-dev/native-app.md).

Lär dig mer om hur du [konfigurerar Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
