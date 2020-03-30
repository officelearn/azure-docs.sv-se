---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67188031"
---
I föregående exempel visades en standard inloggning, som kräver att klienten kontaktar både identitetsprovidern och den bakre Azure-tjänsten varje gång appen startar. Den här metoden är ineffektiv och du kan ha användningsrelaterade problem om många kunder försöker starta appen samtidigt. En bättre metod är att cachelagra auktoriseringstoken som returneras av Azure-tjänsten och försöka använda den här först innan du använder en providerbaserad inloggning.

> [!NOTE]
> Du kan cachelagra den token som utfärdas av den serverdelsbaserade Azure-tjänsten oavsett om du använder klienthanterad eller tjänsthanterad autentisering. Den här självstudien använder tjänsthanterad autentisering.
>
>

1. Öppna filen ToDoActivity.java och lägg till följande importsatser:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Lägg till följande `ToDoActivity` medlemmar i klassen.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. Lägg till följande definition för metoden i `cacheUserToken` filen ToDoActivity.java.

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

    Den här metoden lagrar användar-ID och token i en inställningsfil som är markerad som privat. Detta bör skydda åtkomsten till cachen så att andra appar på enheten inte har åtkomst till token. Inställningen är begränsat för appen. Men om någon får åtkomst till enheten, är det möjligt att de kan få tillgång till tokencachen på annat sätt.

   > [!NOTE]
   > Du kan ytterligare skydda token med kryptering, om tokenåtkomst till dina data anses vara mycket känslig och någon kan få åtkomst till enheten. En helt säker lösning ligger dock utanför den här självstudiens omfattning och beror på dina säkerhetskrav.
   >
   >

4. Lägg till följande definition för metoden i `loadUserTokenCache` filen ToDoActivity.java.

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

5. I filen *ToDoActivity.java* ersätter du metoderna `authenticate` och `onActivityResult` med följande, som använder en tokencache. Ändra inloggningsleverantören om du vill använda ett annat konto än Google.

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

6. Skapa appen och testa autentisering med ett giltigt konto. Kör det minst två gånger. Under den första körningen bör du få en uppmaning att logga in och skapa tokencachen. Därefter försöker varje körning att läsa in tokencachen för autentisering. Du ska inte behöva logga in.
