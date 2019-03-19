---
title: Kom igång med Mobile Apps i Xamarin Android-autentisering
description: Lär dig hur du använder Mobile Apps du autentiserar användare i din Xamarin Android-app genom olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
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
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 0a2d964d60d13f0e71de5776112a4edbe3cdcc45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993913"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Lägg till autentisering i din Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Det här avsnittet visar hur du autentiserar användare i en Mobilapp från klientprogrammet. I den här självstudien lägger du till autentisering till snabbstartsprojektet med hjälp av en identitetsprovider som stöds av Azure Mobile Apps. Efter att har autentiseras och auktoriseras i Mobile App måste visas användaren ID-värdet.

Den här självstudien är baserad på Snabbstart för Mobile App. Du måste också slutföra kursen [skapa en Xamarin.Android-app]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket ditt projekt. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa Omdirigeringswebbadresser

Säker autentisering måste du definiera en ny URL-schema för din app. På så sätt kan autentiseringssystem att omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder vi URL-schema _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på serversidan:

1. Välj din App Service i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativ.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapp.  Den bör följa den normala URL specifikationen för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer eftersom du behöver ändra programkoden mobila med URL-schema på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Kör klientprojektet i Visual Studio eller Xamarin Studio på en enhet eller emulator. Kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar. Detta inträffar eftersom appen försöker få åtkomst till din mobilappsserverdel som oautentiserade användare. Den *TodoItem* tabell nu kräver autentisering.

Därefter uppdaterar du klientappen till begär resurser från serverdelen för Mobilappen med en autentiserad användare.

## <a name="add-authentication"></a>Lägg till autentisering i appen
Appen har uppdaterats så för att användarna trycker du på den **logga in** knappen och autentisera sig innan data visas.

1. Lägg till följande kod till den **TodoActivity** klass:
   
        // Define an authenticated user.
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
   
    Detta skapar en ny metod för att autentisera en användare och en metod-hanterare för en ny **logga in** knappen. Användaren i exempelkoden ovan autentiseras med hjälp av en Facebook-inloggning. En dialogruta som används för att visa det användar-ID som autentiseras en gång.
   
   > [!NOTE]
   > Om du använder en identitetsprovider än Facebook, ändrar du värdet som skickas till **LoginAsync** ovan till något av följande: *MicrosoftAccount*, *Twitter*, *Google*, eller *WindowsAzureActiveDirectory*.
   > 
   > 
2. I den **OnCreate** metod, ta bort eller kommentera följande rad med kod:
   
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
5. Öppna filen AndroidManifest.xml, Lägg till följande kod i `<application>` XML-elementet:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. I Visual Studio eller Xamarin Studio kör klientprojektet på en enhet eller emulator och logga in med din valda identitetsprovider. När du är har loggat in visas inloggnings-ID och en lista över att göra-objekt och du kan göra uppdateringar till data.

## <a name="troubleshooting"></a>Felsökning

**Programmet kraschade med `Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall är i konflikt i supportpaket visas som bara en varning i Visual studio, men programkrascher med det här undantaget vid körning. I det här fallet måste du se till att alla supportpaket som refereras till i ditt projekt har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) har ett `Xamarin.Android.Support.CustomTabs`-beroende för Android-plattformen, så om ditt projekt använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

<!-- URLs. -->
[Skapa en Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started.md
