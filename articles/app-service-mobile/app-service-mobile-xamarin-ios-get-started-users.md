---
title: Komma igång med autentisering för Mobile Apps i Xamarin-iOS
description: Lär dig hur du använder Mobilappar för att autentisera användare av Xamarin iOS-app via en mängd olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
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
ms.openlocfilehash: c2b77d7c9caab1c7ac13af5ccd50e992bcce1caf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31787666"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Lägga till autentisering i en Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Det här avsnittet visar hur du autentiserar användare i en Apptjänst Mobile App från klientprogrammet. I kursen får du lägger till autentisering Xamarin.iOS quickstart projektet med en identitetsleverantör som stöds av App Service. Efter att har autentiseras och auktoriseras av din Mobilapp användar-ID-värde visas och du kommer att kunna få åtkomst till begränsade tabelldata.

Du måste slutföra kursen [skapa en Xamarin.iOS-app]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera Apptjänster
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app. Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för din mobila program. Du vill aktivera omdirigering på serversidan:

1. Välj din Apptjänst i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. Kör klientprojektet i Visual Studio eller Xamarin Studio på en enhet eller emulator. Kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. Felet loggas i konsolen för felsökning. Därför bör du se felet i utdatafönstret i Visual Studio.

&nbsp;&nbsp;Felet obehörig beror på att appen försöker få åtkomst till din mobilappsserverdel som en oautentiserad användare. Den *TodoItem* tabellen nu kräver autentisering.

Därefter uppdaterar du klientappen till begäran resurser från serverdelen för Mobilappen med en autentiserad användare.

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
I det här avsnittet ska du ändra appen för att visas en inloggningsskärm innan data. När appen startar inte kan inte ansluta till din Apptjänst och visas inte några data. När först gången utför användaren uppdatera-gest inloggningsskärmen visas. listan över todo-objekt ska visas efter genomförd inloggning.

1. Öppna filen i klientprojektet **QSTodoService.cs** och Lägg till följande med instruktionen och `MobileServiceUser` med accessorn för att klassen QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Lägga till nya metod med namnet **autentisera** till **QSTodoService** med definitionen av följande:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Om du använder en identitetsleverantör än en Facebook, ändra värdet som skickas till **LoginAsync** ovan till något av följande: _MicrosoftAccount_, _Twitter_,  _Google_, eller _WindowsAzureActiveDirectory_.

3. Öppna **QSTodoListViewController.cs**. Ändra metoddefinitionen av **ViewDidLoad** tar bort anropet till **RefreshAsync()** mot slutet:
   
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
4. Ändra metoden **RefreshAsync** att autentisera om det **användaren** -egenskapen är null. Lägg till följande kod högst upp i metoddefinition:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Öppna **AppDelegate.cs**, lägger du till följande metod:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Öppna **Info.plist** fil, gå till **URL typer** i den **Avancerat** avsnitt. Nu konfigurera den **identifierare** och **URL-scheman** URL-typen och klicka på **Lägg till URL-typen**. **URL-scheman** bör vara samma som {url_scheme_of_your_app}.
7. I Visual Studio, ansluten till Mac-värden eller Visual Studio för Mac, kör klientprojektet inriktning på en enhet eller emulator. Kontrollera att inga data visas i appen.
   
    Utföra en uppdatering gest genom att dra nedåt i listan över objekt, vilket leder till inloggningssidan visas. När du har angett giltiga autentiseringsuppgifter, visas listan över todo-objekt och du kan göra uppdateringar till data.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[skapa en Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started.md
