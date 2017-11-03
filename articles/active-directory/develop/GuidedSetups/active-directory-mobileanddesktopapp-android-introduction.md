---
title: "Azure AD v2 Android komma igång - introduktion | Microsoft Docs"
description: "Hur en Android-app kan få en åtkomst-token och anropa API: erna som kräver åtkomst-token från Azure Active Directory v2 slutpunkten eller Microsoft Graph API"
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
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Anropa Microsoft Graph API från en Android-app

Den här guiden visar hur en ursprunglig Android-program kan få en åtkomst-token och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från Azure Active Directory v2 slutpunkten.

I slutet av den här guiden kommer programmet att kunna anropa en skyddad API med hjälp av personliga konton (inklusive outlook.com och live.com) samt arbets- och skolkonton från alla företag eller organisation som har Azure Active Directory.  

### <a name="how-this-sample-works"></a>Hur det här exemplet fungerar
![Hur det här exemplet fungerar](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

Exempel som skapats av den här guiden bygger på ett scenario där en Android-program används för att fråga en webb-API som accepterar token från Azure Active Directory v2 slutpunkten – i det här fallet Microsoft Graph API. I det här scenariot läggs en token på HTTP-förfrågningar via Authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

### <a name="pre-requisites"></a>Förutsättningar
* Den interaktiva installationen fokuserar på Android Studio, men några andra utvecklingsmiljö för Android-program är också tillåtet. 
* Android SDK 21 eller senare krävs (SDK 25 rekommenderas).
* Google Chrome eller en webbläsare med hjälp av anpassade flikar krävs för den här versionen av Microsoft Authentication Library (MSAL) för Android.

> Obs: Google Chrome ingår inte i Visual Studio-emulatorn för Android. Vi rekommenderar att du kan testa den här koden på en Emulator med API 25 eller en bild med API 21 eller senare som har med Google Chrome installerat.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Hantera token förvärv för att komma åt skyddade webb-API

När användaren autentiseras får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsoft Azure Active Directory v2.

API: er som Microsoft Graph kräver en åtkomst-token för att tillåta åtkomst till specifika resurser – till exempel att läsa en användares profil, åtkomst till användarens kalender eller skicka ett e-postmeddelande. Programmet kan begära en åtkomst-token med MSAL åtkomst till dessa resurser genom att ange API-scope. Åtkomsttoken läggs sedan till http-Authorization-huvud för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet inte behöver.

### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[com.microsoft.Identity.Client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
