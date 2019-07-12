---
title: 'Skrivbordsappen att anrop webb-API: er (översikt) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp att anrop webb-API: er (översikt)'
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
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785639"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Datorprogram som anropar webb-API:er

Lär dig allt du behöver skapa en skrivbordsapp som anropar webb-API: er

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Om du inte redan gjort kan du skapa ditt första program genom att följa Snabbstart för .NET-skrivbord eller UWP-snabbstarten:

> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en UWP-app](./quickstart-v2-uwp.md)

## <a name="overview"></a>Översikt

Du skriver ett skrivbordsprogram och du vill logga in användare i ditt program och anropa webb-API: er som Microsoft Graph, andra Microsoft-APIs eller dina egna webb-API. Du har flera möjligheter:

- Du kan använda den interaktiva tokenförvärv:

  - Om din skrivbordsprogram stöder grafiska kontroller, till exempel om det är en Windows.Form eller en WPF-program.
  - Av om det är ett .NET Core-program och du samtycker till att ha autentisering interaktion med Azure AD sker i webbläsaren system

- Det är också möjligt för program som körs på datorer som är anslutna till en Windows-domän för värd för Windows-program, eller AAD ansluten för att hämta en token tyst genom att använda integrerad Windows-autentisering.
- Slutligen, och även om det inte rekommenderas kan du använda användarnamn/lösenord i offentliga klientprogram. Det krävs fortfarande i vissa fall (till exempel DevOps), men tänk på att använda det medför begränsningar i ditt program. Exempelvis kan logga den inte in användare som behöver utföra multifaktorautentisering (villkorlig åtkomst). Ditt program dra inte också nytta av enkel inloggning (SSO).

  Det är också mot principerna för modern autentisering och finns bara för äldre orsaker.

  ![Skrivbordsprogram](media/scenarios/desktop-app.svg)

- Om du skriver en bärbar kommandoradsverktyget - förmodligen ett .NET Core-program som körs på Linux eller Mac - och om du godkänner att autentiseringen delegeras till system-webbläsaren, kommer du att kunna använda interaktiv autentisering. (.NET core ger inte ännu [webbläsare](https://aka.ms/msal-net-uses-web-browser) och därför autentiseringen sker i webbläsaren system), i annat fall det bästa alternativet i så fall är att använda kodflöde för enheten. Det här flödet används också för program utan en webbläsare, till exempel IoT-program

  ![Browserless program](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Närmare information

Program har ett antal särdrag, vilket beror huvudsakligen på om ditt program använder interaktiv autentisering eller inte.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skrivbordsappen - appregistrering](scenario-desktop-app-registration.md)
