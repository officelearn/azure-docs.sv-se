---
title: Komma igång med iOS – Microsoft identity-plattformen | Azure
description: Hur iOS (Swift)-program kan anropa ett API som kräver åtkomsttoken med Microsoft identity-plattformen
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872094"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Logga in användare och anropa Microsoft Graph från en iOS-app

I de här självstudierna lär du dig att integrera en iOS-app med Microsoft identity-plattformen. Appen ska logga in en användare få en åtkomsttoken att anropa Microsoft Graph API och gör en begäran för Microsoft Graph API.  

När du har slutfört guiden för ditt program ska ta emot inloggningar för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbete eller skola konton från alla företag eller organisation som använder Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hur fungerar den här självstudien

![Visar hur exempelapp som genererats av den här kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Appen i den här självstudien ska logga in användare och hämta data å deras vägnar.  Dessa data kan nås via en skyddad API (Microsoft Graph API i det här fallet) som kräver auktorisering och skyddas av Microsoft identity-plattformen.

Mer specifikt:

* Din app ska logga in användaren antingen via en webbläsare eller Microsoft Authenticator.
* Användaren kommer att acceptera de behörigheter som programmet har begärt.
* Din app kommer att utfärdas en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API.
* Process för Microsoft Graph-svaret.

Det här exemplet använder Microsoft Authentication library (MSAL) för att implementera autentisering. MSAL kommer automatiskt förnya token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hantera konton.

## <a name="prerequisites"></a>Förutsättningar

- XCode version 10.x krävs för att skapa appar i den här guiden. Du kan hämta XCode från den [iTunes webbplats](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode hämta URL: en").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Du kan använda en beroendehanterare eller lägga till i biblioteket manuellt. Anvisningarna nedan visar hur du gör.

Den här självstudien skapas ett nytt projekt. Om du vill ladda ned den slutförda självstudien i stället [ladda ned koden](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Xcode och välj **skapa ett nytt Xcode-projekt**.
2. Välj **iOS** > **vy App** och välj **nästa**.
3. Ange ett produktnamn.
4. Ange den **språk** till **Swift** och välj **nästa**.
5. Välj en mapp för att skapa din app och klicka på **skapa**.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure Portal](https://aka.ms/MobileAppReg)
2. Öppna den [registreringar appbladet](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
3. Ange en **namn** för din app och klicka sedan på, utan att ange en omdirigerings-URI **registrera**.
4. I den **hantera** i fönstret som visas, Välj **autentisering**.
5. Klicka på **testa den nya upplevelsen** överst på skärmen för att öppna den nya app-upplevelsen för registrering och klicka sedan på **+ ny registrering** >  **+ Lägg till en plattform**  >  **iOS**.
    - Ange ditt projekt programpaket-ID. Om du har hämtat koden, är detta `com.microsoft.identitysample.MSALiOS`. Om du skapar dina egna projekt, väljer du ditt projekt i Xcode och öppna den **Allmänt** fliken. Paket-ID som visas i den **identitet** avsnittet.
6. Klicka på `Configure` och spara den **MSAL Configuration** som visas i den **iOS configuration** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

## <a name="add-msal"></a>Lägg till MSAL

Välj något av följande sätt att installera MSAL biblioteket i din app:

### <a name="cocoapods"></a>CocoaPods

1. Om du använder [CocoaPods](https://cocoapods.org/), installera `MSAL` genom att först skapa en tom fil kallas `podfile` i samma mapp som ditt projekt `.xcodeproj` fil. Lägg till följande till `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Ersätt `<your-target-here>` med namnet på ditt projekt.
3. I terminalfönstret, navigera till mappen som innehåller den `podfile` du skapat och kör `pod install` installera MSAL-biblioteket.
4. Stäng Xcode och öppna `<your project name>.xcworkspace` läses in på nytt projekt i Xcode.

### <a name="carthage"></a>Carthage

Om du använder [Carthage](https://github.com/Carthage/Carthage), installera `MSAL` genom att lägga till din `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Manuellt

Du kan också använda Git Submodule eller Kolla in den senaste versionen ska användas som ett ramverk i ditt program.

## <a name="add-your-app-registration"></a>Lägg till appregistrering

Därefter lägger vi till din appregistrering till din kod. 

Lägg först till följande importuttryck längst upp i den `ViewController.swift` och `AppDelegate.swift` filer:

```swift
import MSAL
```

Lägg sedan till följande kod i `ViewController.swift` före `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Ändra värdet som tilldelas `kClientID`ska ditt program-ID. Det här värdet är en del av MSAL konfigurationsdata som du sparade under steget i början av den här kursen och registrera programmet i Azure-portalen.

## <a name="configure-url-schemes"></a>Konfigurera URL-scheman

I det här steget ska du registrera `CFBundleURLSchemes` så att användaren kan omdirigeras tillbaka till appen efter inloggningen. Förresten, `LSApplicationQueriesSchemes` tillåter även appen att använda Microsoft Authenticator.

I Xcode öppnar `Info.plist` som en källkoden filen och Lägg till följande inuti den `<dict>` avsnittet. Ersätt `[BUNDLE_ID]` med värdet som du använde i Azure-portalen som om du har hämtat koden är `com.microsoft.identitysample.MSALiOS`. Om du skapar dina egna projekt, väljer du ditt projekt i Xcode och öppna den **Allmänt** fliken. Paket-ID som visas i den **identitet** avsnittet.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Skapa din app-gränssnitt

Nu skapar ett gränssnitt som innehåller en knapp för att anropa Microsoft Graph API, en annan för att logga ut, och en text visar för att se några utdata genom att lägga till följande kod för att den `ViewController`klass:

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

Nästa också inuti den `ViewController` klass, ersätter den `viewDidLoad()` metoden med:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Använd MSAL

### <a name="initialize-msal"></a>Initiera MSAL

Lägg till följande `InitMSAL` metod för att den `ViewController` klass:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>Hantera motringningen inloggning

Öppna filen `AppDelegate.swift`. Om du vill hantera motringningen efter inloggning, lägger du till `MSALPublicClientApplication.handleMSALResponse` till den `appDelegate` klass så här:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Hämta token

Nu kan vi implementera standardbearbetningslogiken för programmets användargränssnitt och hämta token interaktivt via MSAL.

MSAL visar två huvudsakliga sätt för att hämta token: `acquireTokenSilently()` och `acquireTokenInteractively()`: 

- `acquireTokenSilently()` försöker att logga in en användare och hämta token utan någon användarinteraktion, så länge det finns ett konto.

- `acquireTokenInteractively()` Visar alltid Användargränssnittet när du försöker logga in användaren. Det kan använda cookies i webbläsaren eller ett konto i Microsoft authenticator för att tillhandahålla interaktiva enkel inloggning.

Lägg till följande kod till den `ViewController` klass:

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Hämta en token interaktivt

Koden nedan hämtar en token för första gången genom att skapa en `MSALInteractiveTokenParameters` objektet och anropa `acquireToken`. Sedan lägger du till kod som:

1. Skapar `MSALInteractiveTokenParameters` med omfång.
2. Anrop `acquireToken()` med skapade parametrar.
3. Hantera fel. Mer information finns i den [iOS felhantering guiden](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Hanterar lyckad fallet.

Lägg till följande kod till den `ViewController` klass.

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>Hämta en token tyst

Lägg till följande kod för att hämta en uppdaterad token tyst den `ViewController` klass. Den skapar en `MSALSilentTokenParameters` objekt och anropar `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Anropa Microsoft Graph API 

När du har en token kan kan din app använda HTTP-huvud och gör en auktoriserad begäran för Microsoft Graph:

| Huvud-nyckel    | value                 |
| ------------- | --------------------- |
| Authorization | Ägar \<åtkomst-token > |

Lägg till följande kod till den `ViewController` klass:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Se [Microsoft Graph API](https://graph.microsoft.com) mer information om Microsoft Graph API.

### <a name="use-msal-for-sign-out"></a>Använder MSAL för utloggning

Lägg sedan till stöd för utloggning.

> [!Important]
> Logga ut med MSAL tar bort alla kända information om en användare från programmet, men användaren fortfarande har en aktiv session på sin enhet. Om användaren misslyckas att logga in igen de kan se Användargränssnittet för inloggning, men kanske inte behöver ange sina autentiseringsuppgifter igen eftersom sessionen enheten fortfarande är aktiv.

Lägg till utloggning kapaciteten genom att lägga till följande kod i den `ViewController` klass. Den här metoden går igenom alla konton och tar bort dem:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Aktivera cachelagring för token

Som standard cachelagrar MSAL din Apps tokens i iOS-nyckelringen. 

Aktivera cachelagring av token:
1. Gå till projektinställningarna Xcode > **funktioner fliken** > **aktivera delning av nyckelringar**
2. Klicka på **+** och ange `com.microsoft.adalcache` som en **Nyckelringsgrupper** posten.

### <a name="add-helper-methods"></a>Lägg till hjälpmetoder

Lägg till följande helper-metoder till den `ViewController` klassen för att slutföra exemplet:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>Konto för flera program

Den här appen har skapats för ett enda konto-scenario. MSAL stöder också flera konto scenarier, men det krävs några ytterligare arbete från appar. Du måste skapa gränssnitt för att användarens Välj vilket konto som de vill använda för varje åtgärd som kräver token. Din app kan också implementera en tumregel för att välja vilket konto som ska ta emot via den `getAccounts()` metoden.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Skapa och distribuera appen till en testenhet eller emulator. Du ska kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

Första gången en användare loggar in på din app, uppmanas de av Microsoft identity samtycker till att de behörigheter som begärdes.  De flesta användare är kapabel att samtycka, har vissa Azure AD-klienter inaktiverat tillstånd, vilket kräver att administratörer kan ge samtycke åt alla användare. För det här scenariot måste registrera din app omfång i Azure-portalen.

När du har loggat in visas de data som returneras från Microsoft Graph `/me` slutpunkt.

## <a name="get-help"></a>Få hjälp

Besök [hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här självstudien eller med Microsoft identity-plattformen.
