
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i appen

I det här steget måste du lägga till det program-Id i projektet:

1.  I `ViewController.swift`, ersätter den rad som börjar med '`let kClientID`' med:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
CTRL + klicka <code>Info.plist</code> öppna snabbmenyn och klicka sedan på: <code>Open As</code>> <code>Source Code</code>
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
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
