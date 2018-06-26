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
ms.openlocfilehash: 23b7ca44b72b8840579f369954f41f554d4c8852
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943427"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Logga in användare och anropa Microsoft Graph API från en Android-app

Den här guiden visar hur en ursprunglig Android-program kan få en åtkomst-token och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från Azure Active Directory v2-slutpunkten.

När du har slutfört guiden för kommer ditt program att kunna acceptera inloggningar för personliga (inklusive outlook.com, live.com och andra) och arbets- och skolkonton konton från alla företag eller organisation som använder Azure Active Directory. Programmet sedan anropar en API som skyddas av Azure Active Directory v2-slutpunkten.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar sample-appen som genererats av den här guiden
![Hur det här exemplet fungerar](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

Exempelprogram som du skapar med den här guiden är baserad på ett scenario där en Android-program används för att fråga en webb-API som accepterar token från Azure Active Directory v2 slutpunkten (Microsoft Graph API, i detta fall). I det här scenariot läggs ditt program anskaffats token till HTTP-förfrågningar via Authorization-huvud. Microsoft Authentication Library (MSAL) hanterar token förvärv och förnyelse för dig.

## <a name="prerequisites"></a>Förutsättningar
* Interaktiv installationen fokuserar på Android Studio, men några andra utvecklingsmiljö för Android-program är också tillåtet. 
* Android SDK 21 eller senare krävs (SDK 25 rekommenderas).
* Google Chrome eller en webbläsare som använder anpassade flikar krävs för den här versionen av MSAL för Android.

> [!NOTE]
> Google Chrome ingår inte i Visual Studio-emulatorn för Android. Vi rekommenderar att du testar den här koden på en Emulator med API 25 eller en bild med API 21 eller senare som har Google Chrome installerad.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantera token förvärv för att komma åt skyddade webb-API: er

När användaren har autentiserats får exempelprogrammet en åtkomst-token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Azure Active Directory v2.

API: er som Microsoft Graph kräver en åtkomst-token för att tillåta åtkomst till specifika resurser. Till exempel krävs en åtkomst-token för att läsa en användares profil, åtkomst till en användares kalendrar och skicka e-post. Programmet kan begära en åtkomst-token med hjälp av MSAL åtkomst till dessa resurser genom att ange API-scope. Åtkomsttoken läggs sedan till http-Authorization-huvud för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet behöver.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
