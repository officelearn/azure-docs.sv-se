
1. Öppna projektet i Android Studio.

2. I **Projektutforskaren** i Android Studio öppnar den `ToDoActivity.java` och Lägg till följande importuttryck:

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
        // Login using the Google provider.
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
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Den här koden skapar en metod för att hantera autentiseringsprocessen Google. En dialogruta visas ID för den autentiserade användaren. Du kan bara fortsätta på en lyckad autentisering.

    > [!NOTE]
    > Om du använder en identitetsleverantör än Google, ändra värdet som skickas till den **inloggning** metod för att ett av följande värden: _MicrosoftAccount_, _Facebook_, _Twitter_, eller _windowsazureactivedirectory_.

4. I den **onCreate** metod, Lägg till följande kodrad efter den kod som instantierar den `MobileServiceClient` objekt.

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

6. För att säkerställa omdirigering fungerar som förväntat, lägger du till följande fragment av `RedirectUrlActivity` till `AndroidManifest.xml`:

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

7. Lägg till `redirectUriScheme` till `build.gradle` för din Android-App.

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

8. Lägg till `com.android.support:customtabs:23.0.1` till beroenden i din `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Från den **kör** -menyn klickar du på **kör app** att starta appen och logga in med ditt valda identitetsleverantör.

> [!WARNING]
> URL-schemat som nämns är skiftlägeskänslig. Se till att alla förekomster av `{url_scheme_of_you_app}` används på samma sätt.

När du har loggat in, appen körs utan fel och du ska kunna skicka frågor till backend-tjänsten och göra uppdateringar till data.
