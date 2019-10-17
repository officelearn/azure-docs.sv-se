---
title: Kom igång med autentisering för Mobile Apps i Xamarin Android
description: Lär dig hur du använder Mobile Apps för att autentisera användare av din Xamarin Android-app via olika identitets leverantörer, till exempel AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e0baf64c3490dd5b7edd8c3f7d209c135c546ed5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388434"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Lägg till autentisering i din Xamarin. Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du autentiserar användare av en mobilapp från klient programmet. I den här självstudien lägger du till autentisering i snabb starts projektet med en identitetsprovider som stöds av Azure Mobile Apps. När du har autentiserat och auktoriserat i mobilappen visas värdet för användar-ID.

Den här självstudien baseras på snabb starten av mobilappen. Du måste också först slutföra självstudien [skapa en Xamarin. Android-app]. Om du inte använder det nedladdade projektet för snabb starts Server måste du lägga till paketet med autentiserings tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder _vi program_ varan för URL-program i alla. Du kan dock använda alla URL-scheman du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på Server sidan:

1. I [Azure Portal] väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. I de **tillåtna externa omdirigerings-URL: erna**anger du `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

4. Klicka på **OK**

5. Klicka på **Save** (Spara).

## <a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Visual Studio eller Xamarin Studio kör du klient projektet på en enhet eller emulator. Kontrol lera att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. Detta inträffar eftersom appen försöker få åtkomst till Server delen för mobilappar som en oautentiserad användare. *TodoItem* -tabellen kräver nu autentisering.

Sedan uppdaterar du klient programmet för att begära resurser från Server delen för mobilappar med en autentiserad användare.

## <a name="add-authentication"></a>Lägg till autentisering i appen
Appen uppdateras för att kräva att användare trycker på knappen **Logga in** och autentisera innan data visas.

1. Lägg till följande kod i **TodoActivity** -klassen:
   
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
   
    Detta skapar en ny metod för att autentisera en användare och en metod hanterare för en ny **inloggnings** knapp. Användaren i exempel koden ovan autentiseras med hjälp av en Facebook-inloggning. En dialog ruta används för att Visa användar-ID när autentiseringen har autentiserats.
   
   > [!NOTE]
   > Om du använder en annan identitets leverantör än Facebook ändrar du värdet som skickats till **LoginAsync** ovan till något av följande: *MicrosoftAccount*, *Twitter*, *Google*eller *WindowsAzureActiveDirectory*.
   > 
   > 
2. Ta bort eller kommentera ut följande kodrad i **OnCreate** -metoden:
   
        OnRefreshItemsSelected ();
3. I filen Activity_To_Do. axml lägger du till följande *LoginUser* Button-definition före den befintliga *addItem* -knappen:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Lägg till följande-element i filen Strings. XML-resurser:
   
        <string name="login_button_text">Sign in</string>
5. Öppna filen AndroidManifest. xml och Lägg till följande kod inuti `<application>` XML-element:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. I Visual Studio eller Xamarin Studio kör du klient projektet på en enhet eller emulator och loggar in med den valda identitets leverantören. När du har loggat in visar appen ditt inloggnings-ID och listan med att göra-objekt, och du kan göra uppdateringar av data.

## <a name="troubleshooting"></a>Felsöka

**Programmet kraschade med `Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall visas konflikter i support paketen som en varning i Visual Studio, men programmet kraschar med detta undantag vid körning. I så fall måste du se till att alla de support paket som refereras till i ditt projekt har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) har ett `Xamarin.Android.Support.CustomTabs`-beroende för Android-plattformen, så om ditt projekt använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

<!-- URLs. -->
[Skapa en Xamarin. Android-app]: app-service-mobile-xamarin-android-get-started.md
