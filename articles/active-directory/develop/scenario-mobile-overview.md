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
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076502"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Mobilprogram att anrop webb-API: er

Lär dig allt du behöver skapa en mobilapp som anropar webb-API: er.

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

En personlig, smidig slutanvändarens upplevelse är viktigt när du skapar en mobilapp.  Microsoft identity-plattformen gör det möjligt för mobilutvecklare att göra detta för iOS och Android-användare. Ditt program kan registrera i Azure AD, personligt Microsoft-konto och Azure AD B2C-användare och hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden, använder vi Microsoft Authentication Library (MSAL) som implementerar branschstandard [OAuth2.0-auktoriseringskodflöde](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Mobilapp att tänka på:

- ***Användarupplevelsen är nyckeln***: Tillåt användare att se värdet av din app innan du ställer för att logga in och bara begära behörighet.
- ***Stöd för alla användarkonfigurationer***: Många mobila affärsanvändare är under villkorlig åtkomst och policyer för efterlevnad. Var noga med att stödja dessa viktiga scenarier.
- ***Implementera enkel inloggning (SSO)***: MSAL och Microsoft identity-plattformen gör att aktivera enkel inloggning för enkelt via enhetens webbläsare eller Microsoft Authenticator (och Intune-Företagsportalen för Android).

## <a name="specifics"></a>Närmare information

När du skapar en mobilapp på Microsoft identity-plattformen, har slutpunkt till slutpunkt-upplevelsen några saker:

- Beroende på vilken plattform, kanske logga in utan att användaren inte är möjlig på den första inloggningen. iOS-, till exempel kräver appar att visa interaktion från användaren vid hämtning av enkel inloggning första gången via Microsoft Authenticator (och Intune-Företagsportalen för Android).
- MSAL kan använda en extern webbläsare (som kan visas på din app) att logga in användare på iOS och Android. Detta kan anpassas för att använda app-Webbvyer i stället.
- Använd aldrig en hemlighet i ett mobilt program, blir tillgänglig för alla användare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-mobile-app-registration.md)
