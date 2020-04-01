---
title: Logga in/ut användare & ringa Microsoft Graph (Android) – Microsoft identity platform | Azure
description: Hämta en åtkomsttoken och anropa Microsoft Graph eller API:er som kräver åtkomsttoken från Microsoft identity platform (Android)
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a5333d5a8f0972dac80efe6c641c515102a2d714
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77917942"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Självstudiekurs: Logga in användare och anropa Microsoft Graph från ett Android-program 

>[!NOTE]
>Den här självstudien visar förenklade exempel på hur du arbetar med MSAL för Android. För enkelhetens skull använder den här självstudien endast läget För ett konto. Du kan också visa den [förkonfigurerade exempelappen](https://github.com/Azure-Samples/ms-identity-android-java/) för att utforska mer komplexa scenarier. Visa [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) för mer information om exempelappen, konfigurationen och registreringen. 

I den här självstudien får du lära dig hur du integrerar din Android-app med Microsofts identitetsplattform med Microsoft Authentication Library för Android. Du får lära dig hur du loggar in och loggar ut en användare, får en åtkomsttoken för att anropa Microsoft Graph API och göra en begäran till Graph API. 

> [!div class="checklist"]
> * Integrera din Android-app med Microsoft Identity Platform 
> * Logga in en användare 
> * Hämta en åtkomsttoken för att anropa Microsoft Graph API 
> * Anropa Microsoft Graph API 
> * Logga ut en användare 

När du har slutfört den här självstudien accepterar programmet inloggningar av personliga Microsoft-konton (inklusive outlook.com, live.com och andra) samt arbets- eller skolkonton från alla företag eller organisationer som använder Azure Active Directory.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-this-tutorial-works"></a>Så här fungerar den här självstudien

![Visar hur exempelappen som genereras av den här självstudien fungerar](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Appen i den här självstudien loggar in användare och hämtar data för deras räkning. Dessa data kommer att nås via ett skyddat API (Microsoft Graph API) som kräver auktorisering och skyddas av Microsofts identitetsplattform.

Mer specifikt:

* Appen loggar in användaren antingen via en webbläsare eller i Microsoft Authenticator och Intune Company Portal.
* Slutanvändaren accepterar de behörigheter som ditt program har begärt.
* Din app får en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API:et.
* Bearbeta Microsoft Graph-svaret.

I det här exemplet används Microsoft Authentication-biblioteket för Android (MSAL) för att implementera autentisering: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL förnyar automatiskt token, levererar enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

### <a name="prerequisites"></a>Krav

* Den här självstudien kräver Android Studio version 3.5+

## <a name="create-a-project"></a>Skapa ett projekt
Om du inte redan har ett Android-program följer du dessa steg för att konfigurera ett nytt projekt. 

1. Öppna Android Studio och välj **Starta ett nytt Android Studio-projekt**.
2. Välj **Grundläggande aktivitet** och välj **Nästa**.
3. Namnge ditt program.
4. Spara paketnamnet. Du kommer in i den senare i Azure-portalen.
5. Ändra språk från **Kotlin** till **Java**.
6. Ange **lägsta API-nivå** till **API 19** eller högre och klicka på **Slutför**.
7. I projektvyn väljer du **Projekt** i listrutan för att visa käll- och icke-källprojektfiler, öppna **app/build.gradle** och ange `targetSdkVersion` . `28`

## <a name="integrate-with-microsoft-authentication-library"></a>Integrera med Microsoft Authentication Library 

### <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure-portalen](https://aka.ms/MobileAppReg).
2. Öppna [bladet Appregistreringar](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) och klicka på **+Ny registrering**.
3. Ange ett **namn** för din app och klicka sedan på **Registrera**utan **att** ange en omdirigera URI.
4. I avsnittet **Hantera** i fönstret som visas väljer du **Autentisering** > **+ Lägg till en plattform** > **Android**. (Du kan behöva välja "Växla till den nya upplevelsen" nära toppen av bladet för att se det här avsnittet)
5. Ange projektets paketnamn. Om du har hämtat koden `com.azuresamples.msalandroidapp`är det här värdet .
6. I avsnittet **Signaturh hash** på sidan **Konfigurera din Android-app** klickar du på **Generera en signaturh hash för utveckling.** och kopiera kommandot KeyTool som ska användas för din plattform.

   > [!Note]
   > KeyTool.exe installeras som en del av Java Development Kit (JDK). Du måste också installera OpenSSL-verktyget för att kunna köra kommandot KeyTool. Mer information finns i [Android-dokumentationen](https://developer.android.com/studio/publish/app-signing#generate-key) om hur du genererar en nyckel. 

7. Ange **signaturhhen** som genereras av KeyTool.
8. Klicka `Configure` och spara **MSAL-konfigurationen** som visas på **Android-konfigurationssidan** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

### <a name="configure-your-application"></a>Konfigurera ditt program 

1. I Android Studios projektfönster navigerar du till **appen\src\main\res**.
2. Högerklicka **på res** och välj **Ny** > **katalog**. Ange `raw` som det nya katalognamnet och klicka på **OK**.
3. Skapa en ny JSON-fil som**anropas** > **raw** `auth_configbn_single_account.json` i **app** > **src** > **main** > res raw och klistra in DEN MSAL-konfiguration som du sparade tidigare. 

    Under omdirigerings-URI:en klistrar du in: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Konfigurationsfilen bör likna det här exemplet: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
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
    
   >[!NOTE]
   >Den här självstudien visar bara hur du konfigurerar en app i läget För ett konto. Visa dokumentationen för mer information om [ett enda kontoläge jämfört med flera konton](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) och konfigurera [appen](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. I **app** > **src** > **huvudsakliga** > **AndroidManifest.xml**, lägga till `BrowserTabActivity` aktiviteten nedan till applikationstexten. Med den här posten kan Microsoft ringa tillbaka till ditt program när autentiseringen är klar:

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

    Ersätt paketnamnet som du registrerade i `android:host=` Azure-portalen mot värdet.
    Ersätt nyckelh hash du registrerade i `android:path=` Azure-portalen för värdet. Signatur hash bör **inte** URL-kodas. Se till att `/` det finns en ledande i början av din Signatur Hash. 
    >[!NOTE]
    >Den "Package Name" du `android:host` kommer att ersätta värdet med bör se ut som: "com.azuresamples.msalandroidapp" Den "Signature Hash" du kommer att ersätta ditt `android:path` värde med bör se ut som: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =" Du kommer också att kunna hitta dessa värden i autentisering blad av din app registrering. Observera att din omdirigera URI kommer att se ut som: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Signatur hash är URL-kodad i slutet av det här värdet, men signatur hash bör **inte** url-kodas i ditt `android:path` värde. 

## <a name="use-msal"></a>Använd MSAL 

### <a name="add-msal-to-your-project"></a>Lägga till MSAL i projektet

1. I projektfönstret i Android Studio navigerar du till **app** > **src** > **build.gradle** och lägger till följande: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Mer om Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Nödvändiga importer 

Lägg till följande till toppen av **app** > **src** > **viktigaste**> **java** > **com.example (yourapp)** > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Omedelbar offentligclientApplication
#### <a name="initialize-variables"></a>Initiera variabler 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>påSkap
I `MainActivity` klassen läser du följande onCreate()-metod för att `SingleAccountPublicClientApplication`instansiera MSAL med hjälp av .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initierar gränssnitt
Lyssna på knappar och samtalsmetoder eller logga fel därefter. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Om du loggar ut med MSAL tas all känd information om en användare bort från programmet, men användaren har fortfarande en aktiv session på sin enhet. Om användaren försöker logga in igen kan de se inloggningsgränssnittet, men kanske inte behöver ange sina autentiseringsuppgifter igen eftersom enhetssessionen fortfarande är aktiv. 

### <a name="getauthinteractivecallback"></a>fåAuthInteractiveCallback
Motringning som används för interaktiva begäranden.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>fåAuthSilentCallback
Motringning som används för tysta begäranden 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Anropa Microsoft Graph API 

Följande kod visar hur du anropar GraphAPI med hjälp av Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Lägga till användargränssnitt
### <a name="activity"></a>Aktivitet 
Om du vill modellera användargränssnittet utanför den här självstudien innehåller följande metoder en guide till att uppdatera text och lyssna på knappar.

#### <a name="updateui"></a>updateUI
Aktivera/inaktivera knappar baserat på inloggningstillstånd och ange text.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGrafResultat

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>utföraOperationOnSignOut
Metod för att uppdatera text i användargränssnittet för att återspegla ut logga ut. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

Exempelfil `activity_main.xml` för att visa knappar och textrutor. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Skapa och distribuera appen till en testenhet eller emulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

När du har loggat in visar appen de `/me` data som returneras från slutpunkten i Microsoft Graph.

### <a name="consent"></a>Samtycke

Första gången en användare loggar in på din app uppmanas de av Microsoft-identitet att godkänna de begärda behörigheterna. Vissa Azure AD-klienter har inaktiverat användarmedgivande som kräver att administratörer samtycker för alla användare. För att stödja det här scenariot måste du antingen skapa din egen klient eller få administratörsmedgivande. 

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort appobjektet som du skapade i steget [Registrera ditt program.](#register-your-application)

## <a name="get-help"></a>Få hjälp

Besök [Hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här självstudien eller med Microsofts identitetsplattform.

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
