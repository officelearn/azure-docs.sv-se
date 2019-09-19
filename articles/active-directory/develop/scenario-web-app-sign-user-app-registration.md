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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086709"
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

### <a name="register-an-app-using-azure-portal"></a>Registrera en app med hjälp av Azure Portal

> [!NOTE]
> vilken portal som ska användas är olika beroende på om ditt program körs i Microsoft Azure offentliga molnet eller i ett nationellt eller suveränt moln. Mer information finns i [nationella moln](./authentication-national-cloud.md#app-registration-endpoints)

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto. Du kan också logga in på den nationella moln Azure Portal av Choice.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar** > **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. Välj de konto typer som stöds för ditt program (se [konto typer som stöds](./v2-supported-account-types.md))
   1. I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AspNetCore-WebApp`.
   1. I **omdirigerings-URI**lägger du till den typ av program och URI-destination som accepterar returnerade token-svar efter att autentiseringen har utförts. Till exempel `https://localhost:44321/`.  Välj **Registrera**.
1. Välj menyn **Autentisering** och lägg sedan till följande information:
   1. Lägg till `https://localhost:44321/signin-oidc`i **svars-URL**.
   1. I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:44321/signout-oidc`.
   1. Under **Implicit beviljande** kontrollerar du **ID-token**.
   1. Välj **Spara**.

### <a name="register-an-app-using-powershell"></a>Registrera en app med PowerShell

> [!NOTE]
> För närvarande skapar Azure AD PowerShell endast program med följande konto typer som stöds:
>
> - MyOrg (endast konton i den här organisatoriska katalogen)
> - AnyOrg (konton i valfri organisations katalog).
>
> Om du vill skapa ett program som loggar in användare med sina personliga Microsoft-konton (t. ex. Skype, XBox, Outlook.com) kan du först skapa ett program för flera innehavare (konto typer som stöds = konton i valfri organisations katalog) och sedan ändra `signInAudience` egenskapen i applikations manifestet från Azure Portal. Detta beskrivs i detalj i steg [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) i ASP.net Core själv studie kursen (och kan generaliseras till webbappar på valfritt språk).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-app-sign-user-app-configuration.md)
