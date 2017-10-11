---
title: "Azure AD-.NET-webb-API komma igång | Microsoft Docs"
description: "Hur du skapar ett .NET MVC-webb-API som kan integreras med Azure AD för autentisering och auktorisering."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Skydda ett webb-API med ägar-token från Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Om du utvecklar ett program som ger åtkomst till skyddade resurser, måste du känna till hur förhindra obefogat åtkomst till resurserna.
Azure Active Directory (AD Azure) gör det lätt att skydda ett webb-API med OAuth 2.0-ägar access token med bara några få rader med kod.

Du kan göra det här skyddet med hjälp av Microsofts implementering av den community-driven OWIN mellanprogram ingår i .NET Framework 4.5 i ASP.NET web apps. Här använder vi OWIN att skapa en ”att göra-lista” webb-API som:

* Anger vilket API: er är skyddad.
* Verifierar att webb-API-anrop kan innehålla en giltig åtkomsttoken.

Om du vill skapa att göra listan API: et, måste du först:

1. Registrera ett program med Azure AD.
2. Konfigurera appen för att använda OWIN autentisering pipeline.
3. Konfigurera ett klientprogram att anropa webb-API.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) eller [hämta det slutförda exemplet](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Varje är en lösning för Visual Studio 2013. Du måste också en Azure AD-klient att registrera ditt program. Om du inte redan har en [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Steg 1: Registrera ett program med Azure AD
För att säkra programmet, du måste först skapa ett program i din klient och ger Azure AD med några uppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på ditt konto på den översta raden. I den **Directory** Välj Azure AD-klient som du vill registrera ditt program.

3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.

4. Klicka på **App registreringar**, och välj sedan **Lägg till**.

5. Följ anvisningarna och skapa en ny **webbprogram och/eller webb-API**.
  * **Namnet** beskriver ditt program till användare. Ange **att göra-lista tjänsten**.
  * **Omdirigerings-Uri** är en kombination av schemat och strängen som Azure AD som används för att returnera de token som har begärt att din app. Ange `https://localhost:44321/` för det här värdet.

6. Från den **inställningar** -> **egenskaper** för programmet, uppdatera App-ID-URI. Ange en klient-specifika identifierare. Ange till exempel `https://contoso.onmicrosoft.com/TodoListService`.

7. Spara konfigurationen. Lämna portalen öppen, eftersom du måste också registrera ditt klientprogram inom kort.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Steg 2: Konfigurera appen för att använda OWIN autentisering pipeline
Om du vill validera inkommande begäranden och token, måste du konfigurera ditt program för att kommunicera med Azure AD.

1. Börja, öppna lösningen och Lägg till NuGet-paket OWIN mellanprogram TodoListService projektet med hjälp av Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Lägg till en OWIN-startklass i TodoListService-projektet som kallas `Startup.cs`.  Högerklicka på projektet, Välj **Lägg till** > **nytt objekt**, och sök sedan efter **OWIN**. OWIN-mellanprogrammet anropar `Configuration(…)`-metoden när appen startas.

3. Ändra klassdeklarationen till `public partial class Startup`. Vi har redan implementerats en del av den här klassen för dig i en annan fil. I den `Configuration(…)` gör ett anrop till metoden `ConfgureAuth(…)` du konfigurerar autentisering för ditt webbprogram.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Öppna filen `App_Start\Startup.Auth.cs` och genomföra den `ConfigureAuth(…)` metoden. De parametrar som du anger i `WindowsAzureActiveDirectoryBearerAuthenticationOptions` fungerar som koordinater för din app för att kommunicera med Azure AD.

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Nu kan du använda `[Authorize]` attribut för att skydda dina domänkontrollanter och åtgärder med JSON-Webbtoken (JWT) ägar-autentisering. Skapa snygga den `Controllers\TodoListController.cs` klass med en auktorisera-tagg. Detta tvingar användaren att logga in innan sidan.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    När en auktoriserad anroparen har anropar någon av de `TodoListController` API: er, åtgärden kanske behöver åtkomst till information om anroparen. OWIN ger tillgång till anspråk i ägartoken via den `ClaimsPrincpal` objekt.  

6. Ett vanligt krav för webb-API:er är att validera ”omfång” i token. Detta säkerställer att användaren har godkänt för de behörigheter som krävs för att få åtkomst till att göra tjänsten.

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Öppna den `web.config` filen i roten av projektet TodoListService och ange dina konfigurationsvärden i den `<appSettings>` avsnitt.
  * `ida:Tenant`är namnet på din Azure AD-klient – till exempel contoso.onmicrosoft.com.
  * `ida:Audience`är URI: N App-ID för det program som du angav i Azure-portalen.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Steg 3: Konfigurera ett klientprogram och köra tjänsten
Innan du kan se att göra tjänsten i åtgärden måste du konfigurera klienten för att göra-listan så att den kan hämta token från Azure AD-anrop till tjänsten.

1. Gå tillbaka till den [Azure-portalen](https://portal.azure.com).

2. Skapa ett nytt program i Azure AD-klienten och välj **internt klientprogram** i den resulterande frågan.
  * **Namnet** beskriver ditt program till användare.
  * Ange `http://TodoListClient/` för den **omdirigerings-Uri** värde.

3. När du har slutfört registreringen tilldelar ett unikt ID i Azure AD till din app. Du behöver det här värdet i nästa steg, så kopiera den från appen på sidan.

4. Från den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**. Leta upp och välj att göra tjänsten, Lägg till den **åtkomst TodoListService** behörighet under **delegerade behörigheter**, och klicka sedan på **klar**.

5. Öppna i Visual Studio `App.config` i TodoListClient projektet och sedan ange dina konfigurationsvärden i den `<appSettings>` avsnitt.

  * `ida:Tenant`är namnet på din Azure AD-klient – till exempel contoso.onmicrosoft.com.
  * `ida:ClientId`är det app-ID som du kopierade från Azure-portalen.
  * `todo:TodoListResourceId`är URI: N App-ID för att göra listan tjänstprogrammet som du angav i Azure-portalen.

## <a name="next-steps"></a>Nästa steg
Slutligen, rensa, skapa och kör varje projekt. Om du inte redan gjort nu är det dags att skapa en ny användare i din klient med en *. onmicrosoft.com-domän. Logga in på klienten för att göra-lista med användaren och lägga till vissa uppgifter i användarens att göra-lista.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) är tillgängligt i [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Du kan nu gå vidare till mer identity-scenarier.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
