---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: e99d4f6676795a846b48ccd2a08856f4d32f269f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205645"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur en intern Windows Desktop .NET (XAML)-programmet kan hämta en åtkomst-token och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från en Azure Active Directory v2-slutpunkt.

När du har slutfört guiden för kommer ditt program att kunna anropa en skyddad API som använder personliga konton (inklusive outlook.com och live.com). Programmet kommer också använda arbets- och skolkonton från alla företag eller organisation som använder Azure Active Directory.  

> [!NOTE] 
> Guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Inte har något av dessa versioner? [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar sample-appen som genererats av den här guiden

![Hur den här guiden fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Exempelprogram som du skapar med den här guiden kan ett program för Windows-skrivbordet som frågar Microsoft Graph API eller ett webb-API som accepterar token från en Azure Active Directory v2-slutpunkt. Det här scenariot kan du lägga till en token på HTTP-förfrågningar via Authorization-huvud. Bibliotek för Microsoft-autentisering (MSAL) hanterar token förvärv och förnyelse.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantera token förvärv för att komma åt skyddade webb-API: er

När användaren har autentiserats får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Azure Active Directory v2.

API: er som Microsoft Graph kräver ett token för att tillåta åtkomst till specifika resurser. Till exempel krävs en token för att läsa en användares profil, åtkomst till en användares kalendrar och skicka e-post. Programmet kan begära en åtkomst-token med hjälp av MSAL åtkomst till dessa resurser genom att ange API-scope. Åtkomsttoken läggs sedan till http-Authorization-huvud för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet behöver.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

