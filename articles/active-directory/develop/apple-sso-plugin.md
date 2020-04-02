---
title: Microsoft Enterprise SSO-plugin-program för Apple-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om Microsofts Plugin-program för Azure Active Directory SSO för iOS- och macOS-enheter.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550323"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plugin-program för Apple-enheter (förhandsversion)

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Microsoft Enterprise SSO-plugin-programmet för Apple-enheter* tillhandahåller SSO (Single Sign-on) för Active Directory-konton i alla program som stöder Apples [enstaka inloggningsfunktion för Företag.](https://developer.apple.com/documentation/authenticationservices) Microsoft har haft ett nära samarbete med Apple för att utveckla den här plugin-modulen för att öka programmets användbarhet samtidigt som det ger det bästa skydd som Apple och Microsoft kan ge.

I den här versionen av public preview är Enterprise SSO-plugin-programmet endast tillgängligt för iOS-enheter och distribueras i vissa Microsoft-program.

Vår första användning av Enterprise SSO plug-in är med vår nya [delade enhet läge](msal-ios-shared-devices.md) funktionen.

## <a name="features"></a>Funktioner

Microsoft Enterprise SSO-plugin-programmet för Apple-enheter erbjuder följande fördelar:

- Tillhandahåller SSO för Active Directory-konton i alla program som stöder Apples enstaka inloggningsfunktion för företag.
- Levereras automatiskt i Microsoft Authenticator och kan aktiveras av alla MDM-lösningar (Mobile Device Management).

## <a name="requirements"></a>Krav

Så här använder du Microsoft Enterprise SSO-plugin-program för Apple-enheter:

- iOS 13.0 eller senare måste installeras på enheten.
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För offentlig förhandsversion innehåller dessa program [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md).
- Enheten måste vara MDM-registrerad (till exempel med Microsoft Intune).
- Konfigurationen måste skickas till enheten för att aktivera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter på enheten. Det här säkerhetsvillkoret krävs av Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Aktivera SSO-tillägget med hantering av mobila enheter (MDM)

Om du vill aktivera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste dina enheter skickas en signal via en MDM-tjänst. Eftersom Microsoft innehåller plugin-programmet Enterprise SSO i [Microsoft Authenticator-appen](..//user-help/user-help-auth-app-overview.md)använder du MDM för att konfigurera appen så att microsoft Enterprise SSO-plugin-programmet aktiveras.

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- **Typ**: Omdirigering
- **Tilläggs-ID:**`com.microsoft.azureauthenticator.ssoextension`
- **Team-ID:**`SGGM6D27TK`
- **Webbadresser:**
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Du kan använda Microsoft Intune som MDM-tjänst för att underlätta konfigurationen av Microsoft Enterprise SSO-plugin-programmet. Mer information finns i Dokumentationen för [Intune-konfiguration](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Använda SSO-tillägget i programmet

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och senare stöder Plugin-programmet Microsoft Enterprise SSO för Apple-enheter.

Om du vill ha stöd för delat enhetsläge som tillhandahålls av Microsoft Enterprise SSO-plugin-programmet för Apple-enheter kontrollerar du att dina program använder den angivna minimiversionen av MSAL.

## <a name="next-steps"></a>Nästa steg

Mer information om läget delad enhet på iOS finns i [Läget Delad enhet för iOS-enheter](msal-ios-shared-devices.md).
