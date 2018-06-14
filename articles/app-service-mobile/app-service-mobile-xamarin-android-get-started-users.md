---
title: Kom igång med Mobile Apps i Xamarin Android-autentisering
description: Lär dig hur du använder Mobilappar för att autentisera användare för Xamarin Android-appen via en mängd olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 97207b722b65ccf98c57304cd559b0927aacd5a4
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595303"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Lägg till autentisering i Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Det här avsnittet visar hur du autentiserar användare i en Mobilapp från klientprogrammet. I kursen får du lägger till autentisering i Snabbstart-projektet som en identitetsleverantör som stöds av Azure Mobile Apps. Efter att kunna autentiseras och auktoriseras i Mobile App visas användar-ID-värdet.

Den här kursen är baserad på Mobilapp Snabbstart. Du måste också slutföra kursen [skapa en Xamarin.Android-app]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrera din app för autentisering och konfigurera Apptjänster
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app. Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för din mobila program. Du vill aktivera omdirigering på serversidan:

1. Välj din Apptjänst i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Kör klientprojektet i Visual Studio eller Xamarin Studio på en enhet eller emulator. Kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. Detta beror på att appen försöker få åtkomst till din mobilappsserverdel som en oautentiserad användare. Den *TodoItem* tabellen nu kräver autentisering.

Därefter uppdaterar du klientappen till begäran resurser från serverdelen för Mobilappen med en autentiserad användare.

## <a name="add-authentication"></a>Lägg till autentisering i appen
Appen har uppdaterats så att användare måste trycker du på den **inloggning** knappen och autentisera innan data visas.

1. Lägg till följande kod i den **TodoActivity** klass:
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Detta skapar en ny metod för att autentisera en användare och en metod hanterare för en ny **inloggning** knappen. I ovanstående exempelkoden användaren autentiseras med hjälp av en inloggning med Facebook. En dialogruta för att visa det användar-ID som autentiseras en gång.
   
   > [!NOTE]
   > Om du använder en identitetsleverantör än Facebook, ändra värdet som skickas till **LoginAsync** ovan till något av följande: *MicrosoftAccount*, *Twitter*,  *Google*, eller *WindowsAzureActiveDirectory*.
   > 
   > 
2. I den **OnCreate** metod, ta bort eller kommenterar ut följande kodrad:
   
        OnRefreshItemsSelected ();
3. Lägg till följande i filen Activity_To_Do.axml *LoginUser* knappen definitionen innan den befintliga *AddItem* knappen:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Lägg till följande element i filen Strings.xml resurser:
   
        <string name="login_button_text">Sign in</string>
5. Öppna filen AndroidManifest.xml, Lägg till följande kod inuti `<application>` XML-elementet:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. I Visual Studio eller Xamarin Studio kör klientprojektet på en enhet eller emulator och logga in med ditt valda identitetsleverantör. När du har loggat in, visas inloggnings-ID och listan över todo-objekt och du kan göra uppdateringar till data.

<!-- URLs. -->
[skapa en Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started.md