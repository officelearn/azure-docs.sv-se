---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67188030"
---
1. Öppna projektet i Android Studio.

2. Öppna **Project Explorer** filen i `ToDoActivity.java` Project Explorer i Android Studio och lägg till följande importsatser:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Lägg till följande metod i klassen **ToDoActivity:**

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Den här koden skapar en metod för att hantera Googles autentiseringsprocess. I en dialogruta visas den autentiserade användarens ID. Du kan bara fortsätta med en lyckad autentisering.

    > [!NOTE]
    > Om du använder en annan identitetsleverantör än Google ändrar du värdet som skickas till **inloggningsmetoden** till något av följande värden: _MicrosoftAccount_, _Facebook_, _Twitter_eller _windowsazureactivedirectory_.

4. I **metoden onCreate** lägger du till följande kodrad efter `MobileServiceClient` koden som instansierar objektet.

    ```java
    authenticate();
    ```

    Det här anropet startar autentiseringsprocessen.

5. Flytta den återstående `authenticate();` koden efter i **metoden onCreate** till en ny **createTable-metod:**

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Om du vill vara medveten om omdirigering fungerar som förväntat lägger du till följande utdrag i: `RedirectUrlActivity` `AndroidManifest.xml`

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Lägg `redirectUriScheme` `build.gradle` till i din Android-applikation.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Lägg `com.android.support:customtabs:23.0.1` till beroendena `build.gradle`i din:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. På **Kör-menyn** klickar du på **Kör-appen** för att starta appen och logga in med din valda identitetsleverantör.

> [!WARNING]
> Url-schemat som nämns är skiftlägeskänsligt. Se till att `{url_scheme_of_you_app}` alla förekomster av användning samma fall.

När du har loggat in ska appen köras utan fel och du bör kunna fråga backend-tjänsten och göra uppdateringar av data.
