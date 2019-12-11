---
title: Logga in användare & Call Microsoft Graph (Android)-Microsoft Identity Platform | Azure
description: 'Hämta en åtkomsttoken och anropa Microsoft Graph eller API: er som kräver åtkomsttoken från Microsoft Identity Platform (Android)'
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feefc368815b1bfe57b67db2cd94702db799d78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961565"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Självstudie: Logga in användare och anropa Microsoft Graph från en Android-app

> [!NOTE]
> Den här självstudien har ännu inte uppdaterats för att fungera med MSAL för Android version 1,0-biblioteket. Det fungerar med en tidigare version, enligt konfigurationen i den här självstudien.

I den här självstudien får du lära dig hur du integrerar en Android-app med Microsoft Identity Platform. Din app kommer att logga in en användare, hämta en åtkomsttoken för att anropa Microsoft Graph-API: et och göra en begäran till Microsoft Graph API.  

> [!div class="checklist"]
> * Integrera en Android-app med Microsoft Identity Platform
> * Logga in en användare
> * Hämta en åtkomsttoken för att anropa API: et för Microsoft Graph
> * Anropa Microsoft Graph-API: et.  

När du har slutfört den här självstudien accepterar programmet inloggnings uppgifter för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) samt arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-this-tutorial-works"></a>Hur den här självstudien fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Appen i den här självstudien kommer att logga in användare och hämta data för deras räkning.  Dessa data kommer att nås via en skyddad API (Microsoft Graph-API) som kräver auktorisering och skyddas av Microsoft Identity Platform.

Mer specifikt:

* Din app kommer att logga in användaren antingen via en webbläsare eller Microsoft Authenticator och Intune-företagsportal.
* Slutanvändaren kommer att godkänna de behörigheter som programmet har begärt.
* Din app kommer att utfärda en åtkomsttoken för Microsoft Graph-API: et.
* Åtkomsttoken tas med i HTTP-begäran till webb-API: et.
* Bearbeta Microsoft Graph svaret.

I det här exemplet används Microsoft Authentication Library för Android (MSAL) för att implementera autentisering: [com. Microsoft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL förnyar automatiskt token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

## <a name="prerequisites"></a>Krav

* Den här självstudien kräver Android Studio version 3,5.

## <a name="create-a-project"></a>Skapa ett projekt

I den här kursen skapas ett nytt projekt. Om du vill ladda ned den slutförda självstudien i stället [laddar du ned koden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).

1. Öppna Android Studio och välj **starta ett nytt Android Studio-projekt**.
2. Välj **grundläggande aktivitet** och välj **Nästa**.
3. Namnge ditt program.
4. Spara paket namnet. Du kommer att ange den senare i Azure Portal.
5. Ändra språket från **Kotlin** till **Java**.
6. Ange **lägsta API-nivå** till **API 19** eller högre och klicka på **Slutför**.
7. I projektvyn väljer du **projekt** i list rutan om du vill visa käll-och icke-källfiler, öppna **app/build. gradle** och ange `targetSdkVersion` för `28`.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure-portalen](https://aka.ms/MobileAppReg).
2. Öppna [bladet Appregistreringar](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
3. Ange ett **namn** för din app och klicka sedan på **Registrera**utan att ange en omdirigerings-URI.
4. I avsnittet **Hantera** i fönstret som visas väljer du **autentisering** >  **+ Lägg till en plattform** > **Android**. (Du kanske måste välja "växla till den nya upplevelsen" nära överst på bladet för att se det här avsnittet)
5. Ange ditt projekts paket namn. Om du har hämtat koden är det här värdet `com.azuresamples.msalandroidapp`.
6. I avsnittet **Signature hash** på sidan **Konfigurera din Android-app** klickar du på **skapa en hash för utvecklings signaturen.** och kopiera det kommando kommando som ska användas för din plattform.

   > [!Note]
   > Verktyget för att installera. exe installeras som en del av Java Development Kit (JDK). Du måste också installera OpenSSL-verktyget för att köra kommandot.

7. Ange **signatur-hashen** som genereras av ett-verktyg.
8. Klicka på `Configure` och spara **MSAL-konfigurationen** som visas på sidan med **Android-konfiguration** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

## <a name="build-your-app"></a>Bygg din app

### <a name="add-your-app-registration"></a>Lägg till din app-registrering

1. I Android Studio projekt fönstret navigerar du till **app\src\main\res**.
2. Högerklicka på **res** och välj **ny** > **katalog**. Ange `raw` som det nya katalog namnet och klicka på **OK**.
3. I **appen** > **src** > **main** > **res** > **RAW**skapar du en ny JSON-fil med namnet `auth_config.json` och klistrar in den MSAL-konfiguration som du sparade tidigare. [Mer information](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)finns i MSAL-konfigurationen.
4. I **appen** > **src** > **main** > **AndroidManifest. XML**lägger du till `BrowserTabActivity`-aktiviteten nedan i program texten. Med den här posten kan Microsoft anropa programmet igen när autentiseringen är klar:

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

    Ersätt det paket namn som du registrerade i Azure Portal för `android:host=` svärdet.
    Ersätt den nyckel-hash som du registrerade i Azure Portal för `android:path=` svärdet. Signaturens hash ska inte vara URL-kodad.

5. I **AndroidManifest. XML**, strax ovanför `<application>`-taggen, lägger du till följande behörigheter:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Skapa appens användar gränssnitt

1. I fönstret Android Studio projekt navigerar du till **app** > **src** > **main** > **res** > **layout** och öppnar **activity_main. XML** och **öppnar datavyn.**
2. Ändra aktivitets layouten, till exempel: `<androidx.coordinatorlayout.widget.CoordinatorLayout` att `<androidx.coordinatorlayout.widget.DrawerLayout`. 
3. Lägg till egenskapen `android:orientation="vertical"` till noden `LinearLayout`.
4. Klistra in följande kod i `LinearLayout`-noden och Ersätt aktuellt innehåll:

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

1. I fönstret Android Studio projekt navigerar du till **app** > **src** > **build. gradle**.
2. Under **beroenden**klistrar du in följande:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3+'
    ```

### <a name="use-msal"></a>Använd MSAL

Nu ska du göra ändringar i `MainActivity.java` för att lägga till och använda MSAL i din app.
I fönstret Android Studio projekt navigerar du till **app** > **src** > **main** > **Java** > **com. exempel. ( din app)** och öppna `MainActivity.java`.

#### <a name="required-imports"></a>Obligatoriska importer

Lägg till följande importer längst upp i `MainActivity.java`:

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

#### <a name="instantiate-msal"></a>Instansiera MSAL

I `MainActivity`-klassen måste vi instansiera MSAL tillsammans med några konfigurationer om vad appen ska göra, inklusive de omfång och webb-API som vi vill ha åtkomst till.

Kopiera följande variabler inuti `MainActivity`-klassen:

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

Ersätt innehållet i `onCreate()` med följande kod för att instansiera MSAL:

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

Koden ovan försöker logga in användare tyst när de öppnar ditt program via `getAccounts()` och, om det är klart, `acquireTokenSilentAsync()`.  I kommande avsnitt implementerar vi callback-hanteraren för ärendet det inte finns några inloggade konton.

#### <a name="use-msal-to-get-tokens"></a>Hämta token med hjälp av MSAL

Nu kan vi implementera appens användar gränssnitts bearbetnings logik och hämta token interaktivt via MSAL.

MSAL exponerar två primära metoder för att hämta tokens: `acquireTokenSilentAsync()` och `acquireToken()`.  

`acquireTokenSilentAsync()` loggar in en användare och får tokens utan någon användar interaktion om ett konto finns. Om det lyckas skickar MSAL token till din app, om den Miss lyckas skapas en `MsalUiRequiredException`.  Om detta undantag genereras, eller om du vill att användaren ska ha en interaktiv inloggnings upplevelse (autentiseringsuppgifter, MFA eller andra principer för villkorlig åtkomst kan eller inte krävas), använder du `acquireToken()`.  

`acquireToken()` visar användar gränssnittet när du försöker logga in användaren och hämta tokens. Den kan dock använda sessionscookies i webbläsaren eller ett konto i Microsoft Authenticator för att tillhandahålla den interaktiva-SSO-upplevelsen.

Skapa följande tre UI-metoder i `MainActivity`-klassen:

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

Lägg till följande metoder för att hämta den aktuella aktiviteten och bearbeta tysta & interaktiva återanrop:

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

#### <a name="use-msal-for-sign-out"></a>Använda MSAL för utloggning

Lägg sedan till stöd för utloggning.

> [!Important]
> Om du loggar ut med MSAL tas all känd information om en användare bort från programmet, men användaren kan fortfarande ha en aktiv session på sin enhet. Om användaren försöker logga in igen kan de se inloggnings gränssnittet, men kanske inte behöver ange sina autentiseringsuppgifter på nytt eftersom sessionen fortfarande är aktiv.

Lägg till en utloggnings funktion genom att lägga till följande metod i klassen `MainActivity`. Den här metoden går igenom alla konton och tar bort dem:

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

#### <a name="call-the-microsoft-graph-api"></a>Anropa API: et för Microsoft Graph

När vi har tagit emot en token kan vi göra en begäran till [Microsoft Graph-API: t](https://graph.microsoft.com) för att få åtkomst-token i `AuthenticationResult` i `onSuccess()`s metoden för autentiserings motringning. För att skapa en auktoriserad begäran måste appen lägga till åtkomsttoken i HTTP-huvudet:

| rubrik nyckel    | värde                 |
| ------------- | --------------------- |
| Autentisering | \<åtkomst-token för innehavare > |

Lägg till följande två metoder i `MainActivity`-klassen för att anropa grafen och uppdatera användar gränssnittet:

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

#### <a name="multi-account-applications"></a>Program med flera konton

Den här appen har skapats för ett scenario med ett enda konto. MSAL har också stöd för flera konto scenarier, men det krävs ytterligare arbete från appar. Du måste skapa ett användar gränssnitt för att hjälpa användarna att välja vilket konto de vill använda för varje åtgärd som kräver tokens. Alternativt kan din app implementera en heuristik för att välja vilket konto som ska användas via metoden `getAccounts()`.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Bygg och distribuera appen till en test enhet eller emulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

När du har loggat in visar appen de data som returneras från Microsoft Graph `/me`-slutpunkten.

### <a name="consent"></a>givit

Första gången användaren loggar in i din app uppmanas de av Microsoft-identiteten att godkänna de behörigheter som begärs.  Även om de flesta användare kan samtycka har vissa Azure AD-klienter inaktiverat användar medgivande som kräver att administratörer samtycker till alla användares räkning. För att stödja det här scenariot registrerar du appens scope i Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort app-objektet som du skapade i steget [Registrera ditt program](#register-your-application) när de inte längre behövs.

## <a name="get-help"></a>Få hjälp

Gå till [Hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här själv studie kursen eller med Microsoft Identity Platform.

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
