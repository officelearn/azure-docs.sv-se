---
title: Lägga till autentisering på iOS
description: Lär dig hur du använder Azure Mobile Apps för att autentisera användare av din iOS-app via identitetsleverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459031"
---
# <a name="add-authentication-to-your-ios-app"></a>Lägga till autentisering i din iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

I den här självstudien lägger du till autentisering i [snabbstartsprojektet i iOS] med hjälp av en identitetsprovider som stöds. Den här självstudien baseras på [snabbstartshandledningen iOS,] som du måste slutföra först.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrera appen för autentisering och konfigurera App-tjänsten
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app.  Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar.  I den här självstudien använder vi _url-schemats appnamn_ hela tiden.  Du kan dock använda vilket URL-schema du vill.  Den ska vara unik för din mobilapplikation.  Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen.]

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Klicka på **Azure Active Directory** under avsnittet **Autentiseringsleverantörer.**

4. Ställ in **hanteringsläget** på **Avancerat**.

5. Ange i **url:erna Tillåten extern omdirigering** `appname://easyauth.callback`.  _Appnamnet_ i den här strängen är URL-schemat för ditt mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

6. Klicka på **OK**.

7. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Xcode trycker du på **Kör** för att starta appen. Ett undantag utlöses eftersom appen försöker komma åt serverda som en oautentiserade användare, men *tabellen TodoItem* kräver nu autentisering.

## <a name="add-authentication-to-app"></a><a name="add-authentication"></a>Lägga till autentisering i appen
**Mål C**:

1. Öppna *QSTodoListViewController.m* på din Mac och lägg till följande metod:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Ändra *Google* till *microsoftaccount*, *Twitter*, *Facebook*, eller *windowsazureactivedirectory* om du inte använder Google som din identitetsleverantör. Om du använder Facebook måste du [vitlista Facebook-domäner][1] i din app.

    Ersätt **urlScheme** med ett unikt namn för ditt program.  UrlScheme ska vara samma som url-schemaprotokollet som du angav i fältet **Tillåtna externa omdirigeringsadresser** i Azure-portalen. UrlScheme används av autentiseringsadbacken för att växla tillbaka till ditt program när autentiseringsbegäran är klar.

2. Ersätt `[self refresh]` `viewDidLoad` i *QSTodoListViewController.m* med följande kod:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Öppna filen `QSAppDelegate.h` och lägg till följande kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Öppna filen `QSAppDelegate.m` och lägg till följande kod:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Lägg till den här `#pragma mark - Core Data stack`koden direkt före radläsningen .  Ersätt _appnamnet_ med urlScheme-värdet som du använde i steg 1.

5. Öppna `AppName-Info.plist` filen (ersätter AppName med namnet på din app) och lägg till följande kod:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Den här koden ska `<dict>` placeras inuti elementet.  Ersätt _appnamnsträngen_ (inom matrisen för **CFBundleURLSchemes**) med det appnamn du valde i steg 1.  Du kan också göra dessa ändringar i plist editor - klicka på `AppName-Info.plist` filen i XCode för att öppna plist editor.

    Byt `com.microsoft.azure.zumo` ut strängen för **CFBundleURLName** med din Apple-paketidentifierare.

6. Tryck på *Kör* för att starta appen och logga sedan in. När du är inloggad bör du kunna visa Todo-listan och göra uppdateringar.

**Swift:**

1. Öppna *ToDoTableViewController.swift* på din Mac och lägg till följande metod:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Ändra *Google* till *microsoftaccount*, *Twitter*, *Facebook*, eller *windowsazureactivedirectory* om du inte använder Google som din identitetsleverantör. Om du använder Facebook måste du [vitlista Facebook-domäner][1] i din app.

    Ersätt **urlScheme** med ett unikt namn för ditt program.  UrlScheme ska vara samma som url-schemaprotokollet som du angav i fältet **Tillåtna externa omdirigeringsadresser** i Azure-portalen. UrlScheme används av autentiseringsadbacken för att växla tillbaka till ditt program när autentiseringsbegäran är klar.

2. Ta bort `self.refreshControl?.beginRefreshing()` `self.onRefresh(self.refreshControl)` raderna och `viewDidLoad()` i slutet av i *ToDoTableViewController.swift*. Lägg till `loginAndGetData()` ett samtal i deras ställe:

    ```swift
    loginAndGetData()
    ```

3. Öppna `AppDelegate.swift` filen och lägg till `AppDelegate` följande rad i klassen:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Ersätt _appnamnet_ med urlScheme-värdet som du använde i steg 1.

4. Öppna `AppName-Info.plist` filen (ersätter AppName med namnet på din app) och lägg till följande kod:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Den här koden ska `<dict>` placeras inuti elementet.  Ersätt _appnamnsträngen_ (inom matrisen för **CFBundleURLSchemes**) med det appnamn du valde i steg 1.  Du kan också göra dessa ändringar i plist editor - klicka på `AppName-Info.plist` filen i XCode för att öppna plist editor.

    Byt `com.microsoft.azure.zumo` ut strängen för **CFBundleURLName** med din Apple-paketidentifierare.

5. Tryck på *Kör* för att starta appen och logga sedan in. När du är inloggad bör du kunna visa Todo-listan och göra uppdateringar.

App Service Authentication använder Apples Inter-App Communication.  Mer information om detta ämne finns i [Apple-dokumentationen][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portal]: https://portal.azure.com

[snabbstart med iOS]: app-service-mobile-ios-get-started.md

