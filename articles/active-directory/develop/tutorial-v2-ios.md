---
title: MSAL för iOS & macOS tutorial - Microsoft identity platform | Azure
description: Lär dig hur iOS- och macOS-appar (Swift) kan anropa ett API som kräver åtkomsttoken med Microsofts identitetsplattform
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abf083aacbdc643d780a8061b405752f36e27e45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129935"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Logga in användare och anropa Microsoft Graph från en iOS- eller macOS-app

I den här självstudien får du lära dig hur du integrerar en iOS- eller macOS-app med Microsofts identitetsplattform. Appen loggar in en användare, får en åtkomsttoken för att anropa Microsoft Graph API och göra en begäran till Microsoft Graph API.  

När du har slutfört guiden accepterar programmet inloggningar av personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets- eller skolkonton från alla företag eller organisationer som använder Azure Active Directory.

>[!NOTE]
> Om du inte har gjort det tidigare på Microsofts identitetsplattform rekommenderar vi att du börjar med [inloggningsanvändarna och anropar Microsoft Graph API från en iOS- eller macOS-app](quickstart-v2-ios.md).

## <a name="how-this-tutorial-works"></a>Så här fungerar den här självstudien

![Visar hur exempelappen som genereras av den här självstudien fungerar](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Appen i den här självstudien loggar in användare och hämtar data för deras räkning.  Dessa data kommer att nås via ett skyddat API (Microsoft Graph API i det här fallet) som kräver auktorisering och skyddas av Microsofts identitetsplattform.

Mer specifikt:

* Appen loggar in användaren antingen via en webbläsare eller i Microsoft Authenticator.
* Slutanvändaren accepterar de behörigheter som ditt program har begärt.
* Din app får en åtkomsttoken för Microsoft Graph API.
* Åtkomsttoken inkluderas i HTTP-begäran till webb-API:et.
* Bearbeta Microsoft Graph-svaret.

I det här exemplet används Microsoft Authentication-biblioteket (MSAL) för att implementera autentisering. MSAL förnyar automatiskt token, levererar enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

Den här självstudien gäller både iOS- och macOS-appar. Observera att vissa steg skiljer sig mellan dessa två plattformar. 

## <a name="prerequisites"></a>Krav

- XCode version 10.x eller mer krävs för att bygga appen i den här guiden. Du kan ladda ner XCode från [iTunes webbplats](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Url för XCode-hämtning").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Du kan använda en beroendehanterare eller lägga till biblioteket manuellt. Instruktionerna nedan visar hur du gör.

Den här självstudien skapar ett nytt projekt. Om du vill hämta den färdiga självstudien i stället laddar du ner koden:
- [iOS-exempelkod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [exempelkod för macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Xcode och välj **Skapa ett nytt Xcode-projekt**.
2. För iOS-appar väljer du **iOS** > **Single view App** och väljer **Nästa**.
3. För macOS-appar väljer du **macOS** > **Cocoa App** och väljer **Nästa**.
4. Ange ett produktnamn.
5. Ställ in **språket** på **Swift** och välj **Nästa**.
6. Markera en mapp för att skapa appen och klicka på **Skapa**.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure Portal](https://aka.ms/MobileAppReg)
2. Öppna [bladet Appregistreringar](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+Ny registrering**.
3. Ange ett **namn** för din app och klicka sedan på **Registrera**utan att ange en omdirigera URI.
4. Välj **Autentisering**i avsnittet **Hantera** i fönstret som visas.

5. Klicka på **Prova den nya upplevelsen** högst upp på skärmen för att öppna den nya appregistreringsupplevelsen och klicka sedan på **+Ny registrering** > **+ Lägg till en plattform** > **iOS**.
    - Ange projektets bunt-ID. Om du hämtade koden är `com.microsoft.identitysample.MSALiOS`det här . Om du skapar ett eget projekt väljer du projektet i Xcode och öppnar fliken **Allmänt.** Buntidentifieraren visas i avsnittet **Identitet.**
    - Observera att för macOS bör du också använda iOS-upplevelse. 
6. Klicka `Configure` och spara **MSAL-konfigurationen** som visas på **konfigurationssidan för iOS** så att du kan ange den när du konfigurerar appen senare.  Klicka på **Klar**.

## <a name="add-msal"></a>Lägg till MSAL

Välj något av följande sätt att installera MSAL-biblioteket i appen:

### <a name="cocoapods"></a>CocoaPods (CocoaPods)

1. Om du använder [CocoaPods](https://cocoapods.org/) `MSAL` installerar du genom att `podfile` först skapa en tom `.xcodeproj` fil som anropas i samma mapp som projektets fil. Lägg till `podfile`följande i:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Ersätt `<your-target-here>` med namnet på projektet.
3. I ett terminalfönster navigerar du till `podfile` mappen som `pod install` innehåller den du skapade och kör för att installera MSAL-biblioteket.
4. Stäng Xcode `<your project name>.xcworkspace` och öppna för att ladda om projektet i Xcode.

### <a name="carthage"></a>Karthago

Om du använder [Carthage](https://github.com/Carthage/Carthage) `MSAL` installerar du det `Cartfile`genom att lägga till det i:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Från ett terminalfönster, i samma `Cartfile`katalog som den uppdaterade , kör följande kommando för att få Karthage att uppdatera beroendena i projektet.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuellt

Du kan också använda Git Submodule eller kolla in den senaste versionen som ska användas som ramverk i ditt program.

## <a name="add-your-app-registration"></a>Lägga till din appregistrering

Därefter lägger vi till din appregistrering i din kod. 

Lägg först till följande importsats längst `ViewController.swift` `AppDelegate.swift` upp i filerna och:

```swift
import MSAL
```

Lägg sedan till `ViewController.swift` följande `viewDidLoad()`kod före:

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

Det enda värde du behöver ändra ovan `kClientID`är det värde som tilldelats för att vara ditt [program-ID](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Det här värdet är en del av MSAL-konfigurationsdata som du sparade under steget i början av den här självstudien för att registrera programmet i Azure-portalen.

## <a name="for-ios-only-configure-url-schemes"></a>Endast för iOS konfigurerar du URL-scheman

I det här steget `CFBundleURLSchemes` registrerar du dig så att användaren kan omdirigeras tillbaka till appen efter inloggning. Förresten, `LSApplicationQueriesSchemes` gör det också möjligt för din app att använda Microsoft Authenticator.

Öppna `Info.plist` som en källkodsfil i Xcode och lägg `<dict>` till följande i avsnittet. Ersätt `[BUNDLE_ID]` med det värde som du använde i Azure-portalen `com.microsoft.identitysample.MSALiOS`som, om du hämtade koden, är . Om du skapar ett eget projekt väljer du projektet i Xcode och öppnar fliken **Allmänt.** Buntidentifieraren visas i avsnittet **Identitet.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Endast för macOS konfigurerar du Appsandlåda

1. Gå till fliken Xcode Project Settings > **Capabilities** > **App Sandbox**
2. Välj kryssrutan **Utgående anslutningar (klient).** 

## <a name="create-your-apps-ui"></a>Skapa appens användargränssnitt

Skapa nu ett användargränssnitt som innehåller en knapp för att anropa Microsoft Graph API, en annan för `ViewController`att logga ut och en textvy för att se några utdata genom att lägga till följande kod i klassen:

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

Därefter, även `ViewController` inuti klassen, `viewDidLoad()` ersätter metoden med:

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

Lägg till `initMSAL` följande `ViewController` metod i klassen:

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

Lägg till `initMSAL` följande efter `ViewController` metod i klassen.

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

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Endast för iOS hanterar du motringningen för inloggning

Öppna filen `AppDelegate.swift`. Om du vill hantera motringningen `MSALPublicClientApplication.handleMSALResponse` efter `appDelegate` inloggning lägger du till i klassen så här:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Om du använder Xcode 11**bör du placera `SceneDelegate.swift` MSAL-motringning i stället.
Om du stöder både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS, måste MSAL-motringning placeras i båda filerna.

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

Nu kan vi implementera programmets UI-bearbetningslogik och få tokens interaktivt via MSAL.

MSAL exponerar två primära metoder `acquireTokenSilently()` för `acquireTokenInteractively()`att hämta token: och: 

- `acquireTokenSilently()`försöker logga in en användare och hämta token utan någon användarinteraktion så länge ett konto finns.

- `acquireTokenInteractively()`visar alltid användargränssnittet när du försöker logga in användaren. Den kan använda sessionscookies i webbläsaren eller ett konto i Microsoft-autentiseraren för att tillhandahålla en interaktiv SSO-upplevelse.

Lägg till följande `ViewController` kod i klassen:

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

#### <a name="get-a-token-interactively"></a>Skaffa en token interaktivt

Koden nedan hämtar en token för första `MSALInteractiveTokenParameters` gången `acquireToken`genom att skapa ett objekt och anropa . Därefter lägger du till kod som:

1. Skapar `MSALInteractiveTokenParameters` med scope.
2. Anrop `acquireToken()` med de skapade parametrarna.
3. Hanterar fel. Mer information finns i [MSAL för iOS- och macOS-felhanteringsguiden](msal-handling-exceptions.md).
4. Hanterar det lyckade fallet.

Lägg till följande `ViewController` kod i klassen.

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


#### <a name="get-a-token-silently"></a>Få en token tyst

Om du vill hämta en uppdaterad token `ViewController` tyst lägger du till följande kod i klassen. Det skapar `MSALSilentTokenParameters` ett objekt `acquireTokenSilent()`och anrop:

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

När du har en token kan appen använda den i HTTP-huvudet för att göra en auktoriserad begäran till Microsoft Graph:

| sidhuvudtangent    | värde                 |
| ------------- | --------------------- |
| Auktorisering | Bärare \<åtkomst-token> |

Lägg till följande `ViewController` kod i klassen:

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

Mer information om Microsoft Graph API finns i [Microsoft Graph API.](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Använda MSAL för ut signering

Lägg sedan till stöd för ut logga ut.

> [!Important]
> Om du loggar ut med MSAL tas all känd information om en användare bort från programmet, men användaren har fortfarande en aktiv session på sin enhet. Om användaren försöker logga in igen kan de se inloggningsgränssnittet, men kanske inte behöver ange sina autentiseringsuppgifter igen eftersom enhetssessionen fortfarande är aktiv.

Om du vill lägga till ut signeringsfunktioner lägger du till följande kod i `ViewController` klassen. Den här metoden går igenom alla konton och tar bort dem:

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

Som standard cachelagrar MSAL appens token i iOS- eller macOS-nyckelringen. 

Så här aktiverar du tokencachelagring:
1. Se till att din ansökan är korrekt signerad
2. Gå till fliken > Xcode Project Settings > **Capabilities**Aktivera**nyckelringsdelning**
3. Klicka **+** och ange följande post **för nyckelringsgrupper:** 3.a För iOS anger du `com.microsoft.adalcache` 3.b för macOS-inmatning`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Lägg till hjälpmetoder
Lägg till följande hjälpmetoder `ViewController` i klassen för att slutföra exemplet.

### <a name="ios-ui"></a>iOS-användargränssnittet:

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

### <a name="macos-ui"></a>macOS-användargränssnittet:

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



### <a name="multi-account-applications"></a>Program för flera konton

Den här appen är byggd för ett scenario med ett enda konto. MSAL stöder också scenarier för flera konton, men det kräver lite extra arbete från appar. Du måste skapa användargränssnitt för att hjälpa användarna att välja vilket konto de vill använda för varje åtgärd som kräver token. Alternativt kan din app implementera en heuristisk för att `getAccounts()` välja vilket konto som ska användas via metoden.

## <a name="test-your-app"></a>Testa din app

### <a name="run-locally"></a>Lokal körning

Skapa och distribuera appen till en testenhet eller simulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

Första gången en användare loggar in på din app uppmanas de av Microsoft-identitet att godkänna de begärda behörigheterna.  Medan de flesta användare kan medgivande, vissa Azure AD-klienter har inaktiverat användarens medgivande, vilket kräver administratörer att samtycka på uppdrag av alla användare. Du stöder det här scenariot genom att registrera appens scope i Azure-portalen.

När du har loggat in visar appen de `/me` data som returneras från slutpunkten i Microsoft Graph.

## <a name="get-help"></a>Få hjälp

Besök [Hjälp och support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) om du har problem med den här självstudien eller med Microsofts identitetsplattform.

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
