---
title: Anropa ett ASP.NET-webb-API som skyddas av Azure AD – Microsoft Identity Platform
description: 'I den här snabb starten lär du dig att anropa ett ASP.NET webb-API som skyddas av Azure Active Directory från ett Windows Desktop-program (WPF). WPF-klienten autentiserar en användare, begär en åtkomsttoken och anropar webb-API: et.'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328418"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Bygg en daemon för flera innehavare med slut punkten för Microsoft Identity Platform

I den här självstudien får du lära dig hur du använder Microsoft Identity Platform för att få åtkomst till data från Microsoft Business-kunder i en tids krävande, icke-interaktiv process. I daemon-exemplet används [OAuth2](v2-oauth2-client-creds-grant-flow.md) för att hämta en åtkomsttoken, som sedan används för att anropa [Microsoft Graph](https://graph.microsoft.io) och få åtkomst till organisationens data.

Appen skapas som ett ASP.NET MVC-program och använder OWIN OpenID Connect för att logga in användare.  Komponenten "daemon" i det här exemplet är en API-styrenhet, som, när den anropas, hämtar en lista över användare i kundens Azure AD-klient från Microsoft Graph.  Den här `SyncController.cs` utlöses av ett AJAX-anrop i webb programmet och använder [Microsoft Authentication Library (MSAL) för .net](msal-overview.md) för att hämta en åtkomsttoken för Microsoft Graph.

Ett enklare konsol program för konsol programmet finns i snabb starten för [.net Core daemon](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenario

Eftersom appen är en app för flera klient organisationer som är avsedd att användas av en Microsoft-kund, måste den ge kunderna möjlighet att "registrera sig" eller "ansluta" programmet till företagets data.  Under anslutnings flödet tilldelar företags administratören först **program behörigheter** direkt till appen så att den kan komma åt företagets data på ett icke-interaktivt sätt, utan förekomst av en inloggad användare.  Majoriteten av logiken i det här exemplet visar hur du uppnår det här anslutnings flödet med hjälp av identiteten identitets plattform [admin medgivande](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) .

![Topologi](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

För mer information om de begrepp som används i det här exemplet, se till att läsa [dokumentationen för identitets Plattformens slut punkt för klient identifierings protokoll](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra exemplet i den här snabb starten behöver du:

- [Visual Studio 2017 eller 2019](https://visualstudio.microsoft.com/downloads/)
- En Azure Active Directory-klient (Azure AD). Mer information om hur du skaffar en Azure AD-klient finns i [så här skaffar du en Azure AD-klient](quickstart-create-new-tenant.md)
- Ett eller flera användar konton i din Azure AD-klient. Det här exemplet fungerar inte med en Microsoft-konto (tidigare Windows Live-konto). Om du har loggat in på [Azure Portal](https://portal.azure.com) med en Microsoft-konto och aldrig har skapat ett användar konto i din katalog tidigare, måste du göra det nu.

## <a name="clone-or-download-this-repository"></a>Klona eller hämta den här lagrings platsen

Från gränssnittet eller kommando raden:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Eller [Ladda ned exemplet i en zip-fil](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Registrera exempel programmet med din Azure AD-klient

Det finns ett projekt i det här exemplet. Du kan registrera det genom att:

- Följ anvisningarna för att [Registrera exemplet med Azure Active Directory klient](#register-the-sample-application-with-your-azure-ad-tenant) och [Konfigurera exemplet för att använda din Azure AD-klient](#choose-the-azure-ad-tenant-for-the-applications)
- Eller Använd PowerShell-skript som:
  - Skapar **automatiskt** Azure AD-program och relaterade objekt (lösen ord, behörigheter, beroenden) åt dig
  - Ändra konfigurationsfilerna för Visual Studio-projekt.

Om du vill använda den här automationen:

1. I Windows kör du PowerShell och navigerar till roten i den klonade katalogen
1. I PowerShell-körning:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Kör skriptet för att skapa ett Azure AD-program och konfigurera koden för exempel programmet enligt detta.
1. I PowerShell-körning:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Andra sätt att köra skripten beskrivs i skript för att [Skapa appar](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Öppna Visual Studio-lösningen och klicka på Starta för att köra koden.

Om du inte vill använda den här automatiseringen följer du stegen nedan.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Välj Azure AD-klient för programmen

Som ett första steg behöver du:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient väljer du din profil längst upp till höger på menyn längst upp på sidan och **byter sedan katalog**.
   Ändra din portal-session till önskad Azure AD-klient.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrera klient programmet (dotNet-Web-daemon-v2)

1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `dotnet-web-daemon-v2`.
   - I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog**.
   - I avsnittet omdirigerings-URI (valfritt) väljer du webb i kombinations rutan och anger följande omdirigerings **-** URI: er:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` om det finns fler än en omdirigerings-URI måste du lägga till dem från fliken **autentisering** senare när appen har skapats.
1. Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för projektet.
1. I listan över sidor för appen väljer du **Autentisering**.
   - I avsnittet **Avancerade inställningar** anger du **en utloggnings-URL** till `https://localhost:44316/Account/EndSession`
   - I avsnittet **Avancerade inställningar** | **implicita bidrag** kontrollerar du **åtkomsttoken** och **ID-token** som det här exemplet kräver att det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md) är aktiverat för att logga in användaren och anropa ett API.
1. Välj **Spara**.
1. På sidan **certifikat & hemligheter** går du till avsnittet **klient hemligheter** och väljer **ny klient hemlighet**:

   - Ange en nyckel Beskrivning (från instans `app secret`),
   - Välj en nyckel varaktighet på **minst ett år**, **i två år**eller **upphör aldrig att gälla**.
   - När du trycker på knappen **Lägg till** visas nyckelvärdet, kopiera och spara värdet på en säker plats.
   - Du behöver den här nyckeln senare för att konfigurera projektet i Visual Studio. Det här nyckelvärdet visas inte igen eller kan inte hämtas på något annat sätt, så du kan registrera det så snart det visas från Azure Portal.
1. I listan över sidor för appen väljer du API- **behörigheter**
   - Klicka på knappen **Lägg till en behörighet** och sedan
   - Se till att fliken **Microsoft API: er** är vald
   - I avsnittet *vanliga API: er för Microsoft* klickar du på **Microsoft Graph**
   - I avsnittet **program behörigheter** kontrollerar du att rätt behörigheter är markerade: **User. Read. all**
   - Välj knappen **Lägg till behörigheter**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurera exemplet så att Azure AD-klienten används

I stegen nedan är "ClientID" detsamma som "program-ID" eller "AppId".

Öppna lösningen i Visual Studio för att konfigurera projekten

### <a name="configure-the-client-project"></a>Konfigurera klient projektet

Om du använde installations skripten kommer följande ändringar att gälla.

1. Öppna `UserSync\Web.Config`-filen
1. Hitta appens nyckel `ida:ClientId` och Ersätt det befintliga värdet med program-ID: t (clientId) för det `dotnet-web-daemon-v2` program som kopierats från Azure Portal.
1. Hitta appens nyckel `ida:ClientSecret` och Ersätt det befintliga värdet med den nyckel som du sparade när du skapade `dotnet-web-daemon-v2`-appen i Azure Portal.

## <a name="run-the-sample"></a>Kör exemplet

Rengör lösningen, återskapa lösningen och kör UserSync-programmet och börja med att logga in som administratör i din Azure AD-klient.  Om du inte har en Azure AD-klient för testning kan du hämta en genom att [följa dessa anvisningar](quickstart-create-new-tenant.md) .

När du loggar in ber appen först dig om behörighet att logga in & att läsa användar profilen.  Detta medgivande gör att programmet kan se till att du är företags användare.

![Användar medgivande](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Programmet kommer sedan att försöka synkronisera en lista med användare från Azure AD-klienten via Microsoft Graph.  Om det inte går att göra det uppmanas du (klient administratören) att ansluta din klient till programmet.

Programmet kommer sedan att be om behörighet att läsa listan över användare i din klient organisation.

![Administratörs medgivande](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Du kommer att loggas ut från appen när du har beviljat behörighet**. Detta görs för att se till att alla befintliga åtkomsttoken för grafen tas bort från token cache. När du har loggat in igen kommer den nya token som erhålls att ha de behörigheter som krävs för att anropa MS Graph.
När du beviljar behörigheten kommer programmet sedan att kunna fråga efter användare när som helst.  Du kan kontrol lera detta genom att klicka på knappen **synkronisera användare** på sidan användare och uppdatera listan över användare.  Försök att lägga till eller ta bort en användare och synkronisera om listan (men Observera att den bara synkroniserar den första sidan med användare!).

## <a name="about-the-code"></a>Om koden

Den relevanta koden för det här exemplet är i följande filer:

- Inledande inloggning: `App_Start\Startup.Auth.cs``Controllers\AccountController.cs`.  I synnerhet har åtgärderna på kontrollanten ett auktoriserat attribut, som tvingar användaren att logga in. Programmet använder auktoriseringskod- [flödet](v2-oauth2-auth-code-flow.md) för att logga in användaren.
- Synkronisera listan med användare till det lokala minnet i minnet: `Controllers\SyncController.cs`
- Visar listan över användare från den lokala minnes lagringen: `Controllers\UserController.cs`
- Hämtar behörigheter från klient administratören med hjälp av administratörs slut punkten: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Återskapa den här exempel appen

1. Skapa ett nytt `Visual C#` `ASP.NET Web Application (.NET Framework)`-projekt i Visual Studio. Välj mallen `MVC` projekt på nästa skärm. Lägg också till mapp-och kärn referenser för `Web API` som du skulle lägga till en webb-API-kontrollant senare.  Lämna projektets valda autentiseringsläge som standard, det vill säga `No Authentication`".
2. Välj projektet i fönstret **Solution Explorer** och tryck på **F4** -tangenten för att hämta projekt egenskaper. I projekt egenskaperna anger du att **SSL är aktiverat** som `True`. Notera **SSL-URL: en**. Du behöver den när du konfigurerar programmets registrering i Azure Portal.
3. Lägg till följande ASP.Net OWIN mellan NuGets: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` och `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` och `Microsoft.Identity.Client`. 
4. I mappen `App_Start` skapar du en klass `Startup.Auth.cs`. Ta bort `.App_Start` från namn områdes namnet.  Ersätt koden för klassen `Startup` med koden från samma fil i exempel programmet.  Var noga med att ta hela klass definitionen!  Definitionen ändras från `public class Startup` till `public partial class Startup`
5. I `Startup.Auth.cs` lösa referenser som saknas genom att lägga till `using`-uttryck som föreslås av Visual Studio IntelliSense.
6. Högerklicka på projektet, Välj Lägg till, välj "klass" och i rutan Sök anger du "OWIN".  "OWIN start klass" visas som ett val. Markera det och ge klassen namnet `Startup.cs`.
7. I `Startup.cs`ersätter du koden för klassen `Startup` med koden från samma fil i exempel programmet.  Observera att definitionerna ändras från `public class Startup` till `public partial class Startup`.
8. Lägg till en ny klass med namnet `MsGraphUser.cs`i mappen.  Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
9. Lägg till en ny **MVC 5-kontrollant-tom** anropad `AccountController`. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
10. Lägg till en ny **MVC 5-kontrollant-tom** anropad `UserController`. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
11. Lägg till en ny **webb-API 2-kontrollant-tom** anropad `SyncController`. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
12. För användar gränssnittet i mappen `Views\Account` lägger du till tre **tomma (utan modell) vyer** med namnet `GrantPermissions`, `Index` och `UserMismatch` och en namngiven `Index` i mappen `Views\User`. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
13. Uppdatera `Shared\_Layout.cshtml` och `Home\Index.cshtml` för att länka samman de olika vyerna på rätt sätt.

## <a name="deploy-this-sample-to-azure"></a>Distribuera det här exemplet till Azure

Det här projektet har WebApp/Web API-projekt. Om du vill distribuera dem till Azure-webbplatser behöver du för var och en, för att:

- skapa en Azure-webbplats
- publicera webb program/webb-API: er på webbplatsen och
- uppdatera dess klient (er) för att anropa webbplatsen i stället för att IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Skapa och publicera `dotnet-web-daemon-v2` på en Azure-webbplats

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på `Create a resource` i det övre vänstra hörnet, Välj **webb-**  --> **webbapp**och ge webbplatsen ett namn, till exempel `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Välj därefter `Subscription``Resource Group``App service plan and Location`. `OS` kommer att vara **Windows** och `Publish` kommer att **kodas**.
1. Klicka på `Create` och vänta tills App Service har skapats.
1. När du får `Deployment succeeded` meddelandet klickar du på `Go to resource` för att navigera till den nya app service.
1. När webbplatsen har skapats letar du reda på den på **instrument panelen** och öppnar **app Services** **översikts** skärmen genom att klicka på den.
1. På fliken **Översikt** i App Service laddar du ned publicerings profilen genom att klicka på länken **Hämta publicerings profil** och spara den.  Andra distributions metoder, till exempel från käll kontroll, kan också användas.
1. Växla till Visual Studio och gå till projektet dotNet-Web-daemon-v2.  Högerklicka på projektet i Solution Explorer och välj **publicera**.  Klicka på **Importera profil** i det nedre fältet och importera den publicerings profil som du laddade ned tidigare.
1. Klicka på **Konfigurera** och i `Connection tab`uppdaterar du mål-URL: en så att den är en `https` på Start sidans URL, till exempel [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Klicka på **Nästa**.
1. På fliken Inställningar ser du till att `Enable Organizational Authentication` inte är markerat.  Klicka på **Save** (Spara). Klicka på **publicera** på huvud skärmen.
1. Visual Studio kommer att publicera projektet och automatiskt öppna en webbläsare till projektets URL.  Om du ser projektets standard webb sida slutfördes publikationen.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Uppdatera Azure AD-klientens program registrering för `dotnet-web-daemon-v2`

1. Gå tillbaka till [Azure Portal](https://portal.azure.com).
I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**.
1. I den resulterande skärmen väljer du programmet `dotnet-web-daemon-v2`.
1. I **autentiseringen** | Sidan för ditt program, uppdatera URL-fälten för utloggning med adressen till din tjänst, till exempel [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. Från *anpassnings* menyn uppdaterar du **Start sidans URL**till adressen till din tjänst, till exempel [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Spara konfigurationen.
1. Lägg till samma URL i listan med värden i menyn *autentisering-> omdirigerings-URI: er* . Om du har flera omdirigerings-URL: er, se till att det finns en ny post med App Service-URI för varje omdirigerings-URL.

## <a name="community-help-and-support"></a>Community-hjälp och support

Använd [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) för att få support från communityn.
Ställ dina frågor på Stack Overflow först och bläddra efter befintliga problem för att se om någon har bett din fråga tidigare.
Kontrol lera att dina frågor eller kommentarer är taggade med [`adal` `msal` `dotnet`].

Om du hittar och felsöker i exemplet kan du öka problemet med [GitHub-problem](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Om du hittar ett fel i MSAL.NET kan du läsa problemet om [MSAL.net GitHub-problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Om du vill ha en rekommendation går du till följande [användares röst sida](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Nästa steg
Läs mer om olika [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) som stöds av Microsoft Identity Platform.

Mer information finns i följande konceptuella dokumentation:

- [Innehav i Azure Active Directory](single-and-multi-tenant-apps.md)
- [Förstå medgivande i Azure AD-program](application-consent-experience.md)
- [Så här: Logga in Azure Active Directory användare med program mönstret för flera innehavare](howto-convert-app-to-be-multi-tenant.md)
- [Förstå användar-och administratörs medgivande](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Program-och tjänst huvud objekt i Azure Active Directory](app-objects-and-service-principals.md)
- [Snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md)
- [Snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md)
- [Hämta en token för ett program med flöden för klient referenser](msal-client-applications.md)

Ett enklare program för daemon för flera innehavare finns i snabb starten för [.net Core daemon](quickstart-v2-netcore-daemon.md).
