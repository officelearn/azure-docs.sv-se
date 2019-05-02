---
title: Komma igång med iOS – Microsoft identity-plattformen | Azure
description: Hur iOS (Swift)-program kan anropa ett API som kräver åtkomsttoken med Microsoft identity-plattformen
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e84d97a4ef389981fa30aa08433fef1662367ae
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935954"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Logga in användare och anropa Microsoft Graph från en iOS-app

I de här självstudierna lär du dig att bygga ett iOS-program och integrerar dem i Microsoft identity-plattformen. Mer specifikt ska den här appen logga in en användare få en åtkomsttoken att anropa Microsoft Graph API och gör en grundläggande begäran för Microsoft Graph API.  

När du har slutfört guiden för ditt program ska ta emot inloggningar för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbete eller skola konton från alla företag eller organisation som använder Azure Active Directory.

## <a name="how-this-guide-works"></a>Så här fungerar den här guiden

![Visar hur exempelapp som genererats av den här kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Appen i det här exemplet ska logga in användare och hämta data å deras vägnar.  Dessa data kan nås via en skyddad API (Microsoft Graph API i det här fallet) som kräver auktorisering och skyddas också av Microsoft identity-plattformen.

Mer specifikt:

* Din app ska logga in användaren antingen via en webbläsare eller Microsoft Authenticator.
* Användaren kommer att acceptera de behörigheter som programmet har begärt. 
* Din app kommer att utfärdas en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API.
* Process för Microsoft Graph-svaret.

Det här exemplet använder Microsoft Authentication library (MSAL) för att implementera autentisering. MSAL kommer automatiskt förnya token, leverera enkel inloggning mellan andra appar på enheten och hantera konton.

## <a name="prerequisites"></a>Nödvändiga komponenter

- XCode version 10.x krävs för exemplet som skapas i den här guiden. Du kan hämta XCode från den [iTunes webbplats](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode hämta URL: en").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Du kan använda Beroendehanteraren eller lägga till manuellt. Det finns avsnittet nedan med [mer](#add-msal). 

## <a name="set-up-your-project"></a>Konfigurera projektet

Den här självstudien skapas ett nytt projekt. Om du vill ladda ned den slutförda självstudien i stället [ladda ned koden](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Xcode och välj **skapa ett nytt Xcode-projekt**.
2. Välj **iOS > vy programmet** och välj **nästa**.
3. Ge ett produktnamn och välj **nästa**.
4. Välj en mapp för att skapa din app och klicka på *skapa*.

## <a name="register-your-application"></a>Registrera ditt program 

Du kan registrera programmet i något av två sätt, enligt beskrivningen i följande två avsnitt.

### <a name="register-your-app"></a>Registrera din app

1. Gå till den [Azure-portalen](https://aka.ms/MobileAppReg) > Välj `New registration`. 
2. Ange en **namn** för din app > `Register`. **Ange inte en omdirigerings-URI i det här skedet**. 
3. I den `Manage` avsnittet, gå till `Authentication` > `Add a platform` > `iOS`
    - Ange ditt projekt programpaket-ID. Om du har hämtat koden, är detta `com.microsoft.identitysample.MSALiOS`.
4. Tryck på `Configure` och lagra den `MSAL Configuration` till senare. 

## <a name="add-msal"></a>Lägg till MSAL

### <a name="get-msal"></a>Get MSAL

#### <a name="cocoapods"></a>CocoaPods

Du kan använda [CocoaPods](http://cocoapods.org/) installera `MSAL` genom att lägga till din `Podfile` under mål:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Du kan använda [Carthage](https://github.com/Carthage/Carthage) installera `MSAL` genom att lägga till din `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manuellt

Du kan också använda Git Submodule eller Kolla in den senaste versionen och Använd som framework i ditt program.

### <a name="add-your-app-registration"></a>Lägg till appregistrering

Lägg din appregistrering i koden. Lägg till din ***klient / program-ID*** till `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Konfigurera URL-scheman

Registrera `CFBundleURLSchemes` så att en motringning så att användaren kan omdirigeras tillbaka till appen efter inloggningen.

`LSApplicationQueriesSchemes` gör med din app för att använda Microsoft Authenticator om det är tillgängligt. 

Gör detta genom att öppna `Info.plist` som en källa code och Lägg till följande, byta ut den ***BUNDLE_ID*** med vad du har konfigurerat i Azure-portalen.

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

### <a name="import-msal"></a>Importera MSAL

Importera MSAL framework i `ViewController.swift` och `AppDelegate.swift` filer:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Skapa din app-gränssnitt

Den här självstudien måste du skapa:

- Anropa Graph API-knappen
- Logga ut knappen
- Loggning textview

I `ViewController.swift`, definiera egenskaper och `initUI()` på följande sätt:

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

Lägg till `viewDidLoad()` av ViewController.swift:

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

Först måste du skapa en `MSALPublicClientApplication` med en instans av `MSALPublicClientConfiguration` för ditt program:

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

### <a name="handle-the-callback"></a>Hantera motringningen

Om du vill hantera motringningen efter inloggning, lägger du till `MSALPublicClientApplication.handleMSALResponse` i `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Hämta token

Nu kan vi implementera programmets användargränssnitt bearbetningslogiken och hämta token interaktivt via MSAL. 

MSAL visar två huvudsakliga sätt för att hämta token: `acquireTokenSilently` och `acquireTokenInteractively`.  

`acquireTokenSilently()` försöker att logga in en användare och hämta token utan någon användarinteraktion om ett konto finns.

`acquireTokenInteractively` alltid visa Användargränssnittet när du försöker logga in användaren och hämta token; dock kan den använda cookies i webbläsaren eller ett konto i Microsoft authenticator för att ge en interaktiv SSO-upplevelse. 

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

Om du vill hämta en token för första gången behöver du skapa en `MSALInteractiveTokenParameters` och anropa `acquireToken`.

1. Skapa `MSALInteractiveTokenParameters` med omfång.
2. Anropa `acquireToken` med parametrarna som skapats.
3. Hantera fel på lämpligt sätt. Mer information finns i den [iOS felhantering guiden](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Hantera lyckad fallet. 

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

Om du vill hämta en uppdaterad token tyst, behöver du skapa en `MSALSilentTokenParameters` och anropa `acquireTokenSilent`:

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

När du har en token via `self.accessToken`, din app kan använda det här värdet i HTTP-huvud för att göra en auktoriserad begäran till Microsoft Graph:

| Huvud-nyckel    | value                 |
| ------------- | --------------------- |
| Auktorisering | Ägar < åtkomsttoken > |

Lägg till följande till `ViewController.swift`:

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

Läs mer om den [Microsoft Graph API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Använder MSAL för utloggning

Därefter och vi lägger till stöd för utloggning i vår app. 

Det är viktigt att Observera att logga ut med MSAL tar bort alla kända information om en användare från det här programmet, men användaren fortfarande har en aktiv session på sin enhet. Om användaren misslyckas att logga in igen de kan se interaktion, men kanske inte behöver ange sina autentiseringsuppgifter på grund av enheten sessionen som aktiv igen. 

Om du vill lägga till logga ut, kopiera följande metod i din `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
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

Om du vill aktivera tokencachelagring, gå till projektinställningarna Xcode > `Capabilities tab`  >  `Enable Keychain Sharing` > klickar du på `Plus` > RETUR **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Lägg till hjälpmetoder

Lägg till dessa hjälpmetoder för att slutföra exemplet:

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

Den här appen har skapats för ett enda konto-scenario. MSAL stöder också flera konto-scenarier, men det krävs några ytterligare arbete från appar. Du måste skapa gränssnitt för att användarens Välj vilket konto som de vill använda för varje åtgärd som kräver token. Din app kan också implementera en tumregel för att välja vilket konto som ska ta emot via den `getAllAccounts(...)` metoden.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Om du har följt av koden ovan, försök att skapa och distribuera appen till en testenhet eller emulator. Du ska kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton! När en användare loggar in, den här appen visar data som returneras från Microsoft Graph `/me` slutpunkt. 

Passa på att öppna ett ärende i det här dokumentet eller i biblioteket MSAL och berätta för oss om du har problem. 

### <a name="consent-to-your-app"></a>Godkänna din app

Första gången en användare loggar in på din app, uppmanas de av Microsoft identity samtycker till att de behörigheter som begärdes.  De flesta användare är kapabel att samtycka, har vissa Azure AD-klienter inaktiverat användargodkännande - att kräva att administratörer kan ge samtycke åt alla användare.  För att stödja det här scenariot måste du registrera din app omfång i Azure-portalen.

## <a name="help-and-support"></a>Hjälp och support

Hade problem med den här självstudien eller med Microsoft identity-plattformen? Se [hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

