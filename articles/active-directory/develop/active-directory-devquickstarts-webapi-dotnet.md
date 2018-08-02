---
title: 'Azure AD .NET webb-API: et komma igång | Microsoft Docs'
description: Hur du skapar en .NET MVC-webb-API som kan integreras med Azure AD för autentisering och auktorisering.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 4c5f0bc7f6f23ce16a3e1d6c7ecac1692ca43d9b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398240"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Azure AD .NET webb-API: et komma igång
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Om du skapar ett program som ger åtkomst till skyddade resurser, måste du veta hur du förhindrar obefogat åtkomst till dessa resurser.
Azure Active Directory (Azure AD) blir den lättförståeliga filstrukturen för att skydda ett webb-API med OAuth 2.0-ägar access token med bara några rader kod.

Du kan göra det här skyddet med hjälp av Microsofts implementering av den gruppdrivna OWIN-mellanprogrammet ingår i .NET Framework 4.5 i ASP.NET web apps. Här använder vi OWIN att skapa en ”att göra-lista” webb-API som:

* Anger vilka API: er är skyddade.
* Verifierar att webb-API-anrop innehåller en giltig åtkomsttoken.

Om du vill skapa att göra-lista API: et, måste du först:

1. Registrera ett program med Azure AD.
2. Konfigurera appen för att använda OWIN-autentiseringspipelinen.
3. Konfigurera ett klientprogram att anropa webb-API.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) eller [hämta det färdiga exemplet](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Var och en är en Visual Studio 2013-lösning. Du behöver också en Azure AD-klient som ska registrera ditt program. Om du inte har ett redan, [Lär dig hur du skaffa ett](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Steg 1: Registrera ett program med Azure AD
Om du vill att skydda ditt program, du måste först skapa ett program i din klient och ger Azure AD med några viktiga uppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj Azure AD-klienten genom att klicka på ditt konto i det övre högra hörnet på sidan följt genom att klicka på den **växla katalog** navigering och välj sedan lämplig klienten.
 * Hoppa över det här steget om du har endast en Azure AD-klient under ditt konto eller om du redan har valt rätt Azure AD-klient.

3. I det vänstra navigeringsfönstret, klickar du på **Azure Active Directory**.

4. Klicka på **appregistreringar**, och välj sedan **Lägg till**.

5. Följ anvisningarna och skapa en ny **webbprogram och/eller webb-API**.
  * **Namn på** beskriver programmet för användare. Ange **att göra-lista Service**.
  * **Omdirigerings-Uri** är en kombination av schema och strängen som Azure AD som används för att returnera de token som appen har begärt. Ange `https://localhost:44321/` för det här värdet.

6. Från den **inställningar** -> **egenskaper** för ditt program och uppdatera App-ID-URI. Ange en klientspecifik identifierare. Ange till exempel `https://contoso.onmicrosoft.com/TodoListService`.

7. Spara konfigurationen. Lämna portalen öppen, eftersom du måste också registrera ditt klientprogram inom kort.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Steg 2: Konfigurera appen att använda OWIN-autentiseringspipelinen
För att verifiera inkommande begäranden och token kan behöva du konfigurera programmet att kommunicera med Azure AD.

1. Om du vill börja, öppnar du lösningen och Lägg till NuGet-paket OWIN-mellanprogrammet TodoListService-projektet med hjälp av Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Lägg till en OWIN-startklass i TodoListService-projektet som kallas `Startup.cs`.  Högerklicka på projektet, Välj **Lägg till** > **nytt objekt**, och söker sedan efter **OWIN**. OWIN-mellanprogrammet anropar `Configuration(…)`-metoden när appen startas.

3. Ändra klassdeklarationen till `public partial class Startup`. Vi har redan implementerat en del av den här klassen för dig i en annan fil. I den `Configuration(…)` metod, se anropar `ConfgureAuth(…)` konfigurera autentisering för din webbapp.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Öppna filen `App_Start\Startup.Auth.cs` och implementera den `ConfigureAuth(…)` metoden. De parametrar som du anger i `WindowsAzureActiveDirectoryBearerAuthenticationOptions` kommer att fungera som koordinater för din app att kommunicera med Azure AD.

    ```csharp
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

5. Nu kan du använda `[Authorize]` attribut för att skydda dina domänkontrollanter och åtgärder med JSON Web Token (JWT) ägarautentisering. Anpassa den `Controllers\TodoListController.cs` klassen med en auktorisera-tagg. Detta kommer att tvinga användaren att logga in innan du använder sidan.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    När en auktoriserad anroparen har anropar någon av de `TodoListController` API: er, åtgärden kanske behöver åtkomst till information om anroparen. OWIN ger åtkomst till anspråk i ägartoken via den `ClaimsPrincpal` objekt.  

6. Ett vanligt krav för webb-API:er är att validera ”omfång” i token. Detta säkerställer att användaren har samtyckt till behörigheterna som krävs för att få åtkomst till att göra tjänsten.

    ```csharp
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

7. Öppna den `web.config` filen i roten av projektet TodoListService och ange dina konfigurationsvärden i den `<appSettings>` avsnittet.
  * `ida:Tenant` är namnet på din Azure AD-klient – till exempel contoso.onmicrosoft.com.
  * `ida:Audience` är App-ID-URI för det program som du angav i Azure-portalen.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Steg 3: Konfigurera ett klientprogram och köra tjänsten
Innan du kan se att göra tjänsten fungerar i praktiken, måste du konfigurera klienten för att göra-lista så att den kan hämta token från Azure AD och göra anrop till tjänsten.

1. Gå tillbaka till den [Azure-portalen](https://portal.azure.com).

2. Skapa ett nytt program i Azure AD-klienten och välj **internt klientprogram** resulterande-prompten.
  * **Namn på** beskriver programmet för användare.
  * Ange `http://TodoListClient/` för den **omdirigerings-Uri** värde.

3. När du har slutfört registreringen tilldelar ett unikt ID i Azure AD till din app. Du behöver det här värdet i nästa steg, så kopiera det från programsidan.

4. Från den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**. Leta upp och väljer att göra tjänsten, lägga till den **åtkomst TodoListService** behörighet under **delegerade behörigheter**, och klicka sedan på **klar**.

5. Öppna i Visual Studio `App.config` i TodoListClient projekt och anger sedan dina konfigurationsvärden i den `<appSettings>` avsnittet.

  * `ida:Tenant` är namnet på din Azure AD-klient – till exempel contoso.onmicrosoft.com.
  * `ida:ClientId` är det app-ID som du kopierade från Azure-portalen.
  * `todo:TodoListResourceId` är App-ID-URI för att göra lista tjänstprogrammet som du angav i Azure-portalen.

## <a name="next-steps"></a>Nästa steg
Slutligen, rensa, bygga och köra varje projekt. Om du inte redan gjort är det dags att skapa en ny användare i din klient med en *. onmicrosoft.com-domän. Logga in på klienten för att göra-lista med den användaren och lägga till några uppgifter i användarens att göra-lista.

Referens är färdiga exemplet (utan dina konfigurationsvärden) är tillgänglig i [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Du kan nu gå vidare till mer identity-scenarier.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
