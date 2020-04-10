---
title: Microsoft identitetsplattform Android snabbstart | Azure
description: Lär dig hur Android-program kan anropa ett API som kräver åtkomsttoken av Slutpunkten för Microsoft identity platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 9afb5b7602b220c25d919f8fe0773d5cfa143d89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991202"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en Android-app

Den här snabbstarten använder ett kodexempel för att visa hur ett Android-program kan logga in på personliga konton, arbets- eller skolkonton med hjälp av Microsofts identitetsplattform och sedan få en åtkomsttoken och anropa Microsoft Graph API. (Se [Hur exemplet fungerar](#how-the-sample-works) för en illustration.)

Program måste representeras av ett appobjekt i Azure Active Directory så att Microsoft-identitetsplattformen kan tillhandahålla token till ditt program.

> [!div renderon="docs"]
> Som en bekvämlighet levereras kodexemplet med en standard `redirect_uri` förkonfigurerad i `AndroidManifest.xml` filen så att du inte behöver registrera ditt eget appobjekt först. A `redirect_uri` baseras delvis på appens signeringsnyckel. Exempelprojektet är förkonfigurerat med en signeringsnyckel så att den angivna `redirect_uri` fungerar. Mer information om hur du registrerar ett appobjekt och integrerar det med ditt program finns [i inloggningsanvändarna och anropar Microsoft Graph från en](tutorial-v2-android.md) självstudiekurs för Android-appen.


> [!NOTE]
> **Krav**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen 
>  För att kodexemplet för den här snabbstarten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med Auth-mäklaren.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-android/green-check.png) appen konfigureras med de här attributen
>
> ### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet 
> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med Android Studio.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: Appen är konfigurerad och redo att köras
> Vi har konfigurerat ditt projekt med värden för appens egenskaper och det är redo att köras. 
> Exempelappen startar på skärmen **Ett kontoläge.** Ett standardomfattning, **user.read,** tillhandahålls som standard, som används när du läser dina egna profildata under Microsoft Graph API-anropet. URL:en för Microsoft Graph API-anrop tillhandahålls som standard. Du kan ändra båda dessa om du vill.
>
> ![MSAL-exempelapp som visar användning av ett och flera konton](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Använd appmenyn för att växla mellan enstaka och flera kontolägen.
>
> I ett enda kontoläge loggar du in med ett arbets- eller hemkonto:
>
> 1. Välj **Hämta diagramdata interaktivt** om du vill uppmana användaren om deras autentiseringsuppgifter. Du ser utdata från anropet till Microsoft Graph API längst ned på skärmen.
> 2. När du har loggat in väljer du **Hämta diagramdata tyst** för att ringa ett samtal till Microsoft Graph API utan att användaren uppmanas att ange autentiseringsuppgifter igen. Du ser utdata från anropet till Microsoft Graph API längst ned på skärmen.
>
> I flera kontoläge kan du upprepa samma steg.  Dessutom kan du ta bort det inloggade kontot, som också tar bort de cachelagrade token för det kontot.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Steg 1: Hämta exempelappen
>
> [Ladda ner koden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Steg 2: Kör exempelappen
>
> Välj din emulator, eller fysiska enhet, från Android Studios **tillgängliga enheter** rullgardinsmenyn och kör appen.
>
> Exempelappen startar på skärmen **Ett kontoläge.** Ett standardomfattning, **user.read,** tillhandahålls som standard, som används när du läser dina egna profildata under Microsoft Graph API-anropet. URL:en för Microsoft Graph API-anrop tillhandahålls som standard. Du kan ändra båda dessa om du vill.
>
> ![MSAL-exempelapp som visar användning av ett och flera konton](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Använd appmenyn för att växla mellan enstaka och flera kontolägen.
>
> I ett enda kontoläge loggar du in med ett arbets- eller hemkonto:
>
> 1. Välj **Hämta diagramdata interaktivt** om du vill uppmana användaren om deras autentiseringsuppgifter. Du ser utdata från anropet till Microsoft Graph API längst ned på skärmen.
> 2. När du har loggat in väljer du **Hämta diagramdata tyst** för att ringa ett samtal till Microsoft Graph API utan att användaren uppmanas att ange autentiseringsuppgifter igen. Du ser utdata från anropet till Microsoft Graph API längst ned på skärmen.
>
> I flera kontoläge kan du upprepa samma steg.  Dessutom kan du ta bort det inloggade kontot, som också tar bort de cachelagrade token för det kontot.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Skärmbild av exempelappen](media/quickstart-v2-android/android-intro.svg)


Koden är uppdelad i fragment som visar hur man skriver en enda och flera konton MSAL app. Kodfilerna är ordnade enligt följande:

| Fil  | Demonstrationer  |
|---------|---------|
| Huvudsakligaktivitet | Hanterar användargränssnittet |
| MSGraphRequestWrapper  | Anropar Microsoft Graph API med hjälp av token som tillhandahålls av MSAL |
| MultiplcountModeFragment  | Initierar ett program med flera konton, läser in ett användarkonto och får en token för att anropa Microsoft Graph API |
| SingleAccountModeFragment | Initierar ett program med ett konto, läser in ett användarkonto och får en token för att anropa Microsoft Graph API |
| res/auth_config_multiple_account.json  | Konfigurationsfilen för flera konton |
| res/auth_config_single_account.json  | Konfigurationsfilen för ett konto |
| Gradle Skript/build.grade (Modul:app) | MSAL-biblioteksberoenden läggs till här |

Vi kommer nu att titta på dessa filer mer i detalj och ropa ut MSAL-specifik kod i varje.

### <a name="adding-msal-to-the-app"></a>Lägga till MSAL i appen

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) är det bibliotek som används för att logga in användare och begära token som används för att komma åt ett API som skyddas av Microsofts identitetsplattform. Gradle 3.0+ installerar biblioteket när du lägger till följande i **Gradle Scripts** > **build.gradle (Modul: app)** under **Beroenden:**

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

Du kan se detta i exempelprojektet i build.gradle (Modul: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

Detta instruerar Gradle att ladda ner och bygga MSAL från maven central.

### <a name="msal-imports"></a>Import av msal

Den import som är relevant för `com.microsoft.identity.client.*`MSAL-biblioteket är .  Du ser till exempel `import com.microsoft.identity.client.PublicClientApplication;` vilket namnområde som `PublicClientApplication` är namnet på klassen, som representerar ditt offentliga klientprogram.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Den här filen visar hur du skapar en MSAL-app för ett konto och anropar ett Microsoft Graph-API.

Appar med ett enda konto används endast av en enskild användare.  Du kanske till exempel bara har ett konto som du loggar in på din mappningsapp med.

#### <a name="single-account-msal-initialization"></a>MSAL-initiering för ett konto

I `auth_config_single_account.json`, `onCreateView()`i skapas ett enda konto `PublicClientApplication` med `auth_config_single_account.json` hjälp av den konfigurationsinformation som lagras i filen.  Så här initierar du MSAL-biblioteket för användning i en MSAL-app med ett konto:

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

I `SingleAccountModeFragment.java`finns koden för att logga `initializeUI()`in `signInButton` en användare i , i klickhanteraren.

Anropa `signIn()` innan du försöker hämta token. `signIn()`fungerar som `acquireToken()` om kallas, vilket resulterar i en interaktiv uppmaning för användaren att logga in.

Att logga in på en användare är en asynkron åtgärd. En motringning skickas som anropar Microsoft Graph API och uppdaterar användargränssnittet när användaren loggar in:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Logga ut en användare

I `SingleAccountModeFragment.java`finns koden för att logga `initializeUI()`ut `signOutButton` en användare i , i klickhanteraren.  Att signera en användare är en asynkron åtgärd. Om du signerar användaren rensas även tokencachen för det kontot. En motringning skapas för att uppdatera användargränssnittet när användarkontot har loggat ut:

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

#### <a name="get-a-token-interactively-or-silently"></a>Skaffa en token interaktivt eller tyst

Om du vill visa minst antal uppmaningar till användaren får du vanligtvis en token tyst. Försök sedan att komma till token interaktivt om det finns ett fel. Första gången appen `signIn()`anropar fungerar den `acquireToken()`effektivt som ett anrop till , vilket kommer att uppmana användaren om autentiseringsuppgifter.

Vissa situationer där användaren kan uppmanas att välja sitt konto, ange sina autentiseringsuppgifter eller samtycka till de behörigheter som appen har begärt är:

* Första gången användaren loggar in på applikationen
* Om en användare återställer sitt lösenord måste han eller hon ange sina autentiseringsuppgifter
* Om samtycket återkallas
* Om din app uttryckligen kräver samtycke
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

Koden för att få en token interaktivt, det vill med `SingleAccountModeFragment.java`användargränssnittet som involverar användaren, finns i , i `initializeUI()`, i `callGraphApiInteractiveButton` klickhanteraren:

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

Om användaren redan har `acquireTokenSilentAsync()` loggat in tillåter appar att `initializeUI()`begära token `callGraphApiSilentButton` tyst som visas i , i klickhanteraren:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Ladda ett konto

Koden för att läsa `SingleAccountModeFragment.java` in `loadAccount()`ett konto finns i .  Att läsa in användarens konto är en asynkron åtgärd, så motringningar som ska hanteras när kontot läses in, ändras eller ett fel inträffar skickas till MSAL.  Följande kod hanterar `onAccountChanged()`också , som inträffar när ett konto tas bort, användaren ändras till ett annat konto och så vidare.

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

#### <a name="call-microsoft-graph"></a>Ring Microsoft Graph

När en användare är inloggad görs anropet till Microsoft `callGraphAPI()` Graph via `SingleAccountModeFragment.java`en HTTP-begäran som definieras i . Den här funktionen är ett omslag som förenklar exemplet genom att `authenticationResult` göra vissa uppgifter, till exempel hämta åtkomsttoken från och paketera anropet till MSGraphRequestWrapper och visa resultatet av anropet.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Det här är konfigurationsfilen för en MSAL-app som använder ett enda konto.

Se [Förstå konfigurationsfilen](msal-configuration.md) för Android MSAL för en förklaring av dessa fält.

Observera förekomsten `"account_mode" : "SINGLE"`av , som konfigurerar den här appen för att använda ett enda konto.

`"client_id"`är förkonfigurerad för att använda en registrering av appobjekt som Microsoft underhåller.
`"redirect_uri"`är förkonfigurerad för att använda signeringsnyckeln som medföljer kodexemplet.

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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Den här filen visar hur du skapar en MSAL-app för flera konton och anropar ett Microsoft Graph-API.

Ett exempel på en app för flera konton är en e-postapp som gör att du kan arbeta med flera användarkonton, till exempel ett arbetskonto och ett personligt konto.

#### <a name="multiple-account-msal-initialization"></a>MSAL-initiering för flera konton

I `MultipleAccountModeFragment.java` filen i `onCreateView()`skapas ett appobjekt för flera konton (`IMultipleAccountPublicClientApplication`) `auth_config_multiple_account.json file`med hjälp av den konfigurationsinformation som lagras i :

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

Det `MultipleAccountPublicClientApplication` skapade objektet lagras i en klassmedlemsvariabel så att det kan användas för att interagera med MSAL-biblioteket för att hämta token och läsa in och ta bort användarkontot.

#### <a name="load-an-account"></a>Ladda ett konto

Flera kontoappar `getAccounts()` ringer vanligtvis för att välja det konto som ska användas för MSAL-åtgärder. Koden för att läsa in `MultipleAccountModeFragment.java` ett `loadAccounts()`konto finns i filen i .  Att läsa in användarens konto är en asynkron åtgärd. Så en motringning hanterar de situationer när kontot läses in, ändras eller ett fel uppstår.

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

#### <a name="get-a-token-interactively-or-silently"></a>Skaffa en token interaktivt eller tyst

Vissa situationer där användaren kan uppmanas att välja sitt konto, ange sina autentiseringsuppgifter eller samtycka till de behörigheter som appen har begärt är:

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösenord måste han eller hon ange sina autentiseringsuppgifter 
* Om samtycket återkallas 
* Om din app uttryckligen kräver samtycke 
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

Flera kontoappar bör vanligtvis hämta token interaktivt, det vill säga med `acquireToken()`användargränssnittet som involverar användaren, med ett anrop till .  Koden för att få en token `MultipleAccountModeFragment.java` interaktivt finns i filen i `initializeUI()`, `callGraphApiInteractiveButton` i klickhanteraren:

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

Appar bör inte kräva att användaren loggar in varje gång de begär en token. Om användaren redan har `acquireTokenSilentAsync()` loggat in tillåter appar att begära token `MultipleAccountModeFragment.java` utan att`initializeUI()` användaren, som visas i filen, uppmanas att använda dem i `callGraphApiSilentButton` klickhanteraren:

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

Koden för att ta bort ett konto och alla cachelagrade token för kontot finns i `MultipleAccountModeFragment.java` filen i `initializeUI()` hanteraren för knappen Ta bort konto. Innan du kan ta bort ett konto behöver du ett kontoobjekt som du hämtar från MSAL-metoder som `getAccounts()` och `acquireToken()`. Eftersom det är en asynkron `onRemoved` åtgärd att ta bort ett konto, tillhandahålls motringningen för att uppdatera användargränssnittet.

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

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Det här är konfigurationsfilen för en MSAL-app som använder flera konton.

Se [Förstå konfigurationsfilen](msal-configuration.md) för Android MSAL för en förklaring av de olika fälten.

Till skillnad från konfigurationsfilen [auth_config_single_account.json](#auth_config_single_accountjson) har `"account_mode" : "MULTIPLE"` den `"account_mode" : "SINGLE"` här konfigurationsfilen i stället för eftersom det här är en multipla kontoapp.

`"client_id"`är förkonfigurerad för att använda en registrering av appobjekt som Microsoft underhåller.
`"redirect_uri"`är förkonfigurerad för att använda signeringsnyckeln som medföljer kodexemplet.

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

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig stegen för hur du skapar det program som används i den här snabbstarten

Prova [inloggningsanvändarna och anropa Microsoft Graph från en Android-apphandledning](tutorial-v2-android.md) för en steg-för-steg-guide för att skapa en Android-app som får en åtkomsttoken och använder den för att anropa Microsoft Graph API.

> [!div class="nextstepaction"]
> [Självstudie: Anropa Graph API för Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki: MSAL för Android-bibliotek

Läs mer om MSAL–biblioteket för Android:

> [!div class="nextstepaction"]
> [Wiki: MSAL för Android-bibliotek](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
