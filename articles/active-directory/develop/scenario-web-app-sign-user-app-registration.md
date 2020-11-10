---
title: Registrera en webbapp som loggar in användare – Microsoft Identity Platform | Azure
description: Lär dig hur du registrerar en webbapp som loggar in användare
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 29f5d2960a678204387b2bd1dfd6d4acdc4f9c3d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442522"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Webbapp som loggar in användare: app-registrering

I den här artikeln förklaras registrerings information för appar i en webbapp som loggar in användare.

Du kan registrera ditt program genom att använda:

- [Snabb start för webb program](#register-an-app-by-using-the-quickstarts). Förutom att vara en bra första upplevelse med att skapa ett program, innehåller snabb starter i Azure Portal en knapp som heter **gör den här ändringen åt mig**. Du kan använda den här knappen för att ange de egenskaper du behöver, även för en befintlig app. Du måste anpassa värdena för dessa egenskaper till ditt eget fall. I synnerhet kommer URL: en för webb-API: t för din app förmodligen att skilja sig från det föreslagna standardvärdet, vilket även påverkar utloggnings-URI: n.
- Azure Portal att [Registrera programmet manuellt](#register-an-app-by-using-the-azure-portal).
- PowerShell och kommando rads verktyg.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrera en app med snabb starterna

Du kan använda dessa länkar för att starta skapandet av ditt webb program:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrera en app med hjälp av Azure Portal

> [!NOTE]
> Vilken portal som ska användas är olika beroende på om ditt program körs i Microsoft Azure offentliga molnet eller i ett nationellt eller suveränt moln. Mer information finns i [nationella moln](./authentication-national-cloud.md#app-registration-endpoints).


1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto. Du kan också logga in på [Azure Portal val](./authentication-national-cloud.md#app-registration-endpoints) för det nationella molnet.
2. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ange sedan din portal-session till önskad Azure Active Directory-klient (Azure AD).
3. I den vänstra rutan väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**  >  **ny registrering**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. Välj de konto typer som stöds för programmet. (Se [konto typer som stöds](./v2-supported-account-types.md).)
   1. I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användare av appen. Ange till exempel **AspNetCore-webapp**.
   1. För **omdirigerings-URI** lägger du till den typ av program och URI-destination som accepterar returnerade token-svar efter lyckad autentisering. Ange till exempel **https://localhost:44321** . Välj sedan **Registrera**.
   ![Skärm bild som visar sidan Registrera ett program där du kan välja registrera.](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   1. För **svars-URL** , Lägg till **https://localhost:44321/signin-oidc** typ **webb**.
   1. I avsnittet **Avancerade inställningar** anger du **utloggnings-URL** till **https://localhost:44321/signout-oidc** .
   1. Under **Implicit beviljande** väljer du **ID-token**.
   1. Välj **Spara**.
  ![Skärm bild som visar autentiseringsalternativ där du kan göra ändringarna som beskrivs.](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. När **sidan Registrera ett program** visas anger du programmets registrerings information:
   1. Välj de konto typer som stöds för programmet. (Se [konto typer som stöds](./v2-supported-account-types.md).)
   1. I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användare av appen. Ange till exempel **MailApp-openidconnect-v2**.
   1. I avsnittet **omdirigerings-URI (valfritt)** väljer du **webb** i kombinations rutan och anger följande omdirigerings-URI: **https://localhost:44326/** .
1. Välj **Registrera** för att skapa programmet.
1. Välj menyn **autentisering** .
1. I avsnittet **Avancerade inställningar** för  |  **implicit beviljande** väljer du **ID-token**. Det här exemplet kräver att det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md) är aktiverat för att logga in användaren.
1. Välj **Spara**.

# <a name="java"></a>[Java](#tab/java)

1. När **sidan Registrera ett program** visas anger du ett visnings namn för programmet. Ange till exempel **Java-webapp**.
1. Välj **konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** och välj sedan **webbapp/API** för **program typ**.
1. Välj **Registrera** för att registrera programmet.
1. Välj **autentisering** på den vänstra menyn. Under **omdirigerings-URI: er** väljer du **webb**.

1. Ange två omdirigerings-URI: er: en för inloggnings sidan och en för diagram sidan. För båda använder du samma värd och port nummer, följt av **/msal4jsample/Secure/AAD** för inloggnings sidan och **msal4jsample/graf/me** för sidan med användar information.

   Som standard använder exemplet:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Välj sedan **Spara**.

1. Välj **certifikat & hemligheter** på menyn.
1. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet** och sedan:

   1. Ange en nyckel beskrivning.
   1. Välj nyckel varaktigheten **i 1 år**.
   1. Välj **Lägg till**.
   1. När nyckelvärdet visas kopierar du det för senare. Det här värdet kommer inte att visas igen eller kan hämtas på annat sätt.

# <a name="python"></a>[Python](#tab/python)

1. När **sidan Registrera ett program** visas anger du programmets registrerings information:
   1. I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användare av appen. Ange till exempel **python-webapp**.
   1. Ändra **konto typer som stöds** till **konton i alla organisations kataloger och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)**.
   1. I avsnittet **omdirigerings-URI (valfritt)** väljer du **webb** i kombinations rutan och anger följande omdirigerings-URI: **http://localhost:5000/getAToken** .
1. Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt** för appen letar du reda på **programmets (klient) ID-** värde och registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för projektet.
1. På den vänstra menyn väljer du **certifikat & hemligheter**.
1. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet** och sedan:

   1. Ange en nyckel beskrivning.
   1. Välj en nyckel varaktighet på om **1 år**.
   1. Välj **Lägg till**.
   1. När nyckelvärdet visas kopierar du det. Du behöver det senare.
---

## <a name="register-an-app-by-using-powershell"></a>Registrera en app med hjälp av PowerShell

> [!NOTE]
> Azure AD PowerShell skapar för närvarande endast program med följande konto typer som stöds:
>
> - MyOrg (endast konton i den här organisatoriska katalogen)
> - AnyOrg (konton i valfri organisations katalog)
>
> Du kan skapa ett program som loggar in användare med sina personliga Microsoft-konton (till exempel Skype, Xbox eller Outlook.com). Börja med att skapa ett program med flera innehavare. Konto typer som stöds är konton i valfri organisations katalog. Ändra sedan [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) egenskapen till **2** och [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) egenskapen till `AzureADandPersonalMicrosoftAccount` i [applikations manifestet](./reference-app-manifest.md) från Azure Portal. Mer information finns i [steg 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) i självstudierna för ASP.net Core. Du kan generalisera det här steget till Web Apps på valfritt språk.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [appens kod konfiguration](scenario-web-app-sign-user-app-configuration.md).