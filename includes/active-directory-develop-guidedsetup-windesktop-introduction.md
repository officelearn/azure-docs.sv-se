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
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509928"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur ett internt program för Windows Desktop .NET (XAML) använder en åtkomsttoken för att anropa Microsoft Graph API. Appen kan också komma åt andra API: er som kräver åtkomsttoken från en Microsoft identity-plattformen för utvecklare v2.0-slutpunkten. Den här plattformen hette tidigare Azure AD.

När du har slutfört guiden för kommer ditt program att kunna anropa ett skyddat API som använder personliga konton (inklusive outlook.com, live.com och andra). Programmet kommer också använda arbets- och skolkonton från alla företag eller organisation som använder Azure Active Directory.  

> [!NOTE]
> Guiden kräver Visual Studio 2015 Update 3, Visual Studio 2017 eller Visual Studio 2019. Har inte något av dessa versioner? [Ladda ned Visual Studio 2019 kostnadsfritt](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Visar hur exempelapp som genererats av den här kursen fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Exempelprogrammet som du skapar med den här guiden gör det möjligt för en Windows Desktop-program som frågar Microsoft Graph API eller ett webb-API som accepterar token från en slutpunkt för Microsoft identity-plattformen. Det här scenariot kan du lägga till en token på HTTP-förfrågningar via auktoriseringsrubriken. Microsoft Authentication Library (MSAL) hanterar tokenförvärv och förnyelse.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantering av tokenförvärv för att komma åt skyddade webb-API: er

När användaren har autentiserats får exempelprogrammet en token som du kan använda för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsoft identity-plattformen för utvecklare.

API: er som Microsoft Graph kräver en token för att tillåta åtkomst till specifika resurser. Till exempel krävs en token för att läsa en användares profil, få åtkomst till en användares kalender eller skicka e-post. Ditt program kan begära en åtkomst-token med MSAL för att komma åt resurserna genom att ange API-omfång. Den här åtkomsttoken läggs sedan till HTTP-auktoriseringsrubriken för varje anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdatera åtkomsttoken för dig, så att programmet inte behöver.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
