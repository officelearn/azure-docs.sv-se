---
title: Lägga till och köra kodavsnitt med hjälp av infogad kod
description: Lär dig hur du skapar och kör kodavsnitt med hjälp av infogade kodåtgärder för automatiserade uppgifter och arbetsflöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453499"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Lägga till och köra kodavsnitt med hjälp av infogad kod i Azure Logic Apps

När du vill köra en kod i logikappen kan du lägga till den inbyggda **infogade infogad kod** som ett steg i logikappens arbetsflöde. Den här åtgärden fungerar bäst när du vill köra kod som passar det här scenariot:

* Körs i JavaScript. Fler språk kommer snart.
* Slutar på fem sekunder eller mindre.
* Hanterar data upp till 50 MB i storlek.
* Kräver inte att du arbetar med åtgärderna [ **Variabler** ](../logic-apps/logic-apps-create-variables-store-values.md), som ännu inte stöds.
* Använder Node.js version 8.11.1. Mer information finns [i Standard inbyggda objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > Funktionen `require()` stöds inte av åtgärden **Infogad kod** för att köra JavaScript.

Den här åtgärden kör kodavsnittet och returnerar utdata från det kodavsnittet som en token med namnet **Resultat**, som du kan använda i efterföljande åtgärder i logikappen. För andra scenarier där du vill skapa en funktion för din kod kan du prova att [skapa och anropa en Azure-funktion](../logic-apps/logic-apps-azure-functions.md) i logikappen.

I den här artikeln utlöser exempellogiken när ett nytt e-postmeddelande anländer till ett Office 365 Outlook-konto. Kodavsnittet extraherar och returnerar alla e-postadresser som visas i e-posttexten.

![Exempel översikt](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill lägga till kodavsnittet, inklusive en utlösare. Om du inte har någon logikapp läser du [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Exempellogiken i det här avsnittet använder den här Office 365 Outlook-utlösaren: **När ett nytt e-postmeddelande anländer**

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkat till logikappen

  > [!NOTE]
  > Se till att du använder ett integrationskonto som är lämpligt för ditt användningsfall eller scenario. Till exempel är [kostnadsfria](../logic-apps/logic-apps-pricing.md#integration-accounts) integrationskonton endast avsedda för undersökande scenarier och arbetsbelastningar, inte produktionsscenarier, är begränsade i användning och dataflöde och stöds inte av ett servicenivåavtal (SLA). Andra nivåer medför kostnader, men inkluderar SLA-support, erbjuder mer dataflöde och har högre gränser. Läs mer om integrationskontonivåer, [priser](https://azure.microsoft.com/pricing/details/logic-apps/)och [begränsningar](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). [tiers](../logic-apps/logic-apps-pricing.md#integration-accounts)

## <a name="add-inline-code"></a>Lägg till infogad kod

1. Om du inte redan har öppnat logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Lägg till åtgärden **Infoga kod** på den plats du vill ha i logikappens arbetsflöde i designern.

   * Om du vill lägga till åtgärden i slutet av arbetsflödet väljer du **Nytt steg**.

   * Om du vill lägga till åtgärden mellan befintliga steg flyttar du muspekaren över pilen som kopplar dessa steg åt. Välj plustecknet**+**( ) och välj **Lägg till en åtgärd**.

   I det här exemplet läggs åtgärden **Infogad kod** till under Office 365 Outlook-utlösaren.

   ![Lägg till nytt steg](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Under **Välj en åtgärd**anger du "infogad kod" som filter i sökrutan. Välj den här åtgärden i åtgärdslistan: **Kör JavaScript-kod**

   ![Välj "Kör JavaScript-kod"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Åtgärden visas i designern och innehåller en standardexempelkod, inklusive en retursats.

   ![Infogad kodåtgärd med standardexempelkod](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Ta bort exempelkoden i rutan **Kod** och ange den kod som du vill köra. Skriv kod som du skulle sätta i en metod, men utan att definiera metodsignaturen. 

   När du skriver ett tolkat sökord visas listan komplettera automatiskt så att du kan välja bland tillgängliga sökord, till exempel:

   ![Lista över komplettera automatiskt sökord](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Det här exemplet kodavsnitt skapar först en variabel som lagrar ett *reguljärt uttryck*, som anger ett mönster som ska matchas i indatatext. Koden skapar sedan en variabel som lagrar e-postkroppsdata från utlösaren.

   ![Skapa variabler](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Om du vill göra resultaten från utlösaren och tidigare åtgärder lättare att referera till visas listan med dynamiskt innehåll medan markören finns i **rutan Kod.** I det här exemplet visar listan tillgängliga resultat **Body** från utlösaren, inklusive body-token, som du nu kan välja.

   När du har valt **Body-token** matchar infogad kodåtgärden token till `workflowContext` ett `Body` objekt som refererar till e-postmeddelandets egenskapsvärde:

   ![Välj resultat](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   I rutan **Kod** kan kodavsnittet använda skrivskyddat `workflowContext` objekt som indata. Det här objektet har underegenskaper som ger din kod åtkomst till resultaten från utlösaren och tidigare åtgärder i arbetsflödet.
   Mer information finns i det här avsnittet senare i det här avsnittet: [Referensutlösare och åtgärd resulterar i koden](#workflowcontext).

   > [!NOTE]
   >
   > Om kodavsnittet refererar till åtgärdsnamn som använder operatorn punkt (.) måste du lägga till dessa åtgärdsnamn i [parametern **Åtgärder** ](#add-parameters). Dessa referenser måste också innehålla åtgärdsnamnen med hakparenteser ([]) och citattecken, till exempel:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Den infogade kodåtgärden kräver `return` ingen sats, `return` men resultaten från en sats är tillgängliga som referens i senare åtgärder via **resultattoken.** 
   Kodavsnittet returnerar till exempel resultatet genom `match()` att anropa funktionen, som hittar matchningar i e-posttexten mot det reguljära uttrycket. Åtgärden **Komponera** använder **resultattoken** för att referera till resultaten från infogad kodåtgärd och skapar ett enda resultat.

   ![Färdig logikapp](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. När du är klar sparar du logikappen.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Referensutlösare och åtgärd resulterar i din kod

Objektet `workflowContext` har den här strukturen, `trigger`som `workflow` innehåller `actions`, och underegenskaper:

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

Den här tabellen innehåller mer information om dessa underegenskaper:

| Egenskap | Typ | Beskrivning |
|----------|------|-------|
| `actions` | Objektsamling | Resultatobjekt från åtgärder som körs innan kodavsnittet körs. Varje objekt har ett *nyckelvärdespar* där nyckeln är namnet på en åtgärd, och värdet motsvarar att anropa [actions()-funktionen](../logic-apps/workflow-definition-language-functions-reference.md#actions) med `@actions('<action-name>')`. Åtgärdens namn använder samma åtgärdsnamn som används i den underliggande arbetsflödesdefinitionen, som ersätter blanksteg (" ") i åtgärdsnamnet med understreck (_). Det här objektet ger åtkomst till åtgärdsegenskapsvärden från den aktuella arbetsflödesinstanskörningen. |
| `trigger` | Objekt | Resultatobjekt från utlösaren och motsvarar att anropa [funktionen trigger().](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Det här objektet ger åtkomst till utlösande egenskapsvärden från den aktuella arbetsflödesinstanskörningen. |
| `workflow` | Objekt | Arbetsflödesobjektet och motsvarar [anropar funktionen workflow().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Det här objektet ger åtkomst till värden för arbetsflödesegenskap, till exempel arbetsflödesnamnet, kör-ID och så vidare, från den aktuella arbetsflödesinstanskörningen. |
|||

I det här avsnittets `workflowContext` exempel har objektet följande egenskaper som koden kan komma åt:

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

## <a name="add-parameters"></a>Lägga till parametrar

I vissa fall kan du behöva uttryckligen kräva att **infogad kod-åtgärden** innehåller resultat från utlösaren eller specifika åtgärder som koden refererar till som beroenden genom att lägga till parametrarna **Utlösare** eller **Åtgärder.** Det här alternativet är användbart för scenarier där de refererade resultaten inte hittas vid körning.

> [!TIP]
> Om du planerar att återanvända koden lägger du till referenser till egenskaper med hjälp av rutan **Kod** så att koden innehåller de lösta tokenreferenserna i stället för att lägga till utlösaren eller åtgärderna som explicita beroenden.

Anta till exempel att du har kod som refererar till **SelectedOption-resultatet** från **e-poståtgärden Skicka godkännande** för Office 365 Outlook-anslutningsappen. Vid skapandet analyserar Logic Apps-motorn koden för att avgöra om du har refererat till några utlösare eller åtgärdsresultat och inkluderar dessa resultat automatiskt. Om du vid körning får ett felmeddelande om att det refererade utlösaren `workflowContext` eller åtgärdsresultatet inte är tillgängligt i det angivna objektet, kan du lägga till utlösaren eller åtgärden som ett explicit beroende. I det här exemplet lägger du till parametern **Åtgärder** och anger att åtgärden **Infogad kod** uttryckligen innehåller resultatet från **e-poståtgärden Skicka godkännande.**

Om du vill lägga till dessa parametrar öppnar du listan **Lägg till ny parameter** och väljer önskade parametrar:

   ![Lägga till parametrar](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parameter | Beskrivning |
   |-----------|-------------|
   | **Åtgärder** | Inkludera resultat från tidigare åtgärder. Se [Inkludera åtgärdsresultat](#action-results). |
   | **Utlösare** | Inkludera resultat från utlösaren. Se [Inkludera utlösarresultat](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Inkludera utlösarresultat

Om du väljer **Utlösare**uppmanas du att inkludera utlösarresultat.

* Välj **Ja**i listan **Utlösare** .

<a name="action-results"></a>

### <a name="include-action-results"></a>Inkludera åtgärdsresultat

Om du väljer **Åtgärder**uppmanas du att lägga till de åtgärder som du vill lägga till. Innan du börjar lägga till åtgärder behöver du dock den version av åtgärdsnamnet som visas i logikappens underliggande arbetsflödesdefinition.

* Den här funktionen stöder inte variabler, loopar och iterationsindex.

* Namn i logikappens arbetsflödesdefinition använder ett understreck (_), inte ett blanksteg.

* För åtgärdsnamn som använder punktoperatorn (.), inkludera dessa operatorer, till exempel:

  `My.Action.Name`

1. Välj **Kodvy**i designerverktygsfältet och `actions` sök i attributet efter åtgärdsnamnet.

   Är till `Send_approval_email_` exempel JSON-namnet för åtgärden **Skicka godkännande e-post.**

   ![Hitta åtgärdsnamn i JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Om du vill återgå till designervyn väljer du **Designer**i verktygsfältet kodvy .

1. Om du vill lägga till den första åtgärden anger du åtgärdens JSON-namn i rutan **Åtgärdsobjekt - 1.**

   ![Ange den första åtgärden](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Om du vill lägga till en annan åtgärd väljer du **Lägg till nytt objekt**.

## <a name="reference"></a>Referens

Mer information om instruktionen **Kör JavaScript-kods** struktur och syntax i logikappens underliggande arbetsflödesdefinition med hjälp av referensavsnittet arbetsflödesdefinition finns i [referensavsnittet](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)för den här åtgärden .

## <a name="next-steps"></a>Nästa steg

Läs mer om [kopplingar för Azure Logic Apps](../connectors/apis-list.md)
