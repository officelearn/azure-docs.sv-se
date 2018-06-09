---
title: Exportera en Azure-baserad API till PowerApps och Microsoft flödar | Microsoft Docs
description: Översikt över hur du exponera en värdbaserad API i Apptjänst PowerApps och Microsoft Flow
services: app-service
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: ef3fe5002a28c66478a10909a7e9556449cd9712
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234775"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportera en Azure-baserad API till PowerApps och Microsoft-flöde

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) är en tjänst för att skapa och använda anpassade appar som ansluter till dina data och fungera mellan olika plattformar. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) gör det enkelt att automatisera arbetsflöden och affärsprocesser mellan din favorit appar och tjänster. Både PowerApps och Microsoft Flow har ett antal inbyggda anslutningar till datakällor, till exempel Office 365, Dynamics 365, Salesforce och mycket mer. I vissa fall vill app och flödar builders också ansluta till datakällor och API: er som skapats av organisationen.

På liknande sätt kan kan utvecklare som vill använda sina API: er mer brett inom en organisation få sina API: er app och flödar builders. Det här avsnittet beskrivs hur du exporterar en API som byggts med [Azure Functions](../azure-functions/functions-overview.md) eller [Azure App Service](../app-service/app-service-web-overview.md). Exporterade API blir en *anpassad koppling*, som används i PowerApps och Microsoft Flow precis som en inbyggd kontakt.

## <a name="create-and-export-an-api-definition"></a>Skapa och exportera en API-definition
Innan du exporterar ett API måste du beskriva API: et med ett OpenAPI definition (tidigare känt som en [Swagger](http://swagger.io/) filen). Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade. PowerApps och Microsoft Flow kan skapa egna kopplingar för alla OpenAPI 2.0-definition. Azure Functions och Azure Apptjänst har inbyggt stöd för att skapa, värd och hantera OpenAPI definitioner. Mer information finns i [värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Du kan också skapa anpassade kopplingar i PowerApps och flöda Användargränssnittet i Microsoft utan att använda en OpenAPI definition. Mer information finns i [registrera och använda en anpassad koppling (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) och [registrera och använda en anpassad koppling (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Följ dessa steg om du vill exportera API-definition:

1. I den [Azure-portalen](https://portal.azure.com), navigera till dina Azure-funktioner eller ett annat App-tjänstprogram.

    Om du använder Azure Functions, markera funktionen appen, Välj **plattformsfunktioner**, och sedan **API-definition**.

    ![Azure Functions API-definition](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Om du använder Azure App Service, Välj **API-definition** från inställningslistan över.

    ![Apptjänst API-definition](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Den **exportera till PowerApps + Microsoft Flow** knappen ska vara tillgänglig (om inte, måste du först skapa en definition av OpenAPI). Klicka här om du vill påbörja exporten.

    ![Exportera till PowerApps + Microsoft Flow knappen](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Välj den **exportera läge**:

    **Express** kan du skapa den anpassade-kopplingen från Azure-portalen. Det kräver att du är inloggad på PowerApps eller Microsoft Flow och har behörighet att skapa kopplingar i målmiljön. Detta är den rekommenderade metoden om dessa två krav kan uppfyllas. Om du använder det här läget, följer du de [använda snabb export](#express) anvisningarna nedan.

    **Manuell** kan du exportera API-definition, som du sedan importera med hjälp av PowerApps eller Microsoft Flow portaler. Detta är den rekommenderade metoden om Azure användare och användare med behörighet att skapa kopplingar är olika personer eller om anslutningen måste skapas i en annan Azure-klient. Om du använder det här läget, följer du de [använda manuell export](#manual) anvisningarna nedan.

    ![Exportera läge](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Anpassade anslutningen används ett *kopiera* för API-definition, så PowerApps och Microsoft Flow inte omedelbart vet om du gör ändringar i programmet och dess API-definition. Om du gör ändringar, upprepa stegen för export för den nya versionen.

<a name="express"></a>
## <a name="use-express-export"></a>Använda snabb export

Att slutföra exporten i **Express** läge, gör du följande:

1. Kontrollera att du har loggat in till PowerApps eller Microsoft Flow-klient som du vill exportera. 

2. Använd inställningarna som anges i tabellen.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Miljö**|Välj den miljö som anpassad koppling ska sparas. Mer information finns i [miljöer översikt](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Namn på anpassade API**|Ange ett namn, vilka PowerApps och Microsoft Flow kommer finns i deras connector-listan.|
    |**Förbereda säkerhetskonfiguration**|Om det behövs, tillhandahålla säkerhet konfigurationsinformation som behövs för att bevilja användare åtkomst till ditt API. Det här exemplet illustrerar en API-nyckel. Mer information finns i [ange autentiseringstypen](#auth) nedan.|
 
    ![Exportera till PowerApps och Microsoft Flow Express](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klicka på **OK**. Anpassad koppling har nu skapats och lagts till i miljön som du angav.

Exempel på hur **Express** läge med Azure Functions finns [anropa en funktion från PowerApps](functions-powerapps-scenario.md) och [anropa en funktion från Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Använda manuell export

Att slutföra exporten i **manuell** läge, gör du följande:

1. Klicka på **hämta** och spara filen, eller klicka på kopieringsknappen och spara URL: en. Du använder den hämtade filen eller Webbadressen under importen.
 
    ![Manuell export till PowerApps och Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Om din API-definition innehåller alla definitioner för säkerhet, kallas dessa i steg #2. Under importen, PowerApps och Microsoft Flow identifierar dessa och uppmanas att ange säkerhetsinformation. Samla in de autentiseringsuppgifter som rör varje definition för användning i nästa avsnitt. Mer information finns i [ange autentiseringstypen](#auth) nedan.

    ![Säkerhet för manuell export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Det här exemplet visar nyckelsäkerhet API-definitionen som ingick i OpenAPI-definition.

Nu när du har exporterat API-definitionen kan importera du den för att skapa en anpassad koppling i PowerApps och Microsoft Flow. Anpassade kopplingar delas mellan de två tjänsterna, så du behöver bara importera definition en gång.

Följ dessa steg om du vill importera API-definition i PowerApps och Microsoft Flow:

1. Gå till [powerapps.com](https://web.powerapps.com) eller [flow.microsoft.com](https://flow.microsoft.com).

2. Klicka på ikonen växel i det övre högra hörnet och sedan på **anpassade kopplingar**.

   ![Kugghjulsikon i tjänst](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klicka på **Skapa anpassad koppling**, klicka på **importera en definition av OpenAPI**.

   ![Skapa anpassad anslutningsapp](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Ange ett namn för anpassad koppling och sedan navigera till OpenAPI definitionen som du exporterade och klickar på **Fortsätt**.

   ![Överför OpenAPI definition](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. På den **allmänna** kan du granska informationen som kommer från OpenAPI-definition.

5. På den **säkerhet** om du uppmanas att ange information om autentisering, anger du värden som är lämpliga för autentiseringstyp. Klicka på **fortsätta**.

    ![Säkerhetsfliken](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Det här exemplet visar de obligatoriska fälten för autentisering för API-nyckel. Fälten skiljer sig åt beroende på vilken autentiseringstyp.

6. På den **definitioner** fliken alla åtgärder som definierats i filen OpenAPI är fylls i automatiskt. Om alla nödvändiga åtgärder har definierats kan gå du till nästa steg. Om inte, kan du lägga till och ändra operations här.

    ![Fliken definitioner](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Det här exemplet har en åtgärd som heter `CalculateCosts`. Metadata, till exempel **beskrivning**, alla kommer från filen OpenAPI.

7. Klicka på **skapa koppling** överst på sidan.

Du kan ansluta till den egna kopplingen i PowerApps och Microsoft Flow nu. Mer information om hur du skapar kopplingar i PowerApps och Microsoft Flow portaler finns [registrera din anpassade connector (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) och [registrera din anpassade connector (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Ange autentiseringstyp

PowerApps och Microsoft Flow stöder en mängd identitetsleverantörer som autentisering av anpassade kopplingar. Om din API kräver autentisering, kontrollera att den avbildas som en _säkerhet definition_ i dokumentet OpenAPI som i följande exempel:

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
Under exporten bör ange du konfigurationsvärden som gör att PowerApps och Microsoft Flow att autentisera användare.

Det här avsnittet beskriver de autentiseringstyper som stöds i **Express** läge: API-nyckel, Azure Active Directory och allmänna OAuth 2.0. PowerApps och Microsoft Flow stöder också grundläggande autentisering och OAuth 2.0 för specifika tjänster som Dropbox, Facebook och SalesForce.

### <a name="api-key"></a>API-nyckel
När du använder en API-nyckel, uppmanas användarna anslutningstjänsten för nyckeln när de skapar en anslutning. Du kan ange en API-nyckelnamn hjälper dem att förstå vilka behövs. I det tidigare exemplet använder vi namnet `API Key (contact meganb@contoso.com)` så att användarna förstår var du kan hämta information om API-nyckeln. För Azure Functions är nyckeln vanligtvis en värdnycklar, som omfattar flera funktioner i funktionen appen.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (AD Azure)
När du använder Azure AD, du behöver två registreringar för Azure AD-program: en för API sig själv och en för anpassad koppling:

- Konfigurera registrering för API: et med den [autentisering/auktorisering i Apptjänst](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funktion.

- Om du vill konfigurera registrering för anslutningen, följer du stegen i [lägga till ett Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). Registreringen måste ha delegerad åtkomst till ditt API och svars-URL: en `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Mer information finns i Azure AD registration exempel för [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) och [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Dessa exempel används Azure Resource Manager som API; Ersätt ditt API om du följer instruktionerna.

Följande konfigurationsvärden krävs:
- **Klient-ID** -klient-ID anslutningstjänsten Azure AD-registrering
- **Klienthemlighet** -klienthemlighet anslutningstjänsten Azure AD-registrering
- **Inloggnings-URL** -bas-URL för Azure AD. I Azure, detta är vanligtvis `https://login.windows.net`.
- **Klient-ID** -ID för innehavaren som ska användas för inloggningen. Detta bör vara ”vanliga” eller ID för innehavaren som anslutningen skapas.
- **Resurs-URL** -resurs-URL för Azure AD-registrering för din API

> [!IMPORTANT]
> Om någon annan importeras API-definitionen till PowerApps och Microsoft Flow som en del av manuell flödet, måste du ange dem med klient-ID och klienthemlighet av den *registreringen av anslutningsverktyget*, samt resurs-URL för ditt API. Se till att dessa hemligheter hanteras på ett säkert sätt. **Dela inte säkerhetsreferenser för API: et sig själv.**

### <a name="generic-oauth-20"></a>Generic OAuth 2.0
När du använder den allmänna OAuth 2.0 kan integrera du med valfri OAuth 2.0-provider. På så sätt kan du arbeta med anpassade providers som inte stöds.

Följande konfigurationsvärden krävs:
- **Klient-ID** -OAuth 2.0-klient-ID
- **Klienthemlighet** -klienthemlighet OAuth 2.0
- **Webbadress för auktorisering** -Webbadress för OAuth 2.0-auktorisering
- **Token URL** -URL för OAuth 2.0-token
- **Uppdatera URL** -URL för OAuth 2.0-uppdatering


