---
title: "Azure AD Android komma igång | Microsoft Docs"
description: "Hur du skapar ett Android-program som kan integreras med Azure AD för inloggnings- och Azure AD-anrop skyddade API: er med hjälp av OAuth."
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 1ea39854766332a87eae4f44f52a4853848d2120
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-into-an-android-app"></a>Integrera Azure AD i en Android-app
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Testa förhandsversionen av vår nya [utvecklarportalen](https://identity.microsoft.com/Docs/Android), som hjälper dig att komma igång med Azure AD i bara några minuter. Developer-portalen vägleder dig genom processen med att registrera en app och integrera Azure AD i din kod. När du är klar har du ett enkelt program som kan autentisera användare i din klient och en serverdel som kan acceptera token och utföra valideringen.
>
>

Om du utvecklar ett skrivbordsprogram Azure Active Directory (Azure AD) att gör det lätt att autentisera användarna med hjälp av sina lokala Active Directory-konton. Det gör också ditt program att använda alla webb-API som skyddas av Azure AD, till exempel API: er för Office 365 eller Azure API på ett säkert sätt.

Azure AD innehåller Active Directory Authentication Library (ADAL) för Android-klienter som behöver åtkomst till skyddade resurser. Det enda syftet med ADAL är att göra det lättare för din app för att få åtkomst-token. För att demonstrera hur lätt det är ska vi skapa ett program för Android att göra-lista som:

* Hämtar åtkomst till token för att anropa API en att göra-lista med hjälp av den [OAuth 2.0-autentiseringsprotokollet](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Hämtar uppgiftslista för en användare.
* Loggar ut användare.

Du behöver en Azure AD-klient som du kan skapa användare och registrera ett program för att komma igång. Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Steg 1: Hämta och köra Node.js REST API TODO exempelserver
Node.js REST API TODO-exempel är särskilt utformad för att arbeta mot vår befintliga exemplet för att skapa en enskild klient uppgiften REST API för Azure AD. Det här är en förutsättning för Snabbstart.

Information om hur du konfigurerar detta finns i vår befintliga prover i [Microsoft Azure Active Directory exempel REST API-tjänsten för Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Steg 2: Registrera ditt webb-API med Azure AD-klient
Active Directory har stöd för att lägga till två typer av program:

- Web API: er som tillhandahåller tjänster till användare
- Program (som körs på webben eller på en enhet) som har åtkomst till de webb-API: er

I det här steget du registrera webb-API som du kör lokalt för att testa det här exemplet. Den här webb-API är normalt en REST-tjänst som erbjuder funktioner som du vill använda en app för att få åtkomst till. Azure AD kan du skydda valfri slutpunkt.

Förutsätter vi att att du registrerar TODO REST-API som refererar till tidigare. Men det här fungerar för alla webb-API som du vill använda Azure Active Directory för att skydda.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto på den översta raden. I den **Directory** Välj Azure AD-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Ange ett eget namn för programmet (till exempel **TodoListService**), Välj **webbprogram och/eller webb-API**, och klicka på **nästa**.
6. Ange den grundläggande Webbadressen för för URL-inloggning. Detta är som standard `https://localhost:8080`.
7. Klicka på **OK** att slutföra registreringen.
8. Gå till sidan programmet fortfarande i Azure-portalen, hitta program-ID-värdet och kopiera den. Du behöver det senare när du konfigurerar ditt program.
9. Från den **inställningar** -> **egenskaper** sidan, uppdatera app-ID URI - ange `https://<your_tenant_name>/TodoListService`. Ersätt `<your_tenant_name>` med namnet på din Azure AD-klient.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Steg 3: Registrera Android Native Client exempelprogrammet
I det här exemplet måste du registrera ditt webbprogram. På så sätt kan programmet att kommunicera med den precis har registrerats webben-API. Azure AD att neka även tillåta programmet att begära för inloggning om den är registrerad. Som är del av säkerheten för modellen.

Förutsätter vi att att du registrera exempelprogrammet refererar till tidigare. Men den här metoden fungerar för alla appar som du utvecklar.

> [!NOTE]
> Du kanske undrar varför du ska lägga ett program och ett webb-API i en klient. Du kan skapa en app som ansluter till en extern API som är registrerad i Azure AD från en annan klient som du kan ha gissa. Om du gör det kan uppmanas kunderna att godkänna användningen av API: et i programmet. Active Directory Authentication Library för iOS hand tar om detta godkännande för dig. Får du veta mer avancerade funktioner, visas detta är en viktig del av arbetet som krävs för att komma åt uppsättning Microsoft APIs från Azure och Office, samt-leverantör. Just nu eftersom du har registrerat både webb-API och ditt program under samma klientorganisation, visas inte några uppmaningar om samtycke. Detta är normalt om du utvecklar ett program för ditt företag att använda.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto på den översta raden. I den **Directory** Välj Azure AD-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Ange ett eget namn för programmet (till exempel **TodoListClient Android**), Välj **internt klientprogram**, och klicka på **nästa**.
6. Omdirigerings-URI, ange `http://TodoListClient`. Klicka på **Slutför**.
7. Hitta värdet för program-ID och kopiera den från sidan program. Du behöver det senare när du konfigurerar ditt program.
8. Från den **inställningar** väljer **nödvändiga behörigheter** och välj **Lägg till**.  Leta upp och välj TodoListService, Lägg till den **åtkomst TodoListService** behörighet under **delegerade behörigheter**, och klicka på **klar**.

Du kan använda pom.xml på den översta nivån för att skapa med Maven:

1. Klona den här lagringsplatsen i en katalog som du väljer:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  
2. Följ stegen i den [krav för att konfigurera din Maven-miljö för Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
3. Ställ in emulatorn med SDK-19.
4. Gå till roten i mappen där du klona lagringsplatsen.
5. Kör det här kommandot:`mvn clean install`
6. Ändra katalogen till exempel Snabbstart:`cd samples\hello`
7. Kör det här kommandot:`mvn android:deploy android:run`

   Du bör se app startar.
8. Ange test autentiseringsuppgifter för försök.

JAR paket skickas bredvid AAR-paketet.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Steg 4: Ladda ned Android ADAL och lägga till den i ditt Eclipse-arbetsområde
Vi har gjort det enkelt att du har flera alternativ för att använda ADAL i din Android-projekt:

* Du kan använda källkoden för att importera det här biblioteket i Eclipse och länka till ditt program.
* Om du använder Android Studio du använder formatet AAR paketet och refererar binärfilerna.

### <a name="option-1-source-zip"></a>Alternativ 1: Källa Zip
Om du vill hämta en kopia av källkoden, klickar du på **hämta ZIP** till höger på sidan. Du kan också [ladda ned från GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

### <a name="option-2-source-via-git"></a>Alternativ 2: Källa via Git
För att få källkoden för SDK via Git, skriver du:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Alternativ 3: Binärfiler via Gradle
Du kan hämta de binära filerna från Maven centrala lagringsplatsen. AAR paketet kan ingå i ditt projekt i Android Studio enligt följande:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Alternativ 4: AAR via Maven
Om du använder M2Eclipse plugin-program kan du ange beroendet i filen pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Alternativ 5: JAR-paketet i biblioteksmappen
Du kan hämta JAR-filen från Maven-lagringsplatsen och släpp det i den **libs** mappen i projektet. Du måste kopiera de nödvändiga resurserna i ditt projekt, eftersom JAR-paket inte inkluderas.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Steg 5: Lägg till referenser till Android ADAL i projektet
1. Lägg till en referens i projektet och ange den som en Android-biblioteket. Om du är osäker på hur du gör detta kan du få mer information den [Android Studio plats](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Lägg till ett projekt beroende för felsökning i dina Projektinställningar.
3. Uppdatera ditt projekt AndroidManifest.xml fil:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Skapa en instans av AuthenticationContext på din huvudaktiviteten. Information om det här anropet är utanför omfattningen för det här avsnittet, men du får en bra start genom att titta på den [Android Native Client exempel](https://github.com/AzureADSamples/NativeClient-Android). I följande exempel SharedPreferences är standard och utfärdare är i form av `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Kopiera den här kodblock för att hantera slutet av AuthenticationActivity när användaren anger autentiseringsuppgifter och tar emot en Auktoriseringskoden:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Definiera ett återanrop för att fråga efter en token:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Slutligen be om en token med hjälp av att motringning:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Här följer en förklaring av parametrarna:

* *resursen* krävs och är den resurs som du försöker komma åt.
* *clientid* krävs och kommer från Azure AD.
* *RedirectUri* behöver inte anges för acquireToken-anropet. Du kan konfigurera den som ditt paketnamn.
* *PromptBehavior* bidrar till att be om autentiseringsuppgifter för att hoppa över cache och cookie.
* *motringning* anropas efter Auktoriseringskoden byts ut mot en token. Den har ett objekt av AuthenticationResult som har åtkomst-token, datum har upphört att gälla och ID tokeninformation.
* *acquireTokenSilent* är valfritt. Du kan anropa referensen cachelagring och token uppdatera. Det ger också synkroniserade versionen. Den godkänner *userId* som en parameter.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Du bör ha vad du behöver kunna integrera med Azure Active Directory med hjälp av den här genomgången. Fler exempel på detta fungerar finns i AzureADSamples / databasen på GitHub.

## <a name="important-information"></a>Viktig information
### <a name="customization"></a>Anpassning
Programresurser kan skriva över biblioteksresurser för projektet. Detta händer när din app skapas. Därför kan du anpassa autentisering aktivitet layout som du vill. Se till att hålla ID för kontrollerna som använder ADAL (Webbvy).

### <a name="broker"></a>Service Broker
Microsoft Intune-företagsportalappen ger broker-komponent. Kontot har skapats i AccountManager. Kontotypen är ”com.microsoft.workaccount”. AccountManager kan bara ett enda SSO-konto. Den skapar en SSO-cookie för användaren när du har slutfört enheten utmaning för en av apparna.

ADAL använder kontot broker om ett användarkonto skapas på denna autentiserare och du inte väljer att hoppa över den. Du kan hoppa över broker användare med:

   `AuthenticationSettings.Instance.setSkipBroker(true);`

Du måste registrera en särskild RedirectUri för broker användning. RedirectUri är i formatet `msauth://packagename/Base64UrlencodedSignature`. Du kan hämta din RedirectUri för din app med hjälp av skript brokerRedirectPrint.ps1 eller mContext.getBrokerRedirectUri för API-anrop. Signaturen är kopplad till ditt signeringscertifikat.

Den aktuella modellen broker är för en användare. AuthenticationContext innehåller API-metoden för att få broker användaren.

   `String brokerAccount =  mContext.getBrokerUser(); //Broker user is returned if account is valid.`

App-manifest ska ha följande behörigheter till använder AccountManager konton. Mer information finns i [AccountManager information på webbplatsen för Android](http://developer.android.com/reference/android/accounts/AccountManager.html).

* GET_ACCOUNTS
* USE_CREDENTIALS
* MANAGE_ACCOUNTS

### <a name="authority-url-and-ad-fs"></a>URL: en utfärdare och AD FS
Active Directory Federation Services (AD FS) identifieras inte som produktion STS, så du måste stänga av instans identifiering och gå till FALSKT i konstruktorn AuthenticationContext.

Utfärdare-URL måste en STS-instans och en [klientnamn](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Fråga cacheobjekt
ADAL tillhandahåller en standard-cache i SharedPreferences med några enkla cache frågan funktioner. Du kan hämta aktuell cache från AuthenticationContext med hjälp av:

    ITokenCacheStore cache = mContext.getCache();

Du kan också ge din cache-implementering, om du vill anpassa den.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Fråga beteende
ADAL innehåller ett alternativ för att ange fråga beteende. PromptBehavior.Auto visar Användargränssnittet om uppdateringstoken är ogiltig och autentiseringsuppgifter krävs. PromptBehavior.Always kommer att hoppa över cache-användning och alltid visa Användargränssnittet.

### <a name="silent-token-request-from-cache-and-refresh"></a>Tyst tokenbegäran från cache och uppdatera
En tyst tokenbegäran använder inte popup-Användargränssnittet och kräver inte en aktivitet. Den returnerar en token från cachen om de är tillgängliga. Om token har upphört att gälla, görs uppdatera det här metoden. Om uppdateringstoken som upphört att gälla eller inte fungerar, returnerar AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Du kan också göra en synkronisering anropet med hjälp av den här metoden. Du kan ange null till återanrop eller använda acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostik
Det här är de primära informationskällorna för att diagnostisera problem:

* Undantag
* Logs
* Nätverksspår

Observera att Korrelations-ID: N är centrala för diagnostik i biblioteket. Du kan ställa in din Korrelation ID: N på grundval av per begäran om du vill att korrelera ett ADAL begäran med andra åtgärder i koden. Om du inte anger en Korrelations-ID, att ADAL generera ett slumpmässigt. Alla loggar meddelanden och samtal nätverket kommer sedan stämplad med Korrelations-ID Självgenererat ID ändringarna för varje begäran.

#### <a name="exceptions"></a>Undantag
Undantag är den första diagnostiken. Vi försöker tillhandahålla användbara felmeddelanden. Om du hittar en som inte är användbara du filen ett problem och berätta för oss. Innehåller enheten modellen och SDK-nummer.

#### <a name="logs"></a>Logs
Du kan konfigurera biblioteket för att generera loggmeddelanden som du kan använda för att diagnostisera problem. Du kan konfigurera loggning genom att göra följande anrop för att konfigurera ett återanrop som ADAL använder till manuellt från varje loggmeddelande som genereras.

    Logger.getInstance().setExternalLogger(new ILogger() {
        @Override
        public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
        ...
        // You can write this to log file depending on level or error code.
        writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
        }
    }

Meddelanden kan skrivas till en anpassad loggfil som visas i följande kod. Tyvärr, det går inte standard för att få loggar från en enhet. Det finns vissa tjänster som kan hjälpa dig med detta. Du kan också Skriv ditt eget, till exempel skicka filen till en server.

    private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
    }

Det här är loggningsnivåerna:
* Fel (undantag)
* Varna (varning)
* Info (kännedom)
* Utförlig (Mer information)

Du kan ange loggningsnivån så här:

    Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

 Alla loggmeddelanden skickas till logcat utöver eventuella anpassade loggen återanrop.
Du kan hämta en logg till en fil från logcat på följande sätt:

    adb logcat > "C:\logmsg\logfile.txt"

 Mer information om GDB-kommandon finns i [logcat information på webbplatsen för Android](https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat).

#### <a name="network-traces"></a>Nätverksspår
Du kan använda olika verktyg för att samla in HTTP-trafik som genereras av ADAL.  Detta är mest användbart om du är bekant med OAuth-protokollet eller om du behöver ange diagnostikinformation till Microsoft eller andra supportkanaler.

Fiddler är det enklaste HTTP spårning verktyget. Använd följande länkar för att ange upp till korrekt poster ADAL nätverkstrafik. En spårning verktyget som Fiddler eller Charles ska vara användbar, måste du konfigurera den för att registrera okrypterad SSL-trafik.  

> [!NOTE]
> Spår som skapats på det här sättet kan innehålla mycket Privilegierade information, till exempel åtkomsttoken, användarnamn och lösenord. Om du använder Produktionskonton, dela inte de med tredje part. Om du behöver ange en spårning till någon för att få support återskapa problemet genom att använda ett tillfälligt konto med användarnamn och lösenord som du inte gör något delning.

* Från webbplatsen Telerik: [ställa in Fiddler för Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
* Från GitHub: [konfigurera Fiddler regler för ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)

### <a name="dialog-mode"></a>Dialogrutan läge
Metoden acquireToken utan aktivitet har stöd för en fråga i dialogruta.

### <a name="encryption"></a>Kryptering
ADAL krypterar token och lagra i SharedPreferences som standard. Du kan titta på StorageHelper klassen om du vill se ytterligare information. Android introducerade Android Keystore för 4.3 (API-18) säker lagring av privata nycklar. ADAL används för API-18 och högre. Om du vill använda ADAL för lägre SDK-versioner måste du tillhandahålla en hemlig nyckel på AuthenticationSettings.INSTANCE.setSecretKey.

### <a name="oauth2-bearer-challenge"></a>OAuth2 ägar utmaning
Klassen AuthenticationParameters innehåller funktioner för att hämta authorization_uri från OAuth2 ägar-anrop.

### <a name="session-cookies-in-webview"></a>Cookies i webbvy
Android webbvy rensas inte sessionscookies när appen har stängts. Du kan hantera som med hjälp av den här exempelkoden:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Mer information om cookies finns i [CookieSyncManager information på webbplatsen för Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="resource-overrides"></a>Resursen åsidosättningar
ADAL-biblioteket innehåller engelska strängar för ProgressDialog meddelanden. Ditt program ska skriva över om du vill använda lokaliserade strängar.

     <string name="app_loading">Loading...</string>
     <string name="broker_processing">Broker is processing</string>
     <string name="http_auth_dialog_username">Username</string>
     <string name="http_auth_dialog_password">Password</string>
     <string name="http_auth_dialog_title">Sign In</string>
     <string name="http_auth_dialog_login">Login</string>
     <string name="http_auth_dialog_cancel">Cancel</string>

### <a name="ntlm-dialog-box"></a>Dialogrutan för NTLM
ADAL version 1.1.0 stöder en dialogruta för NTLM som bearbetas via händelsen onReceivedHttpAuthRequest från WebViewClient. Du kan anpassa layout och strängar för dialogrutan.

### <a name="cross-app-sso"></a>Enkel inloggning mellan appar
Läs [aktivera enkel inloggning mellan appar på Android med hjälp av ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
