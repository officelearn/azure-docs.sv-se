---
title: Microsoft Identity Platform Android-snabb start | Azure
description: Lär dig hur Android-program kan anropa ett API som kräver åtkomsttoken av Microsoft Identity Platform-slutpunkten.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c22d93d39f086aaa6e2f103d3becbe9376b49b0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324551"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en Android-app

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur ett Android-program kan logga in personliga konton eller arbets- och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API.

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Krav**
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
> För att registrera din app
> 1. Gå till fönstret ny [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://aka.ms/MobileAppReg) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AndroidQuickstart`.
>      - Du kan hoppa över andra konfigurationer på den här sidan. 
>      - Tryck på `Register` knappen.
> 1. Klicka på den nya appen > gå till `Authentication`.  >  `Add Platform`  >  `Android`    
>      - Ange paket namnet från ditt Android Studio-projekt. 
>      - Generera en signatur-hash. Mer information hittar du i portalen.
> 1. Välj `Configure` och spara ***MSAL*** -konfigurations-JSON för senare. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med auth Broker. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-android/green-check.png) appen konfigureras med de här attributen

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

* [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet

> [!div renderon="docs"]
> Om du valde alternativ 1 ovan kan du hoppa över de här stegen. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. I **app** > **src** main res RAW, öppna auth_config. JSON. >  >  > 
> 1. Redigera **auth_config. JSON** och Ersätt det med json från Azure Portal. Om du i stället vill göra ändringarna manuellt:
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
>    ```
> 
> 1. I **program** > **manifest**öppnar du **AndroidManifest. XML**.
> 1. Klistra in följande aktivitet till **manifest\application** -noden: 
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
> 1. Kör appen! 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Den här snabb starten stöder Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Extrahera och öppna projektet i Android Studio.
> 1. Öppna  >  **auth_config. JSON**i appens**res** > **RAW**.
> 1. Redigera **auth_config. JSON** och Ersätt det med json från Azure Portal. Om du i stället vill göra följande ändringar manuellt:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. I **program** > **manifest**öppnar du **AndroidManifest. XML**.
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
> 1. Ersätt `Enter_the_Package_Name` och`Enter_the_Signature_Hash` med de värden som du registrerade i Azure Portal. 
> 1. Kör appen! 

## <a name="more-information"></a>Mer information

Läs följande avsnitt för att få mer information om den här snabbstarten.

### <a name="getting-msal"></a>Hämtar MSAL

MSAL ([com. Microsoft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan använda Gradle 3.0 + för att installera det genom att lägga till följande i **Gradle-skript** > **build. Gradle (modul: app)** under **beroenden**:

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
> |`R.raw.auth_config` | Den här filen innehåller konfigurationerna för ditt program, inklusive din app/klient-ID, inloggnings mål, omdirigerings-URI och flera andra anpassnings alternativ. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Få en token interaktivt

Vissa situationer kräver att användare interagerar med Microsoft Identity Platform. I dessa fall kan slutanvändaren behöva välja sitt konto, ange sina autentiseringsuppgifter eller medgivande till de behörigheter som din app har begärt. Exempel: 

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösen ord måste de ange sina autentiseringsuppgifter 
* Om medgivande har återkallats 
* Om din app uttryckligen kräver medgivande. 
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Där:||
> |---------|---------|
> | `SCOPES` | Innehåller de omfång som begärs (det vill säga `{ "user.read" }` för Microsoft Graph eller `{ "<Application ID URL>/scope" }` för anpassade webb-API:er (dvs. `api://<Application ID>/access_as_user`)) |
> | `getAuthInteractiveCallback` | Motringning som körs när kontroll ges tillbaka till programmet efter autentisering |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Hämta en token obevakat

Appar bör inte kräva att användarna loggar in varje gång de begär en token. Om användaren redan har loggat in, tillåter den här metoden appar att begära token tyst.

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

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)