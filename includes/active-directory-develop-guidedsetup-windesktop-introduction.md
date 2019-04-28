---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297745"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur en intern Windows Desktop .NET (XAML) program kan hämta en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som behöver komma åt tokens från en Microsoft identity-plattformen för utvecklare (tidigare kallad Azure AD) v2.0-slutpunkten.

När du har slutfört guiden för kommer ditt program att kunna anropa ett skyddat API som använder personliga konton (inklusive outlook.com, live.com och andra). Programmet kommer också använda arbets- och skolkonton från alla företag eller organisation som använder Azure Active Directory.  

> [!NOTE]
> Guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017. Inte har något av dessa versioner? [Ladda ned Visual Studio 2017 kostnadsfritt](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Visar hur exempelappen genereras av den här självstudier fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Exempelprogrammet som du skapar med den här guiden gör det möjligt för en Windows Desktop-program som frågar Microsoft Graph API eller ett webb-API som accepterar token från en slutpunkt för Microsoft identity-plattformen. Det här scenariot kan du lägga till en token på HTTP-förfrågningar via auktoriseringsrubriken. Microsoft Authentication Library (MSAL) hanterar tokenförvärv och förnyelse.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantering av tokenförvärv för att komma åt skyddade webb-API: er

När användaren har autentiserats får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsoft identity-plattformen för utvecklare.

API: er som Microsoft Graph kräver en token för att tillåta åtkomst till specifika resurser. Till exempel krävs en token för att läsa en användares profil, få åtkomst till en användares kalender eller skicka e-post. Ditt program kan begära en åtkomst-token med MSAL för att komma åt resurserna genom att ange API-omfång. Den här åtkomsttoken läggs sedan till HTTP-auktoriseringsrubriken för varje anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdatera åtkomsttoken för dig, så att programmet inte behöver.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
