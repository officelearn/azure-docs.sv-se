---
title: "Azure AD v2 Windows Desktop komma igång - introduktion | Microsoft Docs"
description: "Hur Windows Desktop .NET (XAML) program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur en intern Windows Desktop .NET (XAML)-programmet kan få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från Azure Active Directory v2-slutpunkten.

I slutet av den här guiden kommer programmet att kunna anropa en skyddad API med hjälp av personliga konton (inklusive outlook.com och live.com) samt arbets- och skolkonton från alla företag eller organisation som har Azure Active Directory.  

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har det? [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

Det exempelprogram som skapats av den här guiden kan ett Windows-skrivbordet program frågar Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. I det här scenariot läggs en token på HTTP-förfrågningar via Authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantera token förvärv för att komma åt skyddade webb-API: er

När användaren autentiseras får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsoft Azure Active Directory v2.

API: er som Microsoft Graph kräver en åtkomst-token för att tillåta åtkomst till specifika resurser – till exempel att läsa en användares profil, åtkomst till användarens kalender eller skicka ett e-postmeddelande. Programmet kan begära en åtkomst-token med MSAL åtkomst till dessa resurser genom att ange API-scope. Åtkomsttoken läggs sedan till http-Authorization-huvud för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet inte behöver.


### <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

