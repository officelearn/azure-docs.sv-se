---
title: "Översikt över anpassade kopplingar - Azure Logic Apps | Microsoft Docs"
description: "Översikt över hur du skapar anpassade kopplingar för support och expandera integrationsscenarier"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Översikt över anpassade kopplingar

Utan att behöva skriva någon kod du kan bygga arbetsflöden eller appar med [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com), eller [Microsoft PowerApps](https://powerapps.microsoft.com). För att hjälpa dig integrera din data och affärsprocesser, erbjuda tjänsterna [100 + kopplingar](../connectors/apis-list.md), inte bara för Microsoft-tjänster och produkter som Azure och SQL Server, men andra tjänster, GitHub, Salesforce, Twitter och mycket mer . 

Ibland men du kanske vill anropa API: er, tjänster och datorer som inte är tillgängliga som färdiga kopplingar. För att stödja fler skräddarsydd scenarier för användarnas företag och produktivitet måste du skapa *anpassade kopplingar* med sina egna utlösare och åtgärder.
Anpassade kopplingar Expandera integreringar, reach, synlighet och användning för din tjänst eller produkt som hjälper dig att öka och påskynda kunder.

Det här diagrammet visar till exempel interaktionen mellan en anpassad Logic Apps-koppling som skapats för den API och en logikapp som fungerar med den API med hjälp av anpassade anslutningen en webb-API:

![Översikt för en webb-API, anpassad koppling och logikapp](./media/custom-connector-overview/custom-connector-conceptual.png)

Den här översikten beskrivs Allmänt avancerade åtgärder för att skapa, skydda, registrering, och använder plus om du vill dela eller certifierar kopplingar:

![Anpassad koppling redigering av steg](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Krav

Om du vill skapa en koppling från början till slut, vad du behöver:

* En Azure-prenumeration. Om du inte har en prenumeration kan du starta med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan registrera dig för en [betala per användning prenumeration](https://azure.microsoft.com/pricing/purchase-options/).

* En RESTful-API med någon typ av autentiserad åtkomst. Mer information finns i [skapa egna kopplingar från Web API: er](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Mönster som du kan använda för din koppling utlösare och åtgärder finns i [skapa anpassade API: er som du kan anropa från logik app arbetsflöden](../logic-apps/logic-apps-create-api-app.md).

* Här några objekt:

  * En [OpenAPI 2.0-fil](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), tidigare känt som Swagger
  * En URL till en OpenAPI definition
  * En [Postman samling](../logic-apps/custom-connector-api-postman-collection.md) för din API 

  Om du inte har något av dessa objekt kan ge vi vägledning för dig.

* Valfritt: En bild som ska användas som en ikon för din anpassade connector

## <a name="1-build-your-restful-api"></a>1. Skapa RESTful-API

Tekniskt sett en koppling är en omslutning runt en REST-API som baseras på en OpenAPI (tidigare Swagger) specifikation och gör att underliggande tjänsten prata med Logic Apps, flöde eller PowerApps. Så måste, du först ett fullt fungerande API innan du skapar en anpassad koppling. 

Du kan använda alla språk och plattformar för din API. Vi rekommenderar en av dessa plattformar för Microsoft-teknik:

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API Apps](https://azure.microsoft.com/services/app-service/api/)

Den här kursen visar exempelvis [hur du skapar en anpassad koppling från en webb-API: et](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Mönster som du kan använda för din koppling utlösare och åtgärder finns i [skapa anpassade API: er som du kan anropa från logik app arbetsflöden](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Skydda din API

Du kan använda dessa standarder för autentisering för API: er och kopplingar:

   * [OAuth 2.0](https://oauth.net/2/), inklusive [Azure Active Directory](https://azure.microsoft.com/develop/identity/) eller specifika tjänster, till exempel Dropbox, GitHub och SalesForce
   * Allmän OAuth 2.0
   * [Grundläggande autentisering](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [API-nyckel](https://swagger.io/docs/specification/authentication/api-keys/)

Du kan ställa in Azure Active Directory (AD Azure) autentisering för din API i Azure-portalen så du behöver implementera autentisering via kod. Eller så du behöver och ställer in användarautentisering via din API-koden. 

Följ lämpliga självstudier för mer information:

* [Logic Apps: Skydda din anpassad koppling](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flöde: Skydda din anpassad koppling](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: Skydda din anpassad koppling](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Beskriv ditt API 

Förutsatt att din API har någon typ av autentiserad åtkomst, behöver du beskriva din API-gränssnitt och åtgärder så att Logic Apps, flöde eller PowerApps kan kommunicera med din API.
Använd någon av dessa branschen standarddefinitioner:

* En [OpenAPI 2.0-fil](https://swagger.io/) börjar du med att skapa med en befintlig OpenAPI-fil.

  Om du har använt OpenAPI [komma igång med Swagger](http://swagger.io/getting-started/) på webbplatsen swagger.io.
  En exempelfil OpenAPI, finns det [Text Analytics API-dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* En samling för Postman som genereras automatiskt en OpenAPI-fil för dig. När du inte redan har en OpenAPI-fil och inte vill skapa ett kan du fortfarande enkelt skapa en anpassad koppling med hjälp av en Postman-samling.

  Om du har använt Postman, [installera appen Postman](https://www.getpostman.com/apps) från sin plats. Mer information finns i [beskrivs anpassade kopplingar med Postman](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> Filstorleken måste vara mindre än 1 MB.

I bakgrunden Logic Apps och flödet PowerApps slutligen använder OpenAPI, Parsar en Postman samling och översätter samlingen till en OpenAPI definitionsfilen. Men OpenAPI 2.0 och Postman samlingar använda olika format, är både språkoberoende maskinläsbar dokument som beskriver ditt API åtgärder och parametrar. Du kan skapa dessa dokument från olika verktyg, baserat på språket och plattform används din API. Du kan också skapa en OpenAPI-fil när du registrerar din koppling.

T.ex, du kan skapa en OpenAPI-fil eller en samling Postman från valfri REST API-slutpunkt, inklusive:

* Offentligt tillgängliga kopplingar, till exempel [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/)och så vidare

* En API som du skapas och distribueras till alla molnbaserat webbhotell, till exempel Azure, Heroku, Google Cloud och mycket mer

* En anpassad line-of-business-API som har distribuerats till ditt nätverk men bara om den API exponeras på internet

## <a name="4-register-your-connector"></a>4. Registrera din connector

Registreringen kan Logic Apps flödet eller PowerApps förstå din API-egenskaper, inklusive beskrivning, krävs autentisering och åtgärder, inklusive parametrar och utdata för varje åtgärd. När du startar registreringsprocessen kan ange du en OpenAPI-fil eller en samling Postman som fyller automatiskt metadatafält i Registreringsguiden. Du kan redigera dessa fält när som helst.

![Beskriv ditt API](./media/custom-connector-overview/choose-api-definition-file.png)

Följ lämpliga kursen för att registrera din koppling:

* [Logic Apps: Registrera ditt connector](../logic-apps/logic-apps-custom-connector-register.md)
* [Flöde: Registrera ditt connector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registrera ditt connector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Använd din anslutningen i en logikapp, flöde eller app 

Du kan använda din connector på samma sätt som du använder Microsoft-hanterade anslutningar. Till exempel i ett arbetsflöde för logik app, Lägg till din anpassad koppling så att du kan skapa en anslutning till ditt API och anropa inga åtgärder som API: et finns på samma sätt som du anropa åtgärder för Microsoft-hanterade anslutningar.

## <a name="6-share-your-connector"></a>6. Dela din koppling 

Du kan dela din anslutningstjänst med användare i organisationen på samma sätt som du delar resurser i Logic Apps, flöde eller PowerApps. Delning är valfri, kanske scenarier där du vill dela dina kontakter med andra användare.

Registrerad men ocertifierade anpassade kopplingar fungerar som Microsoft-hanterade anslutningar, men är synliga och tillgängliga *endast* kopplingens författare och användare som har samma Azure Active Directory-klient och Azure-prenumeration för logikappar i den region där de apparna som har distribuerats. Nästa steg beskriver hur du kan dela din anslutningstjänst med externa användare utanför dessa gränser till exempel med alla Logic Apps flödet och PowerApps användare.

> [!IMPORTANT]
> Om du delar en koppling, kan andra börja beror på kopplingen. 
> ***Ta bort din anslutning tar bort alla anslutningar till kopplingen.***

* [Logic Apps: Dela din koppling](../logic-apps/logic-apps-custom-connector-register.md)
* [Flöde: Dela din koppling](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: Dela din koppling](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Certifiera din koppling

Du kan också dela din anslutningstjänst med alla användare i Logic Apps, flöde och PowerApps, skicka din connector för Microsoft-certifikat. Den här processen går igenom din kopplingen kontrollerar tekniska och innehåll och validerar funktioner för Logic Apps, flödesreglering och PowerApps innan Microsoft kan publicera din koppling. Läs [hur du skickar in din connector för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Få support

* För support med onboarding- och e- [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). Microsoft aktivt övervakar det här kontot för utvecklare frågor och problem och skickar dem till lämpliga teamet. 

* Vanliga frågor och svar, finns det [anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa en anpassad koppling från en webb-API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Konfigurera autentisering för anpassade kopplingar](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Beskriv anpassade kopplingar med Postman samlingar](../logic-apps/custom-connector-api-postman-collection.md)
* [Skicka anpassade kopplingar för Microsoft-certifikat](../logic-apps/custom-connector-submit-certification.md)
