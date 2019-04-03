---
title: 'Snabbstart: Azure AD v2 Android | Microsoft Docs'
description: Lär dig hur Android-program kan anropa ett API som kräver åtkomsttoken genom Azure Active Directory v2.0-slutpunkten
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd78e6acd801f3b973cc45609b72f86b257f4d43
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862768"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en Android-app

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur ett Android-program kan logga in personliga konton eller arbets- och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API.

![Visar hur exempelapp som genererats av den här snabbstarten fungerar](media/quickstart-v2-android/android-intro-updated.png)

> [!NOTE]
> **Förutsättningar**
> * Android Studio 3+
> * Android 21 + krävs 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Att registrera din app
> 1. Gå till [Azure-portalen – Programregistrering (förhandsversion)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)** > **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Android-Quickstart`.
>      - Tryck på den `Register` knappen.
> 1. Gå till `Authentication`  >  `Redirect URIs`  >  `Suggested Redirect URIs for public clients`, och välj den omdirigerings-URI i formatet **msal {AppId} :/ / auth**. Spara ändringen.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> För att kodexemplet för den här snabbstarten ska fungera behöver du lägga till en svars-URL som **msal{AppId}://auth** (där {AppId} är program-ID för din app).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen för mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerat](media/quickstart-v2-android/green-check.png) ditt program har konfigurerats med dessa attribut

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

* [Ladda ned Android Studio-projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet

> [!div renderon="docs"]
> Om du har valt alternativ 1 ovan kan du hoppa över de här stegen. Öppna projektet i Android Studio och kör appen. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. Inuti **app** > **res** > **raw**öppnar **auth_config.json**.
> 1. Redigera **auth_config.json** och Ersätt den `client_id` och `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Inuti **app** > **manifest**öppnar **AndroidManifest.xml**.
> 1. Lägg till att följande aktivitet i noden **manifest\application**. Den här koden kan Microsoft återanrop i din app:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. Inuti **app** > **res** > **raw**öppnar **auth_config.json**.
> 1. Redigera **auth_config.json** och Ersätt den `client_id` och `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Inuti **app** > **manifest**öppnar **AndroidManifest.xml**.
> 1. Lägg till att följande aktivitet i noden **manifest\application**. Det här kodavsnittet registrerar en **BrowserTabActivity** så att operativsystemet kan återuppta ditt program när autentiseringen är klar:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Ersätt * med *program-ID för ditt program. Om du behöver hitta *program-ID* går du till sidan *Översikt*.

## <a name="more-information"></a>Mer information

Läs följande avsnitt för att få mer information om den här snabbstarten.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) är det bibliotek som används för att logga in användare och begära token som används för åtkomst till ett API som skyddas av Microsoft Azure Active Directory (Azure AD). Du kan använda Gradle för att installera det genom att lägga till följande i **Gradle-skript** > **build.gradle (modul: app)** under **Beroenden**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```java
import com.microsoft.identity.client.*;
```

Initiera sedan MSAL med hjälp av följande kod:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Där: ||
> |---------|---------|
> |`R.raw.auth_config` | Den här filen innehåller konfigurationer för ditt program, inklusive App/klient-ID, logga in målgrupp och flera andra anpassningsalternativ. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder som används hämta token: `acquireToken` och `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver att användare tvingas interagera med Azure AD v2.0-slutpunkten, vilket resulterar i ett kontextbyte till systemets webbläsare för att antingen verifiera användarnas autentiseringsuppgifter eller för medgivande. Några exempel är:

* Första gången användaren loggar in på programmet
* När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
* När programmet begär åtkomst till en resurs som användaren behöver ge sitt medgivande för
* När tvåfaktorsautentisering krävs

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Där:||
> |---------|---------|
> | `SCOPES` | Innehåller de omfång som begärs (det vill säga `{ "user.read" }` för Microsoft Graph eller `{ "<Application ID URL>/scope" }` för anpassade webb-API:er (dvs. `api://<Application ID>/access_as_user`)) |
> | `getAuthInteractiveCallback` | Motringning som körs när kontroll ges tillbaka till programmet efter autentisering |

#### <a name="getting-a-user-token-silently"></a>Hämta en token obevakat

Du vill inte kräva att användarna verifierar sina autentiseringsuppgifter varje gång de behöver komma åt en resurs. I de flesta fall vill du ha hämtning och förnyelse av token utan någon användarinteraktion. Du kan använda metoden `AcquireTokenSilentAsync` för att hämta token för att komma åt skyddade resurser efter den inledande metoden `acquireToken`:

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Där:||
> |---------|---------|
> | `SCOPES` | Innehåller de omfång som begärs (det vill säga `{ "user.read" }` för Microsoft Graph eller `{ "<Application ID URL>/scope" }` för anpassade webb-API:er (dvs. `api://<Application ID>/access_as_user`)) |
> | `accounts.get(0)` | Innehåller det konto som du försöker hämta token för tyst |
> | `getAuthInteractiveCallback` | Motringning som körs när kontroll ges tillbaka till programmet efter autentisering |

## <a name="next-steps"></a>Nästa steg

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig stegen för hur du skapar det program som används i den här snabbstarten

Prova att använda Android-självstudien för en komplett stegvis guide till att skapa appar och nya funktioner, bland annat en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [Anropa Graph API Android självstudien](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki: MSAL för Android-bibliotek

Läs mer om MSAL–biblioteket för Android:

> [!div class="nextstepaction"]
> [Wiki: MSAL för Android-bibliotek](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
