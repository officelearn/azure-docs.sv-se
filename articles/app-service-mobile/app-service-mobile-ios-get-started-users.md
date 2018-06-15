---
title: Lägg till autentisering på iOS med Azure Mobile Apps
description: Lär dig hur du använder Azure Mobile Apps för att autentisera användare av iOS-appen via en mängd olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592702"
---
# <a name="add-authentication-to-your-ios-app"></a>Lägg till autentisering i din iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

I kursen får du lägger till autentisering till den [iOS snabb start] projekt med en identitetsprovider som stöds. Den här kursen är baserad på den [iOS snabb start] självstudien måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app.  Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar.  I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela.  Du kan dock använda alla URL-schema som du väljer.  Det bör vara unikt för din mobila program.  Du vill aktivera omdirigering på serversidan th:

1. I den [Azure-portalen], Välj din Apptjänst.

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. Klicka på **Azure Active Directory** under den **autentiseringsproviders** avsnitt.

4. Ange den **hanteringsläge** till **Avancerat**.

5. I den **tillåtna externa omdirigerings-URL: er**, ange `appname://easyauth.callback`.  Den _appname_ i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

6. Klicka på **OK**.

7. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Tryck på i Xcode **kör** starta appen. Ett undantagsfel genereras eftersom appen försöker komma åt serverdel som en oautentiserad användare men *TodoItem* tabellen nu kräver autentisering.

## <a name="add-authentication"></a>Lägg till autentisering i appen
**Objective-C**:

1. Öppna på en Mac *QSTodoListViewController.m* i Xcode och Lägg till följande metod:

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

    Ändra *google* till *microsoftaccount*, *twitter*, *facebook*, eller *windowsazureactivedirectory* om du inte använder Google som identitetsprovider. Om du använder Facebook, måste du [godkända Facebook domäner] [ 1] i din app.

    Ersätt den **urlScheme** med ett unikt namn för ditt program.  UrlScheme bör vara samma som det URL-schema-protokoll som du angav i den **tillåtna externa omdirigerings-URL: er** i Azure-portalen. UrlScheme används av återanrop för autentisering för att växla tillbaka till ditt program när autentiseringsbegäran är klar.

2. Ersätt `[self refresh]` i `viewDidLoad` i *QSTodoListViewController.m* med följande kod:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Öppna den `QSAppDelegate.h` och Lägg till följande kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Öppna den `QSAppDelegate.m` och Lägg till följande kod:

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

   Lägg till denna kod direkt före den rad läsningen `#pragma mark - Core Data stack`.  Ersätt den _appname_ med urlScheme-värdet som du använde i steg 1.

5. Öppna den `AppName-Info.plist` (ersätter AppName med namnet på din app), och Lägg till följande kod:

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

    Den här koden ska placeras i den `<dict>` element.  Ersätt den _appname_ sträng (inom en matris för **CFBundleURLSchemes**) med namnet på appen som du valde i steg 1.  Du kan också göra dessa ändringar på plist editor - Klicka på den `AppName-Info.plist` filen i XCode för att öppna Redigeraren för plist.

    Ersätt den `com.microsoft.azure.zumo` sträng för **CFBundleURLName** med ditt Apple-paketidentifierare.

6. Tryck på *kör* att starta appen och sedan logga in. När du har loggat in, bör du kunna visa Todo-listan och göra uppdateringar.

**SWIFT**:

1. Öppna på en Mac *ToDoTableViewController.swift* i Xcode och Lägg till följande metod:

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

    Ändra *google* till *microsoftaccount*, *twitter*, *facebook*, eller *windowsazureactivedirectory* om du inte använder Google som identitetsprovider. Om du använder Facebook, måste du [godkända Facebook domäner] [ 1] i din app.

    Ersätt den **urlScheme** med ett unikt namn för ditt program.  UrlScheme bör vara samma som det URL-schema-protokoll som du angav i den **tillåtna externa omdirigerings-URL: er** i Azure-portalen. UrlScheme används av återanrop för autentisering för att växla tillbaka till ditt program när autentiseringsbegäran är klar.

2. Ta bort rader `self.refreshControl?.beginRefreshing()` och `self.onRefresh(self.refreshControl)` i slutet av `viewDidLoad()` i *ToDoTableViewController.swift*. Lägg till ett anrop till `loginAndGetData()` i stället:

    ```swift
    loginAndGetData()
    ```

3. Öppna den `AppDelegate.swift` och Lägg till följande rad i den `AppDelegate` klass:

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

    Ersätt den _appname_ med urlScheme-värdet som du använde i steg 1.

4. Öppna den `AppName-Info.plist` (ersätter AppName med namnet på din app), och Lägg till följande kod:

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

    Den här koden ska placeras i den `<dict>` element.  Ersätt den _appname_ sträng (inom en matris för **CFBundleURLSchemes**) med namnet på appen som du valde i steg 1.  Du kan också göra dessa ändringar på plist editor - Klicka på den `AppName-Info.plist` filen i XCode för att öppna Redigeraren för plist.

    Ersätt den `com.microsoft.azure.zumo` sträng för **CFBundleURLName** med ditt Apple-paketidentifierare.

5. Tryck på *kör* att starta appen och sedan logga in. När du har loggat in, bör du kunna visa Todo-listan och göra uppdateringar.

Användning-autentisering används äpplen Inter App kommunikation.  Mer information om detta finns i den [Apples dokumentation][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portalen]: https://portal.azure.com

[iOS snabb start]: app-service-mobile-ios-get-started.md

