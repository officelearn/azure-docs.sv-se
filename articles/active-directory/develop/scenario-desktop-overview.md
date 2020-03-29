---
title: Skapa en skrivbordsapp som anropar webb-API:er – Microsoft identity platform | Azure
description: Lär dig hur du skapar en skrivbordsapp som anropar webb-API:er (översikt)
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
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702155"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Skrivbordsapp som anropar webb-API:er

Lär dig allt du behöver för att skapa en skrivbordsapp som anropar webb-API:er.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Komma igång

Om du inte redan har gjort det skapar du ditt första program genom att följa snabbstarten för .NET-skrivbordet, snabbstarten för Universell Windows-plattformen (UWP) eller snabbstarten för macOS-appen:

> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snabbstart: Skaffa en token och anropa Microsoft Graph API från en UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Snabbstart: Skaffa en token och anropa Microsoft Graph API från en inbyggd macOS-app](./quickstart-v2-ios.md)

## <a name="overview"></a>Översikt

Du skriver ett skrivbordsprogram och vill logga in användare på dina program- och anropa webb-API:er som Microsoft Graph, andra Microsoft-API:er eller ditt eget webb-API. Du har flera möjligheter:

- Du kan använda den interaktiva tokeninsamlingen:

  - Om ditt skrivbordsprogram stöder grafiska kontroller, till exempel om det är ett Windows.Form-program, ett WPF-program eller ett macOS-inbyggt program.
  - Eller, om det är ett .NET Core-program och du samtycker till att autentiseringsinteraktionen med Azure Active Directory (Azure AD) ska ske i systembläddraren.

- För Windows-värdprogram är det också möjligt för program som körs på datorer som är anslutna till en Windows-domän eller Azure AD gick med att hämta en token tyst med integrerad Windows-autentisering.
- Slutligen, och även om det inte rekommenderas, kan du använda ett användarnamn och ett lösenord i offentliga klientprogram. Det behövs fortfarande i vissa scenarier som DevOps. Om du använder det medför du begränsningar för ditt program. Den kan till exempel inte logga in en användare som behöver utföra multifaktorautentisering (villkorlig åtkomst). Dessutom kommer ditt program inte dra nytta av enkel inloggning (SSO).

  Det är också emot principerna för modern autentisering och tillhandahålls endast av äldre skäl.

  ![Skrivbordsprogram](media/scenarios/desktop-app.svg)

- Om du skriver ett bärbart kommandoradsverktyg, förmodligen ett .NET Core-program som körs på Linux eller Mac, och om du accepterar att autentisering kommer att delegeras till systembläddraren, kan du använda interaktiv autentisering. .NET Core tillhandahåller ingen [webbläsare,](https://aka.ms/msal-net-uses-web-browser)så autentisering sker i systembläddraren. Annars är det bästa alternativet i så fall att använda enhetskodflödet. Det här flödet används också för program utan webbläsare, till exempel IoT-program.

  ![Program utan webbläsare](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Detaljerna

Skrivbordsprogram har ett antal specifika detaljer. De beror främst på om ditt program använder interaktiv autentisering eller inte.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skrivbordsapp: Appregistrering](scenario-desktop-app-registration.md)
