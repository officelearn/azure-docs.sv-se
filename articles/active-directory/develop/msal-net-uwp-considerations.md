---
title: Överväganden för Universal Windows Platform (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om specifika överväganden när du använder universell Windows-plattform med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076517"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Universal Windows Platform-specifika överväganden med MSAL.NET
Det finns flera saker som du måste tänka på när du använder MSAL.NET på Xamarin iOS.

## <a name="the-usecorporatenetwork-property"></a>Egenskapen UseCorporateNetwork
I WinRT-plattformen `PublicClientApplication` har följande boolesk egenskap ``UseCorporateNetwork``. Den här egenskapen gör Win8.1 och UWP-program kan dra nytta av integrerad Windows-autentisering (och därför enkel inloggning med användaren loggat in med operativsystemet) om användaren är inloggad med ett konto i en federerad Azure AD-klient. Det utnyttjar Windows Adressbok (Webbautentiseringskoordinatorn). 

> [!IMPORTANT]
> Denna egenskap anges till true förutsätter att programutvecklaren har aktiverat integrerad Windows autentisering (IWA) i programmet. För detta:
> - I den ``Package.appxmanifest`` för UWP-appen i den **funktioner** fliken måste du aktivera följande funktioner:
>   - Enterprise-autentisering
>   - Privat nätverk (klient och Server)
>   - Delade användarcertifikat

IWA är inte aktiverad som standard eftersom program som begär Företagsautentisering eller delade användarcertifikat funktioner kräver en högre nivå av kontroll ska godkännas i Windows Store och inte alla utvecklare kanske vill utföra på högre nivå av kontroll. 

Den underliggande implementeringen på UWP-plattformen (Windows Adressbok) fungerar inte korrekt i företagssituationer där villkorlig åtkomst har aktiverats. Problemet är att användaren försöker logga in med Windows hello och planerar att välja ett certifikat, men certifikatet för PIN-koden inte finns eller om användaren väljer den, men aldrig ska uppmanas att PIN-koden. En lösning är att använda en alternativ metod (användarnamn/lösenord + phone autentisering), men upplevelsen är inte bra. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande exempel:

Exempel | Plattform | Beskrivning 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Ett Universal Windows Platform klientprogram som med hjälp av msal.net, åtkomst till Microsoft Graph för att autentisera en användare med Azure AD v2.0-slutpunkten. <br>![Topologi](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera MSA och Azure AD via AAD v2.0-slutpunkten och få åtkomst till Microsoft Graph med resulterande token. <br>![Topologi](media/msal-net-uwp-considerations/topology-xamarin-native.png)|