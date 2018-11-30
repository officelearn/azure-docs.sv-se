---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440465"
---
Föregående exempel visade en standard inloggning, vilket kräver att klienten kan kontakta identitetsprovidern- och backend Azure-tjänsten varje gång appen startas. Den här metoden är ineffektiv och du kan ha användning-relaterade problem om många kunder försöker starta din app samtidigt. En bättre metod är att cachelagra den autentiseringstoken som returneras av Azure-tjänsten och försöker använda den här första innan du använder en provider-baserad inloggning.

> [!NOTE]
> Du kan cachelagra den token som utfärdas av serverdelen Azure-tjänsten oavsett om du använder hanteras av klienten eller service-autentisering. Den här självstudien använder tjänsthanterade autentisering.
>
>

1. Öppna ToDoActivity.java-filen och Lägg till följande importuttryck:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Lägg till följande medlemmar till den `ToDoActivity` klass.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. I ToDoActivity.java-filen lägger du till följande definition för den `cacheUserToken` metoden.

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

    Den här metoden lagrar användar-ID och token i en fil med inställningar som har markerats som privat. Detta ska skydda åtkomsten till cachen så att andra appar på enheten inte har åtkomst till token. Inställningen är i begränsat läge för appen. Om någon får tillgång till enheten, är det dock möjligt att de kan få åtkomst till tokencache på annat sätt.

   > [!NOTE]
   > Du kan ytterligare skydda token med kryptering, om token åtkomst till dina data betraktas som känslig och någon kan få åtkomst till enheten. En helt säker lösning ligger utanför omfånget för den här självstudien, men och beror på dina säkerhetskrav.
   >
   >

4. I ToDoActivity.java-filen lägger du till följande definition för den `loadUserTokenCache` metoden.

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

5. I den *ToDoActivity.java* filen ersätter den `authenticate` och `onActivityResult` metoder med de följande som använder en token-cache. Ändra inloggningsprovidern om du vill använda ett annat konto än Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
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

6. Skapa appen och test-autentisering med hjälp av ett giltigt konto. Kör minst två gånger. Under den första körningen får du en uppmaning om att logga in och skapa token-cache. Efter det varje körning som försöker läsa in tokens cacheminne för autentisering. Du bör inte krävas för att logga in.
