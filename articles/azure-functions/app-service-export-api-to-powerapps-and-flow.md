---
title: Exportera ett Azure-värdbaserat API till PowerApps och Microsoft Flow
description: Översikt över hur du exponerar ett API som finns i App Service för PowerApps och Microsoft Flow
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233080"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportera ett Azure-värdbaserat API till PowerApps och Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) är en tjänst för att skapa och använda anpassade affärsappar som ansluter till dina data och fungerar på olika plattformar. [Microsoft Flow](/learn/modules/get-started-with-flow/index) gör det enkelt att automatisera arbetsflöden och affärsprocesser mellan dina favoritappar och favorittjänster. Både PowerApps och Microsoft Flow levereras med en mängd inbyggda kopplingar till datakällor som Office 365, Dynamics 365, Salesforce med mera. I vissa fall vill app- och flödesbyggare också ansluta till datakällor och API:er som skapats av organisationen.

På samma sätt kan utvecklare som vill exponera sina API:er mer allmänt inom en organisation göra sina API:er tillgängliga för app- och flödesbyggare. Det här avsnittet visar hur du exporterar ett API som skapats med [Azure Functions](../azure-functions/functions-overview.md) eller Azure [App Service](../app-service/overview.md). Det exporterade API:et blir en *anpassad anslutningsapp*, som används i PowerApps och Microsoft Flow precis som en inbyggd anslutningsapp.

> [!IMPORTANT]
> API-definitionsfunktionen som visas i den här artikeln stöds endast för [version 1.x av Azure Functions runtime](functions-versions.md#creating-1x-apps) och App Services-apparna. Version 2.x av funktioner integreras med API Management för att skapa och underhålla OpenAPI-definitioner. Mer information finns i [Skapa en OpenAPI-definition för en funktion med Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Skapa och exportera en API-definition
Innan du exporterar ett API måste du beskriva API:et med en OpenAPI-definition (tidigare känd som en [Swagger-fil).](https://swagger.io/) Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade. PowerApps och Microsoft Flow kan skapa anpassade kopplingar för alla OpenAPI 2.0-definitioner. Azure Functions och Azure App Service har inbyggt stöd för att skapa, vara värd för och hantera OpenAPI-definitioner. Mer information finns i Vara värd för [ett RESTful API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Du kan också skapa anpassade kopplingar i PowerApps- och Microsoft Flow-användargränssnittet, utan att använda en OpenAPI-definition. Mer information finns i [Registrera och använda en anpassad anslutning (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) och Registrera och använda en anpassad [anslutningsapp (Microsoft Flow).](/power-automate/developer/register-custom-api)

Så här exporterar du API-definitionen:

1. Navigera till dina Azure-funktioner eller ett annat App Service-program i [Azure-portalen.](https://portal.azure.com)

    Om du använder Azure Functions väljer du funktionsappen, väljer **Plattformsfunktioner**och sedan **API-definition**.

    ![API-definition av Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Om du använder Azure App Service väljer du **API-definition** i inställningslistan.

    ![API-definition för apptjänst](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Knappen **Exportera till PowerApps + Microsoft Flow** ska vara tillgänglig (om inte, måste du först skapa en OpenAPI-definition). Klicka på den här knappen om du vill påbörja exportprocessen.

    ![Knappen Exportera till PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Välj **exportläge:**

    **Med Express** kan du skapa den anpassade kopplingen inifrån Azure-portalen. Det kräver att du är inloggad i PowerApps eller Microsoft Flow och har behörighet att skapa kopplingar i målmiljön. Detta är den rekommenderade metoden om dessa två krav kan uppfyllas. Om du använder det här läget följer du instruktionerna [för användning av expressexport](#express) nedan.

    **Med manuell kan** du exportera API-definitionen, som du sedan importerar med PowerApps- eller Microsoft Flow-portalerna. Detta är den rekommenderade metoden om Azure-användaren och användaren med behörighet att skapa kopplingar är olika personer eller om kopplingen behöver skapas i en annan Azure-klientorganisation. Om du använder det här läget följer du instruktionerna [för användningshandboksexport](#manual) nedan.

    ![Exportläge](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Den anpassade kopplingen använder en *kopia* av API-definitionen, så PowerApps och Microsoft Flow vet inte omedelbart om du gör ändringar i programmet och dess API-definition. Om du gör ändringar upprepar du exportstegen för den nya versionen.

<a name="express"></a>
## <a name="use-express-export"></a>Använd expressexport

Så här slutför du exporten i **expressläge:**

1. Kontrollera att du är inloggad på den PowerApps- eller Microsoft Flow-klient som du vill exportera till. 

2. Använd de inställningar som anges i tabellen.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Miljö**|Välj den miljö som den anpassade kopplingen ska sparas på. Mer information finns i [Översikt över miljöer](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Anpassat API-namn**|Ange ett namn som PowerApps och Microsoft Flow-byggare ser i deras kopplingslista.|
    |**Förbereda säkerhetskonfiguration**|Om det behövs anger du den information om säkerhetskonfigurationen som behövs för att ge användarna åtkomst till ditt API. I det här exemplet visas en API-nyckel. Mer information finns i [Ange autentiseringstyp](#auth) nedan.|
 
    ![Expressexport till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klicka på **OK**. Den anpassade kopplingen är nu byggd och tillagd i den miljö som du har angett.

<a name="manual"></a>
## <a name="use-manual-export"></a>Använd manuell export

Så här slutför du exporten i **manuellt** läge:

1. Klicka på **Hämta** och spara filen, eller klicka på knappen kopiera och spara webbadressen. Du kommer att använda hämtningsfilen eller URL:en under importen.
 
    ![Manuell export till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Om API-definitionen innehåller några säkerhetsdefinitioner anropas dessa i steg #2. Under importen identifierar PowerApps och Microsoft Flow dessa och frågar efter säkerhetsinformation. Samla in autentiseringsuppgifter som är relaterade till varje definition för användning i nästa avsnitt. Mer information finns i [Ange autentiseringstyp](#auth) nedan.

    ![Säkerhet för manuell export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    I det här exemplet visas den API-nyckelsäkerhetsdefinition som ingick i OpenAPI-definitionen.

Nu när du har exporterat API-definitionen importerar du den för att skapa en anpassad anslutningsapp i PowerApps och Microsoft Flow. Anpassade kopplingar delas mellan de två tjänsterna, så du behöver bara importera definitionen en gång.

Så här importerar du API-definitionen till PowerApps och Microsoft Flow:

1. Gå till [powerapps.com](https://web.powerapps.com) eller [flow.microsoft.com](https://flow.microsoft.com).

2. I det övre högra hörnet klickar du på kugghjulsikonen och sedan på **Anpassade kopplingar**.

   ![Kugghjulsikon i tjänst](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klicka på **Skapa anpassad koppling**och sedan på Importera en **OpenAPI-definition**.

   ![Skapa anpassad anslutningsapp](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Ange ett namn på den anpassade kopplingen och navigera sedan till Den OpenAPI-definition som du exporterade och klicka på **Fortsätt**.

   ![Ladda upp OpenAPI-definition](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. På fliken **Allmänt** granskar du informationen som kommer från OpenAPI-definitionen.

5. Om du uppmanas att ange autentiseringsinformation på fliken **Säkerhet** anger du de värden som är lämpliga för autentiseringstypen. Klicka på **Fortsätt**.

    ![Fliken Säkerhet](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    I det här exemplet visas de fält som krävs för API-nyckelautentisering. Fälten varierar beroende på autentiseringstypen.

6. På fliken **Definitioner fylls** alla åtgärder som definierats i OpenAPI-filen i automatiskt. Om alla nödvändiga åtgärder har definierats kan du gå till nästa steg. Om inte, kan du lägga till och ändra åtgärder här.

    ![Fliken Definitioner](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Det här exemplet har `CalculateCosts`en åtgärd med namnet . Metadata, som **Beskrivning**, kommer alla från OpenAPI-filen.

7. Klicka på **Skapa koppling** högst upp på sidan.

Du kan nu ansluta till den anpassade anslutningen i PowerApps och Microsoft Flow. Mer information om hur du skapar anslutningsappar i PowerApps- och Microsoft Flow-portalerna finns i [Registrera din anpassade anslutning (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) och Registrera din anpassade [anslutningsapp (Microsoft Flow).](/power-automate/get-started-flow-dev#create-a-custom-connector)

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Ange autentiseringstyp

PowerApps och Microsoft Flow stöder en samling identitetsleverantörer som tillhandahåller autentisering för anpassade kopplingar. Om ditt API kräver autentisering kontrollerar du att det fångas in som en _säkerhetsdefinition_ i Ditt OpenAPI-dokument, till exempel följande exempel:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Under exporten anger du konfigurationsvärden som gör att PowerApps och Microsoft Flow kan autentisera användare.

Det här avsnittet omfattar de autentiseringstyper som stöds i **expressläge:** API-nyckel, Azure Active Directory och Generic OAuth 2.0. PowerApps och Microsoft Flow stöder också grundläggande autentisering och OAuth 2.0 för specifika tjänster som Dropbox, Facebook och SalesForce.

### <a name="api-key"></a>API-nyckel
När du använder en API-nyckel uppmanas användarna av kopplingen att ange nyckeln när de skapar en anslutning. Du anger ett API-nyckelnamn för att hjälpa dem att förstå vilken nyckel som behövs. I det tidigare exemplet använder `API Key (contact meganb@contoso.com)` vi namnet så att folk vet var de kan få information om API-nyckeln. För Azure Functions är nyckeln vanligtvis en av värdnycklarna, som täcker flera funktioner i funktionsappen.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
När du använder Azure AD behöver du två Azure AD-programregistreringar: en för själva API:et och en för den anpassade anslutningen:

- Om du vill konfigurera registrering för API:et använder du funktionen [Autentisering/auktorisering av apptjänst.](../app-service/configure-authentication-provider-aad.md)

- Så här konfigurerar du registrering för anslutningsappen genom [att lägga till ett Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Registreringen måste ha delegerad åtkomst till ditt `https://msmanaged-na.consent.azure-apim.net/redirect`API och en svars-URL för . 

Mer information finns i Azure AD-registreringsexempelna för [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) och [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). I de här exemplen används Azure Resource Manager som API. ditt API om du följer stegen.

Följande konfigurationsvärden krävs:
- **Klient-ID** - klient-ID för din anslutning Azure AD-registrering
- **Klienthemlighet** - klienthemligheten för din anslutning Azure AD-registrering
- **Inloggnings-URL** - basadressen för Azure AD. I Azure är `https://login.windows.net`detta vanligtvis .
- **Klient-ID** - ID för klienten som ska användas för inloggningen. Detta bör vara "vanligt" eller ID för klienten där kopplingen skapas.
- **Resurs-URL** – resurs-URL:en för Azure AD-registreringen för ditt API

> [!IMPORTANT]
> Om någon annan importerar API-definitionen till PowerApps och Microsoft Flow som en del av det manuella flödet måste du ange dem med klient-ID och klienthemlighet för *anslutningsregistreringen*samt resurs-URL:en för ditt API. Se till att dessa hemligheter hanteras på ett säkert sätt. **Dela inte säkerhetsautentiseringsuppgifterna för själva API:et.**

### <a name="generic-oauth-20"></a>Allmän OAuth 2.0
När du använder generisk OAuth 2.0 kan du integrera med alla OAuth 2.0-leverantörer. På så sätt kan du arbeta med anpassade leverantörer som inte stöds internt.

Följande konfigurationsvärden krävs:
- **Klient-ID** - klient-ID OAuth 2.0
- **Klienthemlighet** - OAuth 2.0-klienthemligheten
- **URL för auktorisering** – OAuth 2.0-auktoriseringsadressen
- **Token-URL** - OAuth 2.0-token-URL:en
- **Uppdatera URL** - OAuth 2.0 uppdatera URL


