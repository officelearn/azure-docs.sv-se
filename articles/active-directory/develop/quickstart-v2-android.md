---
title: Microsoft identity-plattformen Android Snabbstart | Azure
description: Lär dig hur Android-program kan anropa ett API som kräver åtkomst-token av Microsoft identity-plattformen slutpunkten.
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
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45252cc4d45e96c2bde4a4600630ea578a8d3009
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946727"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en Android-app

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur ett Android-program kan logga in personliga konton eller arbets- och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API.

![Visar hur exempelapp som genererats av den här snabbstarten fungerar](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Förutsättningar**
> * Android Studio 
> * Android 16 + krävs 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Att registrera din app
> 1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) fönstret.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://aka.ms/MobileAppReg) sidan.
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AndroidQuickstart`.
>      - Du kan hoppa över andra konfigurationer på den här sidan. 
>      - Tryck på den `Register` knappen.
> 1. Klicka på den nya appen > Gå till `Authentication`  >  `Add Platform`  >  `Android`.    
>      - Ange paketets namn från Android studio-projekt. 
>      - Generera en signatur-Hash. Referera till portalen för instruktioner.
> 1. Välj `Configure` och spara den ***MSAL Configuration*** JSON till senare. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> Du måste lägga till en omdirigerings-URI som är kompatibel med Auth Service broker för kodexempel för den här snabbstarten ska fungera. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-android/green-check.png) appen konfigureras med de här attributen

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

* [Ladda ned kodexempel](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet

> [!div renderon="docs"]
> Om du har valt alternativ 1 ovan kan du hoppa över de här stegen. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. Inuti **app** > **src** > **huvudsakliga** > **res**  >   **rå**öppnar **auth_config.json**.
> 1. Redigera **auth_config.json** och Ersätt den med JSON från Azure-portalen. Om du i stället vill göra ändringarna manuellt:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
> 1. Inside **app** > **manifests**, open  **AndroidManifest.xml**.
> 1. Paste the following activity to the **manifest\application** node: 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> > 1. Kör appen! 

> [!div renderon="docs"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. Inuti **app** > **res** > **raw**öppnar **auth_config.json**.
> 1. Redigera **auth_config.json** och Ersätt den med JSON från Azure-portalen. Om du istället vill göra dessa ändringar manuellt:
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
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Ersätt `Enter_the_Package_Name` och `Enter_the_Signature_Hash` med de värden som du registrerade i Azure-portalen. 
> 1. Kör appen! 

## <a name="more-information"></a>Mer information

Läs följande avsnitt för att få mer information om den här snabbstarten.

### <a name="getting-msal"></a>Hämta MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) är i biblioteket som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft identity-plattformen. Du kan använda Gradle 3.0 + för att installera det genom att lägga till följande i **Gradle-skripten** > **build.gradle (modul: app)** under **beroenden**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
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
> |`R.raw.auth_config` | Den här filen innehåller konfigurationer för ditt program, inklusive App/klient-ID, logga in målgrupp, omdirigerings-URI och flera andra anpassningsalternativ. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Få en token interaktivt

Vissa situationer kräver att användare interagerar med Microsoft identity-plattformen. I dessa fall kan kan slutanvändaren krävas för att välja sitt konto, ange sina autentiseringsuppgifter eller godkänna de behörigheter som har begärt att din app. Exempel: 

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösenord, uppmanas användaren att ange sina autentiseringsuppgifter 
* Medgivande har återkallats 
* Om din app kräver uttryckligen medgivande. 
* När ditt program begär åtkomst till en resurs för första gången
* När MFA- eller andra principer för villkorlig åtkomst krävs

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Där:||
> |---------|---------|
> | `SCOPES` | Innehåller de omfång som begärs (det vill säga `{ "user.read" }` för Microsoft Graph eller `{ "<Application ID URL>/scope" }` för anpassade webb-API:er (dvs. `api://<Application ID>/access_as_user`)) |
> | `getAuthInteractiveCallback` | Motringning som körs när kontroll ges tillbaka till programmet efter autentisering |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Hämta en token obevakat

Appar bör inte kräver att användarna ska logga in varje gång de begär en token. Om användaren har redan loggat in, kan den här metoden appar att begära token tyst.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Där:||
> |---------|---------|
> | `SCOPES` | Innehåller de omfång som begärs (det vill säga `{ "user.read" }` för Microsoft Graph eller `{ "<Application ID URL>/scope" }` för anpassade webb-API:er (dvs. `api://<Application ID>/access_as_user`)) |
> | `getAccounts(...)` | Innehåller det konto som du försöker hämta token för tyst |
> | `getAuthSilentCallback()` | Motringning som körs när kontroll ges tillbaka till programmet efter autentisering |

## <a name="next-steps"></a>Nästa steg

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig stegen för hur du skapar det program som används i den här snabbstarten

Prova att använda Android-självstudien för en komplett stegvis guide till att skapa appar och nya funktioner, bland annat en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [Självstudie: Anropa Graph API för Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki: MSAL för Android-bibliotek

Läs mer om MSAL–biblioteket för Android:

> [!div class="nextstepaction"]
> [Wiki: MSAL för Android-bibliotek](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
