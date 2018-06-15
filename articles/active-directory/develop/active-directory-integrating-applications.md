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
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 76c6ef7d4cf53872dda308628790994b35d8431c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158004"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrera program med Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Utvecklare av företagsprogram och programvara som en-tjänst (SaaS)-leverantörer kan utveckla kommersiella molntjänster eller line-of-business-program som kan integreras med Azure Active Directory (Azure AD) för att tillhandahålla säker inloggning och auktorisering för sina tjänster. Om du vill integrera en applikation eller tjänst med Azure AD, måste en utvecklare först registrera programmet med Azure AD.

Den här artikeln visar hur du lägger till, uppdatera eller ta bort registreringen av ett program i Azure AD. Du lär dig mer om olika typer av program som kan integreras med Azure AD, hur du konfigurerar ditt program för att komma åt andra resurser, till exempel web API: er och mycket mer.

Läs mer om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem i [program och tjänstens huvudnamn objekt](active-directory-application-objects.md); Läs mer om riktlinjer för varumärkesanpassning bör du använda när du utvecklar program med Azure Active Directory, se [företagsanpassning riktlinjer för integrerade appar](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Lägga till ett program
Alla program som du vill använda funktionerna i Azure AD måste först registreras i Azure AD-klient. Registrering under processen ger Azure AD-information om ditt program, till exempel URL där den finns, URL: en att skicka svar när en användare autentiseras den URI som identifierar app och så vidare.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Att registrera ett nytt program med Azure-portalen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, och klicka på **nya appregistrering**.

   ![Registrera ett nytt program](./media/active-directory-integrating-applications/add-app-registration.png)

4. När den **skapa** visas, ange registreringsinformation för ditt program: 

  - **Namn:** ange ett beskrivande namn
  - **Programtyp av:** 
    - Välj ”inbyggd” för [klientprogram](active-directory-dev-glossary.md#client-application) som installeras lokalt på en enhet. Den här inställningen används för OAuth offentliga [interna klienter](active-directory-dev-glossary.md#native-client).
    - Välj ”webbapp / API” för [klientprogram](active-directory-dev-glossary.md#client-application) och [resurs-API-program](active-directory-dev-glossary.md#resource-server) som är installerade på en säker server. Den här inställningen används för OAuth konfidentiell [web klienter](active-directory-dev-glossary.md#web-client) och offentliga [användaren-agent-baserade klienter](active-directory-dev-glossary.md#user-agent-based-client). Samma program kan också visa både klient- och resurs-API.
  - **Inloggnings-URL:** för ”webbapp / API” program, ange den grundläggande Webbadressen för din app. Till exempel `http://localhost:31544` kan vara URL för ett webbprogram som körs på den lokala datorn. Användare använder den här URL: en för att logga in på ett webbprogram för klienten. 
  - **Omdirigerings-URI:** för ”interna” program, anger du den URI som används av Azure AD för att returnera token svar. Ange ett värde som är specifika för ditt program, till exempel `http://MyFirstAADApp`

   ![Registrera ett nytt program – skapa](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Om du vill att specifika exempel för webbprogram och interna program, Kolla in våra [Snabbstart](active-directory-developers-guide.md#get-started).

5. När du är klar klickar du på **skapa**. Azure AD tilldelas ett unikt ID för ditt program och förflyttas till programmets huvudsakliga registreringssidan. Beroende på om ditt program är en webbplats eller det ursprungliga programmet, är olika alternativ för att lägga till ytterligare funktioner i programmet. Finns i nästa avsnitt för en översikt av medgivande och information om hur du aktiverar ytterligare konfiguration för funktioner i registreringen program (autentiseringsuppgifter, behörigheter, aktivera-inloggning för användare från andra klienter.)

  > [!NOTE]
  > Som standard en nyligen registrerad webbprogrammet har konfigurerats för att tillåta **endast** användare från samma klient att logga in på ditt program.
  > 
  > 

## <a name="updating-an-application"></a>Uppdatering av ett program
När programmet har registrerats med Azure AD, kan den behöva uppdateras för att ge åtkomst till webb-API: er, göras tillgänglig i andra organisationer och mycket mer. Det här avsnittet beskrivs olika sätt som du kan konfigurera ytterligare ditt program. Vi börjar med en översikt över medgivande framework, som är viktigt att veta när du skapar program som ska användas av andra användare eller program.

### <a name="overview-of-the-consent-framework"></a>Översikt över medgivande framework

Azure AD medgivande framework gör det enkelt att utveckla flera innehavare webb- och interna klientprogram. Dessa program Tillåt inloggning av användarkonton från en Azure AD-klient, som skiljer sig från den där programmet har registrerats. De kan också behöva åtkomst till webb-API: er, till exempel Microsoft Graph API (för att få åtkomst till Azure Active Directory, Intune och tjänster i Office 365) och andra Microsoft-tjänster API: er, utöver egna web API: er. Ramen är baserad på en användare eller administratör ge medgivande till ett program som begär registreras i katalogen, vilket kan innebära att få åtkomst till katalogdata.

Till exempel om ett webbprogram för klienten behöver läsa kalenderinformationen om användaren från Office 365, krävs som för att samtycka till klientprogrammet först. Efter tillstånd ges kommer klientprogrammet att kunna anropa Microsoft Graph API för användarens räkning och använda kalenderinformationen efter behov. Den [Microsoft Graph API](https://graph.microsoft.io) ger åtkomst till data i Office 365 (till exempel kalendrar och meddelanden från Exchange, platser och listor från SharePoint, dokument från OneDrive, uppgifter från Planner arbetsböcker från Excel, OneNote-anteckningsböcker osv), samt användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster. 

Medgivande framework bygger på OAuth 2.0 och dess olika flöden som koden bevilja och klienten autentiseringsuppgifter bevilja med hjälp av offentliga eller konfidentiell klienter. Med hjälp av OAuth 2.0 går Azure AD det att skapa många olika typer av program, t.ex på en telefon, surfplatta, server eller ett webbprogram och få åtkomst till resurserna som krävs.

Mer information om hur du använder medgivande framework med OAuth2.0 auktorisering ger finns [auktorisera åtkomst till webbprogram med hjälp av OAuth 2.0 och Azure AD](active-directory-protocols-oauth-code.md) och [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md). Information om hur du får en auktoriserad åtkomst till Office 365 via Microsoft Graph finns [App autentisering med Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exempel på medgivande-upplevelse

Följande steg visar hur samtycke uppleva fungerar för både programutvecklaren och användare.

1. Anta att du har ett webbprogram för klienten som behöver begära särskild behörighet för att komma åt en resurs/API. Du lär dig hur du gör den här konfigurationen i nästa avsnitt, men i praktiken den Azure-portalen som används för att deklarera behörighetsbegäranden konfiguration för närvarande. Precis som andra konfigurationsinställningar blir de en del av programmets Azure AD-registrering:
   
  ![Behörigheter för andra program](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Överväg att ditt program behörighet har uppdaterats programmet körs och en användare kommer att använda den för första gången. Först programmet behöver skaffa en Auktoriseringskoden från Azure AD `/authorize` slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny tillgång och uppdatera token.

3. Om användaren inte är autentiserad, Azure AD'S `/authorize` endpoint frågar efter inloggning.
   
  ![Användaren eller administratören logga in på Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. När användaren har loggat in avgör Azure AD om användaren behöver visas en sida medgivande. Detta baseras på om användaren (eller organisationens administratör) har redan beviljats programmet samtycke. Om ditt medgivande inte redan har fått, Azure AD efterfrågar medgivande och visar de behörigheter som krävs som behövs för att fungera. En uppsättning behörigheter som visas i dialogrutan medgivande överensstämmer med de som valts i den delegerade behörigheter i Azure-portalen.
   
  ![Medgivande användarupplevelse](./media/active-directory-integrating-applications/consent.png)

5. När användaren ger ditt medgivande, returneras ett auktoriseringskod till ditt program Inlöst att förvärva en åtkomst-token och uppdatera token. Mer information om det här flödet finns i [webbprogrammet för API-avsnittet i Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Som administratör kan samtycker du också till att ett program delegerade behörigheter för alla användare i din klient. Administrativa medgivande förhindrar medgivande dialogrutan visas för varje användare i klienten och kan göras i den [Azure-portalen](https://portal.azure.com) av användare med administratörsrollen. Från den **inställningar** för programmet, klickar du på **nödvändiga behörigheter** och klicka på den **bevilja med** knappen. 

  ![Bevilja behörigheter för explicit admin medgivande](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Bevilja uttryckligt medgivande med hjälp av den **bevilja med** knappen krävs för närvarande för enstaka sida program (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram att komma åt web API: er
För ett webbprogram konfidentiell klientprogram för att kunna delta i ett tillstånd bevilja flöde som kräver autentisering (och få en åtkomsttoken), måste den upprätta säkra referenser. Standardmetoden för autentisering som stöds av Azure portal är klient-ID + hemlig nyckel. Det här avsnittet beskriver konfigurationssteg som krävs för att ange den hemliga nyckeln med autentiseringsuppgifter för din klient.

Dessutom innan en klient kan komma åt ett webb-API som exponeras av en resursprogram (till exempel Microsoft Graph API), medgivande framework garanterar klienten hämtar behörighet beviljande krävs, baserat på de behörigheter som begärdes. Som standard kan alla program välja behörigheter från ”Windows Azure Active Directory” (Graph API) och ”Windows Azure Service Management API”. Den [Graph API ”logga in och Läs användarprofil” behörighet](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) också är markerad som standard. Om klienten registreras i en klient som har konton som prenumererar på Office 365, kan web API: er och behörigheter för SharePoint och Exchange Online välja. Du kan välja mellan [två typer av behörigheter](active-directory-dev-glossary.md#permissions) för varje önskad webb-API:

- Behörigheter för program: Ditt klientprogram behöver åtkomst till webb-API som själva (ingen användarkontext). Den här typen av behörighet kräver godkännande av administratören och är inte heller tillgänglig för interna program.

- Delegerade behörigheter: Ditt klientprogram behöver åtkomst till webb-API som den inloggade användaren, men med åtkomst begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare om behörigheten kräver godkännande av administratören. 

  > [!NOTE]
  > Lägga till en delegerad behörighet till ett program ger automatiskt inte medgivande till användare i klienten. Användarna måste fortfarande manuellt medgivande för tillagda delegerade behörigheter vid körning, om inte administratören klickar på den **bevilja med** knappen från den **nödvändiga behörigheter** avsnitt i den sidan program i Azure-portalen. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Att lägga till autentiseringsuppgifter eller behörighet att komma åt web API: er
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/active-directory-integrating-applications/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet. Lägg till en hemlig nyckel för ditt webbprogram autentiseringsuppgifter:
  - Klicka på den **nycklar** avsnitt på den **inställningar** sidan. 
  - Lägg till en beskrivning för din nyckel.
  - Välj en eller två års varaktighet.
  - Klicka på **Spara**. Kolumnen längst till höger innehåller värdet för nyckeln, när du sparar ändringar i konfigurationen. **Se till att kopiera nyckeln** för användning i din programkod för klienten, eftersom den inte är tillgänglig när du lämnar den här sidan.

  ![Uppdatera registreringen för ett program - nycklar](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Att lägga till behörigheter för åtkomst till resursen API: er från din klient
  - Klicka på den **nödvändiga behörigheter** avsnitt på den **inställningar** sidan. 
  - Klicka på knappen **Lägg till**.
  - Klicka på **väljer en API** att välja vilken typ av resurser som du vill välja från.
  - Bläddra igenom listan över tillgängliga API: er eller Använd sökrutan Om du vill välja från tillgänglig resurs för program i din katalog som exponerar ett webb-API. Klicka på den resurs som du är intresserad av och klickar sedan **Välj**.
  - Du förflyttas till den **Aktivera åtkomst** sidan. Välj behörigheter för program och/eller delegerade behörigheter som programmet behöver få åtkomst till API: et.
   
  ![Uppdatera registreringen för ett program - behörigheter api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Uppdatera registreringen för ett program - behörigheter behörighet](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. När du är klar klickar du på den **Välj** knappen på **Aktivera åtkomst** sidan sedan **klar** knappen på den **lägga till API-åtkomst** sidan. Du kommer tillbaka till den **nödvändiga behörigheter** sida, där den nya resursen har lagts till i listan över API: er.

  > [!NOTE]
  > Klicka på den **klar** knapp anger också automatiskt behörigheter för ditt program i katalogen baserat på behörigheterna till andra program som du har konfigurerat. Du kan visa dessa behörigheter för program genom att titta på programmet **inställningar** sidan.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera en resursprogram exponera web API: er

Du kan utveckla ett webb-API och göra den tillgänglig för program genom att exponera åtkomst [scope](active-directory-dev-glossary.md#scopes) och [roller](active-directory-dev-glossary.md#roles). En korrekt konfigurerad web API görs tillgänglig precis som i andra Microsoft webb-API: er, inklusive Graph-API och Office 365-API: er. Åtkomstscope och roller är tillgängliga via din [programmets manifest](active-directory-dev-glossary.md#application-manifest), vilket är en JSON-fil som representerar identitet programkonfigurationen. 

Följande avsnitt visar hur du exponera åtkomstscope, genom att ändra resursen programmanifestet.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Lägga till scope till resursprogram

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.

3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/active-directory-integrating-applications/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet. Växla till den **redigera manifestet** sidan genom att klicka på **Manifest** från programmets registreringssidan. En webbaserad manifestet editor öppnas, så att du kan **redigera** manifestet i portalen. Alternativt kan du klicka på **hämta** och redigera lokalt och sedan använda **överför** på nytt till programmet.

5. I det här exemplet ska vi visa ett nytt scope som kallas `Employees.Read.All` på vår resurs/API, genom att lägga till följande JSON-elementet så att den `oauth2Permissions` samling. Den befintliga `user_impersonation` scope som standard under registreringen. `user_impersonation` tillåter ett klientprogram att begära tillstånd att få åtkomst till resursen under identiteten för den inloggade användaren. Se till att lägga till Kommat efter den befintliga `user_impersonation` omfång element och ändra egenskapsvärden så att de passar din resursbehov. 

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
  > ”Id”-värdet måste skapas med ett verktyg för generering av GUID, till exempel [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) eller programmässigt. Den representerar en unik identifierare för omfånget som exponeras av webb-API. När en klient är korrekt konfigurerad med behörighet att komma åt ditt webb-API, utfärdas en åtkomsttoken som OAuth2.0 av Azure AD. När klientanrop webb-API: Anger den åtkomst-token som har omfång (scp) anspråk som de behörigheter som efterfrågas i registreringen programmet.
  >
  > Du kan visa ytterligare scope senare, vid behov. Överväg att ditt webb-API kan det hända att flera scope som är associerade med en mängd olika funktioner. Resursen kan styra åtkomsten till webb-API vid körning, genom att utvärdera omfattningen (`scp`) anspråken i mottagna OAuth 2.0-åtkomsttoken.
  > 

6. När du är klar klickar du på **spara**. Webb-API har nu konfigurerats för användning av andra program i din katalog. 

  ![Uppdatera registreringen för ett program](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Kontrollera web API exponeras för andra program i din klient
1. Gå tillbaka till din Azure AD-klient klickar du på **App registreringar** igen och sedan hitta/Klicka klientprogrammet som du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/active-directory-integrating-applications/update-app-registration.png)

2. Upprepa steg 5 som du gjorde i [konfigurera ett klientprogram att komma åt web API: er](#configure-a-client-application-to-access-web-apis). När du kommer till den **väljer en API** steg, söka efter resurs genom att ange dess programnamn i sökfältet och klicka på **Välj**. 

3. På den **Aktivera åtkomst** sidan som du bör se det nya omfånget för klientbegäranden för behörighet.

  ![Nya behörigheter visas](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Mer information om programmanifestet

Applikationsmanifestet faktiskt fungerar som en mekanism för att uppdatera entiteten program som definierar alla attribut för ett Azure AD-program identitet konfigurationen, inklusive API åtkomstscope som vi diskuterade. Mer information om entiteten programmet och dess schema finns i [Graph API entitet dokumentationen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Artikeln innehåller utförlig referensinformation om programmet entitetsmedlemmar används för att ange behörighet för din API, inklusive:  

- AppRoles medlemmen, som är en samling av [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entiteter som används för att definiera [programbehörigheter](active-directory-dev-glossary.md#permissions) för ett webb-API. 
- Oauth2Permissions medlemmen, som är en samling av [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entiteter som används för att definiera [delegerad behörighet](active-directory-dev-glossary.md#permissions) för ett webb-API.

Mer information om application manifest begrepp i allmänhet, se [förstå Azure Active Directory-programmanifestet](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Åtkomst till Azure AD-diagram och Office 365 via Microsoft Graph API: er  

Du kan registrera ditt klientprogram att komma åt API: er som exponeras av resurser i Microsofts som nämnts tidigare, förutom exponera/åtkomst till API: er för egna program. Microsoft Graph-API som anges som ”Microsoft Graph” i portalens resurs-API-listan är tillgängliga för alla program som har registrerats med Azure AD. Om du registrerar ditt klientprogram i en klient med konton som har registrerat dig för Office 365-prenumeration kan du också öppna scope som exponeras av olika Office 365-resurser.

En fullständig beskrivning på scope som exponeras av Microsoft Graph API finns i [Microsoft Graph behörigheter referens](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) artikel.

> [!NOTE]
> På grund av en aktuell begränsning native client program kan endast anropa i Azure AD Graph API om de använder behörigheten ”åtkomst till din organisations katalog”. Den här begränsningen gäller inte för webbprogram.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurera program för flera innehavare

När du registrerar ett program i Azure AD kan du vill att programmet ska endast användas av användare i din organisation. Alternativt kan du vill att programmet ska vara tillgängliga för användare i externa organisationer. Dessa två programtyper kallas för en klient och flera innehavare. Det här avsnittet beskrivs hur du ändrar konfigurationen för en enskild klient program så att det blir ett program med flera innehavare.

Det är viktigt att Observera skillnaderna mellan en enskild klient och flera innehavare program:  

- En enskild klient program är avsedd att användas i en organisation. Det är vanligtvis ett line-of-business (LoB)-program som skrivits av en enterprise-utvecklare. En enskild klient programmet kan bara användas av användare med konton i samma klientorganisation som programregistrering. Därför kan måste den bara tillhandahållas i en katalog.
- Ett program med flera innehavare är avsedd att användas i många organisationer. Kallas för ett webbprogram för programvara som en tjänst (SaaS) och skrivs det vanligtvis av en oberoende programvaruleverantör (ISV). Program med flera klienter måste tillhandahållas i varje klient där användare behöver åtkomst. Användaren eller administratören godkännande krävs för klienter än den där programmet har registrerats för att kunna registrera dem. Observera att inbyggda klientprogram flera innehavare som standard när de installeras på enheten för resursägare. Se föregående [översikt över medgivande framework](#overview-of-the-consent-framework) information om ditt medgivande ramen.

Gör ett program med flera innehavare kräver båda programändringar registrering, samt ändras till webbprogrammet sig själv. Följande avsnitt beskriver både.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Ändra programregistrering stöd för flera innehavare

Om du skriver ett program som du vill göra tillgängliga för dina kunder eller partners utanför din organisation, måste du uppdatera programmets definition i Azure-portalen.

> [!IMPORTANT]
> Azure AD kräver App-ID-URI för program med flera klienter ska vara globalt unika. URI för App-ID är ett sätt som ett program har identifierats i protokollmeddelanden. Den är tillräcklig för URI: N App-ID är unikt i den klienten för en enskild klient-program. För ett program med flera innehavare, måste den vara globalt unika så att Azure AD kan hitta programmet på alla klienter. Globala unika tillämpas genom att kräva att App-ID-URI har ett värdnamn som matchar en verifierad domän till Azure AD-klient. Till exempel om namnet på din klient är contoso.onmicrosoft.com en giltig URI för App-ID är https://contoso.onmicrosoft.com/myapp. Om din klient har en verifierad domän contoso.com och sedan en giltig URI för App-ID kan även vara https://contoso.com/myapp. Om App-ID-URI inte följer detta mönster, ange ett program som misslyckas av flera innehavare.
> 

Så här ger externa användare möjlighet att komma åt ditt program: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet.
4. Från den **inställningar** klickar du på **egenskaper** och ändra den **flera innehavare** växla till **Ja**.

När du har gjort ändringarna, kan användare och administratörer i andra organisationer ge användarna möjlighet att logga in på ditt program så att ditt program för att komma åt resurser som skyddas av klienten.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Ändra program med stöd för flera innehavare

Stöd för flera innehavare program använder kraftigt Azure AD medgivande ramen. Medgivande är den mekanism som gör att en användare från en annan innehavare för att ge programmet åtkomst till resurser som skyddas av användarens klient. Det här upplevelsen kallas ”tillstånd”.

Webbprogrammet kan också erbjuda:

- Möjligheten för administratörer att ”logga in mitt företag”. Denna upplevelse som kallas ”admin samtycke”, kan en administratör möjlighet att ge medgivande för räkning *alla användare* i organisationen. En användare som autentiseras med ett konto som tillhör rollen Global administratör kan ge admin samtycke. andra får ett felmeddelande.

- En registrering upplevelse för användare. Det är normalt att användaren får en ”registrering” knapp som omdirigerar webbläsaren till Azure AD-OAuth2.0 `/authorize` slutpunkt eller OpenID Connect `/userinfo` slutpunkt. Dessa slutpunkter gör det möjligt för programmet för att hämta information om den nya användaren genom att kontrollera id_token. Efter registreringen fasen visas med en fråga om medgivande, liknar det som visas på den [översikt över medgivande framework](#overview-of-the-consent-framework) avsnitt.

Mer information om programändringar som krävs för att stödja finns flera innehavare åtkomst och logga-i/sign-upp upplevelser:

- [Så här loggar du in valfri Azure Active Directory (AD)-användare med programmönstret för flera klienter](active-directory-devhowto-multi-tenant-overview.md)
- Listan över [kodexempel för flera innehavare](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Snabbstart: Lägga till företagsanpassning till din inloggningssidan i Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Aktivera OAuth bevilja 2.0 implicit för den enda sidan program

Enskild sida programmets (SPA) är vanligtvis strukturerade med JavaScript-frekventa klientdelen som körs i webbläsaren, som anropar programmets webb-API serverdel för att utföra sitt affärslogik. För SPA finns i Azure AD, använder du OAuth 2.0 Implicit bevilja för att autentisera användaren med Azure AD och få en token som du kan använda för att skydda anrop från programmets JavaScript-klient till dess backend-webb-API. 

När användaren har beviljat medgivande, användas detta samma autentiseringsprotokoll för att hämta token för att skydda anrop mellan klienten och andra webb-API-resurser som konfigurerats för programmet. Mer information om implicit authorization grant och hjälpa dig att avgöra om det är bäst för ditt program-scenario genom att se [förstå OAuth2 implicit bevilja flödet i Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Som standard inaktiveras OAuth 2.0 implicit bevilja för program. Du kan aktivera OAuth 2.0 Implicit Grant för ditt program genom att ange den `oauth2AllowImplicitFlow` värde i dess [programmanifestet](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Så här aktiverar du OAuth 2.0 implicit bevilja

> [!NOTE]
> För information om hur du redigerar programmanifestet, bör du först granska i föregående avsnitt, [konfigurera resursprogram att exponera webb-API: er](#configuring-a-resource-application-to-expose-web-apis).
>

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet.
4. Växla till den **redigera manifestet** sidan genom att klicka på **Manifest** från programmets registreringssidan. En webbaserad manifestet editor öppnas, så att du kan **redigera** manifestet i portalen. Leta upp och ange värdet ”oauth2AllowImplicitFlow” till ”true”. Som standard är den inställd på ”false”.
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Spara det uppdaterade manifestet. När du sparat har ditt webb-API konfigurerats för att använda OAuth 2.0 Implicit bevilja för att autentisera användare.

## <a name="removing-an-application"></a>Tar bort ett program
Det här avsnittet beskrivs hur du tar bort ett program registrering från Azure AD-klienten.

### <a name="removing-an-application-authored-by-your-organization"></a>Tar bort ett program som skapats av din organisation
Program som har registrerats i din organisation visas under den ”Mina appar” filtret på sidan för din klient huvudsakliga ”App registreringar”. Dessa program är sådana du registrerats manuellt via Azure portal eller programmässigt via PowerShell eller Graph API. Mer specifikt representeras de av både ett program och tjänstens huvudnamn objekt i din klient. Mer information finns i [program och tjänstens huvudnamn objekt](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Ta bort en enskild klient programmet från katalogen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet.
4. Klicka på programmets huvudsakliga registreringssidan **ta bort**.
5. Klicka på **Ja** i bekräftelsemeddelandet.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Ta bort ett program med flera innehavare från sin hemkatalog
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto får du tillgång till fler än en, klicka på ditt konto i det övre högra hörnet och ange sessionen portal till önskade Azure AD-klient.
3. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** tjänsten klickar du på **App registreringar**, sedan hitta/klickar du på det program du vill konfigurera. Du kommer till programmets huvudsakliga registreringssidan, vilket öppnar den **inställningar** sidan för programmet.
4. Från den **inställningar** väljer **egenskaper** och ändra den **flera innehavare** växla till **nr**, för att först ändra ditt program till stöd för en innehavare, klicka på **spara**. Programmets service principal objekten finns kvar i innehavare av alla organisationer som redan har samtyckt till den.
5. Klicka på den **ta bort** programmets huvudsakliga registreringssidan-knappen.
6. Klicka på **Ja** i bekräftelsemeddelandet.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Tar bort ett program för flera innehavare godkända av en annan organisation
En delmängd av de program som visas under ”alla appar” filter (förutom i ”Mina appar” registreringar) på din klient ”App registreringar” huvudsidan är program med flera klienter. Tekniskt sett dessa program med flera klienter från en annan klient och registrerades i din klient under processen medgivande. Mer specifikt representeras de av bara en service principal objektet i din klient med ingen motsvarande programobjektet. Mer information om skillnaderna mellan program och tjänstens huvudnamn objekt finns [program och tjänstens huvudnamn objekt i Azure AD](active-directory-application-objects.md).

För att ta bort ett program med flera innehavare åtkomsten till din katalog (när du har beviljat medgivande), företagets administratör måste ta bort dess huvudnamn för tjänsten. Administratören måste ha åtkomst till global administratör, och kan ta bort den via Azure portal eller använda den [Azure AD PowerShell-Cmdlets](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).
- Finns det [företagsanpassning riktlinjer för integrerade appar](active-directory-branding-guidelines.md) tips om visual vägledning för din app.
- Läs mer om förhållandet mellan ett program program och tjänstens huvudnamn objekt [program och tjänstens huvudnamn objekt](active-directory-application-objects.md).
- Läs mer om rollen app manifestet spelar i [förstå Azure Active Directory-programmanifestet](active-directory-application-manifest.md)
- Finns det [Azure AD-utvecklare ordlista](active-directory-dev-glossary.md) definitioner av vissa utvecklare grundbegrepp Azure AD.
- Besök den [Active Directory-guide för utvecklare](active-directory-developers-guide.md) en översikt över alla utvecklare-relaterat innehåll.

