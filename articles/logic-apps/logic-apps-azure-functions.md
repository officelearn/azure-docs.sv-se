---
title: Lägg till och kör anpassad kod i Azure Logic Apps med Azure Functions | Microsoft Docs
description: Lär dig hur du lägger till och köra anpassade kodavsnitt i Azure Logic Apps med Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263198"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Lägga till och köra egna i Azure Logic Apps med Azure Functions

När du vill skapa och köra tillräckligt med kod som åtgärdar ett specifikt problem i dina logic apps, du kan skapa egna funktioner med hjälp av [Azure Functions](../azure-functions/functions-overview.md). Den här tjänsten ger möjlighet att skapa och köra egna som skrivits med Node.js- eller C# i logic apps utan att behöva bekymra dig om hur du skapar en hel app eller infrastruktur för att köra din kod. Azure Functions tillhandahåller serverlös databehandling i molnet och är användbart för att utföra uppgifter, till exempel:

* Utöka din logikapp beteende med funktioner som stöds av Node.js- eller C#.
* Utföra beräkningar i logikappens arbetsflöde.
* Använda avancerad formatering eller compute fälten i dina logic apps.

Du kan också [anropa logic apps från inuti Azure functions](#call-logic-app).

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln, är här de objekt som du behöver:

* Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>. 

* Logikappen där du vill lägga till funktionen

  Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din logikapp 

  Innan du kan lägga till åtgärder för att köra functions, måste din logikapp börja med en utlösare.

* En Azure function-app, vilket är en behållare för Azure functions och Azure-funktion. Om du inte har en funktionsapp, måste du [först skapa din funktionsapp](../azure-functions/functions-create-first-azure-function.md). Du kan sedan skapa din funktion antingen [separat utanför din logikapp](#create-function-external), eller [från inuti logikappen](#create-function-designer) i Logic App Designer.

  Både nya och befintliga Azure-funktionsappar och funktioner har samma krav för att arbeta med dina logic apps:

  * Funktionsappen måste tillhöra samma Azure-prenumeration som din logikapp.

  * Din funktion måste använda den **allmän webhook** funktionsmallen för antingen **JavaScript** eller **C#**. Den här mallen kan acceptera innehåll som har `application/json` typen från din logikapp. Dessa mallar kan också Logic App Designer hitta och visa de anpassa funktioner som du skapar med dessa mallar när du lägger till dessa funktioner i dina logic apps.

  * Kontrollera att din Funktionsmall **läge** är inställd på **Webhook** och **typ av Webbhook** är inställd på **generisk JSON**.

    1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.
    2. Välj på Azure-huvudmenyn **Funktionsappar**. 
    3. I den **Funktionsappar** listan, markera din funktionsapp, expandera funktionen och välj **integrera**. 
    4. Kontrollera din mall **läge** är inställd på **Webhook** och att den **typ av Webbhook** är inställd på **generisk JSON**. 

  * Om din funktion har en [API-definition](../azure-functions/functions-openapi-definition.md), tidigare känt som en [Swagger-fil](http://swagger.io/), Logic Apps Designer erbjuder en rikare upplevelse för arbete med funktionsparametrar. 
  Innan din logikapp kan hitta och få åtkomst till funktioner som har Swagger beskrivningar [konfigurera funktionsappen genom att följa dessa steg](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Skapa functions utanför logic apps

I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, skapa Azure-funktionsapp, som måste ha samma Azure-prenumeration som din logikapp och skapa din Azure-funktion. Om du inte har använt Azure Functions kan du lära dig hur du [skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md), men tänk på dessa krav för att skapa Azure-funktioner som du kan lägga till och anropa från logikappar.

* Kontrollera att du väljer den **allmän webhook** funktionsmallen för antingen **JavaScript** eller **C#**.

  ![Generisk webhook – JavaScript- eller C#](./media/logic-apps-azure-functions/generic-webhook.png)

* När du har skapat din Azure-funktion kontrollerar du att mallens **läge** och **typ av Webbhook** egenskaper är korrekta.

  1. I den **Funktionsappar** listan, expandera funktionen och välj **integrera**. 

  2. Kontrollera att din mall **läge** är inställd på **Webhook** och att den **typ av Webbhook** är inställd på **generisk JSON**. 

     ![Egenskaper för mallen funktionen ”integrera”](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Du kan också om du [Generera en API-definition](../azure-functions/functions-openapi-definition.md), tidigare känt som en [Swagger-fil](http://swagger.io/), för funktionen du får en rikare upplevelse när du arbetar med parametrarna i Logic Apps Designer. Så här skapar funktionsappen så att logikappen kan hitta och få åtkomst till funktioner som har Swagger beskrivningar:

  * Kontrollera att din funktionsapp körs aktivt.

  * Ställ in i din funktionsapp [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) så att alla ursprung är tillåtna:

    1. Från och med den **Funktionsappar** väljer du din funktionsapp > **plattformsfunktioner** > **CORS**.

       ![Markera din funktionsapp > ”plattformsfunktioner” > ”CORS”](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. Under **CORS**, lägga till den `*` jokertecken tecken, men ta bort alla andra ursprung i listan och välj **spara**.

       ![Markera din funktionsapp > ”plattformsfunktioner” > ”CORS”](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

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

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i Logic App Designer. 

2. Under steget där du vill skapa och lägga till funktionen, Välj **nytt steg** > **Lägg till en åtgärd**. 

3. I sökrutan anger du ”azure functions” som filter.
Välj den här åtgärden från åtgärdslistan över: **och välj en Azure-funktion - Azure Functions** 

   ![Hitta ”Azure functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Markera din funktionsapp från applistan funktion. När åtgärderna lista öppnas, Välj den här åtgärden: **Azure Functions – Skapa ny funktion**

   ![Markera din funktionsapp](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. Definiera din funktion i Redigeraren för definitionen av funktionen:

   1. I den **funktionsnamn** ange ett namn för din funktion. 

   2. I den **kod** lägger du till Funktionskoden i mallen, inklusive svar och nyttolast som du vill ha tillbaka till din logikapp när din funktion är klar. 
   Context-objektet i mallkoden beskrivs meddelandet och innehåll som logikappen skickar till din funktion, till exempel:

      ![Definiera din funktion](./media/logic-apps-azure-functions/function-definition.png)

      I din funktion kan du referera till egenskaperna i context-objektet med följande syntax:

      ```text
      context.<token-name>.<property-name>
      ```
      Här är den syntax som du använder det här exemplet:

      ```text
      context.body.content
      ```

   3. När du är klar väljer du **Skapa**.

6. I den **Begärandetext** anger context-objektet ska skickas som din funktion användarens indata som måste vara formaterad i JavaScript Object Notation (JSON). Om du klickar på den **Begärandetext** öppnas med namnet, den dynamiska innehållslistan så du kan välja token för egenskaper som är tillgängliga från föregående steg. 

   Det här exemplet överför objektet i den **brödtext** token från e postutlösare:  

   ![Exempel på ”brödtext i förfrågan” – kontext objektet nyttolast](./media/logic-apps-azure-functions/function-request-body-example.png)

   Logic App Designer genererar baserat på innehållet i context-objektet, en Funktionsmall som du kan sedan redigera infogade. 
   Logic Apps skapar också variabler baserat på inkommande context-objektet.

   I det här exemplet är inte context-objektet omvandla som en sträng, så läggs innehållet direkt till JSON-nyttolast. 
   Om objektet inte är en JSON-token, som måste vara en sträng, ett JSON-objekt eller en JSON-matris, får du ett felmeddelande. 
   Om du vill konvertera context-objektet som en sträng, lägger du till dubbla citattecken, till exempel:

   ![Skicka objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Om du vill ange annan information, till exempel metoden att använda, begärandehuvuden eller frågeparametrar, Välj **visa avancerade alternativ**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Lägg till befintliga funktioner i logikappar

Om du vill anropa befintliga Azure-funktioner från logikappar måste du lägga till Azure functions som andra åtgärder i Logic App Designer. 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i Logic App Designer. 

2. Under steget där du vill lägga till funktionen, Välj **nytt steg** > **Lägg till en åtgärd**. 

3. I sökrutan anger du ”azure functions” som filter.
Välj den här åtgärden från åtgärdslistan över: **och välj en Azure-funktion - Azure Functions** 

   ![Hitta ”Azure functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Markera din funktionsapp från applistan funktion. Välj din funktion när listan funktioner visas. 

   ![Välj din funktionsapp och Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   För funktioner som har API-definitioner (Swagger beskrivningar) och som är [ställa in så att logikappen kan hitta och komma åt dessa funktioner](#function-swagger), kan du välja **Swagger-åtgärder**:

   ![Markera din funktionsapp, ”Swagger-åtgärder” ”, och din Azure-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. I den **Begärandetext** anger context-objektet ska skickas som din funktion användarens indata som måste vara formaterad i JavaScript Object Notation (JSON). Den här context-objektet beskriver meddelandet och innehåll som logikappen skickar till din funktion. 

   Om du klickar på den **Begärandetext** öppnas med namnet, den dynamiska innehållslistan så du kan välja token för egenskaper som är tillgängliga från föregående steg. 
   Det här exemplet överför objektet i den **brödtext** token från e postutlösare:

   ![Exempel på ”brödtext i förfrågan” – kontext objektet nyttolast](./media/logic-apps-azure-functions/function-request-body-example.png)

   I det här exemplet är inte context-objektet omvandla som en sträng, så läggs innehållet direkt till JSON-nyttolast. 
   Om objektet inte är en JSON-token, som måste vara en sträng, ett JSON-objekt eller en JSON-matris, får du ett felmeddelande. 
   Om du vill konvertera context-objektet som en sträng, lägger du till dubbla citattecken, till exempel:

   ![Skicka objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Om du vill ange annan information, till exempel metoden att använda, begärandehuvuden eller frågeparametrar, Välj **visa avancerade alternativ**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Anropa logic apps från functions

För att utlösa en logikapp från inuti en Azure-funktion som logikappen måste ha en anropningsbara slutpunkt, eller mer specifikt en **begära** utlösaren. Sedan, från i din funktion, skicka en HTTP POST-begäran till URL: en för som **begäran** utlösa och inkludera den nyttolast som du vill att logic app ska bearbeta. Mer information finns i [anropa, utlösare, eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
