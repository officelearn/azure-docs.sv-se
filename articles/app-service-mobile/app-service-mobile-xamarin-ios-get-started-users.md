---
title: Kom igång med autentisering i Xamarin iOS
description: Lär dig hur du använder Mobile Apps för att autentisera användare av din Xamarin iOS-app med identitets leverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461325"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Lägg till autentisering i din Xamarin. iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du autentiserar användare av en App Service mobilapp från klient programmet. I den här självstudien lägger du till autentisering i snabb starts projektet Xamarin. iOS med en identitetsprovider som stöds av App Service. När din mobilapp har autentiserats och godkänts av din mobilapp visas värdet användar-ID och du kommer att kunna komma åt data i begränsad tabell.

Du måste först slutföra självstudien [skapa en Xamarin. iOS-app]. Om du inte använder det nedladdade projektet för snabb starts Server måste du lägga till paketet med autentiserings tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder _vi program_ varan för URL-program i alla. Du kan dock använda alla URL-scheman du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på Server sidan:

1. I [Azure Portal](https://portal.azure.com/)väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. I de **tillåtna externa omdirigerings-URL: erna**anger du `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

4. Klicka på **OK**

5. Klicka på **Save** (Spara).

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* I Visual Studio eller Xamarin Studio kör du klient projektet på en enhet eller emulator. Kontrol lera att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. Fel loggas i fel söknings konsolens konsol. Så i Visual Studio bör du se felen i fönstret utdata.

    Detta obehöriga haverier uppstår eftersom appen försöker få åtkomst till Server delen för mobilappar som en oautentiserad användare. *TodoItem* -tabellen kräver nu autentisering.

Sedan uppdaterar du klient programmet för att begära resurser från Server delen för mobilappar med en autentiserad användare.

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
I det här avsnittet ska du ändra appen så att en inloggnings skärm visas innan data visas. När appen startar kommer den inte att ansluta till din App Service och kommer inte att visa några data. När den första gången användaren utför uppdateringen visas inloggnings skärmen. efter lyckad inloggning visas listan över att göra-objekt.

1. Öppna filen **QSTodoService.cs** i klient projektet och Lägg till följande using-instruktion och `MobileServiceUser` med accessor till klassen QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Lägg till en ny metod med namnet **autentisera** till **QSTodoService** med följande definition:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Om du använder en annan identitets leverantör än en Facebook, ändra värdet som skickats till **LoginAsync** ovan till något av följande: _MicrosoftAccount_, _Twitter_, _Google_eller _WindowsAzureActiveDirectory_.

3. Öppna **QSTodoListViewController.cs**. Ändra metod definitionen för **ViewDidLoad** ta bort anropet till **RefreshAsync ()** nära slutet:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Ändra metoden **RefreshAsync** för autentisering om **användar** egenskapen är null. Lägg till följande kod högst upp i metod definitionen:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Öppna **AppDelegate.cs**och Lägg till följande metod:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Öppna filen **info. plist** , navigera till **URL-typer** i avsnittet **Avancerat** . Konfigurera **identifieraren** och URL- **scheman** för din URL-typ och klicka på **Lägg till URL-typ**. **URL-scheman** ska vara samma som {url_scheme_of_your_app}.
7. I Visual Studio, som är ansluten till din Mac-värd eller Visual Studio för Mac, kör du klient projektet som mål en enhet eller emulator. Kontrol lera att appen inte visar några data.

    Utför uppdateringen genom att dra ned listan över objekt, vilket gör att inloggnings skärmen visas. När du har angett giltiga autentiseringsuppgifter kommer appen att visa listan över att göra-objekt och du kan göra uppdateringar av data.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Skapa en Xamarin. iOS-app]: app-service-mobile-xamarin-ios-get-started.md
