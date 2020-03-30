---
title: Lägga till och anropa Azure-funktioner från Azure Logic Apps
description: Anropa och kör anpassad kod i dina Azure-funktioner från automatiserade uppgifter och arbetsflöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284127"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Anropa Azure-funktioner från Azure Logic Apps

När du vill köra kod som utför ett visst jobb i logikapparna kan du skapa din egen funktion med hjälp av [Azure Functions](../azure-functions/functions-overview.md). Den här tjänsten hjälper dig att skapa nod.js-, C#- och F#-funktioner så att du inte behöver skapa en komplett app eller infrastruktur för att köra kod. Du kan också [anropa logikappar inifrån Azure-funktioner](#call-logic-app). Azure Functions tillhandahåller serverlös databehandling i molnet och är användbart för att utföra uppgifter som dessa exempel:

* Utöka logikappens beteende med funktioner i Node.js eller C#.
* Utför beräkningar i logikapparbetsflödet.
* Använd avancerade formaterings- eller beräkningsfält i logikapparna.

Om du vill köra kodavsnitt utan att skapa Azure-funktioner kan du läsa om hur du [lägger till och kör infogad kod](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integrering mellan logikappar och Azure-funktioner fungerar för närvarande inte med slots aktiverade.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Azure-funktionsapp, som är en behållare för Azure-funktioner, tillsammans med din Azure-funktion. Om du inte har en funktionsapp [skapar du funktionsappen först](../azure-functions/functions-create-first-azure-function.md). Du kan sedan skapa din funktion antingen utanför logikappen i Azure-portalen eller [inifrån logikappen](#create-function-designer) i Logic App Designer.

* När du arbetar med logikappar gäller samma krav för funktionsappar och funktioner oavsett om de är befintliga eller nya:

  * Din funktionsapp och logikapp måste använda samma Azure-prenumeration.

  * Nya funktionsappar måste använda antingen .NET eller JavaScript som körningsstack. När du lägger till en ny funktion i befintliga funktionsappar kan du välja antingen C# eller JavaScript.

  * Funktionen använder **HTTP-utlösarmallen.**

    HTTP-utlösarmallen kan `application/json` acceptera innehåll som har typ från logikappen. När du lägger till en Azure-funktion i logikappen visar Logic App Designer anpassade funktioner som skapas från den här mallen i din Azure-prenumeration.

  * Funktionen använder inte anpassade vägar om du inte har definierat en [OpenAPI-definition](../azure-functions/functions-openapi-definition.md) (tidigare känd som en [Swagger-fil).](https://swagger.io/)

  * Om du har en OpenAPI-definition för din funktion ger Logic Apps Designer dig en rikare upplevelse när du arbetar med funktionsparametrar. Innan logikappen kan hitta och komma åt funktioner som har OpenAPI-definitioner [konfigurerar du funktionsappen genom att följa dessa steg](#function-swagger).

* Logikappen där du vill lägga till funktionen, inklusive en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i logikappen

  Innan du kan lägga till åtgärder som kör funktioner måste logikappen börja med en utlösare. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Hitta funktioner som har OpenAPI-beskrivningar

Om du vill ha en rikare upplevelse när du arbetar med funktionsparametrar i Logic Apps Designer [skapar du en OpenAPI-definition](../azure-functions/functions-openapi-definition.md), tidigare känd som en [Swagger-fil,](https://swagger.io/)för din funktion. Så här konfigurerar du funktionsappen så att logikappen kan hitta och använda funktioner som har Swagger-beskrivningar:

1. Kontrollera att funktionsappen körs aktivt.

1. I funktionsappen konfigurerar du [CORS (Cross-Origin Resource Sharing)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) så att alla ursprung tillåts genom att följa dessa steg:

   1. Välj **funktionsapp i** listan Funktionsappar. I den högra rutan väljer du **Plattformsfunktioner** > **CORS**.

      ![Välj din funktionsapp > "Plattformsfunktioner" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Lägg till jokertecknet asterisk**`*`**( ) under **CORS,** men ta bort alla andra ursprung i listan och välj **Spara**.

      ![Ställ in "CORS* på jokertecknet "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Komma åt egenskapsvärden i HTTP-begäranden

Webhook-funktioner kan acceptera HTTP-begäranden som indata och skicka dessa begäranden till andra funktioner. Även om Logic Apps till exempel har [funktioner som konverterar DateTime-värden,](../logic-apps/workflow-definition-language-functions-reference.md)visar den här grundläggande exempelfunktionen JavaScript hur du kan komma åt en egenskap i ett begärandeobjekt som skickas till funktionen och utföra åtgärder på egenskapsvärdet. Om du vill komma åt egenskaper inuti objekt används [den punkt (.) operatorn](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)i det här exemplet:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Här är vad som händer inuti den här funktionen:

1. Funktionen skapar en `data` variabel och `body` tilldelar `request` objektet inuti objektet till den variabeln. Funktionen använder operatorn punkt (.) `body` för `request` att referera till objektet inuti objektet:

   ```javascript
   var data = request.body;
   ```

1. Funktionen kan nu `date` komma åt `data` egenskapen via variabeln och konvertera egenskapsvärdet från `ToDateString()` DateTime-typ till DateString-typ genom att anropa funktionen. Funktionen returnerar också resultatet `body` genom egenskapen i funktionens svar:

   ```javascript
   body: data.date.ToDateString();
   ```

Nu när du har skapat din Azure-funktion följer du stegen för hur du lägger till [funktioner i logikappar](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Skapa funktioner i logikappar

Innan du kan skapa en Azure-funktion som börjar inifrån logikappen med hjälp av Logic App Designer måste du först ha en Azure-funktionsapp, som är en behållare för dina funktioner. Om du inte har en funktionsapp skapar du funktionsappen först. Se [Skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md).

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Om du vill skapa och lägga till din funktion följer du det steg som gäller för ditt scenario:

   * Under det sista steget i logikappens arbetsflöde väljer du **Nytt steg**.

   * Mellan befintliga steg i logikappens arbetsflöde flyttar du musen över pilen, väljer plustecknet (+) och väljer sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "azure-funktioner" som filter. Välj åtgärden **Välj en Azure-funktion** i listan Åtgärder, till exempel:

   ![Hitta "Azure-funktioner"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Välj funktionsappen i listan funktionsappar. När åtgärdslistan har öppnats väljer du den här åtgärden: **Skapa ny funktion**

   ![Välj funktionsapp](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definiera din funktion i funktionsdefinitionsredigeraren:

   1. Ange ett namn för din funktion i rutan **Funktionsnamn.**

   1. I rutan **Kod** lägger du till koden i funktionsmallen, inklusive det svar och den nyttolast som du vill returnera till logikappen när funktionen har körts. När du är klar väljer du **Skapa**.

   Ett exempel:

   ![Definiera din funktion](./media/logic-apps-azure-functions/add-code-function-definition.png)

   I mallens * `context` * kod refererar objektet till meddelandet som logikappen skickar via fältet **Begärandetext** i ett senare steg. Om du `context` vill komma åt objektets egenskaper inifrån funktionen använder du den här syntaxen:

   `context.body.<property-name>`

   Om du till `content` exempel vill `context` referera till egenskapen i objektet använder du den här syntaxen:

   `context.body.content`

   Mallkoden innehåller också `input` en variabel som lagrar `data` värdet från parametern så att funktionen kan utföra åtgärder på det värdet. Inuti JavaScript-funktioner `data` är variabeln `context.body`också en genväg för .

   > [!NOTE]
   > Egenskapen `body` här gäller `context` för objektet och är inte samma som **body-token** från en åtgärds utdata, som du också kan skicka till din funktion.

1. Ange funktionens indata i rutan **Begärandetext,** som måste formateras som ett JSON-objekt (JavaScript Object Notation).

   Den här indata är *det kontextobjekt* eller meddelande som logikappen skickar till din funktion. När du klickar i fältet **Begärandetext** visas listan med dynamiskt innehåll så att du kan välja token för utdata från tidigare steg. I det här exemplet anges att kontextnyttolasten innehåller en egenskap som heter `content` från tokens värde från e-postutlösaren. **From**

   !["Request Body"-exempel - nyttolast för kontextobjekt](./media/logic-apps-azure-functions/function-request-body-example.png)

   Här är kontextobjektet inte cast som en sträng, så objektets innehåll läggs till direkt i JSON-nyttolasten. Men när kontextobjektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris får du ett fel. Om det här exemplet i stället används token **Mottagen tid** kan du casta kontextobjektet som en sträng genom att lägga till dubbla citattecken.

   ![Casta objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Om du vill ange annan information, till exempel vilken metod du ska använda, begära rubriker eller frågeparametrar eller autentisering, öppnar du listan **Lägg till ny parameter** och väljer önskade alternativ. För autentisering skiljer sig alternativen åt beroende på vilken funktion du har valt. Se [Aktivera autentisering för Azure-funktioner](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Lägga till befintliga funktioner i logikappar

Om du vill anropa befintliga Azure-funktioner från dina logikappar kan du lägga till Azure-funktioner som alla andra åtgärder i Logic App Designer.

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Välj **Nytt steg**under det steg där du vill lägga till funktionen .

1. Under **Välj en åtgärd**anger du "azure-funktioner" som filter i sökrutan. Välj åtgärden **Välj en Azure-funktion** i listan Åtgärder.

   ![Hitta "Azure-funktioner"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Välj funktionsappen i listan funktionsappar. När funktionslistan visas väljer du din funktion.

   ![Välj funktionsapp och Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   För funktioner som har API-definitioner (Swagger-beskrivningar) och är [inställda så att logikappen kan hitta och komma åt dessa funktioner](#function-swagger)kan du välja **Swagger-åtgärder**.

   ![Välj funktionsapp, "Swagger-åtgärder" och din Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Ange funktionens indata i rutan **Begärandetext,** som måste formateras som ett JSON-objekt (JavaScript Object Notation).

   Den här indata är *det kontextobjekt* eller meddelande som logikappen skickar till din funktion. När du klickar i fältet **Begärandetext** visas listan med dynamiskt innehåll så att du kan välja token för utdata från tidigare steg. I det här exemplet anges att kontextnyttolasten innehåller en egenskap som heter `content` från tokens värde från e-postutlösaren. **From**

   !["Request Body"-exempel - nyttolast för kontextobjekt](./media/logic-apps-azure-functions/function-request-body-example.png)

   Här är kontextobjektet inte cast som en sträng, så objektets innehåll läggs till direkt i JSON-nyttolasten. Men när kontextobjektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris får du ett fel. Så om det här exemplet använde token **Mottagen tid** i stället kan du casta kontextobjektet som en sträng genom att lägga till dubbla citattecken:

   ![Casta objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Om du vill ange annan information, till exempel vilken metod du ska använda, begära rubriker, frågeparametrar eller autentisering, öppnar du listan **Lägg till ny parameter** och väljer önskade alternativ. För autentisering skiljer sig alternativen åt beroende på vilken funktion du har valt. Se [Aktivera autentisering i Azure-funktioner](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Anropa logikappar från Azure-funktioner

När du vill utlösa en logikapp inifrån en Azure-funktion måste logikappen starta med en utlösare som tillhandahåller en callable slutpunkt. Du kan till exempel starta logikappen med **HTTP-,** **Request-** **azure-köer eller** **utlösaren Event Grid.** I din funktion skickar du en HTTP POST-begäran till utlösarens URL och inkluderar den nyttolast som du vill att logikappen ska bearbeta. Mer information finns i [Anropa, utlösa eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Aktivera autentisering för Azure-funktioner

Om du vill autentisera åtkomst till resurser i andra Azure Active Directory-klienter (Azure AD) utan att behöva logga in och ange autentiseringsuppgifter eller hemligheter kan logikappen använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare känd som Managed Service Identity eller MSI). Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Läs mer om [Azure-tjänster som stöder hanterade identiteter för Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Om du konfigurerar logikappen så att den systemtilldelade identiteten eller en manuellt skapad användartilldelade identitet används, kan Azure-funktionerna i logikappen också använda samma identitet för autentisering. Mer information om autentiseringsstöd för Azure-funktioner i logikappar finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Så här konfigurerar och använder du den hanterade identiteten med din funktion:

1. Aktivera den hanterade identiteten i logikappen och konfigurera identitetens åtkomst till målresursen. Se [Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Aktivera autentisering i din Azure-funktions- och funktionsapp genom att följa dessa steg:

   * [Konfigurera anonym autentisering i din funktion](#set-authentication-function-app)
   * [Konfigurera Azure AD-autentisering i funktionsappen](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Konfigurera anonym autentisering i din funktion

Om du vill använda logikappens hanterade identitet i din Azure-funktion har du ställt in funktionens autentiseringsnivå till anonym. Annars genererar logikappen ett "BadRequest"-fel.

1. Leta reda på och välj din funktionsapp i [Azure-portalen.](https://portal.azure.com) I de här stegen används "FabrikamFunctionApp" som exempelfunktionsapp.

1. Välj **Plattformsfunktioner**i funktionsappfönstret . Under **Utvecklingsverktyg**väljer du **Avancerade verktyg (Kudu).**

   ![Öppna avancerade verktyg för Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. På Kudu-webbplatsens namnlist väljer du **CMD**på menyn **Debug Console** .

   ![Välj alternativet "CMD" på felsökerkonsolens meny](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. När nästa sida visas väljer du **webbplats** > **wwwroot-funktionen** > *your-function*i mapplistan . I de här stegen används "FabrikamAzureFunction" som exempelfunktion.

   ![Välj "webbplats" > "wwwroot" > din funktion](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Öppna `function.json` filen för redigering.

   ![Klicka på redigera för "function.json"-fil](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Kontrollera `bindings` om egenskapen `authLevel` finns i objektet. Om egenskapen finns anger du `anonymous`egenskapsvärdet till . Annars lägger du till egenskapen och anger värdet.

   ![Lägg till egenskapen "authLevel" och ställ in på "anonym"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. När du är klar sparar du inställningarna och fortsätter sedan till nästa avsnitt.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Konfigurera Azure AD-autentisering för din funktionsapp

Innan du startar den här uppgiften bör du söka efter och lägga dessa värden åt sidan för senare användning:

* Det objekt-ID som genereras för den systemtilldelade identitet som representerar logikappen

  * Om du vill generera det här objekt-ID:t [aktiverar du logikappens systemtilldelade identitet](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Om du vill hitta det här objekt-ID:t öppnar du logikappen i Logic App Designer. Välj **Identity** > **System tilldelat**under **Inställningar**på logikapp-menyn.

* Katalog-ID:t för din klient i Azure Active Directory (Azure AD)

  Om du vill hämta klientens katalog-ID kan du köra [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell-kommandot. Eller gör så här i Azure-portalen:

  1. Leta reda på och välj din funktionsapp i [Azure-portalen.](https://portal.azure.com)

  1. Hitta och välj din Azure AD-klientorganisation. I de här stegen används "Fabrikam" som exempelklient.

  1. Välj Egenskaper under **Hantera**på klientens **meny.**

  1. Kopiera till exempel klientens katalog-ID och spara ID:t för senare användning.

     ![Hitta och kopiera Azure AD-klientens katalog-ID](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Resurs-ID för målresursen som du vill komma åt

  * Om du vill hitta dessa resurs-ID:er läser du de [Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Det här resurs-ID:t måste exakt matcha det värde som Azure AD förväntar sig, inklusive eventuella nödvändiga efterföljande snedstreck.

  Det här resurs-ID:t är också samma värde som du senare använder i egenskapen **Målgrupp** när du [ställer in funktionsåtgärden för att använda den systemtilldelade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Nu är du redo att konfigurera Azure AD-autentisering för din funktionsapp.

1. Leta reda på och välj din funktionsapp i [Azure-portalen.](https://portal.azure.com)

1. Välj **Plattformsfunktioner**i funktionsappfönstret . Under **Nätverk**väljer du **Autentisering/auktorisering**.

   ![Visa inställningar för autentisering och auktorisering](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Ändra inställningen **för apptjänstautentisering** till **På**. Välj **Logga in med Azure Active Directory**i listan Åtgärd som ska **vidtas när begäran inte autentiseras** . Under **Autentiseringsleverantörer**väljer du **Azure Active Directory**.

   ![Aktivera autentisering med Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Gör så här i fönstret Active Directory i Azures active directory:On the **Azure Active Directory Settings pane,** follow these steps:

   1. Ställ in **hanteringsläge** till **Avancerat**.

   1. Ange objekt-ID för logikappens systemtilldelade identitet i egenskapen **Klient-ID.**

   1. Ange url:en i egenskapen `https://sts.windows.net/` **Utfärdare url** och lägg till din Azure AD-klients katalog-ID.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. I egenskapen **Tillåten tokenmålgrupp** anger du resurs-ID:t för den målresurs som du vill komma åt.

      Det här resurs-ID:t är samma värde som du senare använder i egenskapen **Målgrupp** när du [ställer in funktionsåtgärden för att använda den systemtilldelade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Nu ser din version ut ungefär som det här exemplet:

   ![Autentiseringsinställningar för Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. När du är klar väljer du **Ok**.

1. Gå tillbaka till Logic App Designer och följ [stegen för att autentisera åtkomst med den hanterade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-kopplingar](../connectors/apis-list.md)
