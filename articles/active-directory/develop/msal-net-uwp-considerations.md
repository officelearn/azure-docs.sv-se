---
title: UWP överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om hur du använder UWP (Universal Windows Platform) med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132533"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Överväganden om hur du använder Universell Windows-plattform med MSAL.NET
Utvecklare av program som använder UWP (Universal Windows Platform) med MSAL.NET bör ta hänsyn till de begrepp som presenteras i den här artikeln.

## <a name="the-usecorporatenetwork-property"></a>Egenskapen UseCorporateNetwork
På Windows Runtime-plattformen (WinRT) `PublicClientApplication` har `UseCorporateNetwork`du egenskapen Boolean . Den här egenskapen gör det möjligt för Windows 8.1-program och UWP-program att dra nytta av integrerad Windows-autentisering (IWA) om användaren är inloggad på ett konto som har en federerad Azure Active Directory (Azure AD) -klientorganisation. Användare som är inloggade på operativsystemet kan också använda enkel inloggning (SSO). När du `UseCorporateNetwork` anger egenskapen använder MSAL.NET en webbautentiseringsmäklare (WAB).

> [!IMPORTANT]
> Om `UseCorporateNetwork` du ställer in egenskapen true förutsätter att programutvecklaren har aktiverat IWA i programmet. Så här aktiverar du IWA:
> - Aktivera följande funktioner på `Package.appxmanifest`fliken **Funktioner** i UWP-programmet:
>   - **Företagsautentisering**
>   - **Privata nätverk (klient & server)**
>   - **Delat användarcertifikat**

IWA är inte aktiverat som standard eftersom Microsoft Store kräver en hög verifieringsnivå innan den accepterar program som begär funktioner för företagsautentisering eller delade användarcertifikat. Alla utvecklare vill inte göra den här verifieringsnivån.

På UWP-plattformen fungerar inte den underliggande WAB-implementeringen korrekt i företagsscenarier där villkorlig åtkomst är aktiverad. Användare ser symptom på det här problemet när de försöker logga in med Windows Hello. När användaren ombeds att välja ett certifikat:

- Certifikatet för PIN-koden hittades inte.
- När användaren har valt ett certifikat uppmanas de inte att ange PIN-koden.

Du kan försöka undvika det här problemet genom att använda en alternativ metod som användarnamn och lösenord och telefonautentisering, men upplevelsen är inte bra.

## <a name="troubleshooting"></a>Felsökning

Vissa kunder har rapporterat följande inloggningsfel i specifika företagsmiljöer där de vet att de har en internetanslutning och att anslutningen fungerar med ett offentligt nätverk.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Du kan undvika det här problemet genom att se till att WAB (den underliggande Windows-komponenten) tillåter ett privat nätverk. Du kan göra det genom att ange en registernyckel:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Mer information finns i [Webbautentiseringsmäklare - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Nästa steg
Följande exempel innehåller mer information.

Exempel | Plattform | Beskrivning 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Ett UWP-klientprogram som använder MSAL.NET. Den kommer åt Microsoft Graph för en användare som autentiserar med hjälp av en Azure AD 2.0-slutpunkt. <br>![Topologi](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera Microsofts personliga konton och Azure AD via slutpunkten för Azure AD 2.0. Den visar också hur du kommer åt Microsoft Graph och visar den resulterande token. <br>![Topologi](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
