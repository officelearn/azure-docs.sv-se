---
title: Microsoft Enterprise SSO-plugin-program för Apple-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om Microsofts Azure Active Directory SSO-plugin-program för iOS-och macOS-enheter.
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
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982590"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plugin-program för Apple-enheter (för hands version)

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Microsoft Enterprise SSO-plugin-programmet för Apple-enheter* tillhandahåller enkel inloggning (SSO) för Azure Active Directory (Azure AD)-konton i alla program som stöder Apples funktion [för enkel inloggning i](https://developer.apple.com/documentation/authenticationservices) Apple. Microsoft har arbetat nära med Apple för att utveckla det här plugin-programmet för att öka programmets användbarhet samtidigt som det ger bästa möjliga skydd som Apple och Microsoft kan tillhandahålla.

I den här offentliga för hands versionen är Enterprise SSO-plugin-programmet endast tillgängligt för iOS-enheter och distribueras i vissa Microsoft-program.

Vår första användning av Enterprise SSO-plugin-programmet är med vår nya funktion för [delad enhets läge](msal-ios-shared-devices.md) .

## <a name="features"></a>Funktioner

Microsoft Enterprise SSO-plugin-programmet för Apple-enheter ger följande fördelar:

- Ger enkel inloggning för Azure AD-konton i alla program som har stöd för enkel inloggnings funktion i Apple.
- Levereras automatiskt i Microsoft Authenticator och kan aktive ras av en lösning för hantering av mobila enheter (MDM).

## <a name="requirements"></a>Krav

Använda Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- iOS 13,0 eller senare måste vara installerat på enheten.
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För den allmänt tillgängliga för hands versionen inkluderar programmen [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md).
- Enheten måste vara MDM-registrerad (till exempel med Microsoft Intune).
- Konfigurationen måste flyttas till enheten för att aktivera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter på enheten. Den här säkerhets begränsningen krävs av Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Aktivera SSO-tillägget med hantering av mobila enheter (MDM)

Om du vill aktivera plugin-programmet Microsoft Enterprise SSO för Apple-enheter måste enheterna skickas en signal via en MDM-tjänst. Eftersom Microsoft innehåller plugin-programmet för enkel inloggning i [Microsoft Authenticator-appen](..//user-help/user-help-auth-app-overview.md)använder du MDM för att konfigurera appen så att den aktiverar Microsoft Enterprise SSO-plugin-programmet.

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- **Typ**: omdirigera
- **Tilläggs-ID**:`com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**:`SGGM6D27TK`
- **URL: er**:
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

Du kan använda Microsoft Intune som MDM-tjänst för att under lätta konfigurationen av Microsoft Enterprise SSO-plugin-programmet. Mer information finns i dokumentation om [Intune-konfiguration](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Använda SSO-tillägget i ditt program

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och högre stöder Microsoft Enterprise SSO-plugin-programmet för Apple-enheter.

Om du vill stödja delat enhets läge som tillhandahålls av Microsoft Enterprise SSO-plugin-programmet för Apple-enheter kontrollerar du att programmen använder den angivna minsta nödvändiga versionen av MSAL.

## <a name="next-steps"></a>Nästa steg

För mer information om delad enhets läge på iOS, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md).
