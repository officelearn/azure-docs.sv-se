---
title: Azure AD v1 ASP.NET-webbserver komma igång | Microsoft Docs
description: Implementera Microsoft-inloggning på en ASP.NET-lösning med ett traditionellt webbläsarbaserade program med OpenID Connect-standarden
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: andret
ms.openlocfilehash: 5353e22d7ae77adecfe126bb589d08c808752550
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579358"
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till logga in med Microsoft i en ASP.NET-webbapp

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med ett traditionellt webbläsarbaserade webbprogram med OpenID Connect. 

I slutet av den här guiden ditt program ska ta emot inloggningar av arbets- och skolkonton konton från organisationer som har integrerat med Azure Active Directory.

> [!NOTE]
> Den här guidade konfigurationen hjälper dig att aktivera inloggningar från arbets- och skolkonton konton i din ASP.NET-program. Om du vill aktivera inloggningar för personliga konton förutom arbets-och skolkonton, du kan använda den [v2-slutpunkten](azure-ad-endpoint-comparison.md). Se [den här ASP.NET interaktiva installation för v2-slutpunkten](tutorial-v2-asp-webapp.md) samt [det här dokumentet](active-directory-v2-limitations.md) förklarar de aktuella begränsningarna för v2-slutpunkten.
<br/><br/>

<!--separator-->

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har den inte?  [Ladda ned Visual Studio 2017 utan kostnad](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Hur fungerar den här guiden

![Hur fungerar den här guiden](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

Den här guiden är baserat på scenariot där en webbläsare ansluter till en ASP.NET-webbplats, begär en användare att autentisera via en knapp för inloggning. I det här scenariot inträffar mesta av arbetet att återge webbsidan på serversidan.

> [!NOTE]
> Den här guidade konfigurationen visar hur du loggar in användare på ASP.NET-webbprogram med början från en tom mall och inkludera steg, till exempel att lägga till ett tecken i knappen och varje styrenhet och metoder, samtidigt som förklarar också vissa begrepp. Alternativt kan du också skapa ett projekt för att logga in Azure Active Directory användare (arbets- och skolkonton konton) med hjälp av den [Visual Studio webbmallen](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) och välja *Organisationskonton* och sedan en av molnalternativ – det här alternativet använder en mall för bättre med ytterligare domänkontrollanter, metoder och vyer.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program att använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som gör att ett program att underhålla användarsessionen med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Aktiverar OWIN-baserade program som körs i IIS med hjälp av ASP.NET förfrågnings-pipelinen|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet visar hur du installerar och konfigurerar autentiseringspipelinen via OWIN-mellanprogrammet på en ASP.NET-projekt med hjälp av OpenID Connect. 

> Om du vill ladda ned det här exemplet Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) och gå vidare till den [konfigurationssteget](#configure-your-webconfig-and-register-an-application) konfigurera kodexemplet innan du kör.

## <a name="create-your-aspnet-project"></a>Skapa din ASP.NET-projekt
1. I Visual Studio: `File` > `New` > `Project`<br/>
2. Under *Visual C# \Web*väljer `ASP.NET Web Application (.NET Framework)`.
3. Namnge ditt program och klicka på *OK*
4. Välj `Empty` och välj sedan kryssrutan för att lägga till `MVC` referenser

## <a name="add-authentication-components"></a>Lägg till autentiseringskomponenter

1. I Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Lägg till *OWIN-mellanprogrammet NuGet-paket* genom att skriva följande i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Om dessa paket
>Bibliotek ovan aktivera enkel inloggning (SSO) med hjälp av OpenID Connect via cookie-baserad autentisering. När autentiseringen är slutförd och den token som representerar användaren skickas till programmet, skapar OWIN-mellanprogrammet en sessions-cookie. Webbläsaren använder sedan den här cookien för efterföljande förfrågningar så att användaren behöver inte att autentiseras på nytt och ingen ytterligare verifiering krävs.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurera autentiseringspipelinen
Följande steg används för att skapa en OWIN-mellanprogrammet *startklass* konfigurera autentisering med OpenID Connect. Den här klassen körs automatiskt.

> [!TIP]
> Om ditt projekt inte har en `Startup.cs` filen i rotmappen:<br/>
> 1. Högerklicka på projektets rotmappen: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Ge den namnet `Startup.cs`<br/>
>
>> Kontrollera att den klass som valts är en OWIN-startklass och inte en standard C#-klass. Kontrollera detta genom att kontrollera om du ser `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` ovan namnområdet.


1. Lägg till *OWIN* och *Microsoft.IdentityModel* namnområden till `Startup.cs`:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Ersätt-startklass med följande kod:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för programmet för att kommunicera med Azure AD. Eftersom OpenID Connect-mellanprogram använder cookies kan behöva du också konfigurera cookie-autentisering som i föregående kod. Den *ValidateIssuer* värdet talar om OpenIdConnect att begränsa inte åtkomst till en specifik organisation.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Lägga till en styrenhet för att hantera inloggning och utloggning begäranden

Det här steget visar hur du skapar en ny kontrollant som exponerar metoder för inloggning och utloggning.

1.  Högerklicka på den `Controllers` mappen och välj `Add` > `Controller`
2.  Välj `MVC (.NET version) Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `HomeController` och klicka på *Lägg till*
5.  Lägg till *OWIN* namnområden till klassen:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Lägg till följande metoder för att hantera inloggning och utloggning i din kontrollant genom att initiera en autentiseringsfråga via kod:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Skapa appens startsida för inloggning av användare via en knapp för inloggning

I Visual Studio skapar du en ny vy för att lägga till knappen Logga in och Visa användarinformation efter autentisering:

1.  Högerklicka på den `Views\Home` mappen och välj `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML, som innehåller knappen Logga in i filen:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Den här sidan lägger till en knapp för inloggning i SVG-format med en svart bakgrund:<br/>![Logga in med Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> För mer inloggning knapparna går du till [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "riktlinjer för anpassning").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Visa användarens anspråk genom att lägga till en kontrollant
Den här styrenheten visar användningen av den `[Authorize]` attribut för att skydda en domänkontrollant. Det här attributet begränsar åtkomsten till kontrollanten genom att bara tillåta autentiserade användare. I följande kod gör använder attributet för att visa användaranspråk som hämtades som en del av inloggningen.

1.  Högerklicka på den `Controllers` mapp: `Add` > `Controller`
2.  Välj `MVC {version} Controller – Empty`.
3.  Klicka på *Lägg till*
4.  Ge den namnet `ClaimsController`
5.  Ersätt Koden i din kontrollantklassen med följande kod – detta lägger till den `[Authorize]` attributet klassen:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> På grund av användningen av den `[Authorize]` attribut, alla metoder för den här domänkontrollanten kan endast utföras om användaren har autentiserats. Om användaren inte har autentiserats och försöker få åtkomst till kontrollanten, initierar en autentiseringsfråga OWIN och tvinga användaren att autentisera. Koden ovan tittar på anspråk insamling av användaren efter specifika attribut som ingår i användarens token. Dessa attribut är användarens fullständiga namn och användarnamn, samt global användare identifierare ämne. Den innehåller också de *klient-ID*, som representerar ID för användarens organisation. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Skapa en vy för att visa användarens anspråk

Skapa en ny vy för att visa användarens anspråk på en webbsida i Visual Studio:

1.  Högerklicka på den `Views\Claims` mappen och: `Add View`
2.  Ge den namnet `Index`.
3.  Lägg till följande HTML-koden i filen:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurera din *web.config* och registrera ett program

1. I Visual Studio lägger du till följande till `web.config` (som finns i rotmappen) under avsnittet `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. Välj projektet i Solution Explorer och titta på den <i>egenskaper</i> fönstret (om du inte ser en egenskapsfönstret trycker på F4)
3. Ändra SSL aktiverat till <code>True</code>
4. Kopiera projektets SSL URL till Urklipp:<br/><br/>![Projektegenskaper](./media/quickstart-v1-aspnet-webapp/visual-studio-project-properties.png)<br />
5. I <code>web.config</code>, Ersätt <code>Enter_the_Redirect_URL_here</code> med SSL-URL: en för ditt projekt 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registrera ditt program i Azure Portal och sedan lägga till dess information till *web.config*

1. Gå till den [Microsoft Azure Portal - App-registreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) att registrera ett program
2. Välj `New application registration`
3. Ange ett namn för ditt program
4. Klistra in Visual Studio-projektet *SSL URL* i `Sign-on URL` (URL: en också läggs automatiskt till i listan över svars-URL för programmet som du registrerar)
5. Klicka på `Create` registrera programmet. Den här åtgärden tar dig tillbaka till listan med program
6. Nu kan söka och/eller välj ett program som du nyss skapade för att öppna dess egenskaper
7. Kopiera guid under `Application ID` till Urklipp
8. Gå tillbaka till Visual Studio och i `web.config`, Ersätt `Enter_the_Application_Id_here` med program-ID från programmet som du just registrerade

> [!TIP]
> Om ditt konto har konfigurerats för att ansluta till flera kataloger ska du kontrollera att du har valt rätt katalog för organisationen vill du programmet registreras genom att klicka på namnet på ditt konto längst upp direkt i Azure-portalen och verifiera den valda katalogen som anges:<br/>![Att välja rätt katalog](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="configure-sign-in-options"></a>Konfigurera alternativ för användarinloggning i

Du kan konfigurera ditt program så att endast användare som tillhör en organisations Azure Active Directory-instans för att logga in, eller Godkänn inloggningar från användare som tillhör en organisation. Följ anvisningarna i någon av följande alternativ:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Konfigurera ditt program så att inloggningar av arbets- och skolkonton konton från alla företag eller din organisation (flera innehavare)

Följ anvisningarna nedan om du vill godkänna inloggningar av arbets- och skolkonton konton från alla företag eller din organisation som har integrerat med Azure Active Directory. Det här är ett vanligt scenario för *SaaS-program*:

1. Gå tillbaka till [Microsoft Azure Portal - App-registreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) och leta upp det program som du just registrerade
2. Under `All Settings` väljer `Properties`
3. Ändra `Multi-tenanted` egenskap `Yes` och klicka på `Save`

Mer information om den här inställningen och begreppet program med flera klienter finns i [i den här artikeln](howto-convert-app-to-be-multi-tenant.md "översikt för flera innehavare").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Hindra användare från att endast en organisations Active Directory-instans för att logga in på ditt program (en enda klient)

Det här alternativet är ett vanligt scenario för *LOB-program*: Om du vill att programmet ska godkänna inloggningar från konton som tillhör en viss Azure Active Directory-instans (inklusive *gästkonton*instansens), ersätter den `Tenant` parameter i *web.config* från `Common` med klientnamnet för organisationen – exempelvis *contoso.onmicrosoft.com*. Efter det att ändra den `ValidateIssuer` argumentet i din [ *OWIN-startklass* ](#configure-the-authentication-pipeline) till `true`.

Om du vill tillåta användare från en lista över specifika organisationer, ange `ValidateIssuer` till true och använda den `ValidIssuers` parametern för att ange en lista över organisationer.

Ett annat alternativ är att implementera en anpassad metod för att verifiera de utfärdare som använder *IssuerValidator* parametern. Mer information om `TokenValidationParameters`, se [den här MSDN-artikeln](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-artikeln").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](./media/quickstart-v1-aspnet-webapp/vsprojectproperties.png)<br />
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

Tryck på `F5` köra projektet i Visual Studio. Webbläsaren öppnas och du *http://localhost:{port}* där du ser den *logga in med Microsoft* knappen. Gå vidare och klicka på den för att logga in.

När du är redo att testa, använda ett arbetskonto (Azure Active Directory) för att logga in. 

![Logga in med Microsoft webbläsarfönster](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin.png)

![Logga in med Microsoft webbläsarfönster](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Förväntat resultat
När du loggar in omdirigeras användaren till sidan för din webbplats är HTTPS-URL som anges i ditt programs registreringsinformation i portalen för registrering av Microsoft-program. Den här sidan visar nu *Hello {User}* och en länk för att logga ut och en länk till finns i användarens anspråk – vilket är en länk till kontrollanten auktorisera skapade tidigare.

### <a name="see-users-claims"></a>Finns i användarens anspråk
Väljer du hyperlänken för att se användarens anspråk. Den här åtgärden leder dig till domänkontrollanten och vy som endast är tillgänglig för användare som har autentiserats.

#### <a name="expected-results"></a>Förväntat resultat
 Du bör se en tabell som innehåller de grundläggande egenskaperna för den inloggade användaren:

| Egenskap  | Värde | Beskrivning|
|---|---|---|
| Namn | {Användarens fullständiga namn} | Användaren förnamn och efternamn
|Användarnamn | <span>user@domain.com</span>| Användarnamnet som används för att identifiera den inloggade användaren
| Ämne| {Subject}|En sträng att unikt identifiera användaren loggar in på webben|
| Klient-ID:t| {Guid}| En *guid* som unikt representerar användarens Azure Active Directory-organisation.|

Dessutom kan se du en tabell, inklusive alla användaranspråk som ingår i autentiseringsbegäran. En lista över alla anspråk i en ID-Token och en beskrivning, finns i den här [artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "lista av anspråk i ID-Token").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har en *[auktorisera]* attribut (valfritt)
I det här steget ska testa du åtkomst kontrollanten anspråk som anonym användare:<br/>
Välj länken för att logga ut användaren och slutföra utloggningen.<br/>
Skriv nu i din webbläsare http://localhost:{port}/claims att få åtkomst till din domänkontrollant som är skyddat med den `[Authorize]` attribut

#### <a name="expected-results"></a>Förväntat resultat
Du får uppmaningen att du behöver autentisera sig för att visa.

## <a name="additional-information"></a>Ytterligare information

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Skydda hela webbplatsen
För att skydda hela webbplatsen kan du lägga till den `AuthorizeAttribute` till `GlobalFilters` i `Global.asax` `Application_Start` metod:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->