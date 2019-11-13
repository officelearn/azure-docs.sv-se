---
title: Microsoft Identity Platform Android-snabb start | Azure
description: Lär dig hur Android-program kan anropa ett API som kräver åtkomsttoken av Microsoft Identity Platform-slutpunkten.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9a194dcd2ccb353f11aa939200213f0db1c2ea
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73942945"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en Android-app

I den här snabb starten används ett kod exempel för att visa hur ett Android-program kan logga in på personliga konton, arbets-eller skol konton med hjälp av Microsoft Identity Platform och hämta en åtkomsttoken och anropa Microsoft Graph-API: et.

Program måste representeras av ett app-objekt i Azure Active Directory så att Microsoft Identity Platform kan dela tokens med ditt program.

> [!div renderon="docs"]
> Kod exemplet levereras med en standard `redirect_uri` förkonfigurerad i `AndroidManifest.xml`-filen så att du inte behöver registrera ditt eget app-objekt först. En `redirect_uri` baseras delvis på appens signerings nyckel. Exempelprojektet är förkonfigurerad med en signerings nyckel så att den tillhandahållna `redirect_uri` fungerar. Mer information om hur du registrerar ett app-objekt och integrerar det med ditt program finns i artikeln [Logga in användare och anropa Microsoft Graph från en Android-app](tutorial-v2-android.md) .

![Skärm bild av exempel appen](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Krav**
> * Android Studio 
> * Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen 
>  För att kod exemplet för den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-android/green-check.png) appen konfigureras med de här attributen
>
> ### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet 
> * [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet
> 1. Extrahera och öppna projektet i Android Studio.
> 2. Inuti **appen** > **src** > **main** > **res** > **RAW**, öppna **auth_config_multiple_account. JSON** och ersätt den med följande kod:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "MULTIPLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 3. Inuti **appen** > **src** > **main** > **res** > **RAW**, öppna **auth_config_single_account. JSON** och ersätt den med följande kod:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "SINGLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 4. Inuti **appen** > **src** > **main**öppnar du **AndroidManifest. XML**.
> 5. I noden **manifest\application** ersätter du noden **aktivitet Android: Name = "com. Microsoft. Identity. client. BrowserTabActivity"** med följande:    
> ```xml
> <!--Intent filter to catch Microsoft's callback after Sign In-->
> <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
>     <intent-filter>
>         <action android:name="android.intent.action.VIEW" />
>         <category android:name="android.intent.category.DEFAULT" />
>         <category android:name="android.intent.category.BROWSABLE" />
>         <!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading "/" is required for android:path
>         -->
>         <data 
>             android:host="Enter_the_Package_Name"
>             android:path="/Enter_the_Signature_Hash"
>             android:scheme= "msauth" />
>     </intent-filter>
> </activity>
> ```
> 6. Kör appen!   
> Exempel appen startar på skärmen med ett **enda konto läge** . En standard omfattning, **User. Read**, tillhandahålls som standard, som används när du läser dina egna profil data under Microsoft Graph API-anropet. URL: en för API-anropet Microsoft Graph anges som standard. Du kan ändra båda dessa om du vill.
>
> ![MSAL exempel app som visar användning av enkel och flera konton](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Använd app-menyn för att ändra mellan lägena för ett och flera konton.
>
> I läget enskilt konto loggar du in med ett arbets-eller hem konto:
>
> 1. Välj **Hämta diagram data interaktivt** för att uppmana användaren att ange sina autentiseringsuppgifter. Du ser utdata från anropet till Microsoft Graph-API längst ned på skärmen.
> 2. När du har loggat in väljer du **Hämta diagram data tyst** för att anropa Microsoft Graph API utan att fråga användaren om autentiseringsuppgifter igen. Du ser utdata från anropet till Microsoft Graph-API längst ned på skärmen.
>
> I läget för flera konton kan du upprepa samma steg.  Dessutom kan du ta bort det inloggade kontot, vilket även tar bort cachelagrade token för det kontot.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Den här snabb starten stöder Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Steg 1: Hämta exempel appen
>
> [Ladda ned koden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Steg 2: Kör exempel appen
>
> Välj emulatorn eller den fysiska enheten från Android Studio rutorna **tillgängliga enheter** och kör appen.
>
> Exempel appen startar på skärmen med ett **enda konto läge** . En standard omfattning, **User. Read**, tillhandahålls som standard, som används när du läser dina egna profil data under Microsoft Graph API-anropet. URL: en för API-anropet Microsoft Graph anges som standard. Du kan ändra båda dessa om du vill.
>
> ![MSAL exempel app som visar användning av enkel och flera konton](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Använd app-menyn för att ändra mellan lägena för ett och flera konton.
>
> I läget enskilt konto loggar du in med ett arbets-eller hem konto:
>
> 1. Välj **Hämta diagram data interaktivt** för att uppmana användaren att ange sina autentiseringsuppgifter. Du ser utdata från anropet till Microsoft Graph-API längst ned på skärmen.
> 2. När du har loggat in väljer du **Hämta diagram data tyst** för att anropa Microsoft Graph API utan att fråga användaren om autentiseringsuppgifter igen. Du ser utdata från anropet till Microsoft Graph-API längst ned på skärmen.
>
> I läget för flera konton kan du upprepa samma steg.  Dessutom kan du ta bort det inloggade kontot, vilket även tar bort cachelagrade token för det kontot.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

Koden är indelad i fragment som visar hur du skriver en enda och flera konton MSAL-appen. Filerna är organiserade enligt följande:

| Fil  | Visat  |
|---------|---------|
| MainActivity | Hanterar användar gränssnittet |
| MSGraphRequestWrapper  | Anropar Microsoft Graph-API med hjälp av token som tillhandahålls av MSAL |
| MultipleAccountModeFragment  | Initierar ett program med flera konton, läser in ett användar konto och hämtar en token för att anropa API: et för Microsoft Graph |
| SingleAccountModeFragment | Initierar ett program med en enda konto, läser in ett användar konto och hämtar en token för att anropa API: et för Microsoft Graph |
| Res/auth_config_multiple_account. JSON  | Konfigurations filen för flera konton |
| Res/auth_config_single_account. JSON  | Konfigurations filen för det enskilda kontot |
| Gradle-skript/bygge. betyg (modul: app) | MSAL biblioteks beroenden läggs till här |

Nu ska vi titta på de här filerna i detalj och anropa MSAL kod i varje.

### <a name="adding-msal-to-the-app"></a>Lägga till MSAL i appen

MSAL ([com. Microsoft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Gradle 3.0 + installerar biblioteket när du lägger till följande i **Gradle-skript** > **build. Gradle (modul: app)** under **beroenden**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Du kan se detta i exempel projektet i build. gradle (modul: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.+'
    ...
}
```

Detta gör att Gradle kan hämta och bygga MSAL från maven Central.

### <a name="msal-imports"></a>MSAL importer

De importer som är relevanta för MSAL-biblioteket är `com.microsoft.identity.client.*`.  Till exempel ser du `import com.microsoft.identity.client.PublicClientApplication;` som är namn området för klassen `PublicClientApplication` som representerar ditt offentliga klient program.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. java

Den här filen visar hur du skapar en MSAL-app för ett enda konto och anropar ett Microsoft Graph-API.

Appar för en enda konto används bara av en enskild användare.  Till exempel kanske du bara har ett konto som du loggar in på mappnings appen med.

#### <a name="single-account-msal-initialization"></a>MSAL initiering av enskilt konto

I `auth_config_single_account.json`skapas ett enda konto `PublicClientApplication` med hjälp av konfigurations informationen som lagras i `auth_config_single_account.json` filen i `onCreateView()`.  Så här initierar du MSAL-biblioteket för användning i en MSAL-app med ett enda konto:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Logga in en användare

I `SingleAccountModeFragment.java`är koden för att logga in en användare i `initializeUI()`, i `signInButton` Klicka på hanterare.

Anropa `signIn()` innan du försöker hämta tokens. `signIn()` fungerar som om `acquireToken()` anropas, vilket resulterar i en interaktiv prompt där användaren kan logga in.

Att logga in en användare är en asynkron åtgärd. Ett återanrop skickas som anropar Microsoft Graph API och uppdaterar användar gränssnittet när användaren loggar in:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Logga ut en användare

I `SingleAccountModeFragment.java`är koden för att logga ut en användare i `initializeUI()`, i `signOutButton` Klicka på hanterare.  Signering av en användare är en asynkron åtgärd. Vid signering av användaren rensas även token-cachen för det kontot. Ett motanrop skapas för att uppdatera användar gränssnittet när användar kontot har loggats ut:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Få en token interaktivt eller tyst

För att presentera det minsta antalet prompter för användaren får du vanligt vis en token i bakgrunden. Försök sedan att nå token interaktivt om ett fel uppstår. Första gången appen anropar `signIn()`fungerar det effektivt som ett anrop till `acquireToken()`, vilket kommer att uppmana användaren att ange autentiseringsuppgifter.

Vissa situationer när användaren kan uppmanas att välja sitt konto, ange sina autentiseringsuppgifter eller medgivande till de behörigheter som appen har begärt är:

* Första gången användaren loggar in i programmet
* Om en användare återställer sitt lösen ord måste de ange sina autentiseringsuppgifter
* Om medgivande har återkallats
* Om din app uttryckligen kräver medgivande
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

Koden för att hämta en token interaktivt, det vill säga användar gränssnittet som kommer att involvera användaren, finns i `SingleAccountModeFragment.java`, i `initializeUI()`, i `callGraphApiInteractiveButton` Klicka på hanterare:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Om användaren redan har loggat in tillåter `acquireTokenSilentAsync()` att appar begär tokens i bakgrunden som visas i `initializeUI()`, i `callGraphApiSilentButton` Klicka på hanterare:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Läs in ett konto

Den kod som används för att läsa in ett konto är i `SingleAccountModeFragment.java` i `loadAccount()`.  Inläsning av användarens konto är en asynkron åtgärd, så återanrop för att hantera när kontot läses in, ändringar eller ett fel uppstår skickas till MSAL.  Följande kod hanterar också `onAccountChanged()`, som inträffar när ett konto tas bort, användaren ändrar till ett annat konto och så vidare.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Anropa Microsoft Graph

När en användare är inloggad görs anropet till Microsoft Graph via en HTTP-begäran med `callGraphAPI()` som definieras i `SingleAccountModeFragment.java`. Den här funktionen är en omslutning som fören klar exemplet genom att utföra vissa uppgifter, till exempel hämta åtkomsttoken från `authenticationResult` och paketera anropet till MSGraphRequestWrapper och visa resultatet av anropet.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. JSON

Det här är konfigurations filen för en MSAL-app som använder ett enda konto.

Se [förstå konfigurations filen för Android-MSAL](msal-configuration.md) för en förklaring av de här fälten.

Observera förekomsten av `"account_mode" : "SINGLE"`, som konfigurerar den här appen att använda ett enda konto.

`"client_id"` är förkonfigurerat för att använda en app-programobjekts registrering som Microsoft underhåller.
`"redirect_uri"`är förkonfigurerad att använda den signerings nyckel som anges i kod exemplet.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. java

Den här filen visar hur du skapar ett MSAL-program för flera konton och anropar en Microsoft Graph-API.

Ett exempel på en app med flera konton är en e-postapp som gör att du kan arbeta med flera användar konton, till exempel ett arbets konto och ett personligt konto.

#### <a name="multiple-account-msal-initialization"></a>MSAL initiering av flera konton

I `MultipleAccountModeFragment.java`-filen i `onCreateView()`skapas ett program objekt för flera konton (`IMultipleAccountPublicClientApplication`) med hjälp av konfigurations informationen som lagras i `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Det skapade `MultipleAccountPublicClientApplication`-objektet lagras i en klass medlems variabel så att det kan användas för att interagera med MSAL-biblioteket för att hämta tokens och läsa in och ta bort användar kontot.

#### <a name="load-an-account"></a>Läs in ett konto

Flera konto appar anropar vanligt vis `getAccounts()` för att välja det konto som ska användas för MSAL-åtgärder. Koden för att läsa in ett konto finns i `MultipleAccountModeFragment.java`-filen i `loadAccounts()`.  Inläsning av användarens konto är en asynkron åtgärd. Det innebär att ett motanrop hanterar situationer när kontot läses in, ändringar eller fel uppstår.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Få en token interaktivt eller tyst

Vissa situationer när användaren kan uppmanas att välja sitt konto, ange sina autentiseringsuppgifter eller medgivande till de behörigheter som appen har begärt är:

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösen ord måste de ange sina autentiseringsuppgifter 
* Om medgivande har återkallats 
* Om din app uttryckligen kräver medgivande 
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

Flera konton bör vanligt vis hämta token interaktivt, vilket är med ett användar gränssnitt som omfattar användaren, med ett anrop till `acquireToken()`.  Koden för att få en token interaktivt finns i `MultipleAccountModeFragment.java`-filen i `initializeUI()`, i `callGraphApiInteractiveButton` Klicka på hanterare:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Appar bör inte kräva att användaren loggar in varje gång de begär en token. Om användaren redan har loggat in tillåter `acquireTokenSilentAsync()` appar att begära token utan att fråga användaren, som du ser i `MultipleAccountModeFragment.java`-filen i`initializeUI()` i `callGraphApiSilentButton` Klicka på hanterare:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Ta bort ett konto

Koden för att ta bort ett konto och alla cachelagrade tokens för kontot finns i `MultipleAccountModeFragment.java`-filen i `initializeUI()` i Hanteraren för knappen Ta bort konto. Innan du kan ta bort ett konto måste du ha ett konto objekt, som du får från MSAL-metoder som `getAccounts()` och `acquireToken()`. Eftersom borttagning av ett konto är en asynkron åtgärd anges `onRemoved` motringning för att uppdatera användar gränssnittet.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Det här är konfigurations filen för en MSAL-app som använder flera konton.

Se [förstå konfigurations filen för Android-MSAL](msal-configuration.md) för en förklaring av de olika fälten.

Till skillnad från konfigurations filen [auth_config_single_account. JSON](#auth_config_single_accountjson) , har den här konfigurations filen `"account_mode" : "MULTIPLE"` i stället för `"account_mode" : "SINGLE"` eftersom detta är en app med flera konton.

`"client_id"` är förkonfigurerat för att använda en app-programobjekts registrering som Microsoft underhåller.
`"redirect_uri"`är förkonfigurerad att använda den signerings nyckel som anges i kod exemplet.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig hur du skapar appen som används i den här snabbstarten

Testa [inloggnings användarna och anropa Microsoft Graph från en](tutorial-v2-android.md) själv studie kurs om Android-appar för en steg-för-steg-guide om hur du skapar en Android-app som hämtar en åtkomsttoken och använder den för att anropa Microsoft Graph API.

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
