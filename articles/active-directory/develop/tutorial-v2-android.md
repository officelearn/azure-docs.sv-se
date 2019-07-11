---
title: Komma igång med Android – Microsoft identity-plattformen | Azure
description: 'Hur en Android-app kan få en åtkomsttoken och anropa Microsoft Graph API eller API: er som kräver åtkomsttoken från Microsoft identity-plattformen.'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c6b0d4cd664b12dbd0fbd4e9423240c8dbebb3
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723810"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Logga in användare och anropa Microsoft Graph från en Android-app

I de här självstudierna lär du dig att integrera en Android-app med Microsoft identity-plattformen. Din app ska logga in en användare få en åtkomsttoken att anropa Microsoft Graph API och gör en begäran för Microsoft Graph API.  

När du har slutfört guiden för ditt program ska ta emot inloggningar för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbete eller skola konton från alla företag eller organisation som använder Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hur fungerar den här självstudien

![Visar hur exempelapp som genererats av den här kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Appen i det här exemplet ska logga in användare och hämta data å deras vägnar.  Dessa data kan användas med en skyddad API (Microsoft Graph API) som kräver auktorisering.

Mer specifikt:

* Din app ska logga in användaren antingen via en webbläsare eller Microsoft Authenticator och Intune-Företagsportalen.
* Användaren kommer att acceptera de behörigheter som programmet har begärt. 
* Din app kommer att utfärdas en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API.
* Process för Microsoft Graph-svaret.

Det här exemplet använder Microsoft Authentication library för Android (MSAL) för att implementera autentisering. MSAL kommer automatiskt förnya token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hantera konton.

## <a name="prerequisites"></a>Förutsättningar

* Den här guidade konfigurationen använder Android Studio.
* Android 16 eller senare krävs (19 + rekommenderas).

## <a name="library"></a>Bibliotek

Den här guiden används av autentiseringsbibliotek för följande:

|Bibliotek|Beskrivning|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="create-a-project"></a>Skapa ett projekt

Den här självstudien skapas ett nytt projekt. Om du vill ladda ned den slutförda självstudien i stället [ladda ned koden](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Öppna Android Studio och välj **starta ett nytt Android Studio-projekt**
2. Välj **grundläggande aktivitet** och klicka på **nästa**.
3. Namnge ditt program
4. Spara paketets namn. Anger du den senare till Azure-portalen. 
5. Ange den **minsta API-nivå** till **API 19** eller senare, och klicka på **Slutför**.
6. I projektvyn väljer **projekt** i listrutan att visa käll- och icke-source projektfilerna öppna **App/build.gradle** och ange `targetSdkVersion` till `27`.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure Portal](https://aka.ms/MobileAppReg)
2. Öppna den [registreringar appbladet](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
3. Ange en **namn** för din app och klicka sedan på, utan att ange en omdirigerings-URI **registrera**.
4. I den **hantera** i fönstret som visas, Välj **autentisering** >  **+ Lägg till en plattform** > **Android**.
5. Ange paketet projektnamn. Om du har hämtat kod som det här värdet är `com.azuresamples.msalandroidapp`.
6. I den **signatur hash** delen av den **konfigurera din Android-app** klickar du på **Generera en signatur-Hash för utveckling.** och kopiera KeyTool-kommandot för att använda för din plattform. Observera att KeyTool.exe installeras som en del av Java Development Kit (JDK) och du måste ha installerat OpenSSL-verktyget för att köra kommandot KeyTool också.
7. Ange den **signatur hash** genereras av KeyTool.
8. Klicka på `Configure` och spara den **MSAL Configuration** som visas i **Android-konfiguration** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

## <a name="build-your-app"></a>Bygg din app

### <a name="configure-your-android-app"></a>Konfigurera din Android-app

1. I fönstret för Android Studio-projektet, går du till **app\src\main\res**.
2. Högerklicka på **res** och välj **New** > **Directory**. Ange `raw` som nya katalognamnet och klicka på **OK**.
3. I **app** > **src** > **res** > **raw**, skapa en ny JSON-fil kallas `auth_config.json`och klistra in MSAL konfigurationen som du sparade tidigare. Se [MSAL konfiguration för mer info](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
4. I **app** > **src** > **huvudsakliga** > **AndroidManifest.xml**, lägga till `BrowserTabActivity`aktivitet nedan. Den här posten kan Microsoft att ringa tillbaka till programmet efter att autentiseringen är klar:

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

    Ersätt paketnamnet som du registrerade i Azure-portalen för den `android:host=` värde.
    Ersätt den hash för nyckel som du registrerade i Azure-portalen för den `android:path=` värde. Signaturen hash-värdet får inte vara URL-kodas.

5. I den **AndroidManifest.xml**, precis ovanför den `<application>` tagga, Lägg till följande behörigheter:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Skapa appens användargränssnitt

1. I fönstret Android Studio-projekt går du till **app** > **src** > **huvudsakliga** > **res**  >  **layout** och öppna **activity_main.xml** och öppna den **Text** vy.
2. Ändra layouten aktivitet, till exempel `<androidx.coordinatorlayout.widget.CoordinatorLayout` till `<androidx.coordinatorlayout.widget.LinearLayout`.
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

1. I fönstret Android Studio-projekt går du till **app** > **src** > **build.gradle**.
2. Under **beroenden**, klistra in följande:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Använd MSAL

Nu göra ändringar i `MainActivity.java` att lägga till och använda MSAL i din app.
I fönstret Android Studio-projekt går du till **app** > **src** > **huvudsakliga** > **java**  >  **com.example.msal**, och öppna `MainActivity.java`

#### <a name="required-imports"></a>Nödvändiga importer

Lägg till följande importer i den övre delen av `MainActivity.java`:

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

#### <a name="instantiate-msal"></a>Skapa en instans av MSAL

I den `MainActivity` klass, måste du skapa en instans av MSAL tillsammans med några få konfigurationer om vilka appen kommer gör inklusive scope och webb-API som vi vill ha åtkomst till.

Kopiera in följande variabler i den `MainActivity` klass:

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

Ersätt innehållet i `onCreate()` med följande kod för att skapa en instans av MSAL:

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

Koden ovan försöker logga in användare tyst när de öppnar ditt program via `getAccounts()` och, om detta lyckas `acquireTokenSilentAsync()`.  I nästa avsnitt kommer vi implementera återanrop hanteraren för om det finns några inloggade konton.

#### <a name="use-msal-to-get-tokens"></a>Använd MSAL för att hämta token

Nu kan vi implementera appens användargränssnitt bearbetningslogiken och hämta token interaktivt via MSAL.

MSAL visar två huvudsakliga sätt för att hämta token: `acquireTokenSilentAsync()` och `acquireToken()`.  

`acquireTokenSilentAsync()` loggar in en användare och hämta token utan någon användarinteraktion om ett konto finns. Om det lyckas MSAL kommer handoff token till appen, om det inte går att generera en `MsalUiRequiredException`.  Om det här undantaget genereras eller om du vill att användaren har en interaktiv inloggning erfarenhet (autentiseringsuppgifter, mfa eller andra villkorlig åtkomst-principer kan eller inte krävas) och sedan använda `acquireToken()`.  

`acquireToken()` Visar Användargränssnittet när du försöker logga in användaren och hämta token. Det kan dock använda cookies i webbläsaren, eller ett konto i Microsoft authenticator för att tillhandahålla interaktiva SSO-upplevelse.

Skapa följande tre Användargränssnittet metoder i den `MainActivity` klass:

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

Lägg till följande metoder för att hämta den aktuella aktiviteten och bearbeta tyst & interaktiva återanrop:

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

Lägg sedan till stöd för utloggning.

> [!Important]
> Logga ut med MSAL tar bort alla kända information om en användare från programmet, men användaren fortfarande har en aktiv session på sin enhet. Om användaren misslyckas att logga in igen de kan se Användargränssnittet för inloggning, men kanske inte behöver ange sina autentiseringsuppgifter igen eftersom sessionen enheten fortfarande är aktiv.

Lägg till utloggning kapaciteten genom att lägga till följande metod i den `MainActivity` klass. Den här metoden går igenom alla konton och tar bort dem:

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

När vi har tagit emot en token, vi kan göra en begäran om att den [Microsoft Graph API](https://graph.microsoft.com) åtkomsttoken kommer att finnas inuti den `AuthenticationResult` i motringningen auth `onSuccess()` metod. Om du vill skapa en auktoriserad begäran måste din app du lägga till åtkomsttoken till HTTP-huvud:

| Huvud-nyckel    | value                 |
| ------------- | --------------------- |
| Authorization | Ägar \<åtkomst-token > |

Lägg till följande två metoder i den `MainActivity` klassen för att anropa graph och uppdatera Användargränssnittet:

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

#### <a name="multi-account-applications"></a>Konto för flera program

Den här appen har skapats för ett enda konto-scenario. MSAL stöder också flera konto scenarier, men det krävs några ytterligare arbete från appar. Du måste skapa gränssnitt för att användarens Välj vilket konto som de vill använda för varje åtgärd som kräver token. Din app kan också implementera en tumregel för att välja vilket konto som ska ta emot via den `getAccounts()` metoden.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Skapa och distribuera appen till en testenhet eller emulator. Du ska kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

När du har loggat in visas de data som returneras från Microsoft Graph `/me` slutpunkt.

### <a name="consent"></a>Medgivande

Första gången en användare loggar in på din app, uppmanas de av Microsoft identity samtycker till att de behörigheter som begärdes.  De flesta användare är kapabel att samtycka, har vissa Azure AD-klienter inaktiverat tillstånd som kräver att administratörer kan ge samtycke åt alla användare. För det här scenariot måste registrera din app omfång i Azure-portalen.

## <a name="get-help"></a>Få hjälp

Besök [hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här självstudien eller med Microsoft identity-plattformen.
