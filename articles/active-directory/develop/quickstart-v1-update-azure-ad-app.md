---
title: Integrera program med Azure Active Directory
description: Lär dig hur du uppdaterar ett program i Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103816"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Snabbstart: Uppdatera ett program i Azure Active Directory

Företagsutvecklare och SaaS-leverantörer (Software as a Service) som har registrerat program med Azure Active Directory (Azure AD) kan behöva konfigurera dina program för att komma åt andra resurser såsom webb-API:er, göra det tillgängligt i andra organisationer och mer.

I den här snabbstarten lär du dig de olika sätt som finns för att konfigurera och uppdatera programmet så att det uppfyller dina och andra organisationers krav eller behov.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång kontrollerar du att du har slutfört följande steg:

* Läs översikt över [Azure AD-ramverket för medgivande](consent-framework.md), vilket är viktigt att förstå när du skapar program som behöver användas av andra användare eller program.
* Ha en Azure AD-klientorganisation som har program som är registrerade till den.
  * Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).
  * Om du inte har appar som är registrerade i din klientorganisation [lär du dig hur du lägger till ett program till Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram för att komma åt webb-API:er

För att ett webbklientprogram eller ett konfidentiellt webbprogram ska kunna delta i ett flöde för auktoriseringsbeviljande (och hämta en åtkomsttoken) måste det kunna etablera säkra autentiseringsuppgifter. Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel. Det här avsnittet beskriver de konfigurationssteg som krävs för att tillhandahålla den hemliga nyckeln med klientens autentiseringsuppgifter.

Innan en klient kan komma åt ett webb-API som görs tillgängligt av ett resursprogram (till exempel Microsoft Graph API) säkerställer ramverket för medgivande att klienten det behörighetsbeviljande som krävs, baserat på de begärda behörigheterna. Som standard kan alla program välja behörigheter från **Azure Active Directory** (Graph API) och den klassiska Azure-distributionsmodellen. [Graph API-behörigheten ”Logga in och läs användarprofil”](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) väljs också som standard. Om klienten registreras i en klientorganisation som har konton som prenumererar på Office 365 är webb-API:er och behörigheter för SharePoint och Exchange Online tillgängliga att väljas. Du kan välja mellan två typer av behörigheter för varje önskat webb-API:

- Programbehörigheter: Klientprogrammet behöver komma åt webb-API:et direkt som sig självt (ingen användarkontext). Den här typen av behörighet kräver administratörens godkännande och är inte heller tillgängligt för interna klientprogram.
- Delegerade behörigheter: Klientprogrammet behöver komma åt webb-API:et som den inloggade användaren men med åtkomst som begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare såvida inte behörigheten kräver administratörens godkännande.

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt medgivande till användare i klientorganisationen. Användarna måste fortfarande manuellt ge medgivande för de tillagda delegerade behörigheterna vid körning såvida inte administratören beviljas medgivande för alla användares räkning.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Lägga till autentiseringsuppgifter för program eller behörighet att komma åt webb-API:er

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto ger dig tillgång till fler än en väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten, väljer **Appregistreringar** och letar upp och väljer det program som du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssida, som innehåller sidan **Inställningar** för programmet. Så här lägger du till en autentiseringsuppgift för webbprogrammet:
   1. Välj avsnittet **Nycklar** på sidan **Inställningar**.
   1. Så här lägger du till ett certifikat:
      - Välj **Ladda upp offentlig nyckel**.
      - Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
   1. Så här lägger du till ett lösenord:
      - Lägg till en beskrivning för nyckeln.
      - Välj en varaktighet.
      - Välj **Spara**. Kolumnen längst till höger innehåller nyckelvärdet när du har sparat konfigurationsändringarna. **Se till att kopiera nyckeln** för användning i din klientprogramkod eftersom den inte är tillgänglig när du har lämnat den här sidan.

5. Så lägger du till behörigheter för att komma åt resurs-API:er från klienten
   1. Välj avsnittet **Nödvändiga behörigheter** på sidan **Inställningar** och välj sedan **Lägg till**.
   1. Välj **Välj ett API** för att välja vilken typ av resurser som du vill välja bland.
   1. Bläddra igenom listan med tillgängliga API:er eller använd sökrutan för att välja bland de tillgängliga resursprogrammen i din katalog som gör ett webb-API tillgängligt. Välj den resurs som du är intresserad av och klicka sedan på **Välj**.
   1. På sidan **Aktivera åtkomst** väljer du programbehörigheter och/eller delegerade behörigheter som programmet behöver vid åtkomst till API:et.
   
   ![Uppdatera registreringen för ett program – behörighets-API](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![Uppdatera registreringen för ett program – behörigheter](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. När du är klar väljer du knappen **Välj** på sidan **Aktivera åtkomst** och sedan knappen **Klar** på sidan **Lägg till API-åtkomst**. Du kommer tillbaka till sidan **Nödvändiga behörigheter**, där den nya resursen har lagts till i listan över API:er.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram för att göra webb API:er tillgängliga

Du kan utveckla ett webb-API och göra det tillgängligt för klientprogram genom att exponera åtkomst till [omfång](developer-glossary.md#scopes) och [roller](developer-glossary.md#roles). Ett korrekt konfigurerat webb-API tillhandahålls precis som de andra webb-API:erna från Microsoft, inklusive Graph API och Office 365-API:erna. Omfång och roller för åtkomst görs tillgängliga via [programmets manifest](developer-glossary.md#application-manifest), vilket är en JSON-fil som representerar programmets identitetskonfiguration.

Följande avsnitt visar hur du gör åtkomstomfång tillgängliga genom att ändra resursprogrammets manifest.

### <a name="add-access-scopes-to-your-resource-application"></a>Lägga till åtkomstomfång i ditt resursprogram

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto ger dig tillgång till fler än en väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3. I det vänstra navigeringsfönstret väljer du **Azure Active Directory > Appregistreringar** och letar upp och väljer det program som du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Du kommer till programmets huvudsakliga registreringssida, vilket öppnar sidan **Inställningar** för programmet. Växla till sidan **Redigera manifest** genom att klicka på **Manifest** från programmets registreringssida. En webbaserad manifestredigerare öppnas så att du kan **Redigera** manifestet i portalen. Alternativt kan du klicka på **Ladda ned** och redigera lokalt och sedan använda **Ladda upp** för att tillämpa det på ditt program på nytt.
5. I det här exemplet gör vi ett nytt omfång som kallas `Employees.Read.All` tillgängligt på resursen/API:et genom att lägga till följande JSON-elementet till så samlingen `oauth2Permissions`. Den omfånget `user_impersonation` tillhandahålls som standard under registreringen. `user_impersonation` gör att ett klientprogram kan begära behörighet att komma åt resursen under identiteten för den inloggade användaren. Se till att lägga till kommatecknet efter det befintliga `user_impersonation`-omfångselementet och ändra egenskapsvärdena så att de passar behoven för din resurs. 

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
   > Värdet `id` måste genereras programmatiskt eller med hjälp av ett GUID-genereringsverktyg såsom [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` representerar en unik identifierare för omfånget såsom det görs tillgängligt av webb-API:et. När en klient är korrekt konfigurerad med behörigheter att komma åt ditt webb-API utfärdas en OAuth2.0-åtkomsttoken till klienten av Azure AD. När klienten anropar webb-API:et presenterar den den åtkomsttoken som har omfångsanspråket (scp) till de behörigheter som begärs i dess programregistrering.
   >
   > Du kan göra ytterligare omfång tillgängliga senare, vid behov. Tänk på att ditt webb-API kanske gör flera omfång tillgängliga som är associerade med en mängd olika funktioner. Din resurs kan styra åtkomsten till webb-API:et vid körning genom att utvärdera omfångsanspråken (`scp`) i den mottagna OAuth 2.0-åtkomsttoken.

6. När det är klart klickar du på **Spara**. Nu är ditt webb-API konfigurerat för användning av andra program i din katalog.

   ![Uppdatera registreringen för ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verifiera att webb-API:et görs tillgängligt för andra program i din klientorganisation

1. Gå tillbaka till din Azure AD-klientorganisation, välj **Appregistreringar** igen och leta upp och välj det klientprogram som du vill konfigurera.

   ![Uppdatera registreringen för ett program](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Upprepa steg 5 som du gjorde i [Konfigurera ett klientprogram för att komma åt webb-API:er](#configure-a-client-application-to-access-web-apis). När du kommer till steget **Välj ett API** söker du efter din resurs genom att ange dess programnamn i sökfältet och klicka på **Välj**.

3. På sidan **Aktivera åtkomst** bör du se det nya omfånget, som är tillgängligt för begäranden om klientbehörighet.

   ![Nya behörigheter visas](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Mer information om programmanifestet

Applikationsmanifestet fungerar som en mekanism för att uppdatera programentiteten, som definierar alla attribut för ett Azure AD-programs identitetskonfiguration, inklusive de API-åtkomstomfång som beskrivits. Mer information om programentiteten och dess schema finns i [dokumentationen om Graph API-programentiteten](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Artikeln innehåller utförlig referensinformation om de programentitetsmedlemmar som används för att ange behörigheter för ditt API, inklusive:  

- Medlemmen appRoles, som är en samling [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type)-entiteter som används för att definiera [programbehörigheter](developer-glossary.md#permissions) för ett webb-API. 
- Medlemmen oauth2Permissions, som är en samling [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type)-entiteter som används för att definiera [delegerade behörigheter](developer-glossary.md#permissions) för ett webb-API.

Mer information om begrepp gällande programmanifest i allmänhet finns i [Azure AD-appmanifest](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Komma åt Azure AD Graph och Office 365 via Microsoft Graph-API:er  

Som vi nämnde tidigare kan du utöver att komma åt och göra API:er tillgängliga för dina egna program även registrera ditt klientprogram för att komma åt API:er som görs tillgängliga av Microsoft-resurser. Microsoft Graph API, som kallas ”Microsoft Graph” i portalens resurs-/API-lista, är tillgängligt för alla program som är registrerade med Azure AD. Om du registrerar ditt klientprogram i en klientorganisation som innehåller konton som har registrerats för en Office 365–prenumeration kan du även komma åt de omfång som görs tillgängliga av de olika Office 365-resurserna.

En fullständig beskrivning av omfång som görs tillgängliga av Microsoft Graph API finns i artikeln [Behörighetsreferens för Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

> [!NOTE]
> På grund av en aktuell begränsning kan interna klientprogram endast anropa Azure AD Graph API om de använder behörigheten ”Access your organization's directory” (Komma åt din organisations katalog). Den här begränsningen gäller inte för webbprogram.

## <a name="configuring-multi-tenant-applications"></a>Konfigurera program för flera klientorganisationer

När du registrerar ett program i Azure AD vill du kanske att ditt program endast kan kommas åt av användare i din organisation. Alternativt vill du kanske att programmet även kan kommas åt av användare i externa organisationer. Dessa två programtyper kallas för program för en enskild klientorganisation respektive flera klientorganisationer. Det här avsnittet beskriver hur du ändrar konfigurationen för ett program för en enskild klientorganisation så att det blir ett program för flera klientorganisationer.

Det är viktigt att observera skillnaderna mellan ett program för en enskild klientorganisation och ett program för flera klientorganisationer:  

- Ett program för en enskild klientorganisation är avsett att användas i en organisation. Det är vanligtvis ett LoB-program (Line of Business, affärsapplikation) som skrivits av en företagsutvecklare. Ett program för en enskild klientorganisation kan bara kommas åt av användare med konton i samma klientorganisation som programregistreringen. Därför behöver det bara etableras i en katalog.
- Ett program för flera klientorganisationer är avsett att användas i många organisationer. Det kallas för ett SaaS-webbprogram (Software as a Service) och skrivs vanligtvis av en oberoende programvaruleverantör (ISV). Program för flera klientorganisationer måste etableras i varje klientorganisation där användarna behöver åtkomst. För andra klientorganisationer än den där programmet har registrerats krävs medgivande från användare eller administratörer för att registrera dem. Observera att interna klientprogram är till för flera klientorganisationer som standard eftersom de är installerade på resursägarens enhet. Se föregående avsnitt Översikt över ramverket för medgivande för att få information om ramverket för medgivande.

Att göra ett program till ett program för flera klientorganisationer kräver både ändringar i programregistreringen och i själva webbprogrammet. I följande avsnitt beskrivs båda dessa.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Ändra programregistrering för att stödja flera klientorganisationer

Om du skriver ett program som du vill göra tillgängliga för dina kunder eller partner utanför din organisation behöver du uppdatera programdefinitionen i Azure-portalen.

> [!IMPORTANT]
> Azure AD kräver att app-ID-URI för program för flera klientorganisationer är globalt unik. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer.
> Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen. Om namnet på din klientorganisation till exempel är contoso.onmicrosoft.com blir en giltig app-ID-URI https://contoso.onmicrosoft.com/myapp. Om din klientorganisation har en verifierad domän som är contoso.com blir en giltig app-ID-URI även https://contoso.com/myapp. Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

Så här ger du externa användare möjlighet att komma åt ditt program:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto ger dig tillgång till fler än en klickar du på ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory**-tjänsten, klickar på **Appregistreringar** och letar upp och klickar på det program som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssida, vilket öppnar sidan **Inställningar** för programmet.
4. På sidan **Inställningar** klickar du på **Egenskaper** och ändrar växeln **Multi-tenanted** (Med flera klientorganisationer) till **Ja**.

När du har gjort ändringarna kan användare och administratörer i andra organisationer ge sina användare möjlighet att logga in på ditt program, vilket gör att programmet kan komma åt resurser som skyddas av deras klientorganisation.

### <a name="changing-the-application-to-support-multi-tenant"></a>Ändra programmet för att stödja flera klientorganisationer

Stöd program för flera klientorganisationer förlitar sig mycket på Azure AD-ramverket för medgivande. Medgivande är den mekanism som tillåter en användare från en annan klientorganisation att ge programmet åtkomst till resurser som skyddas av användarens klientorganisation. Detta kallas för ”användarmedgivande”.

Ditt webbprogram kan även erbjuda:

- Möjligheten för administratörer att ”registrera mitt företag”. Detta kallas för ”administratörsmedgivande” och ger en administratör möjlighet att ge samtycke för *alla användares* räkning i dennes organisation. Det är bara användare som autentiseras med ett konto som tillhör rollen Global administratör som kan administratörsmedgivande; andra får ett felmeddelande.
- En inloggningsfunktion för användare. Det förväntas att användaren får en ”registreringsknapp” som omdirigerar webbläsaren till Azure AD OAuth2.0 `/authorize`-slutpunkten eller en OpenID Connect `/userinfo`-slutpunkt. Dessa slutpunkter gör det möjligt för programmet för att hämta information om den nya användaren genom att granska id_token. Efter registreringsfasen ges användaren en fråga om medgivande som liknar den som visas i avsnittet Översikt över ramverket för medgivande.

Mer information om de programändringar som krävs för att stödja åtkomst för flera klientorganisationer och funktioner för inloggning och utloggning finns i:

- [Så här loggar du in valfri Azure Active Directory (AD)-användare med programmönstret för flera klienter](howto-convert-app-to-be-multi-tenant.md)
- Listan över [kodexempel för flera klientorganisationer](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Snabbstart: Varumärkesanpassa inloggningssidan i Azure AD till ditt företag](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Aktivera implicit OAuth 2.0-beviljande för ensidesprogram

Ensidesprogram (SPA, Single-page applications) är vanligtvis strukturerade med en klientdel som bygger starkt på JavaScript, körs i webbläsaren och anropar programmets webb-API-serverdel för att utföra sin affärslogik. För SPA:er som hanteras i Azure AD använder du implicit OAuth 2.0-beviljande för att autentisera användaren med Azure AD och hämta en token som du kan använda för att skydda anrop från programmets JavaScript-klient till dess serverdels-webb-API.

När användaren har gett sitt medgivande kan det här autentiseringsprotokollet användas till att hämta token för att skydda anrop mellan klienten och andra webb-API-resurser som har konfigurerats för programmet. Om du vill lära dig mer om implicit auktoriseringsbeviljande och få hjälp med att avgöra om det är rätt val för ditt programscenario kan du läsa artikeln om att [förstå flödet för implicit OAuth2-beviljande i Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Som standard är implicit OAuth 2.0-beviljande inaktiverat för program. Du kan aktivera implicit OAuth 2.0-beviljande för ditt program genom att ställa in värdet `oauth2AllowImplicitFlow` i dess [programmanifestet](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Så aktiverar du implicit OAuth 2.0-beviljande

> [!NOTE]
> Det rekommenderas att du först läser informationen om hur du redigerar programmanifestet i föregående avsnitt, [Konfigurera resursprogram för att göra webb API:er tillgängliga](#configuring-a-resource-application-to-expose-web-apis).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om ditt konto ger dig tillgång till fler än en klickar du på ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory**-tjänsten, klickar på **Appregistreringar** och letar upp och klickar på det program som du vill konfigurera. Du kommer till programmets huvudsakliga registreringssida, vilket öppnar sidan **Inställningar** för programmet.
4. Växla till sidan **Redigera manifest** genom att klicka på **Manifest** från programmets registreringssida. En webbaserad manifestredigerare öppnas så att du kan **Redigera** manifestet i portalen. Leta upp och ställ in värdet ”oauth2AllowImplicitFlow” till ”true” (sant). Som standard är det inställt på ”false” (falskt).
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. Sparade det uppdaterade manifestet. När det har sparats är ditt webb-API nu konfigurerat att använda implicit OAuth 2.0-beviljande för att autentisera användare.

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering för appar som använder Azure AD v1.0-slutpunkten:
- [Lägga till ett program till Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Ta bort ett program från Azure AD](quickstart-v1-remove-azure-ad-app.md)

Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
