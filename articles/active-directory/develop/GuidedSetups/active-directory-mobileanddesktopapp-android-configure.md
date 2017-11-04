---
title: "Azure AD v2 Android komma igång - konfigurera | Microsoft Docs"
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
ms.openlocfilehash: 945b09ccdb7537987da33d32d94a3ccacd829ffd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>Skapa ett program (snabb)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att hämta program-ID och klistra in den i din kod

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Lägga till registreringsinformationen program i lösningen (Avancerat)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3. Kontrollera att alternativet för interaktiv installation är markerat
4. Klicka på `Add Platform`och välj `Native Application` och tryck på Spara
5.  Öppna `MainActivity` (under `app`  >  `java`  >   *`{host}.{namespace}`* )
6.  Ersätt den *[Ange program-Id här]* i rad som börjar med `final static String CLIENT_ID` med program-ID som du just har registrerat:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Öppna `AndroidManifest.xml` (under `app`  >  `manifests`) lägger du till att följande aktiviteter `manifest\application` nod. Detta registrerar en `BrowserTabActivity` så att operativsystem och återuppta programmet när autentiseringen har slutförts:
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        
        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
I den `BrowserTabActivity`, Ersätt `[Enter the application Id here]` med program-ID.
</li>
</ol>
