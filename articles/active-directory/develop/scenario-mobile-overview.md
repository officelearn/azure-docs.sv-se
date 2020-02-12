---
title: 'Bygg en mobilapp som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (översikt)'
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
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132425"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: mobil program som anropar webb-API: er

Lär dig hur du skapar en mobilapp som anropar webb-API: er.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Skapa ditt första mobil program och prova en snabb start.

> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en Android-app](./quickstart-v2-android.md)
>
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en iOS-app](./quickstart-v2-ios.md)
>
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en Xamarin iOS-och Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Översikt

En anpassad och smidig användar upplevelse är nödvändig för mobila appar.  Med Microsoft Identity Platform kan mobila utvecklare skapa denna upplevelse för iOS-och Android-användare. Ditt program kan logga in Azure Active Directory (Azure AD)-användare, personliga Microsoft-konto användare och Azure AD B2C användare. Det kan också hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden använder vi Microsoft Authentication Library (MSAL). MSAL implementerar standarden [OAuth 2.0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Överväganden för Mobile Apps:

- **Användar upplevelsen är nyckel**: Tillåt användare att se appens värde innan du ber om inloggning. Begär endast de behörigheter som krävs.
- **Stöd för alla användarkonfigurationer**: många användare av mobila företag måste följa principer för villkorlig åtkomst och principer för enhets efterlevnad. Se till att du har stöd för dessa nyckel scenarier.
- **Implementera enkel inloggning (SSO)** : med hjälp av MSAL och Microsoft Identity Platform kan du aktivera enkel inloggning via enhetens webbläsare eller Microsoft Authenticator (och Intune-företagsportal på Android).

## <a name="specifics"></a>Information

Tänk på följande när du skapar en mobilapp på Microsoft Identity Platform:

- Beroende på plattform kan vissa användar åtgärder krävas första gången användaren loggar in. IOS kräver till exempel appar för att Visa användar interaktion när de använder SSO för första gången via Microsoft Authenticator (och Intune-företagsportal på Android).
- På iOS och Android kan MSAL använda en extern webbläsare för att logga in användare. Den externa webbläsaren kan visas ovanpå din app. Du kan anpassa konfigurationen så att webviews-appar används i stället.
- Använd aldrig en hemlighet i ett mobil program. I dessa program är hemligheter tillgängliga för alla användare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-mobile-app-registration.md)
