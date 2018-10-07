---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9f25734259ce34caa0f0d7e844fc985f953b2795
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843301"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur ett internt Windows Desktop .NET (XAML)-program kan få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomsttoken från en Azure Active Directory v2-slutpunkt.

När du har slutfört guiden för kommer ditt program att kunna anropa ett skyddat API som använder personliga konton (inklusive outlook.com, live.com och andra). Programmet kommer också använda arbets- och skolkonton från alla företag eller organisation som använder Azure Active Directory.  

> [!NOTE] 
> Guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Inte har något av dessa versioner? [Ladda ned Visual Studio 2017 kostnadsfritt](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Så här fungerar den här guiden](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Exempelprogrammet som du skapar med den här guiden gör det möjligt för en Windows Desktop-program som frågar Microsoft Graph API eller ett webb-API som accepterar token från en Azure Active Directory v2-slutpunkt. Det här scenariot kan du lägga till en token på HTTP-förfrågningar via auktoriseringsrubriken. Microsoft Authentication Library (MSAL) hanterar tokenförvärv och förnyelse.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantering av tokenförvärv för att komma åt skyddade webb-API: er

När användaren har autentiserats får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Azure Active Directory v2.

API: er som Microsoft Graph kräver en token för att tillåta åtkomst till specifika resurser. Till exempel krävs en token för att läsa en användares profil, få åtkomst till en användares kalender eller skicka e-post. Ditt program kan begära en åtkomst-token med MSAL för att komma åt resurserna genom att ange API-omfång. Den här åtkomsttoken läggs sedan till HTTP-auktoriseringsrubriken för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken för dig, så att programmet inte behöver.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

