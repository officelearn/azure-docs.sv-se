---
title: "Azure AD-.NET-webbapp komma igång | Microsoft Docs"
description: "Skapa en .NET MVC-webbapp som kan integreras med Azure AD för inloggning."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5025ebda6eb47c4155c098be4a5479d5c3814942
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>ASP.NET-webbprogram inloggning och utloggning med Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Genom att tillhandahålla en enkel inloggning och utloggning med bara några få rader med kod gör Azure Active Directory (AD Azure) det enkelt för dig att flytta ut webbappen Identitetshantering. Du kan signera användare aktivera och inaktivera ASP.NET-webbprogram med hjälp av Microsofts implementering av Open Web Interface för .NET (OWIN) mellanprogram. Community-driven OWIN mellanprogram ingår i .NET Framework 4.5. Den här artikeln visar hur du använder OWIN till:

* Logga in användare till webbprogram med hjälp av Azure AD som identitetsleverantören.
* Visa viss användarinformation.
* Logga in användare utanför apparna.

## <a name="before-you-get-started"></a>Innan du börjar
* Hämta den [app stommen](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) eller hämta den [färdiga exemplet](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Du måste också en Azure AD-klient som ska registrera appen. Om du inte redan har en Azure AD-klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

När du är klar följer du procedurerna i följande fyra avsnitt.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Steg 1: Registrera den nya appen med Azure AD
Om du vill konfigurera appen för att autentisera användare måste först registrera det i din klient genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på namnet på ditt konto på den översta raden. Under den **Directory** väljer du Active Directory-klient som du vill registrera appen.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Följ anvisningarna för att skapa en ny **webbprogram och/eller WebAPI**.
  * **Namnet** beskriver app till användare.
  * **Inloggnings-URL** är den grundläggande Webbadressen till appen. Standard-URL för den stommen är https://localhost:44320 /.
6. När du har slutfört registreringen, tilldelar Azure AD appen ett unikt-ID. Kopiera värdet från appsidan att använda i nästa avsnitt.
7. Från den **inställningar** -> **egenskaper** för programmet, uppdatera App-ID-URI. Den **App-ID URI** är en unik identifierare för appen. Namngivningskonventionen är `https://<tenant-domain>/<app-name>` (till exempel `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Steg 2: Konfigurera appen för att använda OWIN autentisering pipeline
I det här steget konfigurerar du OWIN-mellanprogrammet att använda autentiseringsprotokollet OpenID Connect. Du kan använda OWIN för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarsessioner, hämta användarinformation och så vidare.

1. Om du vill börja lägga till OWIN mellanprogram NuGet-paket i projektet med hjälp av Package Manager-konsolen.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Att lägga till en OWIN-startklass i projektet med namnet `Startup.cs`, högerklicka på projektet, Välj **Lägg till**väljer **nytt objekt**, och sök sedan efter **OWIN**. OWIN-mellanprogram anropar den **Configuration(...)**  metod när appen startar.
3. Ändra klassdeklarationen till `public partial class Startup`. Vi har redan implementerats en del av den här klassen för dig i en annan fil. I den **Configuration(...)**  gör ett anrop till metoden **ConfgureAuth(...)**  du konfigurerar autentisering för appen.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Öppna filen App_Start\Startup.Auth.cs och sedan implementera den **ConfigureAuth(...)**  metod. De parametrar som du anger i *OpenIDConnectAuthenticationOptions* fungerar som koordinater för appen för att kommunicera med Azure AD. Du måste också konfigurera cookie-autentisering, eftersom OpenID Connect mellanprogram använder cookies i bakgrunden.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Öppna web.config-filen i roten av projektet och sedan ange konfigurationsvärden i den `<appSettings>` avsnitt.
  * `ida:ClientId`: Det GUID som du kopierade från Azure-portalen i ”steg 1: registrera den nya appen med Azure AD”.
  * `ida:Tenant`: Namnet på din Azure AD-klient (till exempel contoso.onmicrosoft.com).
  * `ida:PostLogoutRedirectUri`: Indikatorn som talar om Azure AD där en användare ska omdirigeras när en begäran om utloggning har slutförts.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Steg 3: Använd OWIN att utfärda inloggnings- och utloggningsförfrågningar till Azure AD
Appen har nu konfigurerats korrekt för att kommunicera med Azure AD med hjälp av autentiseringsprotokollet OpenID Connect. OWIN hanterades alla detaljer utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessioner. Allt som återstår är att ge användarna ett sätt att logga in och logga ut.

1. Du kan använda auktorisera taggar i dina domänkontrollanter användare måste logga in innan de får tillgång till vissa sidor. Om du vill göra det, öppna Controllers\HomeController.cs och Lägg sedan till den `[Authorize]` tagg om åtgärden.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. Du kan också använda OWIN för att skicka autentiseringsbegäranden från direkt i din kod. Det gör du genom att öppna Controllers\AccountController.cs. Sedan kan utfärda OpenID Connect utmaning och utloggning begäranden i SignIn() och SignOut() åtgärder.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Öppna Views\Shared\_LoginPartial.cshtml att visa användaren app inloggning och utloggning länkar, och för att skriva ut användarens namn i en vy.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
             </li>
             <li>
                 @Html.ActionLink("Sign out", "SignOut", "Account")
             </li>
         </ul>
     </text>
    }
    else
    {
     <ul class="nav navbar-nav navbar-right">
         <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
     </ul>
    }
    ```

## <a name="step-4-display-user-information"></a>Steg 4: Visa användarinformation
När den autentiserar användare med OpenID Connect returnerar ett id_token till appen som innehåller ”anspråk” eller intyg om användaren i Azure AD. Du kan använda dessa anspråk för att anpassa appen genom att göra följande:

1. Öppna filen Controllers\HomeController.cs. Du kan komma åt användarens anspråk i din domänkontrollanter via den `ClaimsPrincipal.Current` säkerhetsobjekt.

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Skapa och köra appen. Om du inte redan skapat en ny användare i din klient med en onmicrosoft.com-domän, nu är det dags att göra det. Så här gör du:

  a. Logga in med den aktuella användaren och Observera hur användarens identitet avspeglas i det översta fältet.

  b. Logga ut och logga sedan in igen som en annan användare i din klient.

  c. Om du känner dig särskilt ambitiösa, registrera och köra en annan instans av den här appen (med sin egen clientId) och titta på enkel inloggning i åtgärden.

## <a name="next-steps"></a>Nästa steg
Referenser finns [det slutförda exemplet](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (utan dina konfigurationsvärden).

Nu kan du gå vidare till mer avancerade avsnitt. Till exempel försöka [säkra ett webb-API med Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
