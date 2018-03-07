
## <a name="create-an-application-express"></a>Skapa ett program (snabb)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att hämta program-ID och klistra in den i din kod

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Lägga till registreringsinformationen program i lösningen (Avancerat)

1.  Gå till [Portal för Microsoft-program](https://apps.dev.microsoft.com/portal/register-app)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Klicka på `Add Platform`och välj `Native Application` och klicka på `Save`
5.  Gå tillbaka till Xcode. I `ViewController.swift`, ersätter den rad som börjar med '`let kClientID`' med program-ID som du just har registrerat:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + klicka <code>Info.plist</code> öppna snabbmenyn och klicka sedan på: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Under den <code>dict</code> rot nod, Lägg till följande:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Ersätt <i> <code>[Your_Application_Id_Here]</code> </i> med program-Id som du precis har registrerats
</li>
</ol>
