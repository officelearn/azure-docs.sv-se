---
title: Azure Active Directory v2.0 .NET inbyggda appen | Microsoft Docs
description: "Hur du skapar en intern app för .NET som loggar användarna in med både personliga Account och arbets-eller skolkonton."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7436db2943a6b3de6ec53cdaa6692aa05d2f2f69
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Lägga till inloggning till en app för Windows-skrivbordet
Med den v2.0-slutpunkten du kan snabbt lägga till autentisering för att dina-program med stöd för både personliga Microsoft-konton och arbets-eller skolkonton.  Det gör också att din app för säker kommunikation med en serverdel webb-api, samt [Microsoft Graph](https://graph.microsoft.io) och några av de [Office 365 Unified-API: er](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Inte alla Azure Active Directory (AD)-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

För [.NET interna appar som körs på en enhet](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD innehåller Autentiseringsbibliotek för Microsoft Identity eller MSAL.  MSALS uteslutande i livslängd är att göra det lättare för din app att hämta token för att anropa webbtjänster.  För att demonstrera hur lätt det är ska här vi skapa en uppgiftslista för .NET WPF-app som:

* Användaren loggar in och hämtar åtkomst-token som använder den [OAuth 2.0-autentiseringsprotokollet](active-directory-v2-protocols.md).
* På ett säkert sätt anropar en serverdel uppgiftslista webbtjänsten, som också är skyddad av OAuth 2.0.
* Användaren sedan loggar ut.

## <a name="download-sample-code"></a>Hämta exempelkoden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) eller klona stommen:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

Den färdiga appen finns i slutet av den här kursen samt.

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa dessa [detaljerade steg](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** tilldelats din app måste den snart.
* Lägg till den **Mobile** plattform för din app.

## <a name="install--configure-msal"></a>Installera och konfigurera MSAL
Du kan installera MSAL och Skriv koden identitetsrelaterade nu när du har en app som registrerats med Microsoft.  Du måste tillhandahålla information om din appregistrering för MSAL för att kunna kommunicera v2.0-slutpunkten.

* Börja genom att lägga till MSAL TodoListClient projektet med Package Manager-konsolen.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* Öppna i projektet TodoListClient `app.config`.  Ersätt värdena för elementen i den `<appSettings>` avsnittet för att återspegla de värden du matar in i portalen för registrering av app.  Koden ska referera till dessa värden när den används i MSAL.
  
  * Den `ida:ClientId` är den **program-Id** för din app som du kopierade från portalen.
* Öppna i TodoList-Service-projekt `web.config` i roten av projektet.  
  
  * Ersätt den `ida:Audience` värde med samma **program-Id** från portalen.

## <a name="use-msal-to-get-tokens"></a>Använd MSAL för att hämta token
Den grundläggande principen bakom MSAL är att när en app måste en åtkomst-token, ringer du bara `app.AcquireToken(...)`, och MSAL gör resten.  

* I den `TodoListClient` projektet öppnar `MainWindow.xaml.cs` och leta upp den `OnInitialized(...)` metoden.  Det första steget är att initiera appens `PublicClientApplication` -MSALS primära klass som representerar interna program.  Det är där du skickar MSAL koordinaterna behöver kommunicera med Azure AD och hur det kan cachelagra token.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Vi vill att kontrollera om användaren redan har loggat in på app när appen startar.  Dock bör inte att anropa en inloggning UI ännu - vi kommer att användaren klicka på ”Logga In” för att göra det.  Även i den `OnInitialized(...)` metoden:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Om användaren inte är inloggad och de klickar på knappen ”Logga In”, som vi vill anropa en UI-inloggning och se till att användaren anger sina autentiseringsuppgifter.  Implementera knappen hanteraren inloggning:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Om användaren har loggar in, MSAL kommer få och cachelagra en token för dig och du kan fortsätta att anropa den `GetTodoList()` metod med förtroende.  Allt som återstår för att hämta en användares uppgifter är att implementera den `GetTodoList()` metoden.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Kör
Grattis! Nu har du en fungerande .NET WPF-program som har möjlighet att autentisera användare & på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0.  Köra båda projekten och logga in med ett personligt microsoftkonto eller ett arbets- eller skolkonto konto.  Lägg till aktiviteter i användarens att göra-lista.  Logga ut och logga in igen som en annan användare att visa sina att göra-lista.  Stäng appen och kör den igen.  Observera hur användarens session förblir intakta, som beror på att appen cachelagrar token i en lokal fil.

MSAL gör det enkelt att införliva vanliga identity-funktioner i din app använder både personliga och arbetsrelaterade konton.  Det hand tar om ändrad arbetet åt dig - hantering av cache, OAuth protokollstöd presentera användaren med en inloggning UI, uppdatera token har upphört att gälla och mycket mer.  Allt du behöver veta är ett enda API-anrop `app.AcquireTokenAsync(...)`.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) [har angetts som en .zip här](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), eller kan du klona den från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till mer avancerade avsnitt.  Du kanske vill prova:

* [Skydda TodoListService webb-API med v2.0-slutpunkten](active-directory-v2-devquickstarts-dotnet-api.md)

För ytterligare resurser, kolla:  

* [Utvecklarhandbok v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow ”msal” taggen >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.

