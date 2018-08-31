---
title: Integrera program med Azure Active Directory
description: Så här lägger du till, uppdatera eller ta bort ett program i Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: celested
ms.openlocfilehash: c9db5169a978875cf639f6c534ce7920909c896e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188248"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrera program med Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Utvecklare av företagsprogram och software-as-a-service (SaaS)-providers kan utveckla kommersiella molntjänster eller line-of-business-program som kan integreras med Azure Active Directory (Azure AD) för att tillhandahålla säker inloggning och auktorisering för sina tjänster. Om du vill integrera en app eller tjänst med Azure AD, måste en utvecklare först registrera programmet med Azure AD.

Den här artikeln visar hur du lägger till, uppdatera eller ta bort registreringen av ett program i Azure AD. Du lär dig mer om olika typer av program som kan integreras med Azure AD, hur du konfigurerar dina program för att komma åt andra resurser, till exempel webb-API: er och mycket mer.

Läs mer om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem i [programobjekt och tjänstobjekt](app-objects-and-service-principals.md); mer information om riktlinjer för varumärkesanpassning bör du använda när du utvecklar program med Azure Active Directory, se [anpassning riktlinjer för integrerade appar](howto-add-branding-in-azure-ad-apps.md).

## <a name="adding-an-application"></a>Lägga till ett program
Alla program som vill använda funktioner i Azure AD måste först registreras i en Azure AD-klient. Registreringsprocessen innebär att du ger Azure AD-information om ditt program, till exempel URL: en där den finns, URL: en att skicka svar när en användare autentiseras den URI som identifierar appen och så vidare.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med Azure-portalen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, och klicka på **ny programregistrering**.

   ![Registrera ett nytt program](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration.png)

4. När den **skapa** visas, ange registreringsinformation för ditt program: 

  - **Namn:** ange ett beskrivande namn
  - **Programtyp:** 
    - Välj ”intern” för [klientprogram](developer-glossary.md#client-application) som är lokalt installerade på en enhet. Den här inställningen används för OAuth-offentliga [interna klienter](developer-glossary.md#native-client).
    - Välj ”webbapp / API” för [klientprogram](developer-glossary.md#client-application) och [resurs/API-program](developer-glossary.md#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth-konfidentiella [webbklienter](developer-glossary.md#web-client) och offentliga [användar-agent-baserade klienter](developer-glossary.md#user-agent-based-client). Samma program kan också visas både en klient och resurs/API.
  - **Inloggnings-URL:** för ”webbapp / API” program, ange den grundläggande Webbadressen för din app. Till exempel `http://localhost:31544` kan vara URL-Adressen för en webbapp som körs på den lokala datorn. Användare använder den här URL: en för att logga in till ett webbprogram för klienten. 
  - **Omdirigerings-URI:** för ”interna” program, anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett specifikt värde till ditt program, till exempel `http://MyFirstAADApp`

   ![Registrera ett nytt program – skapa](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration-create.png)

   Om du behöver specifika exempel på webbprogram eller interna program kan du kolla vår [snabbstarter](azure-ad-developers-guide.md#get-started).

5. Klicka på **Skapa** när du är klar. Azure AD tilldelar ett unikt ID för ditt program och kommer du till registreringssidan för programmets huvudsakliga. Beroende på om ditt program är en webbplats eller ett internt program, tillhandahålls olika alternativ för att lägga till ytterligare funktioner i ditt program. Se nästa avsnitt en översikt av medgivande och information om hur du aktiverar funktioner för ytterligare konfiguration i din programregistrering (autentiseringsuppgifter, behörigheter, aktivera-inloggning för användare från andra klienter)

  > [!NOTE]
  > Som standard ett nyligen registrerade program är konfigurerad för att tillåta **endast** användare från samma klient att logga in på ditt program.
  > 
  > 

## <a name="updating-an-application"></a>Uppdatera ett program
När ditt program har registrerats med Azure AD, kan det behöva uppdateras för att tillhandahålla åtkomst till webb-API: er, vara tillgänglig i andra organisationer och mycket mer. Det här avsnittet beskrivs olika sätt som du kan konfigurera ytterligare ditt program. Först börjar vi med en översikt över ramverket för medgivande, vilket är viktigt att förstå när du skapar program som behöver som ska användas av andra användare eller program.

### <a name="overview-of-the-consent-framework"></a>Översikt över ramverket för medgivande

Azure AD-ramverket för medgivande gör det enkelt att utveckla webbprogram för flera klienter och interna klientprogram. Dessa program kan logga in genom att användarkonton från en Azure AD-klient, som skiljer sig från det där programmet har registrerats. De kan också behöva åtkomst till webb-API: er som Microsoft Graph API (för att få åtkomst till Azure Active Directory, Intune och tjänster i Office 365) och andra Microsoft-tjänster API: er, förutom ditt eget webb-API: er. Ramverket är baserad på en användare eller administratör ge ditt medgivande till ett program som frågar registreras i sin katalog, som kan ge åtkomst till katalogdata.

Om ett webb-klientprogram behöver läsa kalenderinformation om användaren från Office 365, är till exempel att användaren måste godkänna klientprogrammet först. När tillstånd ges kommer klientprogrammet att kunna anropa Microsoft Graph API för användarens räkning och använda kalenderinformationen vid behov. Den [Microsoft Graph API](https://graph.microsoft.io) ger åtkomst till data i Office 365 (till exempel kalendrar och meddelanden från Exchange, platser och listor från SharePoint, dokument från OneDrive, från uppgifter från Planner arbetsböcker från Excel, OneNote-anteckningsböcker osv.), samt användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster. 

Ramverket för medgivande bygger på OAuth 2.0 och dess olika flöden, t.ex. bevilja kod stipendium och klientens autentiseringsuppgifter, med hjälp av offentliga eller konfidentiella klienter. Med hjälp av OAuth 2.0, går Azure AD det att skapa många olika typer av klientprogram, till exempel på en telefon, surfplatta, server eller ett webbprogram och få åtkomst till resurserna som krävs.

Läs mer om hur du använder ramverket för medgivande med OAuth2.0 auktoriseringsbeviljanden [bevilja åtkomst till webbprogram med hjälp av OAuth 2.0 och Azure AD](v1-protocols-oauth-code.md) och [Autentiseringsscenarier för Azure AD](authentication-scenarios.md). Information om hur du får auktoriserad åtkomst till Office 365 via Microsoft Graph finns i [appautentisering med Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exempel på samtycke upplevelsen

Följande steg visar hur samtycke uppleva fungerar för både programutvecklare och användare.

1. Anta att du har ett webbprogram för klienten som behöver begära särskild behörighet för att få åtkomst till en resurs/API. Du får lära dig hur du gör denna konfiguration i nästa avsnitt, men i stort sett Azure-portalen för att deklarera behörighetsbegäranden vid konfigurationen. De blir en del av programmets Azure AD-registrering som andra konfigurationsinställningar:
   
  ![Behörigheter för andra program](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)
    
2. Överväg att behörigheter för ditt program har uppdaterats, programmet körs och en användare kommer att använda den för första gången. Programmet måste först hämta en auktoriseringskod från Azure Active Directorys `/authorize` slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny tillgång och uppdatera token.

3. Om användaren inte är autentiserad och Azure Active Directorys `/authorize` endpoint frågar för att logga in.
   
  ![Användaren eller administratören logga in på Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

4. När användaren har loggat in avgör Azure AD om användaren behöver en samtyckessida visas. Det här fastställs baserat på om användaren (eller organisationens administratör) har redan gett samtycke för programmet. Om medgivande inte redan har getts, Azure AD efterfrågar medgivande och visar de behörigheter som krävs ska fungera. Uppsättningen behörigheter som visas i dialogrutan för medgivande överensstämmer med de som valts i den delegerade behörigheter i Azure-portalen.
   
  ![Användarupplevelsen för medgivande](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

5. När användaren ger ditt medgivande, returneras en auktoriseringskod till programmet, som har löst in att hämta en åtkomsttoken och uppdatera token. Mer information om det här flödet finns i den [webbprogram till webb-API-avsnittet i Autentiseringsscenarier för Azure AD](authentication-scenarios.md#web-application-to-web-api).

6. Som administratör kan också samtycker du till ett programs delegerade behörigheter för alla användare i din klient. Administratörs godkännande förhindrar att godkännande i dialogrutan visas för varje användare i klienten och kan göras i den [Azure-portalen](https://portal.azure.com) av användare med administratörsrollen. Från den **inställningar** för ditt program och klicka på **nödvändiga behörigheter** och klicka på den **bevilja** knappen. 

  ![Bevilja behörigheter för explicit administratörens godkännande](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)
    
  > [!NOTE]
  > Bevilja uttryckliga medgivande med hjälp av den **bevilja** knappen krävs för närvarande för en sida-program (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram för att få åtkomst till webb-API: er
Det måste upprätta säkra autentiseringsuppgifter för ett webb-/ konfidentiell klientprogram för att kunna delta i ett flöde för auktorisering att bevilja som kräver autentisering (och få en åtkomsttoken). Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel. Det här avsnittet beskriver de konfigurationssteg som krävs för att tillhandahålla den hemliga nyckeln med klientens autentiseringsuppgifter.

Dessutom innan en klient kan komma åt ett webb-API som exponeras av resursprogram (till exempel Microsoft Graph-API), ramverket för medgivande säkerställer klienten hämtar åtgärden bevilja behörigheter krävs, baserat på de behörigheter som begärdes. Som standard kan alla program Välj behörigheter från ”Windows Azure Active Directory” (Graph-API) och ”Windows Azure Service Management-API”. Den [Graph API ”logga in och Läs användarprofil” behörighet](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) har markerats som standard. Om klienten registreras i en klient som har konton som prenumererar på Office 365, kan webb-API: er och behörigheter för SharePoint och Exchange Online välja. Du kan välja från [två typer av behörigheter](developer-glossary.md#permissions) för var och en önskad webb-API:

- Behörigheter för programmet: Klientprogrammet behöver åtkomst till webb-API som sig självt (inga användarkontext). Den här typen av behörighet kräver administratörens godkännande och är inte heller tillgängliga för interna klientprogram.

- Delegerade behörigheter: Klientprogrammet behöver åtkomst till webb-API som den inloggade användaren, men med åtkomst begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare, såvida inte behörigheten kräver administratörens godkännande. 

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt ditt medgivande till användare i klienten. Användarna måste manuellt medgivande för de tillagda delegerade behörigheterna vid körning, om inte administratören tilldelar samtycke åt alla användare.

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Om du vill lägga till webb autentiseringsuppgifter eller behörighet att komma åt-API: er
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera.

   ![Uppdatera registreringen av ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet. Lägga till en autentiseringsuppgift för ditt webbprogram:
  - Klicka på den **nycklar** avsnittet på den **inställningar** sidan. 
  - Lägga till ett certifikat:
    - Välj **ladda upp offentlig nyckel**.
    - Markera den fil som du vill ladda upp. Det måste vara något av följande filtyper: .cer, .pem, .crt.
  - Lägga till ett lösenord:
    - Lägg till en beskrivning av nyckeln.
    - Välj en varaktighet.
    - Klicka på **Spara**. Kolumnen längst till höger innehåller nyckelvärde, när du har sparat ändringar i konfigurationen. **Se till att kopiera nyckeln** för användning i din programkod för klienten, eftersom den inte är tillgänglig när du lämnar den här sidan.

5. Att lägga till behörigheter att komma åt resursen API: er från klienten
  - Klicka på den **nödvändiga behörigheter** avsnittet på den **inställningar** sidan. 
  - Klicka på knappen **Lägg till**.
  - Klicka på **Välj en API** välja vilken typ av resurser som du vill välja bland.
  - Bläddra igenom listan med tillgängliga API: er eller Använd sökrutan för att välja bland de tillgängliga resursgrupper program i din katalog som exponerar ett webb-API. Klicka på den resurs som du är intresserad av och sedan klicka på **Välj**.
  - Kommer du till den **Aktivera åtkomst** sidan. Välj behörigheter för programmet och/eller delegerade behörigheter som programmet behöver vid åtkomst till API: et.
   
  ![Uppdatera registreringen av ett program - behörigheter api](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

  ![Uppdatera registreringen av ett program - behörigheter för varje behörighet](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. När du är klar klickar du på den **Välj** knappen **Aktivera åtkomst** kan sedan **klar** knappen på den **Lägg till API-åtkomst** sidan. Du kommer tillbaka till den **nödvändiga behörigheter** sidan där den nya resursen har lagts till i listan över API: er.

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram att exponera webb API: er

Du kan utveckla ett webb-API och göra det tillgängligt för klientprogram genom att exponera åtkomst [scope](developer-glossary.md#scopes) och [roller](developer-glossary.md#roles). Ett korrekt konfigurerat webb-API tillhandahålls precis som andra Microsoft webb API: er, inklusive Graph API och API: er för Office 365. Åtkomstscope och roller är tillgängliga via din [programmets manifest](developer-glossary.md#application-manifest), vilket är en JSON-fil som representerar identitet programkonfiguration. 

Följande avsnitt visar hur du exponera åtkomstscope, genom att ändra resursen programmets manifest.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Att lägga till scope till din resursprogram

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.

3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera.

   ![Uppdatera registreringen av ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet. Växla till den **redigera manifest** sidan genom att klicka på **Manifest** från programmets-registreringssidan. En webbaserad redigeringsprogrammet för applikationsmanifestet öppnas, så att du kan **redigera** manifestet i portalen. Alternativt kan du klicka på **hämta** och redigera lokalt och sedan använda **överför** för att återställa den till ditt program.

5. I det här exemplet exponerar vi ett nytt scope som kallas `Employees.Read.All` på våra resurs/API genom att lägga till följande JSON-elementet så att den `oauth2Permissions` samling. Den befintliga `user_impersonation` omfång som standard under registreringen. `user_impersonation` kan ett klientprogram att begära tillstånd att komma åt resursen under identiteten för den inloggade användaren. Se till att lägga till kommatecknet efter den befintliga `user_impersonation` omfånget elementet och ändra egenskapsvärden så att de passar behoven för din resurs. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > ”Id”-värdet måste skapas med ett verktyg för generering av GUID som [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) eller programmässigt. Representerar en unik identifierare för omfånget som visas av webb-API. När en klient är korrekt konfigurerad med behörighet att komma åt ditt webb-API, utfärdas en åtkomsttoken för OAuth2.0 av Azure AD. När webb-API för klientanrop presenterar åtkomsttoken som har omfång (scp) Ange anspråk till de behörigheter som efterfrågas i dess programregistrering.
  >
  > Du kan lägga upp ytterligare scope senare, vid behov. Överväg att ditt webb-API kan exponera flera scope som är associerade med en mängd olika funktioner. Resursen kan styra åtkomsten till webb-API vid körning, genom att utvärdera omfattningen (`scp`) anspråken i mottagna OAuth 2.0-åtkomsttoken.
  > 

6. När du är klar klickar du på **spara**. Ditt webb-API har nu konfigurerats för användning av andra program i din katalog. 

  ![Uppdatera registreringen av ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verifiera att ditt webb API exponeras för andra program i din klient
1. Gå tillbaka till din Azure AD-klientorganisation, klickar du på **appregistreringar** igen och sedan hitta/klickar du på klientprogram som du vill konfigurera.

   ![Uppdatera registreringen av ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Upprepa steg 5 som du gjorde i [konfigurera ett klientprogram för att få åtkomst till webb-API: er](#configure-a-client-application-to-access-web-apis). När du kommer till den **Välj en API** steg, Sök efter din resurs genom att ange dess programnamn i sökfältet och klicka på **Välj**. 

3. På den **Aktivera åtkomst** sidan som du bör se nytt scope, tillgängliga för klientbegäranden för behörighet.

  ![Nya behörigheter visas](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Mer information om manifestet

Applikationsmanifestet faktiskt fungerar som en mekanism för att uppdatera entiteten program, som definierar alla attribut för ett Azure AD-program identitet konfigurationen, inklusive API-åtkomstscope som beskrivits. Mer information om entiteten programmet och dess schema finns i den [Graph-API-program entity dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Artikeln innehåller utförlig referensinformation om programmet enhetsmedlemmar som används för att ange behörigheter för ditt API, inklusive:  

- Medlemmen appRoles som är en samling av [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entiteter som används för att definiera [programbehörigheter](developer-glossary.md#permissions) för ett webb-API. 
- Oauth2Permissions-medlem, som är en samling av [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entiteter som används för att definiera [delegerade behörigheter](developer-glossary.md#permissions) för ett webb-API.

Mer information om application manifest begrepp i allmänhet, se [förstå programmanifestet för Azure Active Directory](reference-app-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Åtkomst till Azure AD Graph och Office 365 via Microsoft Graph API: er  

Du kan registrera ditt klientprogram att komma åt API: er som exponeras av Microsoft-resurser som tidigare nämnts, förutom att exponera/komma åt API: er för dina egna program. Microsoft Graph API, som anges som ”Microsoft Graph” i portalens resurs/API-lista, är tillgängliga för alla program som är registrerade med Azure AD. Om du registrerar ditt klientprogram i en klient som innehåller konton som har registrerat sig för en prenumeration på Office 365, kan du också komma åt scope som visas av de olika Office 365-resurserna.

En fullständig beskrivning på omfång som exponeras av Microsoft Graph API finns i den [behörighetsreferens för Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) artikeln.

> [!NOTE]
> På grund av en aktuell begränsning, kan interna klientprogram endast anropa Azure AD Graph-API om de använder behörigheten ”åt din organisations katalog”. Den här begränsningen gäller inte för webbprogram.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurera program för flera innehavare

När du registrerar ett program i Azure AD, kan du vill att programmet ska endast användas av användare i din organisation. Alternativt kan du vill att programmet ska vara tillgängliga för användare i externa organisationer. Dessa två programtyper kallas för en enda klient-och flera innehavare. Det här avsnittet beskrivs hur du ändrar konfigurationen för en enda klient-programmet att göra det till ett program med flera innehavare.

Det är viktigt att Observera skillnaderna mellan en enda klient och flera innehavare för program:  

- En enda klient-programmet är avsett att användas i en organisation. Det är vanligtvis en line-of-business (LoB)-program som skrivits av företagsutvecklare. En enda klient-program kan bara användas av användare med konton i samma klient som programregistrering. Därför kan måste den endast etableras i en katalog.
- Ett program med flera innehavare är avsedd att användas i många organisationer. Kallas för ett webbprogram för programvara som tjänst (SaaS) och skrivs den vanligtvis av en oberoende programvaruleverantör (ISV). Program för flera innehavare måste etableras på varje klient där användare behöver åtkomst. För klienter än den där programmet har registrerats kan krävs användaren eller administratören medgivande för att registrera dem. Observera att interna klientprogram är flera innehavare som standard som de är installerade på enheten för ägare till resursen. Se föregående [översikt över ramverket för medgivande](#overview-of-the-consent-framework) avsnitt finns information om ramverket för medgivande.

Att göra ett program med flera innehavare kräver båda programändringar för registrering, samt ändringar i webbprogrammet själva. Följande avsnitt beskriver båda.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Ändra programregistrering för att stödja flera innehavare

Om du skapar ett program som du vill göra tillgängliga för dina kunder eller partners utanför din organisation, måste du uppdatera definitionen för programmet i Azure-portalen.

> [!IMPORTANT]
> Azure AD kräver URI: N för App-ID för program för flera innehavare vara globalt unikt. App-ID-URI är en av de sätt som ett program har identifierats i protokollmeddelanden. För en enda klient-program är det tillräckligt för URI: N för App-ID vara unikt inom den klienten. För ett program med flera innehavare, måste den vara globalt unikt så att Azure AD kan hitta programmet över alla klienter. Globala unika tillämpas genom att kräva att App-ID-URI att ha ett värdnamn som matchar en verifierad domän i Azure AD-klient. Till exempel om namnet på din klient är contoso.onmicrosoft.com och sedan en giltig URI för App-ID blir https://contoso.onmicrosoft.com/myapp. Om klienten har en verifierad domän contoso.com blir en giltig App-ID: T URI skulle också https://contoso.com/myapp. Om App-ID-URI inte följer detta mönster, ställa in ett program som misslyckas av flera innehavare.
> 

Så här ger externa användare möjlighet att komma åt ditt program: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet.
4. Från den **inställningar** klickar du på **egenskaper** och ändra den **med flera innehavare** växla till **Ja**.

När du har gjort ändringarna, kan användare och administratörer i andra organisationer ge användarna möjlighet att logga in på ditt program, så att dina program att komma åt resurser som skyddas av klientorganisationen.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Ändra program som stöder flera innehavare

Stöd för flera innehavare program förlitar sig mycket på Azure AD-ramverket för medgivande. Medgivande är den mekanism som tillåter en användare från en annan klient för att ge programmet åtkomst till resurser som skyddas av användarens klient. Detta kallas för ”användarmedgivande”.

Webbprogrammet kan också erbjuda:

- Möjligheten för administratörer att ”logga in mitt företag”. Den här upplevelsen som kallas ”administratörsmedgivande”, kan en administratör möjlighet att ge samtycke åt *alla användare* i deras organisation. Endast användare som autentiseras med ett konto som tillhör rollen Global administratör kan ge samtycke för administratören. andra får ett felmeddelande.

- En inloggning för användare. Det är normalt att användaren får en ”registrering” knapp som omdirigerar webbläsaren till Azure AD-OAuth2.0 `/authorize` slutpunkt eller en OpenID Connect `/userinfo` slutpunkt. Dessa slutpunkter gör det möjligt för programmet för att hämta information om den nya användaren genom att granska id_token. Efter registreringen fas visas med Kommandotolken medgivande liknar den som visas i den [översikt över ramverket för medgivande](#overview-of-the-consent-framework) avsnittet.

Mer information om programändringar som krävs för att stödja finns åtkomst med flera innehavare och logga-i/registrering upplevelser:

- [Så här loggar du in valfri Azure Active Directory (AD)-användare med programmönstret för flera klienter](howto-convert-app-to-be-multi-tenant.md)
- Listan över [kodexempel för flera innehavare](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Snabbstart: Lägga till företagsprofilering för din inloggningssidan i Azure AD](../fundamentals/customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Om du aktiverar OAuth beviljas 2.0 implicit för den enda sidan program

Enskild sida programmets (SPA) är vanligtvis strukturerade med JavaScript-aktiverat klientdelen som körs i webbläsaren, som anropar programmets webb-API backend-server för att utföra affärslogik på dess. För SPA finns i Azure AD, använder du Implicit beviljande av OAuth 2.0 för att autentisera användare med Azure AD och få en token som du kan använda för att skydda anrop från programmets JavaScript-klient till dess backend-webb-API. 

När användaren har gett ditt medgivande, kan det här samma autentiseringsprotokoll användas till att hämta token för att skydda anrop mellan klienten och andra webb-API-resurserna som konfigurerats för programmet. Mer information om implicit auktoriseringsbeviljande och hjälper dig att avgöra om det är bäst för ditt program-scenario genom att se [förstå OAuth2 implicit ge flow i Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Som standard inaktiveras implicit beviljande av OAuth 2.0 för program. Du kan aktivera Implicit beviljande av OAuth 2.0 för ditt program genom att ange den `oauth2AllowImplicitFlow` värde i dess [programmanifestet](reference-app-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Aktivera implicit beviljande av OAuth 2.0

> [!NOTE]
> För information om hur du redigerar applikationsmanifestet, bör du först granska föregående avsnitt, [konfigurera resursprogram att exponera webb-API: er](#configuring-a-resource-application-to-expose-web-apis).
>

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet.
4. Växla till den **redigera manifest** sidan genom att klicka på **Manifest** från programmets-registreringssidan. En webbaserad redigeringsprogrammet för applikationsmanifestet öppnas, så att du kan **redigera** manifestet i portalen. Leta upp och ange värdet ”oauth2AllowImplicitFlow” till ”true”. Som standard är den inställd på ”false”.
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Spara det uppdaterade manifestet. När du har sparat har ditt webb-API nu konfigurerats för att använda Implicit beviljande av OAuth 2.0 för att autentisera användare.

## <a name="removing-an-application"></a>Ta bort ett program
Det här avsnittet beskrivs hur du tar bort en programregistrering från Azure AD-klienten.

### <a name="removing-an-application-authored-by-your-organization"></a>Ta bort ett program som skapats av din organisation
Program som din organisation har registrerats visas under den ”Mina appar”-filter på sidan för din klient huvudsakliga ”appregistreringar”. Dessa program är de som du registrerade manuellt via Azure portal eller programmässigt via PowerShell eller Graph API. Mer specifikt representeras de av både ett program och tjänstens huvudnamn objekt i din klient. Mer information finns i [programobjekt och tjänstobjekt](app-objects-and-service-principals.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Ta bort en enda klient-program från din katalog
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet.
4. Programmets huvudsakliga registreringssidan klickar du på **ta bort**.
5. Klicka på **Ja** i bekräftelsemeddelandet.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Ta bort ett program med flera innehavare från sin hemkatalog
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange portal sessionen med önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten, klicka på **appregistreringar**, sedan hitta/klickar du på programmet som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** för programmet.
4. Från den **inställningar** väljer **egenskaper** och ändra den **med flera innehavare** växla till **nr**, först ändra ditt program enda klient, klicka sedan på **spara**. Programmets tjänsthuvudnamnsobjekt finns kvar i innehavare av alla organisationer som redan har samtyckt till den.
5. Klicka på den **ta bort** knappen från programmets huvudsakliga registreringssidan.
6. Klicka på **Ja** i bekräftelsemeddelandet.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Ta bort ett program för flera klienter som godkänts av en annan organisation
En delmängd av de program som visas under ”alla appar”-filter (förutom i ”Mina appar” registreringar) på din klient ”appregistreringar” huvudsidan är program för flera innehavare. Tekniskt sett dessa program för flera innehavare är från en annan klient och registrerades i din klient under medgivande. Mer specifikt representeras de av endast en tjänstens huvudnamnsobjekt i din klient med ingen motsvarande programobjektet. Läs mer om skillnaderna mellan program och tjänstobjekt [program och tjänstobjekt i Azure AD](app-objects-and-service-principals.md).

Företagets administratör måste ta bort dess huvudnamn för tjänsten för att ta bort ett program med flera klienter åtkomst till din katalog (efter att ha godkänts). Administratören måste behörighet för global administratör, och kan ta bort den via Azure-portalen eller använda den [Azure AD PowerShell-Cmdlets](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns i [Autentiseringsscenarier för Azure AD](authentication-scenarios.md).
- Se den [anpassning riktlinjer för integrerade appar](howto-add-branding-in-azure-ad-apps.md) tips om visual vägledning för din app.
- Mer information om relationen mellan program och tjänstens huvudnamn objekt i ett program finns i [programobjekt och tjänstobjekt](app-objects-and-service-principals.md).
- Läs mer om rollen app manifest spelar i [förstå programmanifestet för Azure Active Directory](reference-app-manifest.md)
- Se den [ordlista för Azure AD-utvecklare](developer-glossary.md) för definitioner av några av de viktigaste Azure AD-begreppen för utvecklare.
- Gå till den [utvecklarguide för Active Directory](azure-ad-developers-guide.md) en översikt över alla developer-relaterat innehåll.

