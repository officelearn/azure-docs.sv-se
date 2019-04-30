---
title: Skapa ett .NET-webb-API som integreras med Azure AD för autentisering och auktorisering | Microsoft Docs
description: Hur du skapar ett .NET MVC-webb-API som integreras med Azure AD för autentisering och auktorisering.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9b7a807693aa039f068cf4025dc730bba4bc7bd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105079"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Snabbstart: Skapa ett .NET-baserat webb-API som integreras med Azure AD för autentisering och auktorisering

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Om du skapar en app som ger åtkomst till skyddade resurser behöver du veta hur du förhindrar obehörig åtkomst till de resurserna. Azure Active Directory (Azure AD) gör det enkelt att skydda ett webb-API med hjälp av OAuth 2.0-ägaråtkomsttoken med bara några får rader kod.

I ASP.NET-webbappar kan du åstadkomma detta skydd med hjälp av Microsoft-implementeringen för det community-drivna OWIN-programmet som ingår i .NET Framework 4.5. Här använder du OWIN för att skapa ett webb-API för Att göra-lista som:

* Anger vilka API:er som skyddas.
* Verifierar att webb-API-anropen innehåller en giltig åtkomsttoken.

I den här snabbstarten skapar du en ett API för Att göra-lista och lär dig hur du:

1. Registrera ett program med Azure AD.
2. Konfigurerar appen att använda OWIN-autentiserings-pipelinen.
3. Konfigurera ett klientprogram att anropa ett webb-API.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) eller [ladda ned det färdiga exemplet](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Var och en är en Visual Studio 2013-lösning.
* Ha en Azure AD-klientorganisation där du registrerar appen. Om du inte redan har en kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Steg 1: Registrera ett program med Azure AD

För att skydda appen måste du först skapa en app i klientorganisationen och ge Azure AD några viktiga uppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj din Azure AD-klientorganisation genom att välja ditt konto i det övre högra hörnet på sidan. Välj navigeringen **Växla katalog** och välj sedan rätt klientorganisation.
    * Hoppa över det här steget om du bara har en Azure AD-klientorganisation under ditt konto eller om du redan har valt den rätta Azure AD-klientorganisationen.

3. Välj **Azure Active Directory** i det vänstra navigeringsfönstret.
4. Välj **Appregistreringar** och sedan **Lägg till**.
5. Följ anvisningarna och skapa **en ny webbapp och/eller ett nytt webb-API**.
    * **Namn** beskriver appen för dina användare. Ange **To Do List Service** (Att göra-lista).
    * **Omdirigerings-URI** är ett schema och en strängkombination som Azure AD använder för att returnera token som din app har begärt. Ange `https://localhost:44321/` för det här värdet.

6. På sidan **Inställningar > Egenskaper** för appen uppdaterar du app-ID-URI. Ange en klientspecifik identifierare. Ange till exempel `https://contoso.onmicrosoft.com/TodoListService`.
7. Spara konfigurationen. Lämna portalen öppen, eftersom du även måste registrera ditt klientprogram inom kort.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Steg 2: Konfigurera appen att använda pipelinen för OWIN-autentisering

För att verifiera inkommande begäranden och token måste du konfigurera appen att kommunicera med Azure AD.

1. Börja genom att öppna lösningen och lägga till OWIN-mellanprogrammets NuGet-paket i TodoListService-projektet genom att använda Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Lägg till en OWIN Startup-klass i TodoListService-projektet med namnet `Startup.cs`.  Högerklicka på projektet, välj **Lägg till > Nytt objekt** och sök sedan efter **OWIN**. OWIN-mellanprogrammet anropar `Configuration(…)`-metoden när appen startas.

3. Ändra klassdeklarationen till `public partial class Startup`. Vi har redan implementerat en del av denna klass åt dig i en annan fil. I metoden `Configuration(…)` gör du ett anrop till `ConfgureAuth(…)` för att konfigurera autentisering för webbappen.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Öppna filen `App_Start\Startup.Auth.cs` och implementera `ConfigureAuth(…)`-metoden. Parametrarna du anger i `WindowsAzureActiveDirectoryBearerAuthenticationOptions` fungerar som koordinater för appen för att kommunicera med Azure AD. För att använda dem måste du använda klasserna i den `System.IdentityModel.Tokens` namnområde.

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Använd `[Authorize]`-attributen för att skydda dina kontrollanter och åtgärder med ägarautentisering med JSON Web Token (JWT). Anpassa klassen `Controllers\TodoListController.cs` med en auktorisera-tagg, som tvingar användaren att logga in innan användaren får åtkomst till den sidan.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    När en behörig anropare anropar ett av `TodoListController`-API:erna kanske åtgärden behöver åtkomst till information om anroparen. OWIN ger åtkomst till anspråk i ägartoken via objektet `ClaimsPrincpal`.  

6. Ett vanligt krav för webb-API:er är att verifiera ”omfång” som finns i token för att kontrollera att användaren har godkänt behörigheterna som krävs för att få åtkomst till To Do List Service.

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

7. Öppna `web.config`-filen i roten för TodoListService-projektet och ange dina konfigurationsvärden i `<appSettings>`-avsnittet.
    * `ida:Tenant` är namnet din Azure AD-klientorganisation, till exempel contoso.onmicrosoft.com.
    * `ida:Audience` är app-ID-URI för den app som du angav i Azure-portalen.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Steg 3: Konfigurera ett klientprogram och köra tjänsten

Innan du kan se To Do List Service i aktion måste du konfigurera To Do List-klienten så att den kan hämta token från Azure AD och göra anrop till tjänsten.

1. Gå tillbaka till [Azure-portalen](https://portal.azure.com).
1. Skapa en ny app i Azure AD-klienten och välj **Internt klientprogram** i prompten som visas.
    * **Namn** beskriver appen för dina användare.
    * Ange `http://TodoListClient/` för värdet för **Omdirigerings-URI**.

1. När du har slutfört registreringen tilldelar Azure AD ett unikt ID till din app. Du behöver det här värdet i nästa steg, så kopiera det från appsidan.
1. På sidan **Inställningar** väljer du **Nödvändiga behörigheter** och sedan **Lägg till**. Leta upp och välj To Do List Service, lägg till behörigheten **Åtkomst till TodoListService** under **Delegerade behörigheter** och välj sedan **Klart**.
1. I Visual Studio öppnar du `App.config` i TodoListClient-projektet och anger sedan dina konfigurationsvärden i avsnittet `<appSettings>`.

    * `ida:Tenant` är namnet din Azure AD-klientorganisation, till exempel contoso.onmicrosoft.com.
    * `ida:ClientId` är det app-ID som du kopierade från Azure-portalen.
    * `todo:TodoListResourceId` är app-ID-URI för To Do List Service-appen som du angav i Azure-portalen.

1. Rensa, skapa och kör varje projekt.
1. Om du inte redan gjort det skapar du en ny användare i din klientorganisation med en *.onmicrosoft.com-domän.
1. Logga in på To Do List-klienten med den användaren och lägg till några uppgifter i användarens att göra-lista.

## <a name="next-steps"></a>Nästa steg

* Som referens kan du ladda ned det färdiga exemplet (utan dina konfigurationsvärden) från [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Du kan nu gå vidare till andra identity-scenarier.
