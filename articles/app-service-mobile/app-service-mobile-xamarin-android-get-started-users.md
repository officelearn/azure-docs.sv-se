---
title: Komma igång med autentisering i Xamarin Android
description: Läs om hur du använder mobilappar för att autentisera användare av din Xamarin Android-app med identitetsleverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458961"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Lägga till autentisering i din Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt
I det här avsnittet visas hur du autentiserar användare av en mobilapp från klientprogrammet. I den här självstudien lägger du till autentisering i snabbstartsprojektet med hjälp av en identitetsleverantör som stöds av Azure Mobile Apps. När användar-ID-värdet har autentiserats och auktoriserats i mobilappen visas det.

Den här självstudien är baserad på snabbstarten för mobilappen. Du måste också först slutföra självstudien [Skapa en Xamarin.Android app]. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till autentiseringstilläggspaketet i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Registrera appen för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar. I den här självstudien använder vi _url-schemats appnamn_ hela tiden. Du kan dock använda vilket URL-schema du vill. Den ska vara unik för din mobilapplikation. Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen].

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Ange i **url:erna Tillåten extern omdirigering** `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Visual Studio eller Xamarin Studio kör du klientprojektet på en enhet eller emulator. Kontrollera att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen startar. Detta beror på att appen försöker komma åt din Mobile App-backend som en oautentiserade användare. *Tabellen TodoItem* kräver nu autentisering.

Därefter ska du uppdatera klientappen för att begära resurser från mobile app-backend med en autentiserat användare.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Lägga till autentisering i appen
Appen uppdateras så att användarna kräver att användarna trycker på **inloggningsknappen** och autentiserar innan data visas.

1. Lägg till följande kod i klassen **TodoActivity:**
   
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
   
    Detta skapar en ny metod för att autentisera en användare och en metodhanterare för en ny **inloggningsknapp.** Användaren i exempelkoden ovan autentiseras med hjälp av en Facebook-inloggning. En dialogruta används för att visa användar-ID:et när det har autentiserats.
   
   > [!NOTE]
   > Om du använder en annan identitetsleverantör än Facebook ändrar du värdet som skickas till **LoginAsync** ovan till något av följande: *MicrosoftAccount*, *Twitter*, *Google*eller *WindowsAzureActiveDirectory*.
   > 
   > 
2. I metoden **OnCreate** tar du bort eller kommenterar följande kodrad:
   
        OnRefreshItemsSelected ();
3. Lägg till följande *LoginUser-knappdefinition* i filen Activity_To_Do.axml före den befintliga *AddItem-knappen:*
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Lägg till följande element i resursfilen Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Öppna filen AndroidManifest.xml, lägg till `<application>` följande kod i XML-elementet:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. I Visual Studio eller Xamarin Studio kör du klientprojektet på en enhet eller emulator och loggar in med din valda identitetsleverantör. När du har loggat in kommer appen att visa ditt inloggnings-ID och listan över att göra-objekt, och du kan göra uppdateringar av data.

## <a name="troubleshooting"></a>Felsökning

**Programmet kraschade med`Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall visas konflikter i supportpaketen som bara en varning i Visual-studion, men programmet kraschar med det här undantaget vid körning. I det här fallet måste du se till att alla supportpaket som refereras i projektet har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) är `Xamarin.Android.Support.CustomTabs` beroende av Android-plattformen, så om projektet använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

<!-- URLs. -->
[Skapa en Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started.md
