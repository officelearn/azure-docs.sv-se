
Föregående exempel visade en standard inloggning, vilket kräver att klienten kontakta identitetsleverantören- och backend Azure service varje gång appen startas. Den här metoden är ineffektiv och du kan ha problem med användning om många kunder försöker starta din app samtidigt. En bättre metod är att cachelagra den autentiseringstoken som returneras av Azure-tjänsten och försöker använda den här första innan du använder en provider-baserad inloggning.

> [!NOTE]
> Du kan cachelagra den token som utfärdas av serverdel i Azure-tjänsten oavsett om du använder klient-hanteras eller service autentisering. Den här kursen använder autentisering som hanteras av tjänsten.
>
>

1. Öppna ToDoActivity.java-filen och Lägg till följande importuttryck:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Lägga till följande medlemmar i den `ToDoActivity` klass.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. I ToDoActivity.java-filen lägger du till följande definitionen för den `cacheUserToken` metoden.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Den här metoden lagrar användar-ID och token i en fil med inställningar som har markerats som privat. Detta ska skydda åtkomst till cachen så att andra appar på enheten inte har tillgång till token. Inställningen är i begränsat läge för appen. Om någon får tillgång till enheten, är det dock möjligt att de kan komma åt token-cache på annat sätt.

   > [!NOTE]
   > Du kan ge ytterligare skydd token med kryptering, om token åtkomst till dina data betraktas som känslig och någon får tillgång till enheten. En helt säker lösning ligger utanför omfånget för den här kursen, men och beror på dina säkerhetskrav.
   >
   >

4. I ToDoActivity.java-filen lägger du till följande definitionen för den `loadUserTokenCache` metoden.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. I den *ToDoActivity.java* filen ersätter den `authenticate` och `onActivityResult` metoder med de följande som använder token-cache. Ändra inloggningsprovidern om du vill använda ett annat konto än Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
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
                    cacheUserToken(mClient.getCurrentUser());
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

6. Bygga appen och testa autentiseringen med ett giltigt konto. Kör minst två gånger. Du bör få en uppmaning om att logga in och skapa token-cache under den första körningen. Efter att försöker varje körning att läsa in token-cache för autentisering. Du bör inte krävas för att logga in.
