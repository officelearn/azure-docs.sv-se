---
title: 'Bygg en stationär app som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (översikt)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 92f0909660427e414264442523dba3ed2abe0142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83771867"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Skriv bords program som anropar webb-API: er

Lär dig allt du behöver för att bygga en Skriv bords app som anropar webb-API: er.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Kom igång

Om du inte redan har gjort det kan du skapa ditt första program genom att följa snabb starten för .NET Desktop, Universell Windows-plattform (UWP) snabb start eller den inbyggda macOS-appen snabb start:

> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en macOS-inbyggd app](./quickstart-v2-ios.md)

## <a name="overview"></a>Översikt

Du skriver ett Skriv bords program och vill logga in användare i ditt program och anropa webb-API: er, till exempel Microsoft Graph, andra Microsoft API: er eller ditt eget webb-API. Det finns flera möjligheter:

- Du kan använda den interaktiva hämtningen av token:

  - Om ditt Skriv bords program stöder grafiska kontroller, till exempel, om det är ett Windows. Forms-program, ett WPF-program eller ett internt macOS-program.
  - Eller, om det är ett .NET Core-program och du samtycker till att ha autentiseringen med Azure Active Directory (Azure AD) i system läsaren.

- För Windows-värdbaserade program är det också möjligt för program som körs på datorer som är anslutna till en Windows-domän eller Azure AD-ansluten för att hämta en token i tysthet med hjälp av integrerad Windows-autentisering.
- Slutligen, och även om det inte rekommenderas, kan du använda ett användar namn och ett lösen ord i offentliga klient program. Det behövs fortfarande i vissa scenarier som DevOps. Användning av IT-begränsningar i ditt program. Den kan till exempel inte logga in en användare som behöver utföra [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (villkorlig åtkomst). Programmet kommer också inte att dra nytta av enkel inloggning (SSO).

  Det är också mot principerna för modern autentisering och tillhandahålls endast av tidigare skäl.

  ![Skriv bords program](media/scenarios/desktop-app.svg)

- Om du skriver ett bärbart kommando rads verktyg, förmodligen ett .NET Core-program som körs på Linux eller Mac, och om du accepterar att autentiseringen kommer att delegeras till system läsaren, kan du använda interaktiv autentisering. .NET Core tillhandahåller inte någon [webbläsare](https://aka.ms/msal-net-uses-web-browser), så autentiseringen sker i systemets webbläsare. I annat fall är det bästa alternativet att använda enhets kod flödet. Det här flödet används också för program utan webbläsare, till exempel IoT-program.

  ![Webbläsarbaserat program](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Information

Skriv bords program har ett antal enskilda. De är huvudsakligen beroende av om programmet använder interaktiv autentisering eller inte.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Desktop-app: registrera appar](scenario-desktop-app-registration.md)
