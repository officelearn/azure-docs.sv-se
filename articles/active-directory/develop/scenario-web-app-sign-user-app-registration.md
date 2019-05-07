---
title: Webbapp som loggar in användare (appregistrering) - Microsoft identity-plattformen
description: Lär dig att skapa en webbapp som loggar in användare (appregistrering)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f512911811535818f4ad857c5c3b956870f619
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074552"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Webbapp som loggar in användare - appregistrering

Den här sidan förklaras ärendets för app-registrering för en webbapp som loggar in användare.

Du kan använda för att registrera ditt program:

- Den [web app snabbstarter](#register-an-app-using-the-quickstarts) -förutom att vara en positiv första upplevelse med att skapa ett program i Azure portal-Snabbstart innehåller knappen **ändrar för mig**. Du kan använda den här knappen för att ange egenskaper som du måste även för en befintlig app. Du måste anpassa värdena för dessa egenskaper till dina egna skiftläge. I synnerhet web API-URL för din app förmodligen ska skilja sig från det föreslagna standardvärdet, vilket påverkar också logga ut URI.
- Azure portal för att [registrera ditt program manuellt](#register-an-app-using-azure-portal)
- PowerShell och kommandoradsverktyg

## <a name="register-an-app-using-the-quickstarts"></a>Registrera en app med hjälp av Snabbstart

Om du navigerar till den här länken kan du skapa bootstrap skapandet av ditt webbprogram:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrera en app med hjälp av Azure portal

> [!NOTE]
> portalen för att använda skiljer sig beroende på om programmet körs i det offentliga molnet för Microsoft Azure eller i en nationell eller nationella moln. Mer information finns i [nationella moln](./authentication-national-cloud.md#app-registration-endpoints)

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto. Du kan också logga in på nationella Azure-portalen med molnet.
1. Om ditt konto får du tillgång till fler än en klient, Välj ditt konto i det övre högra hörnet och ange din portal-session med önskade Azure AD-klient.
1. I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** tjänsten och välj sedan **appregistreringar** > **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - Välj kontotyperna som stöds för ditt program (se [stöds kontotyper](./v2-supported-account-types.md))
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-WebApp`.
   - I **svars-URL**, lägga till svars-URL för din app, till exempel `https://localhost:44321/`, och välj **registrera**.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
- I **Svars-URL** lägger du till `https://localhost:44321/signin-oidc` och markerar **Registrera**.
- I den **avancerade inställningar** anger **utloggnings-URL** till `https://localhost:44321/signout-oidc`.
- Under **Implicit beviljande** kontrollerar du **ID-token**.
- Välj **Spara**.

### <a name="register-an-app-using-powershell"></a>Registrera en app med hjälp av PowerShell

> [!NOTE]
> Azure AD PowerShell skapar för närvarande endast program med följande kontotyper som stöds:
>
> - MinOrg skriver (konton i den här organisationens katalogen)
> - AnyOrg (konton i en organisations katalog).
>
> Om du vill skapa ett program som loggar in användare med sina personliga Microsoft-Accounts (t.ex. Skype, XBox, Outlook.com) kan du först skapa ett program med flera innehavare (stöds kontotyper = konton i en organisations katalog), och ändra sedan den `signInAudience` egenskapen i applikationsmanifestet från Azure-portalen. Detta beskrivs i informationen i steget [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) av ASP.NET Core-självstudiekursen (och kan generaliseras till web apps på valfritt språk).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-app-sign-user-app-configuration.md)
