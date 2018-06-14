
## <a name="register-your-application"></a>Registrera ditt program
Du kan registrera ditt program på två sätt som beskrivs i följande två avsnitt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge
Du kan snabbt registrera ditt program genom att göra följande:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  I den **programnamn** ange ett namn för ditt program.

3. Se till att den **interaktiv installation** kryssrutan är markerad och välj sedan **skapa**.

4. Följ instruktionerna för att hämta program-ID och klistra in den i din kod.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge
För att registrera ditt program och lägga till registreringsinformationen program i lösningen måste du göra följande:
1. Om du inte redan har registrerat ditt program, gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app).
2. I den **programnamn** ange ett namn för ditt program. 

3. Se till att den **interaktiv installation** kryssrutan är avmarkerad och välj sedan **skapa**.

4. Välj **lägga till plattformen**väljer **programspecifika**, och välj sedan **spara**.

5. Under **app** > **java** > **{värden}. { Namespace}** öppnar `MainActivity`. 

6.  Ersätt *[Ange program-Id här]* i följande rad med program-ID som du just har registrerat:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. Under **app** > **visar**öppnar den *AndroidManifest.xml* fil.

8. I den `manifest\application` nod, Lägg till följande aktivitet. Gör så registrerar en `BrowserTabActivity` aktivitet som gör att operativsystem och återuppta programmet när autentiseringen är klar:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. I den `BrowserTabActivity` nod, Ersätt `[Enter the application Id here]` med program-ID.
