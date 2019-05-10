---
title: Komma igång med Android – Microsoft identity-plattformen | Azure
description: 'Hur en Android-app kan få en åtkomsttoken och anropa Microsoft Graph API eller API: er som kräver åtkomsttoken från Microsoft identity-plattformen.'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2914d6b1f4a6c94d7e3d4456c8255c1563a71b3e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406643"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Logga in användare och anropa Microsoft Graph från en Android-app

I de här självstudierna lär du dig att integrera en Android-app i Microsoft identity-plattformen. Mer specifikt ska din app logga in en användare få en åtkomsttoken att anropa Microsoft Graph API och gör en begäran för Microsoft Graph API.  

När du har slutfört guiden för ditt program ska ta emot inloggningar för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbete eller skola konton från alla företag eller organisation som använder Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hur fungerar den här självstudien

![Visar hur exempelapp som genererats av den här kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Appen i det här exemplet ska logga in användare och hämta data å deras vägnar.  Dessa data kan användas med en skyddad API (Microsoft Graph API i det här fallet) som kräver auktorisering.

Mer specifikt:

* Din app ska logga in användaren antingen via en webbläsare eller Microsoft Authenticator och Intune-Företagsportalen.
* Användaren kommer att acceptera de behörigheter som programmet har begärt. 
* Din app kommer att utfärdas en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API.
* Process för Microsoft Graph-svaret.

Det här exemplet använder Microsoft Authentication library för Android (MSAL) för att implementera autentisering. MSAL kommer automatiskt förnya token, leverera enkel inloggning mellan andra appar på enheten och hantera konton.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den här guidade konfigurationen använder Android Studio.
* Android 16 eller senare krävs (19 + rekommenderas).

## <a name="library"></a>Bibliotek

Den här guiden används av autentiseringsbibliotek för följande:

|Bibliotek|Beskrivning|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>Konfigurera projektet

Den här självstudien skapas ett nytt projekt. Om du vill ladda ned den slutförda självstudien i stället [ladda ned koden](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Android Studio och välj antingen **starta ett nytt Android Studio-projekt**.
    - Om Android Studio redan är öppen väljer **filen** > **New** > **nytt projekt**.
2. Lämna **tom aktivitet** eftersom den är Välj **nästa**.
3. Namnge programmet genom att ange den `Minimum API level` till **API 19 eller nyare**, träffar **Slutför**.
5. I din `app/build.gradle`, ange den `targetedSdkVersion` till 27. 

## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera programmet i något av två sätt, enligt beskrivningen i följande två avsnitt.

### <a name="register-your-app"></a>Registrera din app

1. Gå till den [Azure-portalen](https://aka.ms/MobileAppReg) > Välj `New registration`. 
2. Ange en **namn** för din app > `Register`. **Ange inte en omdirigerings-URI i det här skedet**. 
3. I den `Manage` avsnittet, gå till `Authentication` > `Add a platform` > `Android`
    - Ange paketet projektnamn. Om du har hämtat kod som det här värdet är `com.azuresamples.msalandroidapp`. 
    - Ange din debug/utveckling signatur-hash. Använd kommandot KeyTool i portalen för att generera en signatur-Hash. 
4. Tryck på `Configure` och lagra den ***MSAL Configuration*** till senare. 

## <a name="build-your-app"></a>Bygg din app

### <a name="configure-your-android-app"></a>Konfigurera din Android-app

1. Högerklicka på **res** > **New** > **mappen** > **Raw mappen för resurser**
2. I **app** > **res** > **raw**, skapa en ny JSON-fil som heter `auth_config.json` och klistra in din ***MSAL Configuration***. Se [MSAL konfiguration för mer info](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. I **app** > **manifest** > **AndroidManifest.xml**, lägga till den `BrowserTabActivity` aktivitet nedan. Den här posten kan Microsoft att ringa tillbaka till programmet efter att autentiseringen är klar:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Observera att signatur-Hash som används inte får vara URL-kodas i den **AndroidManifest.xml**. 

4. I den **AndroidManifest.xml** och precis ovanför den `<application>` tagga, Lägg till följande behörigheter:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. I den `BrowserTabActivity`, ersätter den ***paketnamn*** och ***signatur Hash*** med de värden som registrerats i Azure-portalen.

### <a name="create-the-apps-ui"></a>Skapa appens användargränssnitt

1. Gå till **res** > **layout**, och öppna sedan **activity_main.xml**.
2. Ändra layouten aktivitet från `android.support.constraint.ConstraintLayout` eller andra till `LinearLayout`.
3. Lägg till den `android:orientation="vertical"` egenskap enligt den `LinearLayout` noden.
4. Klistra in följande kod till den `LinearLayout` nod, ersätter det aktuella innehållet:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet

1. I Android Studio väljer **Gradle-skripten** > **build.gradle (modul: app)**.
2. Under **beroenden**, klistra in följande kod:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Använd MSAL 

Nästa avsnitt kommer att göra ändringar i den `MainAcitivty.java`. Vi kommer att gå igenom varje steg som behövs för att lägga till och använda MSAL i din app.

#### <a name="required-imports"></a>Nödvändiga importer

Lägg till följande importer i projektet: 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>Instansiera MSAL 

I den `MainActivity` klass, måste du skapa en instans av MSAL tillsammans med några få konfigurationer om vilka appen kommer gör inklusive scope och webb-API som vi vill ha åtkomst till. 

Kopiera in följande variabler i den `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Nu för att skapa en instans av MSAL, kopiera följande kod i den `onCreate(...)` metoden:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Ovanstående kodblocket försöker logga in användare tyst när de öppnar ditt program via `getAccounts(...)` och, om detta lyckas `acquireTokenSilentAsync(...)`.  I nästa avsnitt kommer vi implementera återanrop hanteraren för om det finns några inloggade konton. 

#### <a name="use-msal-to-get-tokens"></a>Använd MSAL för att hämta token

Nu kan vi implementera appens användargränssnitt bearbetningslogiken och hämta token interaktivt via MSAL. 

MSAL visar två huvudsakliga sätt för att hämta token: `acquireTokenSilentAsync` och `acquireToken`.  

`acquireTokenSilentAsync` loggar in en användare och hämta token utan någon användarinteraktion om ett konto finns. Om det lyckas MSAL kommer handoff token till appen, om det inte går att generera en `MsalUiRequiredException`.  Om det här undantaget genereras eller om du vill att användarna har en interaktiv inloggning erfarenhet (autentiseringsuppgifter, mfa eller andra villkorlig åtkomst-principer kan eller inte krävas), kan du använda `acquireToken`.  

`acquireToken` alltid visa Användargränssnittet när du försöker logga in användaren och hämta token; dock kan den använda cookies i webbläsaren eller ett konto i Microsoft authenticator för att ge en interaktiv SSO-upplevelse. 

Börja genom att skapa följande tre Användargränssnittet metoder i den `MainActivity` klass:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Lägg sedan till en metod för att hämta den aktuella aktiviteten och bearbeta tyst & interaktiva återanrop:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Använder MSAL för utloggning

Därefter och vi lägger till stöd för utloggning i vår app. 

Det är viktigt att Observera att logga ut med MSAL tar bort alla kända information om en användare från det här programmet, men användaren fortfarande har en aktiv session på sin enhet. Om användaren misslyckas att logga in igen de kan se interaktion, men kanske inte behöver ange sina autentiseringsuppgifter på grund av enheten sessionen som aktiv igen. 

Om du vill lägga till logga ut, kopiera följande metod i din app som går igenom alla konton och tar bort dem:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Anropa Microsoft Graph API

När vi har lyckats få en token, kan vi skicka en förfrågan till Microsoft Graph API. Åtkomsttoken kommer att finnas inuti den `AuthenticationResult` i motringningen auth `onSuccess(...)` metod. Om du vill skapa en auktoriserad begäran måste din app du lägga till åtkomsttoken till HTTP-huvud:

| Huvud-nyckel    | value                 |
| ------------- | --------------------- |
| Auktorisering | Ägar < åtkomsttoken > |

Lägg till följande två metoder i din app att anropa graph och uppdatera Användargränssnittet för att göra det i koden: 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

Läs mer om den [Microsoft Graph API](https://graph.microsoft.com)!

#### <a name="multi-account-applications"></a>Konto för flera program

Den här appen har skapats för ett enda konto-scenario. MSAL stöder också flera konto-scenarier, men det krävs några ytterligare arbete från appar. Du måste skapa gränssnitt för att användarens Välj vilket konto som de vill använda för varje åtgärd som kräver token. Din app kan också implementera en tumregel för att välja vilket konto som ska ta emot via den `getAccounts(...)` metoden. 

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Om du har följt av koden ovan, försök att skapa och distribuera appen till en testenhet eller emulator. Du ska kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton! När en användare loggar in, den här appen visar data som returneras från Microsoft Graph `/me` slutpunkt. 

Passa på att öppna ett ärende i det här dokumentet eller i biblioteket MSAL och berätta för oss om du har problem. 

### <a name="consent-to-your-app"></a>Godkänna din app

Första gången en användare loggar in på din app, uppmanas de av Microsoft identity samtycker till att de behörigheter som begärdes.  De flesta användare är kapabel att samtycka, har vissa Azure AD-klienter inaktiverat användargodkännande - att kräva att administratörer kan ge samtycke åt alla användare.  För att stödja det här scenariot måste du registrera din app omfång i Azure-portalen.

## <a name="help-and-support"></a>Hjälp och support

Hade problem med den här självstudien eller med Microsoft identity-plattformen? Se [hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
