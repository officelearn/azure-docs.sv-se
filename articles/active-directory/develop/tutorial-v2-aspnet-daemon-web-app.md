---
title: Skapa en multitenant daemon som använder slutpunkten för Microsoft identity platform
description: I den här självstudien kan du läsa om hur du anropar ett ASP.NET webb-API som skyddas av Azure Active Directory från ett WPF-program (Windows Desktop). WPF-klienten autentiserar en användare, begär en åtkomsttoken och anropar webb-API:et.
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
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 635b12cc2ffc4d318eaaa74fffc17e4ce4d58c0b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129962"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Självstudiekurs: Skapa en multitenant daemon som använder slutpunkten för Microsoft-identitetsplattform

I den här självstudien får du lära dig hur du använder Microsofts identitetsplattform för att komma åt data från Microsofts företagskunder i en tidskrävande, icke-interaktiv process. Exempeldemonen använder [bevilja OAuth2-klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) för att hämta en åtkomsttoken. Demonen använder sedan token för att anropa [Microsoft Graph](https://graph.microsoft.io) och komma åt organisationsdata.

> [!div class="checklist"]
> * Integrera en daemonapp med Microsofts identitetsplattform
> * Bevilja programbehörigheter direkt till appen av en administratör
> * Hämta en åtkomsttoken för att anropa Microsoft Graph API
> * Anropa Microsoft Graph API.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Appen är byggd som ett ASP.NET MVC-program. Den använder OWIN OpenID Connect middleware för att logga in användare.  

Komponenten "daemon" i det här `SyncController.cs`exemplet är en API-styrenhet. När styrenheten anropas hämtas en lista över användare i kundens Azure Active Directory-klientorganisation (Azure AD) från Microsoft Graph. `SyncController.cs`utlöses av ett AJAX-samtal i webbprogrammet. Den använder [MICROSOFT Authentication Library (MSAL) för .NET för](msal-overview.md) att hämta en åtkomsttoken för Microsoft Graph.

>[!NOTE]
> Om du inte har gjort det tidigare till Microsofts identitetsplattform rekommenderar vi att du börjar med [snabbstarten .NET Core daemon](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenario

Eftersom appen är en app med flera signaturer för Microsofts företagskunder måste den vara ett sätt för kunder att "registrera sig" eller "ansluta" programmet till sina företagsdata. Under anslutningsflödet beviljar en företagsadministratör först *programbehörigheter* direkt till appen så att den kan komma åt företagsdata på ett icke-interaktivt sätt, utan närvaro av en inloggad användare. Majoriteten av logiken i det här exemplet visar hur du uppnår det här anslutningsflödet med hjälp av identitetsplattformens slutpunkt [för administratörsmedgivande.](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

![Topologi](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Mer information om de begrepp som används i det här exemplet finns i [dokumentationen till klientautentiseringsprotokollet för slutpunkten för identitetsplattformen](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Krav

Om du vill köra exemplet i den här snabbstarten måste du:

- [Visual Studio 2017 eller 2019](https://visualstudio.microsoft.com/downloads/).
- En Azure AD-klientorganisation. Mer information finns i [Så här skaffar du en Azure AD-klientorganisation](quickstart-create-new-tenant.md).
- Ett eller flera användarkonton i din Azure AD-klientorganisation. Det här exemplet fungerar inte med ett Microsoft-konto (tidigare Windows Live-konto). Om du loggade in på [Azure-portalen](https://portal.azure.com) med ett Microsoft-konto och aldrig har skapat ett användarkonto i din katalog måste du göra det nu.

## <a name="clone-or-download-this-repository"></a>Klona eller hämta den här databasen

Från skalet eller kommandoraden anger du det här kommandot:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Eller [ladda ner exemplet i en zip-fil](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrera ditt program

Det här exemplet har ett projekt. Om du vill registrera programmet hos din Azure AD-klient kan du antingen:

- Följ stegen i [Registrera exemplet med din Azure Active Directory-klient](#register-your-application) och konfigurera exemplet för att använda din Azure [AD-klientorganisation](#choose-the-azure-ad-tenant).
- Använd PowerShell-skript som:
  - *Skapa Automatiskt* Azure AD-program och relaterade objekt (lösenord, behörigheter, beroenden) för dig.
  - Ändra Visual Studio-projektens konfigurationsfiler.

Om du vill använda automatiseringen:

1. På Windows kör du PowerShell och går till roten i den klonade katalogen.
1. Kör följande kommando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Kör skriptet för att skapa ditt Azure AD-program och konfigurera koden för exempelprogrammet därefter:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Andra sätt att köra skript beskrivs i [App creation scripts](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Öppna Visual Studio-lösningen och välj **Start** för att köra koden.

Om du inte vill använda automatiseringen följer du stegen i följande avsnitt.

### <a name="choose-the-azure-ad-tenant"></a>Välj Azure AD-klienten

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i mer än en Azure AD-klient väljer du din profil högst upp på sidan och väljer sedan **Växla katalog**.
1. Ändra din portalsession till önskad Azure AD-klientorganisation.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrera klientappen (dotnet-web-daemon-v2)

1. Gå till sidan Appregistreringar i Microsofts [identitetsplattform](https://go.microsoft.com/fwlink/?linkid=2083908) för utvecklare.
1. Välj **Ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **Namn** anger du ett meningsfullt programnamn som ska visas för användare av appen. Ange till exempel **dotnet-web-daemon-v2**.
   - I avsnittet **Konton som stöds** väljer du Konton i en **organisationskatalog**.
   - I avsnittet **Omdirigera URI (valfritt)** väljer du **Webben** i kombinationsrutan och anger följande omdirigerings-URI:er:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Om det finns fler än två omdirigerings-URI:er måste du lägga till dem från fliken **Autentisering** senare, när appen har skapats.
1. Välj **Registrera** för att skapa programmet.
1. På appens **översiktssida** letar du reda på värdet **program (klient) och** registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för det här projektet.
1. I listan över sidor för appen väljer du **Autentisering**. Sedan:
   - I avsnittet **Avancerade inställningar** anger du URL **för utloggning** till **https://localhost:44316/Account/EndSession**.
   - I avsnittet Implicit**beviljande** **av avancerade inställningar** > väljer du **Access-token** och **ID-token**. Det här exemplet kräver att det [implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md) kan aktiveras för att logga in användaren och anropa ett API.
1. Välj **Spara**.
1. Välj **Ny klienthemlighet**i avsnittet **Klienthemligheter** på sidan **Certifikat & hemligheter** . Sedan:

   1. Ange en nyckelbeskrivning (till exempel **apphemlighet**)
   1. Välj en nyckellängd på antingen **I 1 år**, **I 2 år**eller Aldrig upphör **.**
   1. Välj knappen **Lägg till**. 
   1. När nyckelvärdet visas kopierar och sparar du det på en säker plats. Du behöver den här nyckeln senare för att konfigurera projektet i Visual Studio. Det kommer inte att visas igen eller hämtas på något annat sätt.
1. Välj **API-behörigheter**i listan över sidor för appen . Sedan:
   1. Välj knappen **Lägg till en behörighet**.
   1. Kontrollera att fliken **Microsoft API:er** är markerad.
   1. I avsnittet **Microsoft API:er används** väljer du **Microsoft Graph**.
   1. Kontrollera att rätt behörigheter är markerade i avsnittet **Programbehörigheter:** **User.Read.All**.
   1. Välj knappen **Lägg till behörigheter.**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurera exemplet för att använda din Azure AD-klient

I följande steg är **ClientID** samma som "application ID" eller **AppId**.

Öppna lösningen i Visual Studio för att konfigurera projekten.

### <a name="configure-the-client-project"></a>Konfigurera klientprojektet

Om du använde inställningsskripten har följande ändringar tillämpats för dig.

1. Öppna filen **UserSync\Web.Config.**
1. Hitta appnyckeln **ida:ClientId**. Ersätt det befintliga värdet med program-ID för **dotnet-web-daemon-v2-programmet** som kopierats från Azure-portalen.
1. Hitta appnyckeln **ida:ClientSecret**. Ersätt det befintliga värdet med nyckeln som du sparade när **dotnet-web-daemon-v2-appen** skapades i Azure-portalen.

## <a name="run-the-sample"></a>Kör exemplet

Rensa lösningen, återskapa lösningen, kör UserSync-programmet och logga sedan in som administratör i din Azure AD-klientorganisation. Om du inte har en Azure AD-klient för testning kan du [följa dessa instruktioner](quickstart-create-new-tenant.md) för att få en.

När du loggar in ber appen dig först om tillåtelse att logga in dig och läsa din användarprofil. Med det här medgivandet kan appen se till att du är en företagsanvändare.

![Användargodkännande](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Appen försöker sedan synkronisera en lista över användare från din Azure AD-klient, via Microsoft Graph. Om den inte går uppmanas du (klientadministratören) att ansluta din klient till appen.

Appen ber sedan om tillstånd att läsa listan över användare i din klientorganisation.

![Administratörsmedgivande](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

När du har beviljat behörighet loggas du ut från appen. Den här utskrivningen säkerställer att alla befintliga åtkomsttoken för Microsoft Graph tas bort från tokencachen. När du loggar in igen har den nya token som erhålls de behörigheter som krävs för att ringa samtal till Microsoft Graph.


När du beviljar behörigheten kan appen sedan fråga efter användare när som helst. Du kan kontrollera detta genom att välja knappen **Synkronisera användare** och uppdatera listan över användare. Prova att lägga till eller ta bort en användare och synkronisera om listan. (Men observera att appen bara synkroniserar den första sidan av användare.)

## <a name="about-the-code"></a>Om koden

Den relevanta koden för det här exemplet finns i följande filer:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Inledande inloggning. I synnerhet har åtgärderna på styrenheten ett **authorize-attribut,** vilket tvingar användaren att logga in. Programmet använder [auktoriseringskodflödet](v2-oauth2-auth-code-flow.md) för att logga in användaren.
- **Controllers\SyncController.cs**: Synkronisera listan över användare till det lokala minneslagringsarkivet.
- **Controllers\UserController.cs**: Visar listan över användare från det lokala minneslagringsarkivet.
- **Controllers\AccountController.cs**: Hämta behörigheter från klientadministratören med hjälp av slutpunkten för administratörsgodkännande.

## <a name="re-create-the-sample-app"></a>Återskapa exempelappen

1. Skapa ett nytt **visual C#** **ASP.NET Web Application -projekt (.NET Framework)** i Visual Studio. 
1. Välj **MVC-projektmallen** på nästa skärm. Lägg också till mapp- och kärnreferenser för **webb-API,** eftersom du lägger till en webb-API-styrenhet senare. Lämna projektets valda autentiseringsläge som standard: **Ingen autentisering**.
1. Markera projektet i fönstret **Solution Explorer** och välj **F4-tangenten.** 
1. Ange **SSL-aktiverad** **i**projektegenskaperna . Observera informationen i **SSL URL**. Du behöver det när du konfigurerar det här programmets registrering i Azure-portalen.
1. Lägg till följande ASP.NET OWIN middleware NuGet-paket: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. I **mappen App_Start:**
   1. Skapa en klass som heter **Startup.Auth.cs**. 
   1. Ta bort **. App_Start** från namnområdets namn. 
   1. Ersätt koden för **klassen Start** med koden från samma fil i exempelappen.       
   Var noga med att ta hela klassdefinitionen. Definitionen ändras från **offentlig klass Start** till offentlig partiell klass **Start.**
1. I **Startup.Auth.cs**löser du saknade referenser genom att lägga till **med hjälp av** satser som föreslagits av Visual Studio IntelliSense.
1. Högerklicka på projektet, välj **Lägg till**och välj sedan **Klass**.
1. Skriv **OWIN**i sökrutan . **OWIN Startup-klass** visas som en markering. Markera den och ge klassen **namnet Startup.cs**.
1. I **Startup.cs**ersätter du koden för **klassen Start** med koden från samma fil i exempelappen. Observera återigen att definitionen ändras från **offentlig klass Start** till offentlig partiell klass **Start**.
1. Lägg till en ny klass som heter **MsGraphUser.cs**i mappen **Modeller** . Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **MVC 5 Controller - Tom** instans som kallas **AccountController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **MVC 5 Controller - Tom** instans som kallas **UserController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till en ny **Web API 2 Controller - Tom** instans som kallas **SyncController**. Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Lägg till tre **tomma vyer i** mappen **Views\Account** för användargränssnittet med namnet **GrantPermissions**, **Index**och **UserMismatch**. Lägg till och ett med namnet **Index** i mappen **Views\User.** Ersätt implementeringen med innehållet i filen med samma namn från exemplet.
1. Uppdatera **\_delad layout.cshtml** och **Start\Index.cshtml** för att länka samman de olika vyerna på rätt sätt.

## <a name="deploy-the-sample-to-azure"></a>Distribuera exemplet till Azure

Det här projektet har webbapp- och webb-API-projekt. Så här distribuerar du dem till Azure-webbplatser:

1. Skapa en Azure-webbplats.
1. Publicera webbappen och webb-API:erna på webbplatsen.
1. Uppdatera klienter för att ringa webbplatsen i stället för IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Skapa och publicera dotnet-web-daemon-v2 till en Azure-webbplats

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** i det övre vänstra hörnet.
1. Välj **Web** > **Web App**och ge sedan webbplatsen ett namn. Nämn till exempel **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Välj information för **Prenumeration,** **Resursgrupp**och **App-tjänstplan och plats**. **OS** är **Windows**och **Publicera** är **kod**.
1. Välj **Skapa** och vänta på att apptjänsten har skapats.
1. När du får meddelandet **Om distribution har slutförts** väljer du **Gå till resurs** för att gå till den nyskapade apptjänsten.
1. När webbplatsen har skapats hittar du den i **instrumentpanelen** och väljer den för att öppna apptjänstens **översiktsskärm.**
1. Ladda ned publiceringsprofilen på fliken **Översikt** för apptjänsten genom att välja länken **Hämta publiceringsprofil** och spara den. Du kan använda andra distributionsmekanismer, till exempel distribuera från källkontroll.
1. Växla till Visual Studio och sedan:
   1. Gå till **projektet dotnet-web-daemon-v2.** 
   1. Högerklicka på projektet i Lösningsutforskaren och välj sedan **Publicera**.
   1. Välj **Importera profil** i det nedre fältet och importera publiceringsprofilen som du hämtade tidigare.
1. Välj **Konfigurera**.
1. På fliken **Anslutning** uppdaterar du mål-URL:en så att den använder "https". Använd till [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)exempel . Välj **Nästa**.
1. Kontrollera att **Aktivera organisationsautentisering** är avmarkerat på fliken **Inställningar.**  
1. Välj **Spara**. Välj **Publicera** på huvudskärmen.

Visual Studio publicerar projektet och öppnar automatiskt en webbläsare för projektets URL. Om du ser projektets standardwebbsida lyckades publikationen.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Uppdatera registreringen av Azure AD-klientprogram för dotnet-web-daemon-v2

1. Gå tillbaka till [Azure-portalen](https://portal.azure.com).
1. I den vänstra rutan väljer du **Azure Active Directory-tjänsten** och väljer sedan **Appregistreringar**.
1. Välj **dotnet-web-daemon-v2** ansökan.
1. På sidan **Autentisering** för ditt program uppdaterar du **URL-fälten för utloggning** med tjänstens adress. Använd till [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)exempel .
1. Uppdatera **startsidesadressen** till tjänstens adress på **Branding-menyn.** Använd till [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)exempel .
1. Spara konfigurationen.
1. Lägg till samma URL i listan med värden på > **autentiseringsomdirigerings-urierna.** **Authentication** Om du har flera omdirigeringsadresser kontrollerar du att det finns en ny post som använder apptjänstens URI för varje omdirigerings-URL.

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs tar du bort appobjektet som du skapade i steget [Registrera ditt program.](#register-your-application)  Om du vill ta bort programmet följer du instruktionerna i [Ta bort ett program som du eller din organisation har skapat](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Få hjälp

Använd [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) för att få support från communityn.
Ställ dina frågor om Stack Overflow först och bläddra bland befintliga problem för att se om någon har ställt din fråga tidigare.
Kontrollera att dina frågor eller kommentarer är taggade med "adal", "msal" och "dotnet".

Om du hittar ett fel i exemplet kan du ta upp problemet på [GitHub-problem](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Om du hittar ett fel i MSAL.NET, vänligen ta upp frågan om [MSAL.NET GitHub Frågor](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Om du vill ge en rekommendation går du till [sidan Användarröst](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Nästa steg
Läs mer om de olika [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md) som Microsoft identity-plattformen stöder.

Mer information finns i följande konceptuella dokumentation:

- [Hyresrätt i Azure Active Directory](single-and-multi-tenant-apps.md)
- [Förstå medgivande i Azure AD-program](application-consent-experience.md)
- [Logga in alla Azure Active Directory-användare med hjälp av programmönstret för flera ants](howto-convert-app-to-be-multi-tenant.md)
- [Förstå användarens och administratörens medgivande](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objekt för program och tjänstens huvudnamn i Azure Active Directory](app-objects-and-service-principals.md)
- [Snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md)
- [Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md)
- [Skaffa en token för ett program med klientautentiseringsflöden](msal-client-applications.md)

En enklare multitenant konsol daemon ansökan, se [.NET Core daemon quickstart](quickstart-v2-netcore-daemon.md).
