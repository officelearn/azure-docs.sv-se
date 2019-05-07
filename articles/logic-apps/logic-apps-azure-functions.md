---
title: Lägga till och köra kod i Azure Logic Apps med Azure Functions
description: Lägga till och köra kod i Azure Logic Apps med Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.openlocfilehash: e371a6abe32a1a41d3babeaa27aaec3e30bd3323
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142310"
---
# <a name="add-and-run-code-by-using-azure-functions-in-azure-logic-apps"></a>Lägga till och köra kod med hjälp av Azure Functions i Azure Logic Apps

När du vill köra kod som utför ett specifikt jobb i dina logic apps kan du skapa egna funktioner med [Azure Functions](../azure-functions/functions-overview.md). Den här tjänsten hjälper dig att skapa Node.js, C#, och F# code så att du inte behöver skapa en fullständig app eller infrastrukturen för att köra din kod. Du kan också [anropa logic apps från inuti Azure functions](#call-logic-app).
Azure Functions tillhandahåller serverlös databehandling i molnet och är användbart för att utföra uppgifter som till exempel:

* Utöka din logikapp beteende med funktioner i Node.js- eller C#.
* Utföra beräkningar i logikappens arbetsflöde.
* Använda avancerad formatering eller compute fälten i dina logic apps.

Om du vill köra kodavsnitt utan att skapa Azure functions, lär du dig hur du [lägga till och köra infogad kod](../logic-apps/logic-apps-add-run-inline-code.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Azure function-app, vilket är en behållare för Azure functions och Azure-funktion. Om du inte har en funktionsapp [först skapa din funktionsapp](../azure-functions/functions-create-first-azure-function.md). Du kan sedan skapa din funktion antingen [separat utanför din logikapp](#create-function-external), eller [från inuti logikappen](#create-function-designer) i Logic App Designer.

  Både befintliga och nya funktionsappar och funktioner har samma krav för att arbeta med logic apps:

  * Funktionsappen måste ha samma Azure-prenumeration som din logikapp.

  * Din funktion använder en HTTP-utlösare, till exempel, **HTTP-utlösare** funktionsmallen för **JavaScript** eller **C#**. 

    HTTP-utlösarmallen kan acceptera innehåll som har `application/json` typen från din logikapp. 
    När du lägger till en Azure-funktion i din logikapp visar Logic App Designer anpassade funktioner som skapas från den här mallen i Azure-prenumerationen. 

  * Din funktion använder inte anpassade vägar om du inte har definierat en [OpenAPI-definition](../azure-functions/functions-openapi-definition.md), tidigare känt som en [Swagger-filen](https://swagger.io/). 
  
  * Om du har definierat en OpenAPI-definition för din funktion, dig Logic Apps Designer en rikare upplevelse för att arbeta med funktionsparametrar. Innan din logikapp kan hitta och få tillgång till funktioner som har OpenAPI-definitioner [konfigurera funktionsappen genom att följa dessa steg](#function-swagger).

* Logikappen där du vill lägga till funktionen, inklusive en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din logikapp 

  Innan du kan lägga till åtgärder som kan köra functions, måste din logikapp börja med en utlösare.

  Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Skapa functions utanför logic apps

I den [Azure-portalen](https://portal.azure.com), skapa Azure-funktionsapp, som måste ha samma Azure-prenumeration som din logikapp och skapa din Azure-funktion.
Om du inte har använt till att skapa Azure functions kan du lära dig hur du [skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md), men tänk på dessa krav för att skapa funktioner som du kan anropa från logikappar:

* Kontrollera att du väljer den **HTTP-utlösare** funktionsmallen för antingen **JavaScript** eller **C#**.

  ![HTTP-utlösare – JavaScript- eller C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Du kan också om du [Generera en API-definition](../azure-functions/functions-openapi-definition.md), tidigare känt som en [Swagger-fil](https://swagger.io/), för funktionen du får en rikare upplevelse när du arbetar med parametrarna i Logic Apps Designer. Följ dessa steg om du vill konfigurera funktionsappen så att logikappen kan hitta och använda funktioner som har Swagger beskrivningar:

  1. Kontrollera att din funktionsapp körs aktivt.

  2. Ställ in i din funktionsapp [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) så att alla ursprung tillåts genom att följa dessa steg:

     1. Från den **Funktionsappar** väljer du din funktionsapp > **plattformsfunktioner** > **CORS**.

        ![Markera din funktionsapp > ”plattformsfunktioner” > ”CORS”](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. Under **CORS**, lägga till den `*` jokertecken tecken, men ta bort alla andra ursprung i listan och välj **spara**.

        ![Ange ”CORS * till jokertecknet” * ”](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Åtkomst egenskapsvärden i HTTP-begäranden

Webhook-funktioner kan acceptera HTTP-begäranden som indata och skicka dessa begäranden till andra funktioner. Till exempel även om Logic Apps har [funktioner som konvertera datum/tid-värden](../logic-apps/workflow-definition-language-functions-reference.md), JavaScript-funktionen grundläggande exempel visar hur du kan komma åt en egenskap i en begäran som skickas till funktionen och utföra åtgärder på Egenskapsvärdet. För att komma åt egenskaper inuti objekt, det här exemplet används den [punkt (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Här är vad som händer i den här funktionen:

1. Funktionen skapar en `data` variabeln och tilldelar den `body` objekt i den `request` objekt till den variabeln. Funktionen använder operatorn punkt (.) för att referera till den `body` objekt i den `request` objekt: 

   ```javascript
   var data = request.body;
   ```

2. Funktionen kan nu komma åt den `date` egenskapen genom den `data` variabeln och konvertera egenskapsvärdet från datum och tid till DateString anger genom att anropa den `ToDateString()` funktion. Funktionen returnerar också resultatet via den `body` -egenskapen i funktionens svaret: 

   ```javascript
   body: data.date.ToDateString();
   ```

Nu när du har skapat din Azure-funktion, följer du stegen för hur du [Lägg till funktioner i logikappar](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Skapa funktioner i logikappar

Innan du kan skapa en Azure-funktion från och med i din logikapp i Logic App Designer, måste du först ha en Azure function-app, vilket är en behållare för dina funktioner. Om du inte har en funktionsapp kan du skapa den funktionsappen först. Se [skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md). 

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer. 

2. Följ de anvisningar som gäller för ditt scenario för att skapa och lägga till din funktion:

   * Välj under det sista steget i logikappens arbetsflöde **nytt steg**.

   * Mellan befintliga steg i logikappens arbetsflöde musen över pilen, klicka på plustecknet (+), och sedan markera **Lägg till en åtgärd**.

3. I sökrutan anger du ”azure functions” som filter.
Välj den här åtgärden från åtgärdslistan över: **Välja en Azure-funktion – Azure Functions** 

   ![Hitta ”Azure functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Markera din funktionsapp från applistan funktion. När åtgärdslistan öppnas väljer du den här åtgärden: **Azure Functions – Skapa ny funktion**

   ![Markera din funktionsapp](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. Definiera din funktion i Redigeraren för definitionen av funktionen:

   1. I den **funktionsnamn** ange ett namn för din funktion. 

   2. I den **kod** lägger du till koden för funktionen mallen, inklusive svar och nyttolast som du vill att returneras till logikappen när din funktion är klar. 

      ![Definiera din funktion](./media/logic-apps-azure-functions/function-definition.png)

      I mallens kod i  *`context` objekt* refererar till meddelandet som logikappen skickar via den **Begärandetext** i ett senare steg. 
      Åtkomst till den `context` objektets egenskaper från i din funktion, använder du följande syntax: 

      `context.body.<property-name>`

      Till exempel till referens i `content` egenskap i den `context` objekt, använder du följande syntax: 

      `context.body.content`

      Mallkoden innehåller också en `input` variabel som lagrar värdet från den `data` parametern så att din funktion kan utföra åtgärder på det värdet. 
      I JavaScript-funktioner i `data` variabeln är också en genväg till `context.body`.

      > [!NOTE]
      > Den `body` här egenskaper gäller den `context` objekt och är inte densamma som den **brödtext** token från en åtgärd utdatafiler, som du kan även skicka till din funktion. 
 
   3. När du är klar väljer du **Skapa**.

6. I den **Begärandetext** ange din funktion användarens indata, som måste vara formaterad som en JavaScript Object Notation (JSON)-objekt. 

   Detta indata är den *context-objektet* eller meddelande som logikappen skickar till din funktion. Om du klickar på den **Begärandetext** fältet, visas den dynamiska innehållslistan så du kan välja token för utdata från föregående steg. Det här exemplet anger att kontexten nyttolasten innehåller en egenskap med namnet `content` som har den **från** token är värdet från postutlösaren för e-:

   ![Exempel på ”brödtext i förfrågan” – kontext objektet nyttolast](./media/logic-apps-azure-functions/function-request-body-example.png)

   Context-objektet är inte här, omvandla som en sträng, så läggs objektets innehåll direkt till JSON-nyttolast. När context-objektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris, får du ett felmeddelande. Om det här exemplet används den **togs emot** token i stället, du kan omvandla context-objektet som en sträng genom att lägga till dubbla citattecken:  

   ![Skicka objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Om du vill ange annan information, till exempel metoden att använda, begärandehuvuden eller frågeparametrar, Välj **visa avancerade alternativ**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Lägg till befintliga funktioner i logikappar

Om du vill anropa befintliga Azure-funktioner från logikappar måste du lägga till Azure functions som andra åtgärder i Logic App Designer. 

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer. 

2. Under steget där du vill lägga till funktionen, Välj **nytt steg** > **Lägg till en åtgärd**. 

3. I sökrutan anger du ”azure functions” som filter.
Välj den här åtgärden från åtgärdslistan över: **Välja en Azure-funktion – Azure Functions** 

   ![Hitta ”Azure functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Markera din funktionsapp från applistan funktion. Välj din funktion när listan funktioner visas. 

   ![Välj din funktionsapp och Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   För funktioner som har API-definitioner (Swagger beskrivningar) och är [ställa in så att logikappen kan hitta och komma åt dessa funktioner](#function-swagger), kan du välja **Swagger-åtgärder**:

   ![Markera din funktionsapp, ”Swagger-åtgärder” ”, och din Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. I den **Begärandetext** ange din funktion användarens indata, som måste vara formaterad som en JavaScript Object Notation (JSON)-objekt. 

   Detta indata är den *context-objektet* eller meddelande som logikappen skickar till din funktion. Om du klickar på den **Begärandetext** fältet, visas den dynamiska innehållslistan så du kan välja token för utdata från föregående steg. Det här exemplet anger att kontexten nyttolasten innehåller en egenskap med namnet `content` som har den **från** token är värdet från postutlösaren för e-:

   ![Exempel på ”brödtext i förfrågan” – kontext objektet nyttolast](./media/logic-apps-azure-functions/function-request-body-example.png)

   Context-objektet är inte här, omvandla som en sträng, så läggs objektets innehåll direkt till JSON-nyttolast. När context-objektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris, får du ett felmeddelande. Om det här exemplet används den **togs emot** token i stället, du kan omvandla context-objektet som en sträng genom att lägga till dubbla citattecken: 

   ![Skicka objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Om du vill ange annan information, till exempel metoden att använda, begärandehuvuden eller frågeparametrar, Välj **visa avancerade alternativ**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Anropa logic apps från functions

När du vill utlösa en logikapp från inuti en Azure-funktion måste i logikapp börja med en utlösare som ger en anropsbar slutpunkt. Du kan till exempel börja logikapp med de **HTTP**, **begära**, **Azure Queues**, eller **Event Grid** utlösaren. Skicka en HTTP POST-begäran till URL: en med hjälp av olika i din funktion och inkludera nyttolasten som du vill att logic app ska bearbeta. Mer information finns i [anropa, utlösare, eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
