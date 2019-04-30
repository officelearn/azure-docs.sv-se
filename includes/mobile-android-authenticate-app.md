---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119780"
---
1. Öppna projektet i Android Studio.

2. I **Projektutforskaren** Android Studio, öppna den `ToDoActivity.java` filen och Lägg till följande importuttryck:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Lägg till följande metod för att den **ToDoActivity** klass:

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

    Den här koden skapar en metod för att hantera autentiseringsprocessen Google. En dialogruta visas ID för den autentiserade användaren. Du kan bara fortsätta på en lyckad autentisering.

    > [!NOTE]
    > Om du använder en identitetsprovider än Google, ändrar du värdet som skickas till den **inloggning** metod till något av följande värden: _MicrosoftAccount_, _Facebook_, _Twitter_, eller _windowsazureactivedirectory_.

4. I den **onCreate** metoden Lägg till följande rad med kod efter den kod som skapar en instans av den `MobileServiceClient` objekt.

    ```java
    authenticate();
    ```

    Det här anropet startar autentiseringsprocessen.

5. Flytta återstående kod efter `authenticate();` i den **onCreate** metod för att en ny **createTable** metod:

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

6. Lägg till följande kodavsnitt för att säkerställa att omdirigeringen fungerar som förväntat, `RedirectUrlActivity` till `AndroidManifest.xml`:

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

7. Lägg till `redirectUriScheme` till `build.gradle` på din Android-App.

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

8. Lägg till `com.android.support:customtabs:23.0.1` i beroenden i din `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Från den **kör** -menyn klickar du på **kör app** att starta programmet och logga in med din valda identitetsprovider.

> [!WARNING]
> URL-schema som tidigare nämnts är skiftlägeskänsligt. Se till att alla förekomster av `{url_scheme_of_you_app}` används på samma sätt.

När du har loggat in, appen bör köras utan fel och du ska kunna skicka frågor till backend-tjänsten och gör uppdateringar till data.
