---
title: "Lägga till inloggning i en .NET MVC-webb-API med hjälp av Azure AD v2.0-slutpunkten | Microsoft Docs"
description: "Hur du skapar en .NET MVC webb-Api som accepterar token från både personliga Account och arbets-eller skolkonton."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 65f25e2496065ca1aaba443a9d6b3e29239e0218
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="secure-an-mvc-web-api"></a>Skydda en MVC-webb-API
Med Azure Active Directory v2.0-slutpunkten kan du skydda ett webb-API med hjälp av [OAuth 2.0](active-directory-v2-protocols.md) åtkomst-token så att användarna med både personliga Microsoft-konto och arbets- eller skolkonto konton för säker åtkomst webb-API.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

I ASP.NET webb-API: er, kan du göra detta med hjälp av Microsofts OWIN mellanprogram som ingår i .NET Framework 4.5.  Vi använder här OWIN för att skapa en ”att göra-lista” MVC webb-API som tillåter klienter att skapa och läsa uppgifter från en användares att göra-lista.  Webb-API verifierar att inkommande begäranden innehåller en giltig åtkomst-token och avvisa alla begäranden som inte klarar valideringen på en skyddad väg.  Det här exemplet har skapats med Visual Studio 2015.

## <a name="download"></a>Ladda ned
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) eller klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Appen stommen innehåller formaterad exempelkod för ett enkelt API, men saknar alla identitetsrelaterade bitar. Om du inte vill att följa instruktionerna du i stället klona eller [hämta det slutförda exemplet](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa dessa [detaljerade steg](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** tilldelats din app måste den snart.

Visual studio-lösning innehåller också en ”TodoListClient”, vilket är en enkel WPF-program.  TodoListClient används för att demonstrera hur en användare loggar in och hur en klient kan skicka begäranden till ditt webb-API.  I det här fallet representeras både TodoListClient och TodoListService av samma app.  Om du vill konfigurera TodoListClient, bör du också:

* Lägg till den **Mobile** plattform för din app.

## <a name="install-owin"></a>Installera OWIN
Nu när du har registrerat en app som du behöver konfigurera din app att kommunicera med v2.0-slutpunkten för att kunna verifiera inkommande förfrågningar och token.

* För att börja, öppna lösningen och Lägg till NuGet-paket OWIN mellanprogram TodoListService projektet med Package Manager-konsolen.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Konfigurera OAuth-autentisering
* Lägg till en OWIN-startklass i TodoListService-projektet som kallas `Startup.cs`.  Högerklicka på projektet--> **Lägg till** --> **nytt objekt** --> Sök efter ”OWIN”.  OWIN-mellanprogrammet anropar `Configuration(…)`-metoden när appen startas.
* Ändra klassdeklarationen till `public partial class Startup` -vi har implementerat en del av den här klassen som du redan i en annan fil.  I den `Configuration(…)` metod, gör ett anrop till ConfgureAuth(...) du konfigurerar autentisering för ditt webbprogram.

```csharp
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Öppna filen `App_Start\Startup.Auth.cs` och genomföra den `ConfigureAuth(…)` metod som ställer in webb-API för att acceptera token från v2.0-slutpunkten.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Nu kan du använda `[Authorize]` attribut för att skydda dina domänkontrollanter och åtgärder med OAuth 2.0-ägar-autentisering.  Skapa snygga den `Controllers\TodoListController.cs` klass med en auktorisera-tagg.  Detta tvingar användaren att logga in innan sidan.

```csharp
[Authorize]
public class TodoListController : ApiController
{
```

* När en auktoriserad anroparen har anropar någon av de `TodoListController` API: er, åtgärden kanske behöver åtkomst till information om anroparen.  OWIN ger tillgång till anspråk i ägartoken via den `ClaimsPrincipal` objekt.  

```csharp
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Slutligen, öppna den `web.config` filen i roten av projektet TodoListService och ange dina konfigurationsvärden i den `<appSettings>` avsnitt.
  * Din `ida:Audience` är den **program-Id** för den app som du angav på portalen.

## <a name="configure-the-client-app"></a>Konfigurera klientappen
Innan du kan se tjänsten Todo i åtgärden måste du konfigurera klienten för Todo-listan så att den kan hämta token från v2.0-slutpunkten och anrop till tjänsten.

* Öppna i projektet TodoListClient `App.config` och ange dina konfigurationsvärden i den `<appSettings>` avsnitt.
  * Din `ida:ClientId` program-Id som du kopierade från portalen.

Slutligen, rensa, skapa och köra varje projekt!  Nu har du en .NET MVC webb-API som accepterar token från både personliga Microsoft-konton och arbets-eller skolkonton.  Logga in på TodoListClient och anropa dina webb-api för att lägga till aktiviteter i användarens att göra-lista.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) [har angetts som en .zip här](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), eller kan du klona den från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till ytterligare information.  Du kanske vill prova:

[Anropa ett webb-API från ett webbprogram >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

För ytterligare resurser, kolla:

* [Utvecklarhandbok v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow ”azure-active-directory” taggen >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.
