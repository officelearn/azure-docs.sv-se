---
title: Lägga till och köra kodavsnitt – Azure Logic Apps
description: Lägga till och köra kodfragment med infogad kod i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160552"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Lägga till och köra kodfragment med infogad kod i Azure Logic Apps

När du vill köra en del av koden i din logikapp kan du lägga till inbyggt **infogad kod** åtgärd som ett steg i logikappens arbetsflöde. Den här åtgärden fungerar bäst när du vill köra kod som passar det här scenariot:

* Körs i JavaScript. Fler språk kommer snart.
* Har slutförts körs i fem sekunder eller färre.
* Hanterar data upp till 50 MB i storlek.
* Använder Node.js version 8.11.1. Mer information finns i [inbyggda standardobjekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Den här åtgärden kör kodfragmentet och returnerar resultatet från det kodfragmentet som en token med namnet **resultatet**, som du kan använda i efterföljande åtgärder i din logikapp. Andra scenarier där du vill skapa en funktion för din kod, försök [skapa och anropa en Azure-funktion](../logic-apps/logic-apps-azure-functions.md) i din logikapp.

I den här artikeln exempel logic app utlöses när ett nytt e-postmeddelande tas emot i ett Office 365 Outlook-konto. Kodfragmentet extraherar och returnerar alla e-postadresser som visas i e-postmeddelandets brödtext.

![Exempel-översikt](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill lägga till din kodfragmentet, inklusive en utlösare. Om du inte har en logikapp kan se [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Exempellogikapp i det här avsnittet använder den här Office 365 Outlook-utlösare: **När ett nytt e-postmeddelande**

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkad till din logikapp

## <a name="add-inline-code"></a>Lägg till infogad kod

1. Om du inte redan har gjort i den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. Lägg till i designern den **infogad kod** åtgärden på den plats som du vill ha i logikappens arbetsflöde.

   * Om du vill lägga till åtgärden i slutet av arbetsflödet, Välj **nytt steg**.

   * Flytta muspekaren över pilen som ansluter de här stegen för att lägga till åtgärden mellan befintliga steg. Välj plustecknet (**+**), och välj **Lägg till en åtgärd**.

   Det här exemplet lägger till den **infogad kod** åtgärd under Office 365 Outlook-utlösaren.

   ![Lägg till nytt steg](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Under **Välj en åtgärd**, ange ”infogad kod” i sökrutan som filter. Välj den här åtgärden från åtgärdslistan över: **Köra JavaScript-kod**

   ![Välj ”kör JavaScript-kod”](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Åtgärden visas i designern och innehåller vissa standard exempelkod, inklusive en return-instruktion.

   ![Infogad kod åtgärd med standard-exempelkod](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. I den **kod** , ta bort exempelkoden och ange koden som du vill köra. Skriv kod som du placerar i en metod, men utan att definiera Metodsignaturen. 

   När du skriver ett känt nyckelord, visas listan Komplettera automatiskt så att du kan välja från tillgängliga nyckelord, till exempel:

   ![Nyckelordet automatisk komplettering lista](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Det här exemplet kodfragmentet skapar först en variabel som lagrar en *reguljärt uttryck*, som anger ett mönster som ska matchas i indatatexten. Koden skapar sedan en variabel som lagrar data på brödtext e-post från utlösaren.

   ![Skapa variabler](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Du kan förenkla resultaten från tidigare åtgärder och utlösare-referensen i listan med dynamiskt innehåll visas när markören är i den **kod** box. I det här exemplet listan visar tillgängliga resultat från utlösaren, inklusive den **brödtext** token, som nu kan du välja.

   När du har valt den **brödtext** token, infogad kod åtgärden matchar denna token till en `workflowContext` objekt som refererar till den e-postadress `Body` egenskapsvärde:

   ![Välj resultat](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   I den **kod** rutan din fragment kan använda den skrivskyddade `workflowContext` objekt som indata. Det här objektet har subegenskaper som ger din kodåtkomst till resultaten från utlösaren och den tidigare åtgärder i arbetsflödet.
   Mer information finns i det här avsnittet i detta kapitel: [Referera till utlösare och åtgärd resultat i din kod](#workflowcontext).

   > [!NOTE]
   >
   > Om din kodfragmentet hänvisar till åtgärdsnamn som använder operatorn punkt (.), måste du lägga till de åtgärder som ska den [ **åtgärder** parametern](#add-parameters). Dessa referenser måste även skriva kolumnnamnen åtgärd med hakparenteser ([]) och citattecken, till exempel:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Infogad kod åtgärden kräver inte en `return` uttrycket, men resultaten från en `return` instruktionen är tillgängliga som referens för senare åtgärder via den **resultatet** token. 
   Till exempel kodfragmentet returnerar resultatet genom att anropa den `match()` funktion, vilket ska söka efter matchar i e-postmeddelandets brödtext mot det reguljära uttrycket. Den **Compose** åtgärden använder den **resultatet** token-referensen resultaten från infogat code åtgärd och skapar ett enskilt resultat.

   ![Färdig logikapp](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. När du är klar sparar du din logikapp.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Referens för utlösare och åtgärd resultat i din kod

Den `workflowContext` objekt med den här strukturen, som innehåller den `actions`, `trigger`, och `workflow` subegenskaper:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Den här tabellen innehåller mer information om dessa subegenskaper:

| Egenskap  | Typ | Beskrivning |
|----------|------|-------|
| `actions` | Objektsamling | Resultatobjekt från åtgärder som körs innan din kodfragmentet körs. Varje objekt har en *nyckel / värde-* par där nyckeln är namnet på en åtgärd och värdet motsvarar att anropa den [actions() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#actions) med `@actions('<action-name>')`. Åtgärdens namn använder samma åtgärdsnamn som används i den underliggande arbetsflödesdefinitionen som ersätter blanksteg (””) i Åtgärdsnamnet på med understreck (_). Det här objektet ger tillgång till åtgärden egenskapsvärden från aktuella arbetsflödesinstansen körs. |
| `trigger` | Object | Objekt från utlösaren och motsvarande att anropa den [trigger() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Det här objektet ger tillgång till utlösaren egenskapsvärden från aktuella arbetsflödesinstansen körs. |
| `workflow` | Object | Arbetsflödesobjekt och motsvarar anropar den [workflow() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Det här objektet ger tillgång till arbetsflödet egenskapsvärden som Arbetsflödesnamn, körnings-ID och så vidare från aktuella arbetsflödesinstansen körs. |
|||

I det här avsnittet exempelvis den `workflowContext` objektet har dessa egenskaper som kan komma åt din kod:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Lägg till parametrar

I vissa fall kan du behöva uttryckligen kräver att den **infogad kod** åtgärd inkluderar resultat från utlösaren eller specifika åtgärder som din kod refererar till som beroenden genom att lägga till den **utlösaren** eller **Åtgärder** parametrar. Det här alternativet är användbart för scenarier där refererade resultatet inte hittades vid körning.

> [!TIP]
> Om du planerar att återanvända kod, lägger du till referenser till egenskaper med hjälp av den **kod** så att din kod innehåller löst token referenserna i stället för att lägga till utlösare eller åtgärder som explicit beroenden.

Anta exempelvis att du har kod som refererar till den **SelectedOption** beror på den **skicka e-postgodkännande** åtgärd för Office 365 Outlook-anslutningen. Vid skapa tid, Logic Apps-motorn analyserar din kod för att avgöra om du har refererat till en utlösare eller åtgärd och ingår dessa resultat automatiskt. Vid körningstillfället, bör du får ett felmeddelande att refererade utlösaren eller åtgärden resultatet är inte tillgänglig i den angivna `workflowContext` objektet, du kan lägga till den utlösaren eller åtgärden som en explicit beroende. I det här exemplet lägger du till den **åtgärder** parametern och ange att den **infogad kod** åtgärd uttryckligen resultatet från den **skicka e-postgodkännande** åtgärd.

Om du vill lägga till dessa parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill:

   ![Lägg till parametrar](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parameter | Beskrivning |
   |-----------|-------------|
   | **Åtgärder** | Inkludera resultat från tidigare åtgärder. Se [inkludera åtgärd resultat](#action-results). |
   | **Utlösare** | Inkludera resultat från utlösaren. Se [inkludera utlösaren resultat](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Inkludera resultat för utlösare

Om du väljer **utlösare**, du uppmanas om så här inkluderar utlösaren.

* Från den **utlösaren** väljer **Ja**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Inkludera åtgärd resultat

Om du väljer **åtgärder**, uppmanas du för de åtgärder som du vill lägga till. Innan du börjar lägga till åtgärder, behöver du versionen av Åtgärdsnamnet på som visas i logic app underliggande arbetsflödesdefinitionen.

* Den här funktionen stöder inte variabler, slingor och iteration index.

* Namn i din logikapp arbetsflödesdefinitionen använder ett understreck (_), inte ett blanksteg.

* Inkludera dessa operatörer för åtgärdsnamn som använder punktoperatorn (.), till exempel:

  `My.Action.Name`

1. I verktygsfältet för appdesignern väljer **Kodvyer**, och Sök i den `actions` attribut för Åtgärdsnamnet.

   Till exempel `Send_approval_email_` är JSON-namn för den **skicka e-postgodkännande** åtgärd.

   ![Hitta åtgärdsnamn i JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Om du vill gå tillbaka till designverktygsvyn på verktygsfältet kod, Välj **Designer**.

1. Så här lägger du till den första åtgärden i den **åtgärder artikel - 1** anger åtgärdens JSON-namn.

   ![Ange första åtgärd](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Om du vill lägga till en annan åtgärd, Välj **Lägg till nytt objekt**.

## <a name="reference"></a>Referens

Mer information om den **köra JavaScript-kod** åtgärdens strukturen och syntaxen i din logikapp underliggande arbetsflödesdefinitionen med Definitionsspråk för arbetsflödet finns i den här åtgärden [referera till avsnittet ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Nästa steg

Läs mer om [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md)
