---
title: 'Mobila appar som anropar webb-API: er – översikt | Microsoft identity-plattformen'
description: 'Lär dig att skapa en mobilapp att anrop webb-API: er (översikt)'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9978905f8ae0719f6a77ca2351f2c43bbf94ee0
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550375"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Mobilprogram att anrop webb-API: er

Lär dig allt du behöver veta för att skapa en mobilapp som anropar webb-API: er.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Skapa din första mobila program och prova att använda en Snabbstart!

> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Android-app](./quickstart-v2-android.md)
>
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en iOS-app](./quickstart-v2-ios.md)
>
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Xamarin iOS och Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Översikt

En anpassad och sömlös användarupplevelse är viktig för mobila appar.  Microsoft identity-plattformen mobila ger utvecklare möjlighet att skapa den upplevelsen för iOS och Android-användare. Ditt program kan logga in Azure Active Directory (Azure AD)-användare, användare med personliga Microsoft-konto och Azure AD B2C-användare och hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden, använder vi Microsoft Authentication Library (MSAL), som implementerar branschstandard [OAuth2.0-auktoriseringskodflöde](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Att tänka på för mobilappar:

- **Användarupplevelsen är nyckeln**: Tillåt användare att se värdet av din app innan du ställer för att logga in och begär bara behörigheterna som krävs.
- **Stöd för alla användarkonfigurationer**: Många mobila affärsanvändare är under villkorlig åtkomst och policyer för efterlevnad. Var noga med att stödja dessa viktiga scenarier.
- **Implementera enkel inloggning (SSO)**: MSAL och Microsoft identity-plattformen gör att aktivera enkel inloggning för enkelt via enhetens webbläsare eller Microsoft Authenticator (och Intune-Företagsportalen för Android).

## <a name="specifics"></a>Närmare information

Ha detta i åtanke när du skapar en mobilapp på Microsoft identity-plattformen:

- Beroende på vilken plattform eventuellt några åtgärder från användaren krävs första gången användarna loggar in. Till exempel kräver iOS appar att visa användaråtgärder när du använder enkel inloggning första gången via Microsoft Authenticator (och Intune-Företagsportalen för Android).
- MSAL kan använda en extern webbläsare (som kan visas på din app) att logga in användare på iOS och Android. Du kan anpassa konfigurationen för att använda app-Webbvyer i stället.
- Använd aldrig en hemlighet i ett mobilt program. Det ska vara tillgänglig för alla användare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-mobile-app-registration.md)
