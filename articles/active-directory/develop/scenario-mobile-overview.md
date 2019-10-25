---
title: 'Mobilapp som anropar webb-API: er – översikt'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98f05470a07bad82a1e51517a787fb98c78f92ce
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803759"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: mobil program som anropar webb-API: er

Lär dig allt du behöver veta för att bygga en mobilapp som anropar webb-API: er.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Skapa ditt första mobil program och testa en snabb start!

> [!div class="nextstepaction"]
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en Android-app](./quickstart-v2-android.md)
>
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en iOS-app](./quickstart-v2-ios.md)
>
> [Snabb start: Hämta en token och anropa Microsoft Graph API från en Xamarin iOS & Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Översikt

En anpassad och smidig användar upplevelse är nödvändig för mobila appar.  Med Microsoft Identity Platform kan mobila utvecklare skapa denna upplevelse för iOS-och Android-användare. Ditt program kan logga in Azure Active Directory (Azure AD)-användare, personliga Microsoft-konto användare och Azure AD B2C användare och hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden använder vi Microsoft Authentication Library (MSAL), som implementerar bransch standarden [OAuth 2.0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Överväganden för Mobile Apps:

- **Användar upplevelsen är en nyckel**: Tillåt användare att se appens värde innan du ber om inloggning och begära bara de behörigheter som krävs.
- **Stöd för alla användarkonfigurationer**: många mobila företags användare finns under principer för villkorlig åtkomst och efterlevnadsprinciper. Se till att du har stöd för dessa nyckel scenarier.
- **Implementera enkel inloggning (SSO)** : MSAL och Microsoft Identity Platform gör det enkelt att aktivera enkel inloggning via enhetens webbläsare eller Microsoft Authenticator (och Intune-företagsportal på Android).

## <a name="specifics"></a>Information

Tänk på följande när du skapar en mobilapp på Microsoft Identity Platform:

- Beroende på plattform kan vissa användar åtgärder krävas första gången användarna loggar in. IOS kräver till exempel appar för att Visa användar interaktion när SSO används första gången via Microsoft Authenticator (och Intune-företagsportal på Android).
- På iOS och Android kan MSAL använda en extern webbläsare (som kan visas ovanpå appen) för att logga in användare. Du kan anpassa konfigurationen så att webviews-appar används i stället.
- Använd aldrig en hemlighet i ett mobil program. Den kommer att vara tillgänglig för alla användare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-mobile-app-registration.md)
