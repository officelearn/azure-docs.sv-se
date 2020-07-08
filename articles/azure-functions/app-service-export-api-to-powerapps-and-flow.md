---
title: Exportera en Azure-värdbaserad API till PowerApps och Microsoft Flow
description: Översikt över hur du exponerar ett API som finns i App Service till PowerApps och Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 8ded1c5fba902adeaeb883894452c00c4ae1d617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83115836"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportera en Azure-värdbaserad API till PowerApps och Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) är en tjänst för att skapa och använda anpassade affärsappar som ansluter till dina data och arbetar på olika plattformar. [Microsoft Flow](/learn/modules/get-started-with-flow/index) är det enkelt att automatisera arbets flöden och affärs processer mellan dina favorit program och-tjänster. Både PowerApps och Microsoft Flow levereras med en mängd inbyggda anslutningar till data källor som Office 365, Dynamics 365, Salesforce med mera. I vissa fall vill app-och flödes byggare också ansluta till data källor och API: er som skapats av organisationen.

På samma sätt kan utvecklare som vill exponera sina API: er mer brett inom en organisation göra sina API: er tillgängliga för app-och flödes byggare. Den här artikeln visar hur du exporterar ett API som skapats med [Azure Functions](../azure-functions/functions-overview.md) eller [Azure App Service](../app-service/overview.md). Det exporterade API: et blir en *anpassad anslutning*som används i PowerApps och Microsoft Flow precis som en inbyggd koppling.

> [!IMPORTANT]
> API-definitionerna som visas i den här artikeln stöds bara för [version 1. x av Azure Functions runtime](functions-versions.md#creating-1x-apps) och app Services appar. Version 2. x av Functions integreras med API Management för att skapa och underhålla OpenAPI-definitioner. Mer information finns i [skapa en openapi-definition för en funktion med Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Skapa och exportera en API-definition
Innan du exporterar ett API måste du beskriva API: et med en OpenAPI-definition (tidigare kallad [Swagger](https://swagger.io/) -fil). Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade. PowerApps och Microsoft Flow kan skapa anpassade anslutningar för valfri OpenAPI 2,0-definition. Azure Functions och Azure App Service har inbyggt stöd för att skapa, vara värd för och hantera OpenAPI-definitioner. Mer information finns i [hantera en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Du kan också bygga anpassade anslutningar i PowerApps och Microsoft Flow användar gränssnitt, utan att använda en OpenAPI-definition. Mer information finns i [Registrera och använda en anpassad anslutning (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) och [Registrera och använda en anpassad anslutning (Microsoft Flow)](/power-automate/developer/register-custom-api).

Exportera API-definitionen genom att följa dessa steg:

1. I [Azure Portal](https://portal.azure.com)navigerar du till din Function-app eller ett app service program.

    På den vänstra menyn, under **API**, väljer du **API-definition**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Azure Functions API-definition":::

2. Knappen **Exportera till PowerApps + Microsoft Flow** bör vara tillgänglig (om inte, måste du först skapa en openapi-definition). Välj den här knappen för att starta export processen.

    ![Exportera till PowerApps + Microsoft Flow knapp](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Välj **export läge**:

    Med **Express** kan du skapa anpassade anslutnings program i Azure Portal. Det kräver att du är inloggad på PowerApps eller Microsoft Flow och har behörighet att skapa anslutningar i mål miljön. Den här metoden rekommenderas om dessa två krav kan uppfyllas. Om du använder det här läget följer du anvisningarna för [Använd Express export](#express) nedan.

    Med **manuell** kan du exportera API-definitionen, som du sedan importerar med hjälp av PowerApps eller Microsoft Flow portaler. Den här metoden rekommenderas om Azure-användaren och användaren med behörighet att skapa kopplingar är olika personer, eller om anslutningen måste skapas i en annan Azure-klient. Om du använder det här läget följer du anvisningarna för att [använda manuella exporter](#manual) nedan.

    ![Export läge](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Det anpassade anslutnings programmet använder en *kopia* av API-definitionen, så PowerApps och Microsoft Flow kommer inte omedelbart att känna till om du gör ändringar i programmet och dess API-definition. Om du gör ändringar upprepar du export stegen för den nya versionen.

<a name="express"></a>
## <a name="use-express-export"></a>Använd Express export

Slutför exporten i **Express** läge genom att följa dessa steg:

1. Kontrol lera att du är inloggad på den PowerApps eller Microsoft Flow klient som du vill exportera till. 

2. Använd de inställningar som anges i tabellen.

    |Inställningen|Beskrivning|
    |--------|------------|
    |**Miljö**|Välj den miljö som den anpassade anslutningen ska sparas i. Mer information finns i [Översikt över miljöer](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Anpassat API-namn**|Ange ett namn som PowerApps och Microsoft Flows byggare ser i sin kopplings lista.|
    |**Förbered säkerhets konfiguration**|Vid behov kan du ange den säkerhets konfigurations information som krävs för att ge användarna åtkomst till ditt API. Det här exemplet visar en API-nyckel. Mer information finns i [Ange autentiseringstyp](#auth) nedan.|
 
    ![Express export till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klicka på **OK**. Det anpassade anslutnings programmet har nu skapats och lagts till i den miljö som du har angett.

<a name="manual"></a>
## <a name="use-manual-export"></a>Använd manuell export

Följ dessa steg om du vill slutföra exporten i **manuellt** läge:

1. Klicka på **Hämta** och spara filen, eller klicka på kopierings knappen och spara URL: en. Du kommer att använda nedladdnings filen eller URL: en under importen.
 
    ![Manuell export till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Om din API-definition innehåller några säkerhets definitioner, anropas dessa definitioner i steg #2. Under importen identifierar PowerApps och Microsoft Flow dessa definitioner och begär säkerhets information. Samla in de autentiseringsuppgifter som är relaterade till varje definition som ska användas i nästa avsnitt. Mer information finns i [Ange autentiseringstyp](#auth) nedan.

    ![Säkerhet för manuell export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    I det här exemplet visas den säkerhets definition för API-nyckeln som ingick i OpenAPI-definitionen.

Nu när du har exporterat API-definitionen importerar du den för att skapa en anpassad anslutning i PowerApps och Microsoft Flow. Anpassade anslutningar delas mellan de två tjänsterna, så du behöver bara importera definitionen en gång.

Följ dessa steg om du vill importera API-definitionen till PowerApps och Microsoft Flow:

1. Gå till [powerapps.com](https://web.powerapps.com) eller [flow.microsoft.com](https://flow.microsoft.com).

2. Klicka på kugg hjuls ikonen i det övre högra hörnet och klicka sedan på **anpassade anslutningar**.

   ![Kugghjulsikon i tjänst](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klicka på **Skapa anpassad anslutning**och klicka sedan på **Importera en openapi-definition**.

   ![Skapa anpassad anslutningsapp](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Ange ett namn för den anpassade anslutningen och navigera sedan till den OpenAPI-definition som du exporterade och klicka på **Fortsätt**.

   ![Ladda upp OpenAPI-definition](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. På fliken **Allmänt** granskar du den information som kommer från openapi-definitionen.

5. Om du uppmanas att ange autentiseringsinformation på fliken **säkerhet** anger du lämpliga värden för autentiseringstypen. Klicka på **Fortsätt**.

    ![Fliken säkerhet](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    I det här exemplet visas obligatoriska fält för autentisering med API-nyckel. Fälten skiljer sig åt beroende på autentiseringstypen.

6. Alla de åtgärder som definierats i din OpenAPI-fil fylls i automatiskt på fliken **definitioner** . Om alla nödvändiga åtgärder har definierats kan du gå till nästa steg. Om inte, kan du lägga till och ändra åtgärder här.

    ![Fliken definitioner](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Det här exemplet har en åtgärd med namnet `CalculateCosts` . Metadata, till exempel **Beskrivning**, kommer från openapi-filen.

7. Klicka på **Skapa koppling** överst på sidan.

Nu kan du ansluta till den anpassade anslutningen i PowerApps och Microsoft Flow. Mer information om hur du skapar anslutningar i PowerApps och Microsoft Flow portaler finns i [Registrera din anpassade anslutning (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) och [Registrera din anpassade anslutning (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Ange autentiseringstyp

PowerApps och Microsoft Flow stöd för en samling identitets leverantörer som tillhandahåller autentisering för anpassade anslutningar. Om ditt API kräver autentisering, se till att det är infångat som en _säkerhets definition_ i openapi-dokumentet, som i följande exempel:

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
Under exporten anger du konfigurations värden som tillåter PowerApps och Microsoft Flow att autentisera användare.

I det här avsnittet beskrivs de autentiseringstyper som stöds i **Express** -läge: API-nyckel, Azure Active Directory och generisk OAuth 2,0. PowerApps och Microsoft Flow stöder även grundläggande autentisering och OAuth 2,0 för vissa tjänster som Dropbox, Facebook och SalesForce.

### <a name="api-key"></a>API-nyckel
När du använder en API-nyckel uppmanas användarna av din anslutning att ange nyckeln när de skapar en anslutning. Du anger ett API-nyckel namn för att hjälpa dem att förstå vilken nyckel som behövs. I det tidigare exemplet använder vi namnet så att `API Key (contact meganb@contoso.com)` människor vet var du ska hämta information om API-nyckeln. För Azure Functions är nyckeln vanligt vis en av värd nycklarna, som täcker flera funktioner i Function-appen.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
När du använder Azure AD behöver du två Azure AD-programregistreringar: en för själva API: et och en för det anpassade anslutnings programmet:

- Om du vill konfigurera registrering för API: et använder du funktionen [App Service autentisering/auktorisering](../app-service/configure-authentication-provider-aad.md) .

- Om du vill konfigurera registreringen för anslutningen följer du stegen i [lägga till ett Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Registreringen måste ha delegerad åtkomst till ditt API och svars-URL för `https://msmanaged-na.consent.azure-apim.net/redirect` . 

Mer information finns i exempel på Azure AD-registrering för [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) och [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). I de här exemplen används Azure Resource Manager som API: et. Ersätt ditt API om du följer anvisningarna.

Följande konfigurations värden måste anges:
- **Klient-ID** – klient-ID för din ANSLUTNINGS Azure AD-registrering
- **Klient hemlighet** – klient hemligheten för din ANSLUTNINGS Azure AD-registrering
- **Inloggnings-URL** – bas-URL: en för Azure AD. I Azure, vanligt vis `https://login.windows.net` .
- **Klient-ID** – ID för den klient som ska användas för inloggningen. Detta ID ska vara "common" eller ID: t för den klient som anslutningen skapas i.
- **Resurs-URL** – resurs-URL för Azure AD-registreringen för ditt API

> [!IMPORTANT]
> Om någon annan ska importera API-definitionen till PowerApps och Microsoft Flow som en del av det manuella flödet måste du ge dem klient-ID och klient hemlighet för *anslutnings registreringen*samt resurs-URL: en för ditt API. Se till att dessa hemligheter hanteras på ett säkert sätt. **Dela inte säkerhets referenser för själva API: et.**

### <a name="generic-oauth-20"></a>Allmän OAuth 2.0
När du använder allmän OAuth 2,0 kan du integrera med valfri OAuth 2,0-Provider. På så sätt kan du arbeta med anpassade providers som inte stöds internt.

Följande konfigurations värden måste anges:
- **Klient-ID** – klient-ID för OAuth 2,0
- **Klient hemlighet** – klient hemligheten för OAuth 2,0
- **URL för auktorisering** – URL för OAuth 2,0-auktorisering
- **Token-URL** – URL för OAuth 2,0-token
- **Uppdatera URL** – uppdaterings-URL: en för OAuth 2,0


