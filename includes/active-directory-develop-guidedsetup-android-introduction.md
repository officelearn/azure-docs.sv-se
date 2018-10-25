---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 9bc8f30d2bbf6a084ad680306da9b1e330d488e3
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988367"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Logga in användare och anropa Microsoft Graph från en Android-app

I de här självstudierna lär du dig att skapa ett Android-program och integrerar dem i Microsoft identity-plattformen. Mer specifikt ska den här appen logga in en användare få en åtkomsttoken att anropa Microsoft Graph API och gör en grundläggande begäran för Microsoft Graph API.  

När du har slutfört guiden för ditt program ska ta emot inloggningar för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbete eller skola konton från alla företag eller organisation som använder Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Hur det här exemplet fungerar](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

Appen i det här exemplet ska logga in användare och hämta data å deras vägnar.  Dessa data kan nås via en fjärr-API (Microsoft Graph API i det här fallet) som kräver auktorisering och också skyddas av Microsoft identity-plattformen.

Mer specifikt:

* Din app nu öppnas en webbsida att logga in användaren.
* Din app kommer att utfärdas en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API.
* Process för Microsoft Graph-svaret.

Det här exemplet använder Microsoft Authentication library för Android (MSAL) för att samordna och hjälper till med autentisering. MSAL kommer automatiskt förnya token, leverera enkel inloggning mellan andra appar på enheten, hjälper dig att hantera konton och hantera villkorlig åtkomst oftast.

## <a name="prerequisites"></a>Förutsättningar

* Den här guidade konfigurationen använder Android Studio 3.0.
* Android 21 eller senare krävs (25 + rekommenderas).
* Google Chrome eller en webbläsare som använder anpassade flikar krävs för den här versionen av MSAL för Android.

## <a name="library"></a>Bibliotek

Den här guiden används av autentiseringsbibliotek för följande:

|Bibliotek|Beskrivning|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
