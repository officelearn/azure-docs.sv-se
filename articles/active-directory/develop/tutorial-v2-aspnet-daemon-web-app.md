---
title: Bygg en daemon för flera innehavare som använder slut punkten för Microsoft Identity Platform
description: 'I den här självstudien får du lära dig att anropa ett ASP.NET-webb-API som skyddas av Azure Active Directory från ett Windows Desktop-program (WPF). WPF-klienten autentiserar en användare, begär en åtkomsttoken och anropar webb-API: et.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4b05bbf818676cc70f485dd94ece79141e8f01a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982860"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Självstudie: Bygg en daemon för flera innehavare som använder slut punkten för Microsoft Identity Platform

I den här självstudien får du lära dig hur du använder Microsoft Identity Platform för att få åtkomst till data från Microsoft Business-kunder i en tids krävande, icke-interaktiv process. I daemon-exemplet används [OAuth2-klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) för att hämta en åtkomsttoken. Daemon använder sedan token för att anropa [Microsoft Graph](https://graph.microsoft.io) och få åtkomst till organisationens data.

> [!div class="checklist"]
> * Integrera en daemon-app med Microsoft Identity Platform
> * Ge program behörigheter direkt till appen av en administratör
> * Hämta en åtkomsttoken för att anropa API: et för Microsoft Graph
> * Anropa Microsoft Graph-API: et.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Appen skapas som ett ASP.NET MVC-program. Den använder OWIN OpenID Connect-mellanprogram för att logga in användare.

"Daemon"-komponenten i det här exemplet är en API-styrenhet `SyncController.cs` . När styrenheten anropas, hämtas en lista med användare i kundens Azure Active Directory-klient (Azure AD) från Microsoft Graph. `SyncController.cs` utlöses av ett AJAX-anrop i webb programmet. Det använder [Microsoft Authentication Library (MSAL) för .net](msal-overview.md) för att hämta en åtkomsttoken för Microsoft Graph.

>[!NOTE]
> Om du är nybörjare på Microsoft Identity Platform rekommenderar vi att du börjar med snabb starten för [.net Core daemon](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenario

Eftersom appen är en app för flera klienter för Microsoft Business-kunder måste den ge kunderna möjlighet att "registrera sig" eller "ansluta" programmet till företagets data. Under anslutnings flödet beviljar en företags administratör först *program behörigheter* direkt till appen så att den kan komma åt företagets data på ett icke-interaktivt sätt, utan förekomst av en inloggad användare. Majoriteten av logiken i det här exemplet visar hur du uppnår anslutnings flödet med hjälp av identitets Plattformens slut punkt för [Administratörs medgivande](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) .

![Diagrammet visar UserSync-appen med tre lokala objekt som ansluter till Azure, med start punkts-auth som hämtar en token interaktivt för att ansluta till Azure A D, AccountController får administratörs medgivande för att ansluta till Azure A D och SyncController läsa användare för att ansluta till Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Mer information om de begrepp som används i det här exemplet finns i [protokoll dokumentationen för klientens autentiseringsuppgifter för identitets Plattformens slut punkt](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra exemplet i den här snabb starten behöver du:

- [Visual Studio 2017 eller 2019](https://visualstudio.microsoft.com/downloads/).
- En Azure AD-klientorganisation. Mer information finns i [så här hämtar du en Azure AD-klient](quickstart-create-new-tenant.md).
- Ett eller flera användar konton i din Azure AD-klient. Det här exemplet fungerar inte med en Microsoft-konto (tidigare Windows Live-konto). Om du har loggat in på [Azure Portal](https://portal.azure.com) med en Microsoft-konto och aldrig har skapat ett användar konto i din katalog, måste du göra det nu.

## <a name="clone-or-download-this-repository"></a>Klona eller hämta den här lagrings platsen

I ditt gränssnitt eller kommando raden anger du följande kommando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Eller [Ladda ned exemplet i en zip-fil](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrera ditt program

Det här exemplet har ett projekt. Om du vill registrera programmet med din Azure AD-klient kan du antingen:

- Följ stegen i [Registrera exemplet med Azure Active Directory klient](#register-your-application) och [Konfigurera exemplet för att använda Azure AD-klienten](#choose-the-azure-ad-tenant).
- Använd PowerShell-skript som:
  - Skapa *automatiskt* Azure AD-program och relaterade objekt (lösen ord, behörigheter, beroenden) åt dig.
  - Ändra konfigurationsfilerna för Visual Studio-projekt.

Om du vill använda Automation:

1. I Windows kör du PowerShell och går till roten i den klonade katalogen.
1. Kör följande kommando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Kör skriptet för att skapa ett Azure AD-program och konfigurera koden för exempel programmet enligt följande:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Andra sätt att köra skript beskrivs i skript för att [Skapa appar](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Öppna Visual Studio-lösningen och välj **Starta** för att köra koden.

Om du inte vill använda Automation följer du stegen i följande avsnitt.

### <a name="choose-the-azure-ad-tenant"></a>Välj Azure AD-klient

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient väljer du din profil på menyn längst upp på sidan och väljer sedan **Växla katalog**.
1. Ändra din portal-session till önskad Azure AD-klient.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrera klient programmet (dotNet-Web-daemon-v2)

1. Gå till sidan [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) i Microsoft Identity Platform för utvecklare.
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användare av appen. Ange till exempel **dotNet-Web-daemon-v2**.
   - I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog**.
   - I avsnittet **omdirigerings-URI (valfritt)** väljer du webb i kombinations rutan och anger följande omdirigerings **-** URI: er:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     Om det finns fler än två omdirigerings-URI: er måste du lägga till dem från fliken **autentisering** senare, när appen har skapats.
1. Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt** för appen letar du reda på **programmets (klient) ID-** värde och registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för projektet.
1. I listan över sidor för appen väljer du **Autentisering**. Efter det:
   - I avsnittet **Avancerade inställningar** anger du **utloggnings-URL** till **https://localhost:44316/Account/EndSession** .
   - I avsnittet **Avancerade inställningar**för  >  **implicit beviljande** väljer du **åtkomsttoken** och **ID-token**. Det här exemplet kräver att det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md) är aktiverat för att logga in användaren och anropa ett API.
1. Välj **Spara**.
1. På sidan **certifikat & hemligheter** väljer du **ny klient hemlighet**i avsnittet **klient hemligheter** . Efter det:

   1. Ange en nyckel Beskrivning (till exempel **app Secret**),
   1. Välj en nyckel varaktighet på **minst ett år**, **i två år**eller **upphör aldrig att gälla**.
   1. Välj knappen **Lägg till**.
   1. När nyckelvärdet visas kopierar du och sparar det på en säker plats. Du behöver den här nyckeln senare för att konfigurera projektet i Visual Studio. Den visas inte igen eller kan hämtas på annat sätt.
1. I listan över sidor för appen väljer du API- **behörigheter**. Efter det:
   1. Välj knappen **Lägg till en behörighet**.
   1. Se till att fliken **Microsoft API: er** är markerad.
   1. I avsnittet **vanliga API: er för Microsoft** väljer du **Microsoft Graph**.
   1. I avsnittet **program behörigheter** kontrollerar du att rätt behörigheter är markerade: **User. Read. all**.
   1. Välj knappen **Lägg till behörigheter** .

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurera exemplet så att Azure AD-klienten används

I följande steg är **ClientID** detsamma som "program-ID" eller **AppId**.

Öppna lösningen i Visual Studio för att konfigurera projekten.

### <a name="configure-the-client-project"></a>Konfigurera klient projektet

Om du använde installations skripten kommer följande ändringar att gälla.

1. Öppna **UserSync\Web.Config** -filen.
1. Hitta appens nyckel **Ida: ClientId**. Ersätt det befintliga värdet med program-ID: t för programmet **dotNet-Web-daemon-v2** som har kopierats från Azure Portal.
1. Hitta appens nyckel **Ida: ClientSecret**. Ersätt det befintliga värdet med den nyckel som du sparade när du skapade **dotNet-Web-daemon-v2-** appen i Azure Portal.

## <a name="run-the-sample"></a>Kör exemplet

Rengör lösningen, återskapa lösningen, kör UserSync-programmet och logga sedan in som administratör i din Azure AD-klient. Om du inte har en Azure AD-klient för testning kan du hämta en genom att [följa dessa anvisningar](quickstart-create-new-tenant.md) .

När du loggar in ber appen först dig om behörighet att logga in och läsa din användar profil. Detta medgivande tillåter appen att se till att du är företags användare.

![Användargodkännande](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Appen försöker sedan synkronisera en lista med användare från Azure AD-klienten via Microsoft Graph. Om den inte gör det uppmanas du (klient administratören) att ansluta din klient till appen.

Appen ber sedan om behörighet att läsa listan över användare i din klient organisation.

![Administratörsmedgivande](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

När du har beviljat behörighet loggas du ut från appen. Den här utloggningen säkerställer att alla befintliga åtkomsttoken för Microsoft Graph tas bort från token cache. När du loggar in igen får den nya token som du har fått de behörigheter som krävs för att anropa Microsoft Graph.


När du beviljar behörigheten kan appen sedan fråga efter användare när som helst. Du kan kontrol lera detta genom att välja knappen **synkronisera användare** och uppdatera listan över användare. Försök att lägga till eller ta bort en användare och synkronisera om listan. (Men Observera att appen bara synkroniserar den första sidan med användare.)

## <a name="about-the-code"></a>Om koden

Den relevanta koden för det här exemplet är i följande filer:

- **App_Start \STARTUP.auth.cs**, **Controllers\AccountController.cs**: inledande inloggning. I synnerhet har åtgärderna på kontrollanten ett **auktoriserat** attribut, som tvingar användaren att logga in. Programmet använder auktoriseringskod- [flödet](v2-oauth2-auth-code-flow.md) för att logga in användaren.
- **Controllers\SyncController.cs**: synkronisera listan med användare till det lokala minnet i minnet.
- **Controllers\UserController.cs**: visar listan över användare från den lokala minnes lagringen.
- **Controllers\AccountController.cs**: hämtar behörigheter från klient administratören genom att använda slut punkten för administratörs medgivande.

## <a name="re-create-the-sample-app"></a>Återskapa exempel appen

1. I Visual Studio skapar du ett nytt **Visual C#** **ASP.net-projekt (Web Application .NET Framework)** .
1. På nästa skärm väljer du mallen **MVC** -projekt. Lägg även till mapp-och kärn referenser för **webb-API**, eftersom du kommer att lägga till en webb-API-kontrollant senare. Lämna projektets valda autentiseringsläge som standard: **Ingen autentisering**.
1. Välj projektet i fönstret **Solution Explorer** och välj **F4** -nyckeln.
1. I projekt egenskaperna ställer du in **SSL aktiverat** på **True**. Anteckna informationen i **SSL-URL**. Du behöver den när du konfigurerar programmets registrering i Azure Portal.
1. Lägg till följande ASP.NET OWIN-NuGet-paket:
   - Microsoft. OWIN. Security. ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft. IdentityModel. Protocol. Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft. Identity. client
1. I mappen **App_Start** :
   1. Skapa en klass med namnet **startup.auth.cs**.
   1. Ta bort **. App_Start** från namn områdes namnet.
   1. Ersätt koden för **Start** klassen med koden från samma fil i exempel programmet.
   Se till att du tar hela klass definitionen. Definitionen ändras från en **offentlig klass som startar** till en **offentlig del klass.**
1. I **startup.auth.cs**löser du de referenser som saknas genom att lägga till **using** -instruktioner som föreslås av Visual Studio IntelliSense.
1. Högerklicka på projektet, Välj **Lägg till**och välj sedan **klass**.
1. Skriv **OWIN**i rutan Sök. **Start klassen OWIN** visas som ett val. Markera det och ge klassen namnet **startup.cs**.
1. I **startup.cs**ersätter du koden för **Start** klassen med koden från samma fil i exempel programmet. Observera återigen att definitions ändringarna från den **offentliga klassen startar** till en **offentlig, delvis klass start**.
1. Lägg till en ny klass med namnet **MsGraphUser.cs**i mappen **modeller** . Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **MVC 5-kontrollant – tom** instans med namnet **AccountController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **MVC 5-kontrollant – tom** instans med namnet **UserController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **Web API 2-kontrollant-tom** instans med namnet **SyncController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. För användar gränssnittet, i mappen **Views\Account** , lägger du till tre **tomma (utan modell) vyer** av instanser med namnet **GrantPermissions**, **index**och **UserMismatch**. Lägg till och ett namngivet **index** i **Views\User** -mappen. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Uppdatera **delad \_ layout. cshtml** och **Home\Index.cshtml** för att länka ihop de olika vyerna på rätt sätt.

## <a name="deploy-the-sample-to-azure"></a>Distribuera exemplet till Azure

Det här projektet har Web App-och Web API-projekt. För att distribuera dem till Azure Websites, utför följande steg för var och en:

1. Skapa en Azure-webbplats.
1. Publicera webbappen och webb-API: erna på webbplatsen.
1. Uppdatera klienter för att anropa webbplatsen i stället för IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Skapa och publicera dotNet-Web-daemon-v2 till en Azure-webbplats

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** i det övre vänstra hörnet.
1. Välj **webb**  >  **-** webbapp och ge din webbplats ett namn. Du kan till exempel ge den namnet **dotNet-Web-daemon-v2-contoso.azurewebsites.net**.
1. Välj information för **prenumeration**, **resurs grupp**och **App Service-plan och plats**. **OS** är **Windows**och **publicera** är **kod**.
1. Välj **skapa** och vänta tills App Service har skapats.
1. När du får ett meddelande om att **distributionen har slutförts** väljer du **gå till resurs** för att gå till den nyligen skapade app-tjänsten.
1. När webbplatsen har skapats letar du reda på den på **instrument panelen** och väljer den för att öppna app Services **översikts** skärm.
1. På fliken **Översikt** i App Service laddar du ned publicerings profilen genom att välja länken **Hämta publicerings profil** och spara den. Du kan använda andra distributions metoder, t. ex. distribution från käll kontroll.
1. Växla till Visual Studio och sedan:
   1. Gå till projektet **dotNet-Web-daemon-v2** .
   1. Högerklicka på projektet i Solution Explorer och välj sedan **publicera**.
   1. Välj **import profil** i det nedre fältet och importera den publicerings profil som du laddade ned tidigare.
1. Välj **Konfigurera**.
1. På fliken **anslutning** uppdaterar du mål-URL: en så att den använder "https". Använd till exempel `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Välj **Nästa**.
1. På fliken **Inställningar** ser du till att **Aktivera organisatorisk autentisering** är avmarkerad.
1. Välj **Spara**. Välj **publicera** på huvud skärmen.

Visual Studio kommer att publicera projektet och automatiskt öppna en webbläsare till projektets URL. Om du ser projektets standard webb sida har publikationen slutförts.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Uppdatera Azure AD-klientens program registrering för dotNET-Web-daemon-v2

1. Gå tillbaka till [Azure Portal](https://portal.azure.com).
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**.
1. Välj programmet **dotNet-Web-daemon-v2** .
1. På sidan **autentisering** för ditt program uppdaterar du URL-fälten för **utloggning** med adressen för din tjänst. Använd till exempel `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Från **anpassnings** menyn uppdaterar du **Start sidans URL** till adressen till din tjänst. Använd till exempel `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Spara konfigurationen.
1. Lägg till samma URL i listan med värden i menyn för **Authentication**  >  **omdirigering** av autentisering. Om du har flera omdirigerings-URL: er, se till att det finns en ny post som använder App Service-URI: n för varje omdirigerings-URL.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort app-objektet som du skapade i steget [Registrera ditt program](#register-your-application) när de inte längre behövs.  Ta bort programmet genom att följa anvisningarna i [ta bort ett program som skapats av dig eller din organisation](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Få hjälp

Använd [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) för att få support från communityn.
Ställ dina frågor till Stack Overflow först och bläddra efter befintliga problem för att se om någon har bett din fråga tidigare.
Se till att dina frågor eller kommentarer är taggade med "ADAL", "msal" och "dotNet".

Om du hittar ett fel i exemplet kan du öka problemet med GitHub- [problem](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Om du hittar ett fel i MSAL.NET kan du läsa problemet om [MSAL.net GitHub-problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Om du vill ha en rekommendation går du till [sidan användarens röst](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Nästa steg
Läs mer om olika [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) som stöds av Microsoft Identity Platform.

Mer information finns i följande konceptuella dokumentation:

- [Innehav i Azure Active Directory](single-and-multi-tenant-apps.md)
- [Förstå medgivande i Azure AD-program](application-consent-experience.md)
- [Logga in Azure Active Directory användare med program mönstret flera innehavare](howto-convert-app-to-be-multi-tenant.md)
- [Förstå användar-och administratörs medgivande](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objekt för program och tjänstens huvudnamn i Azure Active Directory](app-objects-and-service-principals.md)
- [Snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md)
- [Snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md)
- [Hämta en token för ett program med flöden för klient referenser](msal-client-applications.md)

Ett enklare program för daemon för flera klient program finns i [snabb starten för .net Core daemon](quickstart-v2-netcore-daemon.md).
