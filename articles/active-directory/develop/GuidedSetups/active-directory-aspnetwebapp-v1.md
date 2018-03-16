---
title: "Azure AD v1 ASP.NET webbserver komma igång | Microsoft Docs"
description: "Implementera Microsoft logga In på en ASP.NET-lösning med ett traditionellt webbläsarbaserade program med OpenID Connect standard"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med en traditionell webbläsarbaserad webbapp med OpenID Connect. 

I slutet av den här guiden tillämpningsprogrammet accepterar inloggningar av arbets- och skolkonton konton från organisationer som har integrerat med Azure Active Directory.

> [!NOTE]
> Den här interaktiv installation kan du aktivera inloggningar från arbets- och skolkonton konton i ASP.NET-programmet. Om du är intresserad av att aktivera inloggningar för personliga konton förutom arbets-och skolkonton kan du använda den [v2 endpoint](../active-directory-v2-compare.md). Se [denna ASP.NET interaktiv installation för slutpunkten v2](./active-directory-aspnetwebapp.md) samt [det här dokumentet](../active-directory-v2-limitations.md) förklarar aktuella begränsningar för v2-slutpunkten.
<br/><br/>

<!--separator-->

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har det?  [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Den här guiden bygger på ett scenario där en webbläsare har åtkomst till en ASP.NET-webbplats, begär en användare autentiseras via en knapp för inloggning. I det här scenariot inträffar mesta av arbetet ska renderas webbsidan på serversidan.

> [!NOTE]
> Interaktiv installationen visar hur du loggar in användare i ASP.NET-webbprogram från en tom mall och innehåller steg som att lägga till ett tecken i knappen och varje styrenhet och metoder, samtidigt som förklarar också några begrepp. Alternativt kan du också skapa ett projekt för att logga in Azure Active Directory användare (arbets- och skolkonton konton) med hjälp av den [Visual Studio webbmallen](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) och välja *Organisationskonton* och sedan något av alternativen cloud - Det här alternativet använder en rikare mall med ytterligare domänkontrollanter, metoder och vyer.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som aktiverar ett program att använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som aktiverar ett program att underhålla användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program körs på IIS med hjälp av ASP.NET förfrågnings-pipelinen|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet visar hur du installerar och konfigurerar autentisering pipeline via OWIN mellanprogram på en ASP.NET-projekt med OpenID Connect. 

> Om du vill hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) och gå vidare till den [konfigurationssteget](#configure-your-webconfig-and-register-an-application) konfigurera kodexemplet innan du kör.

## <a name="create-your-aspnet-project"></a>Skapa din ASP.NET-projekt
1. I Visual Studio: `File` > `New` > `Project`<br/>
2. Under *Visual C# \Web*väljer `ASP.NET Web Application (.NET Framework)`.
3. Namnge ditt program och klicka på *OK*
4. Välj `Empty` och markera sedan kryssrutan för att lägga till `MVC` referenser

## <a name="add-authentication-components"></a>Lägga till autentiseringskomponenter

1. I Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Lägg till *OWIN mellanprogram NuGet-paket* genom att skriva följande i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Om dessa paket
>Bibliotek ovan aktivera enkel inloggning (SSO) med OpenID Connect via cookie-baserad autentisering. När autentiseringen är slutförd och token som representerar användaren skickas till ditt program, skapar OWIN mellanprogram sessions-cookie. Webbläsaren använder sedan cookien för efterföljande förfrågningar så att användaren inte behöver autentiseras och ingen ytterligare verifiering krävs.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentisering pipeline
Följande steg används för att skapa en OWIN-mellanprogram *startklass* konfigurera OpenID Connect autentisering. Den här klassen körs automatiskt.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs` filen i rotmappen:<br/>
> 1. Högerklicka på projektets rotmapp: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Ge den namnet `Startup.cs`<br/>
>
>> Kontrollera att den klass som valts är en OWIN-startklass och inte en standard C#-klass. Kontrollera detta genom att kontrollera om du ser `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` ovanför namnområdet.


1. Lägg till *OWIN* och *Microsoft.IdentityModel* namnområden till `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Ersätt startklass med följande kod:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för programmet som ska kommunicera med Azure AD. Eftersom OpenID Connect mellanprogram använder cookies, måste du också konfigurera cookie-autentisering som i föregående kod. Den *ValidateIssuer* värde anger OpenIdConnect inte begränsa åtkomsten till en specifik organisation.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägg till en domänkontrollant för att hantera begäranden för inloggning och utloggning

Det här steget visar hur du skapar en ny domänkontrollant om du vill exponera metoder för inloggning och utloggning.

1.  Högerklicka på den `Controllers` och väljer `Add` > `Controller`
2.  Välj `MVC (.NET version) Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `HomeController` och på *Lägg till*
5.  Lägg till *OWIN* namnområden för klassen:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Lägg till följande metoder för att hantera inloggning och utloggning till en styrenhet genom att initiera en autentiseringsfråga via kod:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Skapa appens startsida för att logga in användare via en knapp för inloggning

Skapa en ny vy för att lägga till knappen Logga in och visa information om användare efter autentisering i Visual Studio:

1.  Högerklicka på den `Views\Home` och väljer `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML, som innehåller knappen Logga in i filen:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Den här sidan lägger till en knapp för inloggning i SVG-format med svart bakgrund:<br/>![Logga in med Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Gå till flera logga in, på knappar [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "företagsanpassning riktlinjer").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Visa användarens anspråk genom att lägga till en domänkontrollant
Den här domänkontrollanten visar användning av den `[Authorize]` attribut för att skydda en domänkontrollant. Det här attributet begränsar åtkomsten till styrenhet genom att bara tillåta autentiserade användare. Följande kod gör använda för attributet för att visa användaranspråk som har hämtats som en del av inloggningen.

1.  Högerklicka på den `Controllers` mapp: `Add` > `Controller`
2.  Välj `MVC {version} Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `ClaimsController`
5.  Ersätt Koden i controller-klassen med följande kod - läggs till i `[Authorize]` attribut i klassen:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> På grund av användningen av den `[Authorize]` attribut, alla metoderna för den här styrenheten kan endast utföras om användaren har autentiserats. Om användaren inte har autentiserats och försöker komma åt domänkontrollanten, initierar en autentiseringsfråga OWIN och tvinga användaren att autentisera. Koden ovan tittar på samlingen anspråk för användaren efter specifika attribut som ingår i användarens token. Attributen omfattar användarens fullständiga namn och användarnamn samt global användare identifierare ämne. Det innehåller även den *klient-ID*, som representerar ID för användarens organisation. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy för att visa användarens anspråk i en webbsida i Visual Studio:

1.  Högerklicka på den `Views\Claims` mappen och: `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML i filen:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurera din *web.config* och registrera ett program

1. Lägg till följande i Visual Studio `web.config` (finns i rotmappen) under avsnittet `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. Välj projektet i Solution Explorer och titta på den <i>egenskaper</i> fönstret (om du inte ser en egenskapsfönstret trycker du på F4)
3. Ändra SSL aktiverat för <code>True</code>
4. Kopiera projektets SSL URL till Urklipp:<br/><br/>![Egenskaper för](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. I <code>web.config</code>, Ersätt <code>Enter_the_Redirect_URL_here</code> med SSL-URL för ditt projekt 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registrera ditt program i Azure-portalen och sedan lägga till dess information i *web.config*

1. Gå till den [Microsoft Azure Portal - App registreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) registrera ett program
2. Välj `New application registration`
3. Ange ett namn för ditt program
4. Klistra in Visual Studio-projekt *SSL URL* i `Sign-on URL` (Webbadressen också läggs automatiskt till i listan över svars-URL: er för programmet som du registrerar)
5. Klicka på `Create` registrera programmet. Den här åtgärden går du till listan med program
6. Nu kan söka och/eller välj ett program som du just har skapat för att öppna dess egenskaper
7. Kopiera guid under `Application ID` till Urklipp
8. Gå tillbaka till Visual Studio och i `web.config`, Ersätt `Enter_the_Application_Id_here` med program-ID från programmet som du precis har registrerats

> [!TIP]
> Om ditt konto har konfigurerats för åtkomst till flera kataloger, se till att du har valt rätt katalog för organisationen du vill att programmet ska registreras genom att klicka på namnet på ditt konto i upp till höger i Azure-portalen och verifiera den valda katalogen som anges:<br/>![Att välja rätt katalogen](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Konfigurera inloggningsalternativ

Du kan konfigurera programmet så att endast användare som tillhör en organisations Azure Active Directory-instans för att logga in, eller Godkänn inloggningar från användare som hör till en organisation. Följ instruktionerna i något av följande alternativ:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Konfigurera programmet så att inloggningar av arbets- och skolkonton konton från alla företag eller din organisation (flera innehavare)

Följ anvisningarna nedan om du vill acceptera inloggningar av arbets- och skolkonton konton från alla företag eller organisation som har integrerat med Azure Active Directory. Det här är ett vanligt scenario för *SaaS-program*:

1. Gå tillbaka till [Microsoft Azure Portal - App registreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) och leta reda på programmet som du precis har registrerats
2. Under `All Settings` väljer `Properties`
3. Ändra `Multi-tenanted` egenskapen `Yes` och klicka på `Save`

Mer information om den här inställningen och begreppet program med flera klienter finns [i den här artikeln](../active-directory-devhowto-multi-tenant-overview.md "översikt över flera innehavare").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Begränsa användare från Active Directory-instans i en enda organisation att logga in på ditt program (single-klient)

Det här alternativet är ett vanligt scenario för *LOB-program*: Om du vill att programmet ska acceptera inloggningar från konton som tillhör en viss Azure Active Directory-instans (inklusive *gästkonton*på instansen), ersätter den `Tenant` parameter i *web.config* från `Common` med klientnamnet för organisationen – exempelvis *contoso.onmicrosoft.com*. Sedan kan ändra den `ValidateIssuer` argument i din [ *OWIN-startklass* ](#configure-the-authentication-pipeline) till `true`.

Om du vill tillåta att användare från en lista med specifika organisationer, ange `ValidateIssuer` till true och använda den `ValidIssuers` parametern för att ange en lista över organisationer.

Ett annat alternativ är att implementera en anpassad metod för att verifiera de utfärdare som använder *IssuerValidator* parameter. Mer information om `TokenValidationParameters`, se [MSDN-artikel](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-artikel").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Testa din kod

Tryck på `F5` köra projektet i Visual Studio. Webbläsaren öppnas och dirigerar dig till *http://localhost:{port}* där du ser den *logga in med Microsoft* knappen. Gå vidare och genom att klicka på Logga in.

När du är redo att testa, använda ett arbetskonto (Azure Active Directory) för att logga in. 

![Logga in med Microsoft webbläsarfönster](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Logga in med Microsoft webbläsarfönster](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Förväntat resultat
Efter inloggning omdirigeras användaren till startsidan för din webbplats är HTTPS-URL som anges i ditt program registreringsinformation i portalen för registrering av Microsoft-program. Den här sidan visas nu *Hello {User}* och en länk till logga ut och en länk till finns användaranspråk – som är en länk till auktorisera controller skapade tidigare.

### <a name="see-users-claims"></a>Se användarens anspråk
Välj länken för att se användarens anspråk. Den här åtgärden leder dig till domänkontrollanten och visa som endast är tillgängliga för användare som autentiseras.

#### <a name="expected-results"></a>Förväntat resultat
 Du bör se en tabell som innehåller de grundläggande egenskaperna för den inloggade användaren:

| Egenskap | Värde | Beskrivning|
|---|---|---|
| Namn | {Fullständig användarnamn} | Användaren förnamn och efternamn
|Användarnamn | <span>user@domain.com</span>| Användarnamnet som används för att identifiera den inloggade användaren
| Ämne| {Ämne}|En sträng för att identifiera användaren loggar in på Internet|
| Klient-ID:t| {Guid}| En *guid* som unikt representerar användarens Azure Active Directory-organisation.|

Dessutom visas en tabell inklusive alla användaranspråk som ingår i autentiseringsbegäran. En lista över alla anspråk i en ID-Token och deras beskrivning finns [artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "lista av anspråk i ID-Token").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har en *[auktorisera]* attribut (valfritt)
I det här steget kan testa du åtkomst till anspråk domänkontrollant som en anonym användare:<br/>
Välj en länk till utloggning användaren och slutföra utloggning.<br/>
Skriv nu i din webbläsare http://localhost:{port}/utger sig för att komma åt den domänkontrollant som är skyddat med den `[Authorize]` attribut

#### <a name="expected-results"></a>Förväntat resultat
Du får uppmaningen att kräva att du autentisera för att visa.

## <a name="additional-information"></a>Ytterligare information

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Skydda hela webbplatsen
För att skydda hela webbplatsen, lägger du till den `AuthorizeAttribute` till `GlobalFilters` i `Global.asax` `Application_Start` metod:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->