---
title: Registrera en webbapp som loggar in användare – Microsofts identitetsplattform | Azure
description: Läs om hur du registrerar en webbapp som loggar in användare
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
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701577"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Webbapp som loggar in användare: Appregistrering

I den här artikeln beskrivs appregistreringsdetecknen för en webbapp som loggar in hos användare.

För att registrera din ansökan kan du använda:

- [Snabbstarten för webbappen](#register-an-app-by-using-the-quickstarts). Förutom att vara en bra första upplevelse med att skapa ett program, snabbstarter i Azure-portalen innehåller en knapp som heter **Gör den här ändringen för mig**. Du kan använda den här knappen för att ange de egenskaper du behöver, även för en befintlig app. Du måste anpassa värdena för dessa egenskaper till ditt eget fall. I synnerhet är webb-API-URL:en för din app förmodligen kommer att skilja sig från den föreslagna standard, vilket också kommer att påverka ut logga ut URI.
- Azure-portalen för att [registrera ditt program manuellt](#register-an-app-by-using-the-azure-portal).
- PowerShell och kommandoradsverktyg.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrera en app med hjälp av snabbstarterna

Du kan använda dessa länkar för att bootstrap skapandet av din webbapplikation:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrera en app med hjälp av Azure-portalen

> [!NOTE]
> Portalen som ska användas är olika beroende på om ditt program körs i det offentliga Microsoft Azure-molnet eller i ett nationellt eller suveränt moln. Mer information finns i [Nationella moln](./authentication-national-cloud.md#app-registration-endpoints).


1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto. Du kan också logga in på azure-portalen för det nationella molnet.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ange sedan portalsessionen till önskad Azure Active Directory-klientorganisation (Azure AD).
1. I den vänstra rutan väljer du **Azure Active Directory-tjänsten** och väljer sedan **Appregistreringar** > **Ny registrering**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. Välj de kontotyper som stöds för ditt program. (Se [kontotyper som stöds](./v2-supported-account-types.md).)
   1. I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen. Ange till exempel **AspNetCore-WebApp**.
   1. För **Omdirigera URI**lägger du till den typ av program och URI-målet som accepterar returnerade tokensvar efter lyckad autentisering. Ange **https://localhost:44321**till exempel . Välj sedan **Registrera**.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   1. För **Svars-URL**lägger du till **https://localhost:44321/signin-oidc** **typwebb**.
   1. I avsnittet **Avancerade inställningar** anger du URL **för utloggning** till **https://localhost:44321/signout-oidc**.
   1. Under **Implicit beviljande** väljer du **ID-token**.
   1. Välj **Spara**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan:
   1. Välj de kontotyper som stöds för ditt program. (Se [kontotyper som stöds](./v2-supported-account-types.md).)
   1. I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen. Ange till exempel **MailApp-openidconnect-v2**.
   1. I avsnittet **Omdirigera URI (valfritt)** väljer du **Webben** i kombinationsrutan och anger följande omdirigera URI: **https://localhost:44326/**.
1. Välj **Registrera** för att skapa programmet.
1. Välj **autentiseringsmenyn.**
1. I avsnittet Implicit**beviljande** **av avancerade inställningar** | väljer du **ID-token**. Det här exemplet kräver att det [implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md) kan aktiveras för att logga in användaren.
1. Välj **Spara**.

# <a name="java"></a>[Java](#tab/java)

1. När **sidan Registrera ett program** visas anger du ett visningsnamn för programmet. Ange till exempel **java-webapp**.
1. Välj **Konton i en organisationskatalog och personliga Microsoft-konton (t.ex Outlook.com.** **Web app / API** **Application Type**
1. Välj **Registrera för** att registrera programmet.
1. Välj **Autentisering**på den vänstra menyn . Välj **Webb**under **Omdirigera URI:er**.

1. Ange två omdirigerings-URI:er: en för inloggningssidan och en för diagramsidan. För båda använder du samma värd- och portnummer, följt av **/msal4jsample/secure/aad** för inloggningssidan och **msal4jsample/graph/me** för sidan med användarinformation.

   Som standard används följande exempel:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Välj sedan **Spara**.

1. Välj **Certifikat & hemligheter** på menyn.
1. I avsnittet **Klienthemligheter** väljer du **Ny klienthemlighet**och sedan:

   1. Ange en nyckelbeskrivning.
   1. Välj **nyckellängden Om 1 år**.
   1. Välj **Lägg till**.
   1. När nyckelvärdet visas kopierar du det till senare. Det här värdet visas inte igen eller kan hämtas på något annat sätt.

# <a name="python"></a>[Python](#tab/python)

1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan:
   1. I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen. Ange till exempel **python-webapp**.
   1. Ändra **kontotyper som stöds** till konton i alla **organisationskataloger och personliga Microsoft-konton (t.ex Outlook.com.**
   1. I avsnittet **Omdirigera URI (valfritt)** väljer du **Webben** i kombinationsrutan och anger följande omdirigera URI: **http://localhost:5000/getAToken**.
1. Välj **Registrera** för att skapa programmet.
1. På appens **översiktssida** letar du reda på värdet **program (klient) och** registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för det här projektet.
1. Välj Certifikat & **hemligheter på**menyn till vänster .
1. I avsnittet **Klienthemligheter** väljer du **Ny klienthemlighet**och sedan:

   1. Ange en nyckelbeskrivning.
   1. Välj en nyckellängd **på 1 år**.
   1. Välj **Lägg till**.
   1. När nyckelvärdet visas kopierar du det. Du behöver det senare.
---

## <a name="register-an-app-by-using-powershell"></a>Registrera en app med PowerShell

> [!NOTE]
> Azure AD PowerShell skapar program med endast följande kontotyper som stöds:
>
> - MyOrg (endast konton i den här organisationskatalogen)
> - AnyOrg (konton i valfri organisationskatalog)
>
> Du kan skapa ett program som loggar in användare med deras personliga Microsoft-konton (till exempel Skype, Xbox eller Outlook.com). Skapa först ett program med flera data. Kontotyper som stöds är konton i alla organisationskataloger. Ändra sedan `signInAudience` egenskapen i programmanifestet från Azure-portalen. Mer information finns i [steg 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) i självstudien ASP.NET Core. Du kan generalisera det här steget till webbappar på alla språk.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kodkonfiguration](scenario-web-app-sign-user-app-configuration.md)
