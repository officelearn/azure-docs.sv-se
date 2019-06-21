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
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150234"
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
   1. Välj kontotyperna som stöds för ditt program (se [stöds kontotyper](./v2-supported-account-types.md))
   1. I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-WebApp`.
   1. I **omdirigerings-URI**lägger du till typ av program och URI-mål som ska ta emot returnerade tokensvar efter autentisering. Till exempel `https://localhost:44321/`.  Välj **Registrera**.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   1. I **svars-URL**, lägga till `https://localhost:44321/signin-oidc`.
   1. I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:44321/signout-oidc`.
   1. Under **Implicit beviljande** kontrollerar du **ID-token**.
   1. Välj **Spara**.

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
