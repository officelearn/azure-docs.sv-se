---
title: Webbapp som loggar in användare (app-registrering) – Microsoft Identity Platform
description: Lär dig hur du skapar en webbapp som loggar in användare (app-registrering)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641f71f6111930b54d0a2bd548f16d3cb0c07189
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175258"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Webbapp som loggar in användare – app-registrering

På den här sidan förklaras program registreringen för en webbapp som loggar in användare.

Du kan registrera ditt program genom att använda:

- [Webbappens snabb starter](#register-an-app-using-the-quickstarts) – förutom att det är en bra första upplevelse med att skapa ett program kan snabb starter i Azure Portal innehålla en knapp **som heter gör den här ändringen åt mig**. Du kan använda den här knappen för att ange de egenskaper du behöver, även för en befintlig app. Du måste anpassa värdena för dessa egenskaper till ditt eget fall. I synnerhet kommer URL: en för webb-API: t för din app förmodligen att skilja sig från det föreslagna standardvärdet, vilket också påverkar utloggnings-URI.
- Azure Portal att [Registrera ditt program manuellt](#register-an-app-using-azure-portal)
- PowerShell och kommando rads verktyg

## <a name="register-an-app-using-the-quickstarts"></a>Registrera en app med snabb starterna

Om du navigerar till den här länken kan du skapa en start för att skapa ditt webb program:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>Registrera en app med hjälp av Azure Portal

> [!NOTE]
> vilken portal som ska användas är olika beroende på om ditt program körs i Microsoft Azure offentliga molnet eller i ett nationellt eller suveränt moln. Mer information finns i [nationella moln](./authentication-national-cloud.md#app-registration-endpoints)


1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto. Du kan också logga in på den nationella moln Azure Portal av Choice.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar** > **ny registrering**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. Välj de konto typer som stöds för ditt program (se [konto typer som stöds](./v2-supported-account-types.md))
   1. I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-WebApp`.
   1. I **omdirigerings-URI**lägger du till den typ av program och URI-destination som accepterar returnerade token-svar efter att autentiseringen har utförts. Till exempel `https://localhost:44321/`.  Välj **Registrera**.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   1. Lägg till `https://localhost:44321/signin-oidc` av typen "Web" i **svars-URL**.
   1. I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:44321/signout-oidc`.
   1. Under **Implicit beviljande** kontrollerar du **ID-token**.
   1. Välj **Spara**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. Välj de konto typer som stöds för ditt program (se [konto typer som stöds](./v2-supported-account-types.md))
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `MailApp-openidconnect-v2`.
   - I avsnittet omdirigerings-URI (valfritt) väljer du webb i kombinations rutan och anger följande omdirigerings **-** URI: er: `https://localhost:44326/`.
1. Välj **Registrera** för att skapa programmet.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   - I avsnittet **Avancerade inställningar** | **implicita bidrag** kontrollerar du **ID-token** som det här exemplet kräver att det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md) är aktiverat för att logga in användaren.
1. Välj **Spara**.

# <a name="javatabjava"></a>[Java](#tab/java)

4. När **sidan Registrera ett program** visas anger du ett eget namn för programmet, till exempel "Java-webapp", väljer "konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)" och välj "webbapp" /API "som *program typ*.
1. Klicka på **Registrera** för att registrera programmet.
1. Klicka på **autentisering**på den vänstra menyn och under *omdirigerings-URI: er*väljer du "Web". Du måste ange två olika omdirigerings-URI: er: en för inloggnings sidan och en för diagram sidan. För båda ska du använda samma värd och port nummer, följt av "/msal4jsample/Secure/AAD" för inloggnings sidan och "msal4jsample/Graph/Me" på sidan användar information.
 Som standard använder exemplet:

    - `http://localhost:8080/msal4jsample/secure/aad`.
    - `http://localhost:8080/msal4jsample/graph/me`

    I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `http://localhost:8080/msal4jsample/sign_out`.

     Klicka på **Spara**.

1. Välj **certifikaten & hemligheter** på menyn och klicka på **ny klient hemlighet**i avsnittet **klient hemligheter** :

    - Ange en nyckel Beskrivning
    - Välj en nyckel varaktighet **i ett år**.
    - Nyckelvärdet visas när du väljer **Lägg till**.
    - Kopiera värdet för nyckeln för senare. Det här nyckelvärdet visas inte igen eller kan inte hämtas på något annat sätt, så du kan registrera det så snart det visas från Azure Portal.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `python-webapp`.
   - Ändra **konto typer som stöds** till **konton i alla organisations kataloger och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** .
   - I avsnittet omdirigerings-URI (valfritt) väljer du webb i kombinations rutan och anger följande omdirigerings **-** URI: er: `http://localhost:5000/getAToken`.
1. Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för projektet.
1. Välj avsnittet **autentisering** på appens översikts sida.
   - I avsnittet **Avancerade inställningar** anger du **en utloggnings-URL** till `http://localhost:5000/logout`

  Välj **Spara**.
1. På den vänstra menyn väljer du **certifikat & hemligheter** och klickar på **ny klient hemlighet** i avsnittet **klient hemligheter** :

      - Ange en nyckel Beskrivning
      - Välj en nyckel varaktighet på om **1 år**.
      - När du klickar på **Lägg till**visas nyckelvärdet.
      - Kopiera värdet för nyckeln. Du behöver den senare.
---

## <a name="register-an-app-using-powershell"></a>Registrera en app med PowerShell

> [!NOTE]
> För närvarande skapar Azure AD PowerShell endast program med följande konto typer som stöds:
>
> - MyOrg (endast konton i den här organisatoriska katalogen)
> - AnyOrg (konton i valfri organisations katalog).
>
> Om du vill skapa ett program som loggar in användare med sina personliga Microsoft-konton (t. ex. Skype, XBox, Outlook.com) kan du först skapa ett program för flera innehavare (konto typer som stöds = konton i valfri organisations katalog) och sedan ändra Egenskapen `signInAudience` i applikations manifestet från Azure Portal. Detta beskrivs i detalj i steg [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) i ASP.net Core själv studie kursen (och kan generaliseras till webbappar på valfritt språk).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-app-sign-user-app-configuration.md)
