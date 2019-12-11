---
title: 'Bygg en stationär app som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (översikt)'
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 545012629686e1fe3ece8a48ed852542e09e54fe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965525"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Skriv bords program som anropar webb-API: er

Lär dig allt du behöver för att bygga en stationär app som anropar webb-API: er

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Om du inte redan har gjort det kan du skapa ditt första program genom att följa snabb starten för .NET Desktop, UWP snabb start eller den virtuella macOS-appen snabb start:

> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en Windows Desktop-app](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en macOS-inbyggd app](./quickstart-v2-ios.md)

## <a name="overview"></a>Översikt

Du skriver ett Skriv bords program och vill logga in användare i ditt program och anropa webb-API: er, till exempel Microsoft Graph, andra Microsoft API: er eller ditt eget webb-API. Det finns flera möjligheter:

- Du kan använda den interaktiva hämtningen av token:

  - Om ditt Skriv bords program stöder grafiska kontroller, till exempel om det är ett Windows. Forms-program, ett WPF-program eller ett internt macOS-program.
  - Om det är ett .NET Core-program och du samtycker till att interagera med Azure AD sker i system läsaren

- För Windows-värdbaserade program är det också möjligt för program som körs på datorer som är anslutna till en Windows-domän eller AAD-ansluten för att hämta en token i tysthet med hjälp av integrerad Windows-autentisering.
- Slutligen, och även om det inte rekommenderas, kan du använda användar namn/lösen ord i offentliga klient program. Det behövs fortfarande i vissa scenarier (t. ex. DevOps), men tänk på att använda den för att införa begränsningar i ditt program. Den kan till exempel inte logga in användare som behöver utföra Multi-Factor Authentication (villkorlig åtkomst). Även om ditt program inte drar nytta av enkel inloggning (SSO).

  Det är också mot principerna för modern autentisering och tillhandahålls endast av tidigare skäl.

  ![Skriv bords program](media/scenarios/desktop-app.svg)

- Om du skriver ett bärbart kommando rads verktyg – förmodligen ett .NET Core-program som körs på Linux eller Mac – och om du godkänner att autentiseringen ska delegeras till system läsaren, kommer du att kunna använda interaktiv autentisering. (.NET Core tillhandahåller inte ännu en [webbläsare](https://aka.ms/msal-net-uses-web-browser) och därför sker autentiseringen i systemets webbläsare), annars är det bästa alternativet i det fallet att använda enhets kod flödet. Det här flödet används också för program utan webbläsare, till exempel IoT-program

  ![Webbläsarbaserat program](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Information

Skriv bords program har ett antal specifika funktioner, vilket beror huvudsakligen på om programmet använder den interaktiva autentiseringen eller inte.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Desktop app-app-registrering](scenario-desktop-app-registration.md)
