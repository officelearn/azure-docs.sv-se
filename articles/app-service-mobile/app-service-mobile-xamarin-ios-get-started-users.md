---
title: Komma igång med autentisering i Xamarin iOS
description: Läs om hur du använder mobilappar för att autentisera användare av din Xamarin iOS-app med identitetsleverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461325"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Lägga till autentisering i din Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt

I det här avsnittet visas hur du autentiserar användare av en App Service Mobile App från klientprogrammet. I den här självstudien lägger du till autentisering i snabbstartsprojektet Xamarin.iOS med hjälp av en identitetsprovider som stöds av App Service. När du har autentiserats och auktoriserats av din mobilapp visas användar-ID-värdet och du kan komma åt begränsade tabelldata.

Du måste först slutföra självstudien [Skapa en Xamarin.iOS-app]. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till autentiseringstilläggspaketet i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera appen för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar. I den här självstudien använder vi _url-schemats appnamn_ hela tiden. Du kan dock använda vilket URL-schema du vill. Den ska vara unik för din mobilapplikation. Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen.](https://portal.azure.com/)

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Ange i **url:erna Tillåten extern omdirigering** `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* I Visual Studio eller Xamarin Studio kör du klientprojektet på en enhet eller emulator. Kontrollera att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen startar. Felet loggas till konsolen för felsökningsprogrammet. Så i Visual Studio bör du se felet i utdatafönstret.

    Det här obehöriga felet inträffar eftersom appen försöker komma åt din mobile app-backend som en oautentiserade användare. *Tabellen TodoItem* kräver nu autentisering.

Därefter ska du uppdatera klientappen för att begära resurser från mobile app-backend med en autentiserat användare.

## <a name="add-authentication-to-the-app"></a>Lägga till autentisering i appen
I det här avsnittet ändrar du appen för att visa en inloggningsskärm innan du visar data. När appen startar kommer den inte att ansluta till din App Service och kommer inte att visa några data. Efter första gången som användaren utför uppdateringsgesten visas inloggningsskärmen. efter lyckad inloggning visas listan över todoobjekt.

1. Öppna filen **QSTodoService.cs** i klientprojektet och lägg till följande `MobileServiceUser` med hjälp av utdrag och med accessor i klassen QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Lägg till ny metod med namnet **Authenticate** i **QSTodoService** med följande definition:

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
    > Om du använder en annan identitetsleverantör än en Facebook ändrar du värdet som skickas till **LoginAsync** ovan till något av följande: _MicrosoftAccount_, _Twitter_, _Google_eller _WindowsAzureActiveDirectory_.

3. Öppna **QSTodoListViewController.cs**. Ändra metoddefinitionen för **ViewDidLoad som** tar bort anropet till **RefreshAsync()** nära slutet:

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

4. Ändra metoden **RefreshAsync** för att autentisera om egenskapen **Användare** är null. Lägg till följande kod högst upp i metoddefinitionen:

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

5. Öppna **AppDelegate.cs**lägger du till följande metod:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Öppna **info.plist-filen,** navigera till **URL-typer** i avsnittet **Avancerat.** Konfigurera nu **identifieraren** och **URL-schemana** för url-typen och klicka på **Lägg till URL-typ**. **URL-scheman** ska vara samma som {url_scheme_of_your_app}.
7. I Visual Studio, som är ansluten till din Mac-värd eller Visual Studio för Mac, kör du klientprojektet som riktar sig till en enhet eller emulator. Kontrollera att appen inte visar några data.

    Utför uppdateringsgesten genom att dra ned listan med objekt, vilket gör att inloggningsskärmen visas. När du har angett giltiga autentiseringsuppgifter visar appen listan över att göra-objekt och du kan göra uppdateringar av data.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Skapa en Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started.md
