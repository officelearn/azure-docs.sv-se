## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använd Microsoft Authentication Library (MSAL) för att hämta en token för Microsoft Graph API

Öppna `ViewController.swift` och Ersätt Koden med:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current signed-in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive sign-in.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mer information
#### <a name="getting-a-user-token-interactively"></a>Hämta token för en användare interaktivt
Anropar den `acquireToken` metoden resulterar i ett webbläsarfönster som uppmanar användaren att logga in. Program kräver vanligen en användare att logga in interaktivt första gången de behöver för att få åtkomst till en skyddad resurs, eller när en tyst åtgärd att hämta en token misslyckas (t.ex. användarens lösenord har upphört att gälla).

#### <a name="getting-a-user-token-silently"></a>Hämta token för en användare tyst
Den `acquireTokenSilent` metoden hanterar token förvärv av organisationer och förnyelse utan någon användarinteraktion. Efter `acquireToken` körs för första gången `acquireTokenSilent` är den metod som används ofta för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop - eftersom anrop till begära eller förnya token görs tyst.

Slutligen `acquireTokenSilent` misslyckas – t.ex. användaren har loggat ut eller har ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, den utlöses en `MSALErrorCode.interactionRequired` undantag. Programmet kan hantera det här undantaget på två sätt:

1.  Gör ett anrop mot `acquireToken` direkt, vilket innebär att användaren uppmanas att logga in. Det här mönstret används vanligtvis i Onlineprogram där det finns inget offline innehåll i programmet för användaren. Exempelprogrammet som genererats av den här interaktiv installation använder det här mönstret: du kan se den i åtgärden första gången du kör programmet. Eftersom ingen användare har använt programmet, `applicationContext.users().first` innehåller ett null-värde och ett ` MSALErrorCode.interactionRequired ` undantag. Koden i exemplet hanterar undantaget genom att anropa `acquireToken` ledde till att användaren uppmanas att logga in.

2.  Program kan också göra en indikering för användaren som en interaktiv inloggning krävs, så att användaren kan välja att logga in rätt tidpunkt eller programmet kan försöka `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligtvis när användaren kan använda andra funktioner i programmet utan att något stör – t.ex, det finns offline innehåll i programmet. I det här fallet användaren kan välja när de vill logga in till den skydda resursen eller uppdatera inaktuell information eller ditt program kan välja att försök `acquireTokenSilent` när nätverket återställs efter att ha tillfälligt otillgänglig.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Anropa använder token som du precis har köpt Microsoft Graph API

Lägg till den nya metoden nedan till `ViewController.swift`. Den här metoden används för att göra en `GET` begäran mot Microsoft Graph API med hjälp av en *HTTP Authorization-huvud*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Att göra ett REST-anrop mot ett skyddade API

I det här exempelprogrammet i `getContentWithToken()` metoden används för att en HTTP `GET` begäran mot en skyddad resurs som kräver ett token och returnera innehållet till anroparen. Den här metoden lägger till anskaffats token i den *HTTP Authorization-huvud*. Resursen för det här exemplet är Microsoft Graph API *mig* slutpunkt – som visar information om användarens profil.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Ställ in utloggning

Lägg till följande metod för att `ViewController.swift` logga ut användaren:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Mer information om utloggning

Den `signoutButton` metoden tar bort användaren från användarcachen MSAL – detta effektivt talar MSAL att glömma den aktuella användaren, så en framtida begäran om att hämta en token lyckas bara om det görs för att interaktivt.

Även om programmet i det här exemplet har stöd för en enskild användare, stöder MSAL scenarier där flera konton kan logga in på samma gång – ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="register-the-callback"></a>Registrera återanropet

När användaren autentiseras omdirigeras användaren till programmet i webbläsaren. Följ stegen nedan för att registrera den här motringning:

1.  Öppna `AppDelegate.swift` och importera MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Lägg till följande metod för att din <code>AppDelegate</code> klassen för att hantera återanrop:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```

