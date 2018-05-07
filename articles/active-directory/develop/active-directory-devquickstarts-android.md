---
title: Azure AD Android komma igång | Microsoft Docs
description: 'Hur du skapar ett Android-program som kan integreras med Azure AD för inloggnings- och Azure AD-anrop skyddade API: er med OAuth2.0.'
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 7af33005d5984dbf76a16a0daa92f5e1ebf85f9e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android komma igång
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Om du utvecklar ett Android-program, Microsoft att gör det lätt att logga in användare i Azure Active Directory (AD Azure). Azure AD gör att programmet kan användare komma åt data via Microsoft Graph eller egna skyddade webb-API.  

Android-biblioteket för Azure AD Authentication Library (ADAL) gör att din app kan börja använda den [Microsoft Azure Cloud](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) genom att stödja [ Microsoft Azure Active Directory-konton](https://azure.microsoft.com/services/active-directory/) med hjälp av branschens standard OAuth2 och OpenID Connect. Det här exemplet visar alla normala livscykler ditt program inte bör uppleva, inklusive:

* Hämta en token för Microsoft Graph
* Uppdatera en token
* Anropa Microsoft Graph
* Logga ut användaren

Du behöver en Azure AD-klient där du kan skapa användare och registrera ett program för att komma igång. Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scenario: Logga in användare och anropa Microsoft Graph

![topologi](./media/active-directory-android-topology.png)

Den här appen kan användas för alla Azure AD-konton. Den stöder både enkla och flera organisationens scenarier (beskrivs i steg). Den visar hur en utvecklare kan bygga appar för att ansluta till företaget och komma åt sina Azure + O365 data via Microsoft Graph. Användarna måste logga in under auth-flöde och medgivande till behörigheter för programmet och i vissa fall kan kräva att en administratör att samtycka till appen. Merparten av logiken i det här exemplet visar hur auth en slutanvändare och göra en grundläggande anrop till Microsoft Graph.

## <a name="example-code"></a>Exempelkod

Du kan hitta fullständig exempelkoden [på Github](https://github.com/Azure-Samples/active-directory-android). 

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

### <a name="register-and-configure-your-app"></a>Registrera och konfigurera din app 
Behöver du ha en native client-program som har registrerats med Microsoft genom att använda den [Azure-portalen](https://portal.azure.com). 

1. Hämtning av registreringen av appen
    - Navigera till [Azure-portalen](https://aad.portal.azure.com).  
    - Välj ***Azure Active Directory*** > ***App registreringar***. 

2. Skapa appen
    - Välj **Ny programregistrering**.  
    - Ange ett programnamn i den **namn** fältet. 
    - I **programtyp** Välj **interna**. 
    - I **omdirigerings-URI**, ange `http://localhost`.  

3. Konfigurera Microsoft Graph
    - Välj **Inställningar > nödvändiga behörigheter**.
    - Välj **Lägg till**, inuti **väljer en API** Välj ***Microsoft Graph***. 
    - Välj behörigheten **logga in och Läs användarprofil**, och tryck sedan på **Välj** att spara. 
        - Den här behörigheten mappar till den `User.Read` omfång. 
    - Valfritt: I **nödvändiga behörigheter > Windows Azure Active Directory**, ta bort den valda behörigheten **logga in och Läs användarprofil**. På så sätt undviker sidan medgivande lista behörigheten två gånger.   

4. Congrats! Appen har konfigurerats. I nästa avsnitt behöver du:
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

Du kan hitta all konfiguration för det här kodexemplet i den ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** fil.  

1. Ersätt konstanten `CLIENT_ID` med den `ApplicationID`.
2. Ersätt konstanten `REDIRECT URI` med den `Redirect URI` du tidigare har konfigurerat (`http://localhost`). 

### <a name="run-the-sample"></a>Kör exemplet

1. Välj **skapa > Rensa projekt**. 
2. Välj **kör > Kör app**. 
3. Appen ska skapa och visa vissa grundläggande UX. När du klickar på den `Call Graph API` knappen, det fråga efter en inloggning och sedan anropa tyst Microsoft Graph-API med den nya token.  

## <a name="important-info"></a>Viktig information

1. Checka ut den [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) för mer information om biblioteket säkerhetsnivån och hur du konfigurerar nya scenarier och funktioner. 
2. I scenarier med interna appen kommer att använda ett inbäddat webbvy och lämnar inte appen. Den `Redirect URI` kan vara skadlig. 
3. Hitta problem eller har förfrågningar? Du kan skapa ett problem eller ett inlägg i Stackoverflow med taggen `azure-active-directory`. 

### <a name="cross-app-sso"></a>Enkel inloggning mellan appar
Läs [aktivera enkel inloggning mellan appar på Android med hjälp av ADAL](active-directory-sso-android.md).  

### <a name="auth-telemetry"></a>Auth telemetri
ADAL-biblioteket visar auth telemetri för att förstå hur sina appar fungerar och skapa bättre upplevelse för utvecklare av program.  På så sätt kan du avbilda inloggning lyckades, aktiva användare och flera andra intressanta insikter. Med hjälp av auth telemetri kräver apputvecklare att upprätta en telemetri-tjänsten för att sammanställa och lagra händelser.

Läs mer om auth telemetri utcheckning [ADAL Android auth telemetri](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
