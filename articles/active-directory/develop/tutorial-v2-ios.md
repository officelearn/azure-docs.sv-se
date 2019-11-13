---
title: Komma igång med iOS och macOS – Microsoft Identity Platform | Azure
description: Hur iOS-och macOS-program (SWIFT) kan anropa ett API som kräver åtkomsttoken med hjälp av Microsoft Identity Platform
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e3892a03ffe097a51f294e698168f00e1359f92
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960670"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Logga in användare och anropa Microsoft Graph från en iOS-eller macOS-app

I den här självstudien får du lära dig hur du integrerar en iOS-eller macOS-app med Microsoft Identity Platform. Appen loggar in en användare, får en åtkomsttoken för att anropa Microsoft Graph-API: et och gör en begäran till Microsoft Graph API.  

När du har slutfört guiden kommer ditt program att godkänna inloggningar med personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hur den här självstudien fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Appen i den här självstudien kommer att logga in användare och hämta data för deras räkning.  Dessa data kommer att nås via ett skyddat API (Microsoft Graph-API i det här fallet) som kräver auktorisering och skyddas av Microsoft Identity Platform.

Mer specifikt:

* Din app kommer att logga in användaren antingen via en webbläsare eller Microsoft Authenticator.
* Slutanvändaren kommer att godkänna de behörigheter som programmet har begärt.
* Din app kommer att utfärda en åtkomsttoken för Microsoft Graph-API: et.
* Åtkomsttoken tas med i HTTP-begäran till webb-API: et.
* Bearbeta Microsoft Graph svaret.

I det här exemplet används Microsoft Authentication Library (MSAL) för att implementera autentisering. MSAL förnyar automatiskt token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

Den här självstudien gäller för både iOS-och macOS-appar. Observera att vissa steg skiljer sig mellan de två plattformarna. 

## <a name="prerequisites"></a>Krav

- XCode version 10. x krävs för att bygga appen i den här hand boken. Du kan hämta XCode från [iTunes-webbplatsen](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Nedladdnings-URL för XCode").
- Microsoft Authentication Library ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Du kan använda en beroende hanterare eller lägga till biblioteket manuellt. Anvisningarna nedan visar hur du gör.

I den här kursen skapas ett nytt projekt. Om du vill hämta den slutförda självstudien i stället laddar du ned koden:
- [exempel kod för iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS-exempel kod](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Xcode och välj **skapa ett nytt Xcode-projekt**.
2. För iOS-appar väljer du **ios** > **enskild View-app** och väljer **Nästa**.
3. För macOS-appar väljer du **macos** > **kakao-app** och väljer **sedan nästa**.
4. Ange ett produkt namn.
5. Ange **språket** till **Swift** och välj **Nästa**.
6. Välj en mapp för att skapa din app och klicka på **skapa**.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure Portal](https://aka.ms/MobileAppReg)
2. Öppna [bladet Appregistreringar](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
3. Ange ett **namn** för din app och klicka sedan på **Registrera**utan att ange en omdirigerings-URI.
4. I avsnittet **Hantera** i fönstret som visas väljer du **autentisering**.

5. Klicka på **testa den nya upplevelsen** längst upp på skärmen för att öppna den nya appens registrerings upplevelse och klicka sedan på **+ ny registrering** >  **+ Lägg till en plattform** > **iOS**.
    - Ange ditt projekts paket-ID. Om du har hämtat koden är det `com.microsoft.identitysample.MSALiOS`. Om du skapar ett eget projekt väljer du ditt projekt i Xcode och öppnar fliken **Allmänt** . Paket-ID visas i avsnittet **identitet** .
    - Observera att för macOS bör du även använda iOS-upplevelse. 
6. Klicka på `Configure` och spara **MSAL-konfigurationen** som visas på sidan **iOS-konfiguration** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

## <a name="add-msal"></a>Lägg till MSAL

Välj ett av följande sätt för att installera MSAL-biblioteket i appen:

### <a name="cocoapods"></a>CocoaPods

1. Om du använder [CocoaPods](https://cocoapods.org/)installerar du `MSAL` genom att först skapa en tom fil med namnet `podfile` i samma mapp som projektets `.xcodeproj`-fil. Lägg till följande i `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Ersätt `<your-target-here>` med namnet på ditt projekt.
3. I ett terminalfönster navigerar du till mappen som innehåller `podfile` som du skapade och kör `pod install` för att installera MSAL-biblioteket.
4. Stäng Xcode och öppna `<your project name>.xcworkspace` för att läsa in projektet på nytt i Xcode.

### <a name="carthage"></a>Carthage

Om du använder [Carthage](https://github.com/Carthage/Carthage)installerar du `MSAL` genom att lägga till den i `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Från ett terminalfönster, i samma katalog som den uppdaterade `Cartfile`, kör du följande kommando för att låta Carthage uppdatera beroendena i projektet.

iOS:

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuellt

Du kan också använda Git-undermodulen eller ta en titt på den senaste versionen för användning som ett ramverk i ditt program.

## <a name="add-your-app-registration"></a>Lägg till din app-registrering

Nu ska vi lägga till din app-registrering i koden. 

Lägg först till följande import-instruktion överst i `ViewController.swift` och `AppDelegate.swift` filer:

```swift
import MSAL
```

Lägg sedan till följande kod för att `ViewController.swift` före `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

Det enda värde du behöver ändra ovan är värdet som tilldelas `kClientID`som [program-ID](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Det här värdet är en del av de konfigurations data för MSAL som du sparade under steget i början av den här självstudien för att registrera programmet i Azure Portal.

## <a name="for-ios-only-configure-url-schemes"></a>Konfigurera URL-scheman enbart för iOS

I det här steget ska du registrera `CFBundleURLSchemes` så att användaren kan omdirigeras tillbaka till appen efter inloggning. På det här sättet kan `LSApplicationQueriesSchemes` även tillåta att din app använder Microsoft Authenticator.

Öppna `Info.plist` som en käll kods fil i Xcode och Lägg till följande i avsnittet `<dict>`. Ersätt `[BUNDLE_ID]` med det värde som du använde i Azure Portal som, om du har hämtat koden, är `com.microsoft.identitysample.MSALiOS`. Om du skapar ett eget projekt väljer du ditt projekt i Xcode och öppnar fliken **Allmänt** . Paket-ID visas i avsnittet **identitet** .

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
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Konfigurera begränsat läge för enbart macOS

1. Gå till Xcode-projekt inställningar **fliken > funktioner** > **app sandbox**
2. Kryss rutan Välj **utgående anslutningar (klient)** . 

## <a name="create-your-apps-ui"></a>Skapa appens användar gränssnitt

Nu ska du skapa ett användar gränssnitt som innehåller en knapp för att anropa Microsoft Graph API, en annan för att logga ut och en datavy för att se vissa utdata genom att lägga till följande kod i `ViewController`-klassen:

### <a name="ios-ui"></a>iOS-användargränssnitt

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

### <a name="macos-ui"></a>macOS-användargränssnitt

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

I `ViewController`-klassen ersätter du sedan `viewDidLoad()`-metoden med:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Använd MSAL

### <a name="initialize-msal"></a>Initiera MSAL

Lägg till följande `initMSAL` metod i `ViewController`-klassen:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Lägg till följande efter `initMSAL`-metoden i `ViewController`-klassen.

### <a name="ios-code"></a>iOS-kod:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kod:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Hantera inloggnings återanropet endast för iOS

Öppna filen `AppDelegate.swift`. Om du vill hantera återanropet efter inloggning lägger du till `MSALPublicClientApplication.handleMSALResponse` i `appDelegate`-klassen så här:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Om du använder Xcode 11**bör du placera MSAL motringning i `SceneDelegate.swift` i stället.
Om du har stöd för både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS måste MSAL-återanropet placeras i båda filerna.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Hämta token

Nu kan vi implementera programmets GRÄNSSNITTs bearbetnings logik och hämta token interaktivt via MSAL.

MSAL exponerar två primära metoder för att hämta tokens: `acquireTokenSilently()` och `acquireTokenInteractively()`: 

- `acquireTokenSilently()` försöker logga in en användare och hämta tokens utan användar interaktion så länge ett konto finns.

- `acquireTokenInteractively()` visar alltid gränssnitt när användaren försöker logga in användaren. Den kan använda sessionscookies i webbläsaren eller ett konto i Microsoft Authenticator för att tillhandahålla en interaktiv SSO-upplevelse.

Lägg till följande kod i `ViewController`-klassen:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
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

#### <a name="get-a-token-interactively"></a>Få en token interaktivt

Koden nedan hämtar en token för första gången genom att skapa ett `MSALInteractiveTokenParameters`-objekt och anropa `acquireToken`. Härnäst ska du lägga till kod som:

1. Skapar `MSALInteractiveTokenParameters` med omfång.
2. Anropar `acquireToken()` med de skapade parametrarna.
3. Hanterar fel. Mer information finns i [MSAL för iOS-och MacOS-fel hanterings guide](msal-handling-exceptions.md).
4. Hanterar det framgångna ärendet.

Lägg till följande kod i `ViewController`-klassen.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
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

Om du vill hämta en uppdaterad token i bakgrunden lägger du till följande kod i `ViewController`-klassen. Det skapar ett `MSALSilentTokenParameters` objekt och anropar `acquireTokenSilent()`:

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

### <a name="call-the-microsoft-graph-api"></a>Anropa API: et för Microsoft Graph 

När du har en token kan appen använda den i HTTP-huvudet för att skapa en auktoriserad begäran till Microsoft Graph:

| rubrik nyckel    | värde                 |
| ------------- | --------------------- |
| Auktorisering | \<åtkomst-token för innehavare > |

Lägg till följande kod i `ViewController`-klassen:

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

Läs mer om Microsoft Graph API i [Microsoft Graph API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Använda MSAL för utloggning

Lägg sedan till stöd för utloggning.

> [!Important]
> Om du loggar ut med MSAL tas all känd information om en användare bort från programmet, men användaren kan fortfarande ha en aktiv session på sin enhet. Om användaren försöker logga in igen kan de se inloggnings gränssnittet, men kanske inte behöver ange sina autentiseringsuppgifter på nytt eftersom sessionen fortfarande är aktiv.

Lägg till en utloggnings funktion genom att lägga till följande kod inuti `ViewController`-klassen. Den här metoden går igenom alla konton och tar bort dem:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Aktivera cachelagring av token

Som standard cachelagrar MSAL appens tokens i nyckel ringen för iOS eller macOS. 

Så här aktiverar du cachelagring av token:
1. Se till att ditt program är korrekt signerat
2. Gå till Xcode-projekt inställningar **fliken > funktioner** > **Aktivera delning av nyckel ringar**
3. Klicka på **+** och ange följande **grupper för nyckel ringar** : 3. a för iOS, ange `com.microsoft.adalcache` 3. b för MacOS Enter `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Lägga till hjälp metoder
Slutför exemplet genom att lägga till följande hjälp metoder i `ViewController`-klassen.

### <a name="ios-ui"></a>iOS-gränssnitt:

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

### <a name="macos-ui"></a>macOS-gränssnitt:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Program med flera konton

Den här appen har skapats för ett scenario med ett enda konto. MSAL har också stöd för flera konto scenarier, men det krävs ytterligare arbete från appar. Du måste skapa ett användar gränssnitt för att hjälpa användarna att välja vilket konto de vill använda för varje åtgärd som kräver tokens. Alternativt kan din app implementera en heuristik för att välja vilket konto som ska användas via metoden `getAccounts()`.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Bygg och distribuera appen till en test enhet eller Simulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

Första gången en användare loggar in på din app uppmanas de av Microsoft-identiteten att godkänna de behörigheter som begärs.  Även om de flesta användare kan samtycka har vissa Azure AD-klienter inaktiverat användar medgivande, vilket kräver att administratörer samtycker till alla användares räkning. För att stödja det här scenariot registrerar du appens scope i Azure Portal.

När du har loggat in visar appen de data som returneras från Microsoft Graph `/me`-slutpunkten.

## <a name="get-help"></a>Få hjälp

Gå till [Hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här själv studie kursen eller med Microsoft Identity Platform.

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
