---
title: Lägg till autentisering på iOS med Azure Mobile Apps
description: Lär dig hur du använder Azure Mobile Apps du autentiserar användare i din iOS-app genom olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
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
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128157"
---
# <a name="add-authentication-to-your-ios-app"></a>Lägg till autentisering i din iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

I den här självstudien lägger du till autentisering till den [Snabbstart för iOS] projektet med en identitetsprovider som stöds. Den här självstudien är baserad på den [Snabbstart för iOS] kursen måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa Omdirigeringswebbadresser

Säker autentisering måste du definiera en ny URL-schema för din app.  På så sätt kan autentiseringssystem att omdirigera tillbaka till din app när autentiseringen är klar.  I den här självstudien använder vi URL-schema _appname_ i hela.  Du kan dock använda alla URL-schema som du väljer.  Det bör vara unikt för det mobila programmet.  Aktivera omdirigering på serversidan för th:

1. I den [Azure Portal], Välj din App Service.

2. Klicka på den **autentisering / auktorisering** menyalternativ.

3. Klicka på **Azure Active Directory** under den **autentiseringsproviders** avsnittet.

4. Ange den **hanteringsläge** till **Avancerat**.

5. I den **tillåtna externa omdirigerings-URL: er**, ange `appname://easyauth.callback`.  Den _appname_ i den här strängen är URL-schemat för din mobilapp.  Den bör följa den normala URL specifikationen för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer eftersom du behöver ändra programkoden mobila med URL-schema på flera platser.

6. Klicka på **OK**.

7. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Xcode, trycker du på **kör** starta appen. Ett undantagsfel genereras eftersom appen försöker komma åt serverdelen som en oautentiserad användare men *TodoItem* tabell nu kräver autentisering.

## <a name="add-authentication"></a>Lägg till autentisering i appen
**Objective-C**:

1. Öppna på din Mac *QSTodoListViewController.m* i Xcode och Lägg till följande metod:

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

    Ändra *google* till *microsoftaccount*, *twitter*, *facebook*, eller *windowsazureactivedirectory* om du inte använder Google som identitetsprovider. Om du använder Facebook, måste du [vitlista Facebook domäner] [ 1] i din app.

    Ersätt den **urlScheme** med ett unikt namn för ditt program.  UrlScheme bör vara samma som det URL-schema-protokoll som du angav i den **tillåtna externa omdirigerings-URL: er** i Azure-portalen. UrlScheme används av återanrop för autentisering för att växla tillbaka till programmet när autentiseringsbegäran är klar.

2. Ersätt `[self refresh]` i `viewDidLoad` i *QSTodoListViewController.m* med följande kod:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Öppna den `QSAppDelegate.h` filen och Lägg till följande kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Öppna den `QSAppDelegate.m` filen och Lägg till följande kod:

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

   Lägg till den här koden före rad läsning `#pragma mark - Core Data stack`.  Ersätt den _appname_ med urlScheme-värde som du använde i steg 1.

5. Öppna den `AppName-Info.plist` filen (ersättning AppName med namnet på din app) och Lägg till följande kod:

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

    Den här koden ska placeras i den `<dict>` element.  Ersätt den _appname_ sträng (inom matrisen för **CFBundleURLSchemes**) med det namn du valde i steg 1.  Du kan också göra dessa ändringar i plist redigeraren – Klicka på den `AppName-Info.plist` filen i XCode för att öppna plist-redigeraren.

    Ersätt den `com.microsoft.azure.zumo` sträng för **CFBundleURLName** med ditt Apple-paketidentifierare.

6. Tryck på *kör* att starta appen och sedan logga in. När du är inloggad, bör du kunna visa att göra-listan och göra uppdateringar.

**Swift**:

1. Öppna på din Mac *ToDoTableViewController.swift* i Xcode och Lägg till följande metod:

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

    Ändra *google* till *microsoftaccount*, *twitter*, *facebook*, eller *windowsazureactivedirectory* om du inte använder Google som identitetsprovider. Om du använder Facebook, måste du [vitlista Facebook domäner] [ 1] i din app.

    Ersätt den **urlScheme** med ett unikt namn för ditt program.  UrlScheme bör vara samma som det URL-schema-protokoll som du angav i den **tillåtna externa omdirigerings-URL: er** i Azure-portalen. UrlScheme används av återanrop för autentisering för att växla tillbaka till programmet när autentiseringsbegäran är klar.

2. Ta bort rader `self.refreshControl?.beginRefreshing()` och `self.onRefresh(self.refreshControl)` i slutet av `viewDidLoad()` i *ToDoTableViewController.swift*. Lägg till ett anrop till `loginAndGetData()` i stället:

    ```swift
    loginAndGetData()
    ```

3. Öppna den `AppDelegate.swift` filen och Lägg till följande rad för att den `AppDelegate` klass:

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

    Ersätt den _appname_ med urlScheme-värde som du använde i steg 1.

4. Öppna den `AppName-Info.plist` filen (ersättning AppName med namnet på din app) och Lägg till följande kod:

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

    Den här koden ska placeras i den `<dict>` element.  Ersätt den _appname_ sträng (inom matrisen för **CFBundleURLSchemes**) med det namn du valde i steg 1.  Du kan också göra dessa ändringar i plist redigeraren – Klicka på den `AppName-Info.plist` filen i XCode för att öppna plist-redigeraren.

    Ersätt den `com.microsoft.azure.zumo` sträng för **CFBundleURLName** med ditt Apple-paketidentifierare.

5. Tryck på *kör* att starta appen och sedan logga in. När du är inloggad, bör du kunna visa att göra-listan och göra uppdateringar.

App Service-autentisering använder äpplen Inter-App-kommunikation.  Mer information om det här ämnet i den [Apples dokumentation][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[Snabbstart för iOS]: app-service-mobile-ios-get-started.md

