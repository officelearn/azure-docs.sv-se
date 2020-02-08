---
title: UWP-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Universell Windows-plattform med Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.openlocfilehash: 4803b2bda63ef0e14137aaafe95a422089e7f671
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083657"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Universell Windows-plattform-/regionsspecifika överväganden med MSAL.NET
På UWP finns det flera saker som du måste tänka på när du använder MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Egenskapen UseCorporateNetwork
I WinRT-plattformen har `PublicClientApplication` följande booleska egenskap ``UseCorporateNetwork``. Den här egenskapen gör att Win 8.1-och UWP-program kan dra nytta av integrerad Windows-autentisering (och därmed SSO med användaren inloggad med operativ systemet) om användaren är inloggad med ett konto i en federerad Azure AD-klient. När du ställer in den här egenskapen utnyttjar MSAL.NET WAB (Web Authentication Broker).

> [!IMPORTANT]
> Om den här egenskapen ställs in på Sant antas att programutvecklaren har aktiverat integrerad Windows-autentisering (IWA) i programmet. För detta:
> - I ``Package.appxmanifest`` för ditt UWP-program, på fliken **funktioner** , aktiverar du följande funktioner:
>   - Enterprise-autentisering
>   - Privata nätverk (klient & Server)
>   - Delat användar certifikat

IWA är inte aktiverat som standard eftersom program som efterfrågar funktionerna Enterprise-autentisering eller delade användar certifikat kräver en högre verifierings nivå för att godkännas i Windows Store, och det är inte alla utvecklare som vill utföra den högre kontroll nivå.

Den underliggande implementeringen av UWP Platform (WAB) fungerar inte korrekt i företags scenarier där villkorlig åtkomst har Aktiver ATS. Problemet är att användaren försöker logga in med Windows Hello, och det föreslås att du väljer ett certifikat, men:

- Det gick inte att hitta certifikatet för PIN-koden,
- eller användaren väljer den, men kommer aldrig att uppmanas att ange PIN-koden.

En lösning är att använda en alternativ metod (username/Password + Phone Authentication), men upplevelsen är inte felfri.

## <a name="troubleshooting"></a>Felsökning

Vissa kunder har rapporterat att i vissa företags miljöer har följande inloggnings fel uppstått:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

de vet att de har en Internet anslutning och att det fungerar med ett offentligt nätverk.

En lösning är att se till att WAB (den underliggande Windows-komponenten) tillåter privat nätverk. Det kan du göra genom att ange en register nyckel:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Mer information finns i [Web Authentication Broker-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande exempel:

Exempel | Plattform | Beskrivning 
|------ | -------- | -----------|
|[Active Directory – dotNet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Ett Universell Windows-plattform klient program som använder msal.net för att komma åt Microsoft Graph för en användare som autentiseras med Azure AD v 2.0-slutpunkten. <br>![Topologi](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som demonstrerar hur du använder MSAL för att autentisera MSA och Azure AD via AAD v 2.0-slutpunkten och komma åt Microsoft Graph med den resulterande token. <br>![Topologi](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
