---
title: Lägg till autentisering på iOS
description: Lär dig hur du använder Azure Mobile Apps för att autentisera användare av iOS-appen via identitets leverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 925894cab00537cb9aeb03ca05c9699bb4bf6a84
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668455"
---
# <a name="add-authentication-to-your-ios-app"></a>Lägg till autentisering i din iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

I den här självstudien lägger du till autentisering till [snabb start för iOS] projektet för iOS med en identitets leverantör som stöds. Den här självstudien baseras på [snabb start för iOS] guiden för iOS, som du måste slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app.  Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar.  I den här självstudien använder _vi program_ varan för URL-program i alla.  Du kan dock använda alla URL-scheman du väljer.  Det bör vara unikt för det mobila programmet.  Aktivera omdirigering på Server sidan:

1. I [Azure-portalen]väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. Klicka på **Azure Active Directory** under avsnittet **autentiseringsprovider** .

4. Ange **hanterings läget** till **Avancerat**.

5. I de **tillåtna externa omdirigerings-URL: erna**anger du `appname://easyauth.callback`.  _APPNAME_ i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

6. Klicka på **OK**

7. Klicka på **Save** (Spara).

## <a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Xcode trycker du på **Kör** för att starta appen. Ett undantag aktive ras eftersom appen försöker komma åt Server delen som en oautentiserad användare, men *TodoItem* -tabellen kräver nu autentisering.

## <a name="add-authentication"></a>Lägg till autentisering i appen
**Mål-C**:

1. Öppna *QSTodoListViewController. m* i Xcode på din Mac och Lägg till följande metod:

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

    Ändra *Google* till *MicrosoftAccount*, *Twitter*, *Facebook*eller *windowsazureactivedirectory* om du inte använder Google som identitets leverantör. Om du använder Facebook måste du [vitlista Facebook-domäner][1] i din app.

    Ersätt **urlScheme** med ett unikt namn för ditt program.  UrlScheme ska vara samma som det URL-schema som du har angett i fältet **tillåtna externa omdirigerings-URL: er** i Azure Portal. UrlScheme används av autentiseringen motringning för att växla tillbaka till programmet när autentiseringsbegäran har slutförts.

2. Ersätt `[self refresh]` i `viewDidLoad` i *QSTodoListViewController. m* med följande kod:

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

   Lägg till den här koden direkt före rad läsningen `#pragma mark - Core Data stack`.  Ersätt _APPNAME_ med det urlScheme-värde som du använde i steg 1.

5. Öppna `AppName-Info.plist`-filen (Ersätt AppName med namnet på din app) och Lägg till följande kod:

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

    Den här koden ska placeras inuti `<dict>`-elementet.  Ersätt _APPNAME_ -strängen (i matrisen för **CFBundleURLSchemes**) med det app-namn som du valde i steg 1.  Du kan också göra dessa ändringar i plist-redigeraren och klicka på `AppName-Info.plist`-filen i XCode för att öppna plist-redigeraren.

    Ersätt `com.microsoft.azure.zumo` strängen för **CFBundleURLName** med ditt Apple-paket-ID.

6. Tryck på *Kör* för att starta appen och logga sedan in. När du är inloggad bör du kunna visa att göra-listan och göra uppdateringar.

**Swift**:

1. Öppna *ToDoTableViewController. SWIFT* i Xcode på din Mac och Lägg till följande metod:

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

    Ändra *Google* till *MicrosoftAccount*, *Twitter*, *Facebook*eller *windowsazureactivedirectory* om du inte använder Google som identitets leverantör. Om du använder Facebook måste du [vitlista Facebook-domäner][1] i din app.

    Ersätt **urlScheme** med ett unikt namn för ditt program.  UrlScheme ska vara samma som det URL-schema som du har angett i fältet **tillåtna externa omdirigerings-URL: er** i Azure Portal. UrlScheme används av autentiseringen motringning för att växla tillbaka till programmet när autentiseringsbegäran har slutförts.

2. Ta bort raderna `self.refreshControl?.beginRefreshing()` och `self.onRefresh(self.refreshControl)` i slutet av `viewDidLoad()` i *ToDoTableViewController. SWIFT*. Lägg till ett anrop till `loginAndGetData()` på plats:

    ```swift
    loginAndGetData()
    ```

3. Öppna `AppDelegate.swift`-filen och Lägg till följande rad i `AppDelegate`-klassen:

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

    Ersätt _APPNAME_ med det urlScheme-värde som du använde i steg 1.

4. Öppna `AppName-Info.plist`-filen (Ersätt AppName med namnet på din app) och Lägg till följande kod:

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

    Den här koden ska placeras inuti `<dict>`-elementet.  Ersätt _APPNAME_ -strängen (i matrisen för **CFBundleURLSchemes**) med det app-namn som du valde i steg 1.  Du kan också göra dessa ändringar i plist-redigeraren och klicka på `AppName-Info.plist`-filen i XCode för att öppna plist-redigeraren.

    Ersätt `com.microsoft.azure.zumo` strängen för **CFBundleURLName** med ditt Apple-paket-ID.

5. Tryck på *Kör* för att starta appen och logga sedan in. När du är inloggad bör du kunna visa att göra-listan och göra uppdateringar.

App Service autentisering använder kommunikation mellan appar.  Mer information om det här ämnet finns i [Apples dokumentation][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portalen]: https://portal.azure.com

[snabb start för iOS]: app-service-mobile-ios-get-started.md

