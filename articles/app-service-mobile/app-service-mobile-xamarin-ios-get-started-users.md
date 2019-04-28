---
title: Komma igång med autentisering för Mobile Apps i Xamarin-iOS
description: Lär dig hur du använder Mobile Apps du autentiserar användare i din Xamarin iOS-app genom olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120917"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Lägga till autentisering i en Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Det här avsnittet visar hur du autentiserar användare i en App Service Mobile App från ditt klientprogram. I den här självstudien lägger du till autentisering till snabbstartsprojektet Xamarin.iOS med hjälp av en identitetsprovider som stöds av App Service. Efter att har autentiseras och auktoriseras av din Mobilapp, användar-ID-värdet visas och du kommer att kunna få åtkomst till begränsade tabelldata.

Du måste slutföra kursen [skapa en Xamarin.iOS-app]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket ditt projekt. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Lägg till din app i de tillåtna externa Omdirigeringswebbadresser

Säker autentisering måste du definiera en ny URL-schema för din app. På så sätt kan autentiseringssystem att omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder vi URL-schema _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på serversidan:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din App Service.

2. Klicka på den **autentisering / auktorisering** menyalternativ.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapp.  Den bör följa den normala URL specifikationen för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer eftersom du behöver ändra programkoden mobila med URL-schema på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Kör klientprojektet i Visual Studio eller Xamarin Studio på en enhet eller emulator. Kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar. Felet loggas i konsolen för felsökning. Du bör därför se felet i utdatafönstret i Visual Studio.

    Den här obehörig felet inträffar eftersom appen försöker få åtkomst till din mobilappsserverdel som oautentiserade användare. Den *TodoItem* tabell nu kräver autentisering.

Därefter uppdaterar du klientappen till begär resurser från serverdelen för Mobilappen med en autentiserad användare.

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
I det här avsnittet ska du ändra appen om du vill visa en inloggningsskärm innan den visas data. När appen startar kommer inte att ansluta till din App Service och visas inte några data. När först gången som utför användaren uppdatering-gest inloggningsskärmen visas. efter genomförd inloggning visas listan över att göra-objekt.

1. Öppna filen i klientprojektet **QSTodoService.cs** och Lägg till följande med instruktionen och `MobileServiceUser` med accessor till QSTodoService-klassen:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Lägg till ny metod som heter **autentisera** till **QSTodoService** med följande definition:

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
    > Om du använder en identitetsprovider än ett Facebook, ändrar du värdet som skickas till **LoginAsync** ovan till något av följande: _MicrosoftAccount_, _Twitter_, _Google_, eller _WindowsAzureActiveDirectory_.

3. Öppna **QSTodoListViewController.cs**. Ändra metoddefinitionen av **ViewDidLoad** tar bort anropet till **RefreshAsync()** mot slutet:

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

4. Ändra metoden **RefreshAsync** att autentisera om det **användaren** -egenskapen är null. Lägg till följande kod högst upp på metoddefinitionen:

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

5. Öppna **AppDelegate.cs**, lägger du till följande metod:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Öppna **Info.plist** filen, gå till **URL typer** i den **Avancerat** avsnittet. Nu konfigurera den **identifierare** och **URL-scheman** av din URL-typ och klicka på **Lägg till URL-typen**. **URL-scheman** bör vara samma som din {url_scheme_of_your_app}.
7. I Visual Studio, som är anslutna till Mac-värd eller Visual Studio för Mac, kör klientprojektet riktar in sig på en enhet eller emulator. Kontrollera att appen visar inga data.

    Utför gesten uppdatering genom att dra nedåt i listan med objekt, vilket leder till inloggningsskärmen visas. När du har angett giltiga autentiseringsuppgifter, visas listan över att göra-objekt och du kan göra uppdateringar till data.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Skapa en Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started.md
