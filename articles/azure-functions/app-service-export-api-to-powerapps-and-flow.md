---
title: Exportera en Azure-värdbaserade API till PowerApps och Microsoft Flow | Microsoft Docs
description: Översikt över hur du kan exponera ett API som körs i App Service till PowerApps och Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: c9ff4332a10247787e3b11c5508d0d94a1f1c8ba
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410473"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportera en Azure-värdbaserade API till PowerApps och Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) är en tjänst för att skapa och använda anpassade företagsappar som ansluter till dina data och fungerar på olika plattformar. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) gör det enkelt att automatisera arbetsflöden och affärsprocesser mellan dina favoritappar och tjänster. Både PowerApps och Microsoft Flow levereras med en mängd inbyggda anslutningar till datakällor som Office 365, Dynamics 365, Salesforce med mera. I vissa fall vill appar och flöden builders också ansluta till datakällor och API: er som skapats av deras organisation.

På samma sätt kan kan utvecklare som vill använda sina API: er bredare inom en organisation göra sina API: er tillgängliga för appar och flöden builders. Det här avsnittet visar hur du exporterar ett API som skapats med [Azure Functions](../azure-functions/functions-overview.md) eller [Azure App Service](../app-service/app-service-web-overview.md). Exporterade API blir en *anslutningsapp*, som används i PowerApps och Microsoft Flow precis som en inbyggd kontakt.

## <a name="create-and-export-an-api-definition"></a>Skapa och exportera en API-definition
Innan du exporterar ett API, måste du beskriva API: et med hjälp av en OpenAPI-definition (kallades en [Swagger](https://swagger.io/) fil). Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade. PowerApps och Microsoft Flow kan skapa anpassade anslutningsappar för alla OpenAPI 2.0-definition. Azure Functions och Azure App Service har inbyggt stöd för att skapa, som är värd för och hantera OpenAPI-definitioner. Mer information finns i [vara värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Du kan också skapa anpassade anslutningsappar i PowerApps och Microsoft Flow-Användargränssnittet utan att använda en OpenAPI-definition. Mer information finns i [registrera och använda en anpassad anslutningsapp (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) och [registrera och använda en anpassad anslutning (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Följ dessa steg om du vill exportera API-definition:

1. I den [Azure-portalen](https://portal.azure.com), navigera till din Azure-Functions eller en annan App Service-programmet.

    Om du använder Azure Functions, markera din funktionsapp, Välj **plattformsfunktioner**, och sedan **API-definition**.

    ![Azure Functions-API-definition](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Om du använder Azure App Service, Välj **API-definition** från inställningslistan över.

    ![App Service-API-definition](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Den **exportera till PowerApps + Microsoft Flow** knappen ska vara tillgängliga (om inte, måste du först skapa en OpenAPI-definition). Klicka här om du vill börja exporten.

    ![Exportera till PowerApps + Microsoft Flow-knapp](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Välj den **exportläge**:

    **Express** kan du skapa den anpassade anslutningsappen från Azure Portal. Det kräver att du är inloggad i PowerApps eller Microsoft Flow och har behörighet att skapa kopplingar i målmiljön. Detta är den rekommenderade metoden om dessa två krav kan uppfyllas. Om du använder det här läget, följer du de [använda uttryckliga export](#express) anvisningarna nedan.

    **Manuell** kan du exportera den API-definitionen som du sedan importerar med hjälp av PowerApps eller Microsoft Flow-portaler. Det här är den rekommenderade metoden om Azure-användare och användare med behörighet att skapa kopplingar är olika personer eller om anslutningen måste skapas i en annan Azure-klient. Om du använder det här läget, följer du de [Använd manuell export](#manual) anvisningarna nedan.

    ![Exportläge](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Den anpassade anslutningsappen använder en *kopia* av API-definition, så att PowerApps och Microsoft Flow inte omedelbart vet om du gör ändringar i programmet och dess API-definition. Om du gör ändringar, upprepa stegen för export för den nya versionen.

<a name="express"></a>
## <a name="use-express-export"></a>Använd uttryckliga export

Att slutföra exporten i **Express** läge, Följ dessa steg:

1. Kontrollera att du har loggat in till PowerApps eller Microsoft Flow-klient som du vill exportera. 

2. Använd inställningarna som anges i tabellen.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Miljö**|Välj den miljö som den anpassade anslutningsappen ska sparas. Mer information finns i [översikt över miljöer](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Namn på anpassat API**|Ange ett namn, vilka PowerApps och Microsoft Flow kommer finns i listan connector.|
    |**Förbered säkerhetskonfiguration**|Om det behövs, tillhandahålla säkerhet konfigurationsinformation som behövs för att ge användare tillgång till ditt API. Det här exemplet visar en API-nyckel. Mer information finns i [ange autentiseringstyp](#auth) nedan.|
 
    ![Express exportera till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klicka på **OK**. Den anpassade anslutningsappen är nu skapats och läggs till i den miljö som du har angett.

Exempel på hur du använder **Express** läge med Azure Functions finns i [anropa en funktion från PowerApps](functions-powerapps-scenario.md) och [anropa en funktion från Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Använd manuell export

Att slutföra exporten i **manuell** läge, Följ dessa steg:

1. Klicka på **hämta** och spara filen, eller klicka på kopieringsknappen och spara URL: en. Du använder den hämtade filen eller URL: en under importen.
 
    ![Manuell export till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Om API-definitionen innehåller några säkerhetsdefinitioner, kallas dessa i steg #2. Under importen, PowerApps och Microsoft Flow identifierar dessa och frågar efter säkerhetsinformation. Samla in de autentiseringsuppgifter som rör varje definition för användning i nästa avsnitt. Mer information finns i [ange autentiseringstyp](#auth) nedan.

    ![Säkerhet för manuell export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Det här exemplet visar nyckelsäkerhet API-definition som ingick i OpenAPI-definitionen.

Nu när du har exporterat API-definition kan importera du den för att skapa en anpassad anslutningsapp i PowerApps och Microsoft Flow. Anpassade anslutningsappar som delas mellan de två tjänsterna, så du behöver bara importera definitionen en gång.

Följ dessa steg om du vill importera API-definitionen i PowerApps och Microsoft Flow:

1. Gå till [powerapps.com](https://web.powerapps.com) eller [flow.microsoft.com](https://flow.microsoft.com).

2. Klicka på kugghjulsikonen i det övre högra hörnet och klicka sedan på **anpassade anslutningar**.

   ![Kugghjulsikon i tjänst](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klicka på **skapa anpassat anslutningsprogram**, klicka sedan på **importera en OpenAPI-definition**.

   ![Skapa anpassad anslutningsapp](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Ange ett namn för den anpassade anslutningsappen och sedan navigera till den OpenAPI-definition som du exporterade och klicka på **Fortsätt**.

   ![Ladda upp OpenAPI-definition](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. På den **Allmänt** fliken kan du granska informationen som kommer från OpenAPI-definitionen.

5. På den **Security** fliken om du uppmanas att tillhandahålla information om autentisering, anger du värden som är lämpliga för autentiseringstypen. Klicka på **Fortsätt**.

    ![Säkerhetsfliken](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Det här exemplet visar de obligatoriska fälten för API-nyckelautentisering. Fälten skiljer sig åt beroende på vilken autentiseringstyp.

6. På den **definitioner** fliken alla åtgärder som definierats i din OpenAPI-fil är automatiskt ifylld. Om alla nödvändiga åtgärder har definierats kan gå du till nästa steg. Om inte, du kan lägga till och ändra åtgärder här.

    ![Fliken definitioner](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Det här exemplet har en åtgärd, med namnet `CalculateCosts`. Metadata, till exempel **beskrivning**, alla kommer från OpenAPI-filen.

7. Klicka på **skapa koppling** överst på sidan.

Du kan nu ansluta till den anpassade anslutningsappen i PowerApps och Microsoft Flow. Läs mer om hur du skapar anslutningar i PowerApps och Microsoft Flow-portaler, [registrera din anpassade anslutningsapp (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) och [registrera din anpassade anslutning (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Ange autentiseringstyp

PowerApps och Microsoft Flow stöder en uppsättning identitetsleverantörer som tillhandahåller autentisering för anpassade anslutningsappar. Om ditt API kräver autentisering, se till att avbildningen som en _säkerhetsdefinition_ i din OpenAPI-dokument, som i följande exempel:

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
Under exporten kan du ange konfigurationsvärden som gör att PowerApps och Microsoft Flow att autentisera användare.

Det här avsnittet beskriver de autentiseringstyper som stöds i **Express** läge: API-nyckel, Azure Active Directory och allmän OAuth 2.0. PowerApps och Microsoft Flow stöder grundläggande autentisering och OAuth 2.0 för specifika tjänster som Dropbox, Facebook och SalesForce.

### <a name="api-key"></a>API-nyckel
När du använder en API-nyckel, uppmanas användare av din anslutningsapp du ange nyckeln när de skapar en anslutning. Du anger en API-nyckelnamn som hjälper dem att förstå vilken nyckel krävs. I det tidigare exemplet, använder vi namnet `API Key (contact meganb@contoso.com)` så att folk vet var du vill få information om API-nyckeln. Azure Functions är nyckeln vanligtvis en av nycklarna för värden, som täcker flera funktioner i funktionsappen.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
När du använder Azure AD kan du behöver två Azure AD-programregistreringar: en för själva API: T och en för den anpassade anslutningsappen:

- Konfigurera registrering för API: et med den [App Service autentisering/auktorisering](../app-service/configure-authentication-provider-aad.md) funktionen.

- Om du vill konfigurera registrering för anslutningstjänsten, följer du stegen i [att lägga till en Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). Registreringen måste få begränsad åtkomst till ditt API och en svars-URL för `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Mer information finns i exemplen för Azure AD-registrering för [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) och [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). De här exemplen använder Azure Resource Manager som API: et Ersätt ditt API om du följer instruktionerna.

Följande konfigurationsvärden krävs:
- **Klient-ID** -klient-ID för anslutningsappens Azure AD-registrering
- **Klienthemlighet** -klienthemlighet för anslutningsappens Azure AD-registrering
- **Inloggnings-URL** -bas-URL för Azure AD. I Azure, det är vanligtvis `https://login.windows.net`.
- **Klient-ID** -ID för innehavaren som ska användas för inloggningen. Det här ska vara ”vanliga” eller ID för innehavaren som anslutningen har skapats.
- **Resurs-URL** -resurs-URL: en för Azure AD-registrering för ditt API

> [!IMPORTANT]
> Om någon annan importeras API-definition till PowerApps och Microsoft Flow som en del av manuell flödet, måste du ange dem med klient-ID och klienthemlighet för den *registreringen av anslutningsverktyget*, samt resurs-URL för ditt API. Se till att dessa hemligheter hanteras på ett säkert sätt. **Dela inte säkerhetsreferenser för själva API: T.**

### <a name="generic-oauth-20"></a>Generic OAuth 2.0
När du använder generisk OAuth 2.0 kan integrera du med valfri provider som OAuth 2.0. På så sätt kan du arbeta med anpassade providers som inte stöds.

Följande konfigurationsvärden krävs:
- **Klient-ID** -OAuth 2.0-klient-ID
- **Klienthemlighet** -klienthemlighet för OAuth 2.0
- **Webbadress för auktorisering** -Webbadress för OAuth 2.0-auktorisering
- **Token URL** -Webbadress för OAuth 2.0-token
- **Uppdatera Webbadress** -URL: en för OAuth 2.0-uppdatering


