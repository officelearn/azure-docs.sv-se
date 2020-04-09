---
title: 'Skapa en mobilapp som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform | Azure
description: Läs om hur du skapar en mobilapp som anropar webb-API:er (översikt)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882581"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Mobilprogram som anropar webb-API:er

Läs om hur du skapar en mobilapp som anropar webb-API:er.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Skapa din första mobilapplikation och prova en snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Skaffa en token och anropa Microsoft Graph API från en Android-app](./quickstart-v2-android.md)
>
> [Snabbstart: Skaffa en token och anropa Microsoft Graph API från en iOS-app](./quickstart-v2-ios.md)
>
> [Snabbstart: Skaffa en token och anropa Microsoft Graph API från en Xamarin iOS- och Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Översikt

En personlig och sömlös användarupplevelse är avgörande för mobilappar.  Microsofts identitetsplattform gör det möjligt för mobila utvecklare att skapa den upplevelsen för iOS- och Android-användare. Ditt program kan logga in Azure Active Directory (Azure AD) användare, personliga Microsoft-kontoanvändare och Azure AD B2C-användare. Det kan också hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden använder vi Microsoft Authentication Library (MSAL). MSAL implementerar branschstandarden [OAuth2.0 auktoriseringskodflöde](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Överväganden för mobilappar:

- **Användarupplevelsen är nyckeln:** Tillåt användare att se värdet på din app innan du ber om inloggning. Begär endast de behörigheter som krävs.
- **Stöd för alla användarkonfigurationer:** Många mobila företagsanvändare måste följa principer för villkorlig åtkomst och principer för enhetsefterlevnad. Var noga med att stödja dessa viktiga scenarier.
- **Implementera enkel inloggning (SSO):** Genom att använda MSAL- och Microsoft-identitetsplattform kan du aktivera enkel inloggning via enhetens webbläsare eller Microsoft Authenticator (och Intune Company Portal på Android).
- **Implementera läget delad enhet:** Aktivera ditt program så att det används i scenarier med delade enheter, till exempel sjukhus, tillverkning, detaljhandel och ekonomi. [Läs mer om att stödja läget för delade enheter](msal-shared-devices.md).

## <a name="specifics"></a>Detaljerna

Tänk på följande när du skapar en mobilapp på Microsofts identitetsplattform:

- Beroende på plattformen kan viss användarinteraktion krävas första gången som användare loggar in. IOS kräver till exempel att appar visar användarinteraktion när de använder SSO för första gången via Microsoft Authenticator (och Intune Company Portal på Android).
- På iOS och Android kan MSAL använda en extern webbläsare för att logga in användare. Den externa webbläsaren kan visas ovanpå appen.
- Använd aldrig en hemlighet i en mobilapplikation. I dessa program är hemligheter tillgängliga för alla användare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-mobile-app-registration.md)
