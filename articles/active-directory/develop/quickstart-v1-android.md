---
title: Azure AD-Android komma igång | Microsoft Docs
description: 'Hur du skapar ett Android-program som kan integreras med Azure AD för inloggnings- och Azure AD-anrop skyddade API: er med OAuth2.0.'
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: b428549d7dc804371368d8c252b949ac2fae62c2
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506257"
---
# <a name="azure-ad-android-getting-started"></a>Azure AD-Android komma igång
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Om du utvecklar ett Android-program gör Microsoft det lättförståeliga filstrukturen för inloggning av Azure Active Directory (Azure AD)-användare. Azure AD gör dina program få åtkomst till användardata via Microsoft Graph eller dina egna skyddade webb-API. 

Android-biblioteket för Azure AD Authentication Library (ADAL) gör att din app kan börja använda den [Microsoft Azure-molnet](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) genom att stödja [ Microsoft Azure Active Directory-konton](https://azure.microsoft.com/services/active-directory/) med hjälp av branschens standard OAuth2 och OpenID Connect. Detta exempel visar de normala livscykler som ditt program inte bör uppleva, inklusive:

* Hämta en token för Microsoft Graph
* Uppdatera en token
* Anropa Microsoft Graph
* Logga ut användaren

För att komma igång behöver du en Azure AD-klient där du kan skapa användare och registrera ett program. Om du inte redan har en klient, [Lär dig hur du skaffa ett](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scenario: Logga in användare och anropa Microsoft Graph

![Topologi](./media/quickstart-v1-android/active-directory-android-topology.png)

Den här appen kan användas för alla Azure AD-konton. Den stöder både enkla och flera organisationens scenarier (beskrivs i steg). Den visar hur utvecklare kan skapa appar för att ansluta till företagsanvändare och få åtkomst till deras Azure + O365 data via Microsoft Graph. Under autentiseringsflöde, slutanvändare kommer att behöva logga in och medgivande till behörigheter för programmet och i vissa fall kan kräva att en administratör att godkänna appen. Merparten av logiken i det här exemplet visar hur till auth slutanvändarna och gör en grundläggande anrop till Microsoft Graph.

## <a name="example-code"></a>Exempelkod

Du hittar en fullständig exempelkoden [på Github](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Steg för att köra

### <a name="register-and-configure-your-app"></a>Registrera dig och konfigurera din app 
Du måste ha ett internt klientprogram som har registrerats med Microsoft med hjälp av den [Azure-portalen](https://portal.azure.com). 

1. Gå till appregistrering
    - Navigera till [Azure-portalen](https://aad.portal.azure.com). 
    - Välj ***Azure Active Directory*** > ***Appregistreringar***. 

2. Skapa appen
    - Välj **Ny programregistrering**. 
    - Ange ett namn i den **namn** fält. 
    - I **programtyp** Välj **interna**. 
    - I **omdirigerings-URI**, ange `http://localhost`. 

3. Konfigurera Microsoft Graph
    - Välj **Inställningar > behörigheter som krävs för**.
    - Välj **Lägg till**, inuti **Välj en API** Välj ***Microsoft Graph***. 
    - Välj behörigheten **logga in och läsa användarprofil**, tryck sedan på **Välj** att spara. 
        - Den här behörigheten mappar till den `User.Read` omfång. 
    - Valfritt: I **nödvändiga behörigheter > Windows Azure Active Directory**, ta bort den valda behörigheten **logga in och läsa användarprofil**. På så sätt undviker medgivande användarsidan lista behörigheten två gånger. 

4. Gratulerar! Din app har konfigurerats. I nästa avsnitt behöver du:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Hämta exempelkoden

1. Klona koden.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Öppna exemplet i Android Studio.
    - Välj **öppna ett befintligt Android Studio-projekt**.

### <a name="configure-your-code"></a>Konfigurera din kod

Du kan hitta konfigurationen för det här kodexemplet i den ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** fil. 

1. Ersätt Ständiga `CLIENT_ID` med den `ApplicationID`.
2. Ersätt Ständiga `REDIRECT URI` med den `Redirect URI` du konfigurerade tidigare (`http://localhost`). 

### <a name="run-the-sample"></a>Kör exemplet

1. Välj **skapa > Rensa projekt**. 
2. Välj **kör > Kör app**. 
3. Appen ska skapa och visa vissa grundläggande UX. När du klickar på den `Call Graph API` knapp, det efterfrågar en inloggning och sedan anropa Microsoft Graph API med den nya token tyst. 

## <a name="important-info"></a>Viktig information

1. Kolla in den [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) för mer information om biblioteket säkerhetsnivån och hur du konfigurerar nya scenarier och funktioner. 
2. I inbyggda appen kommer att använda en inbäddad Webview och kommer inte att lämna appen. Den `Redirect URI` kan vara skadlig. 
3. Hitta eventuella problem eller har förfrågningar? Du kan skapa ett ärende eller publicera på Stackoverflow med taggen `azure-active-directory`. 

### <a name="cross-app-sso"></a>Enkel inloggning mellan appar
Lär dig [så här aktiverar du enkel inloggning mellan appar på Android med hjälp av ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Auth-telemetri
ADAL-biblioteket visar auth telemetri för att hjälpa apputvecklare förstå hur deras appar fungerar och bygga bättre upplevelser. På så sätt kan du avbilda inloggning lyckades, aktiva användare och flera andra intressanta insikter. Med hjälp av auth telemetri kräver apputvecklare att upprätta en telemetritjänst för att sammanställa och lagra händelser.

Mer information om auth telemetri, Kolla in [ADAL Android auth telemetri](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
