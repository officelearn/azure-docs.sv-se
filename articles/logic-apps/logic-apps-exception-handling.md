---
title: Hantera fel och undantag i arbetsflöden
description: Lär dig hur du hanterar fel och undantag som inträffar i automatiserade uppgifter och arbetsflöden som skapats med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284036"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Det sätt på vilket all integrationsarkitektur hanterar driftstopp eller problem som orsakas av beroende system kan utgöra en utmaning. För att hjälpa dig att skapa robusta och motståndskraftiga integrationer som smidigt hanterar problem och fel ger Logic Apps en förstklassig upplevelse för att hantera fel och undantag.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Återförsöksprinciper

För de mest grundläggande undantagen och felhanteringen kan du använda en *återförsöksprincip* i alla åtgärder eller utlösare där stöds, till exempel, se [HTTP-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). En återförsöksprincip anger om och hur åtgärden eller utlösaren försöker en begäran igen när den ursprungliga begäran tar time out eller misslyckas, vilket är en begäran som resulterar i ett 408, 429 eller 5xx-svar. Om ingen annan återförsöksprincip används används standardprincipen.

Här är de principtyper som försöker igen:

| Typ | Beskrivning |
|------|-------------|
| **Default** | Den här principen skickar upp till fyra försök med *exponentiellt ökande* intervall, som skalas med 7,5 sekunder men är begränsade mellan 5 och 45 sekunder. |
| **Exponentiellt intervall**  | Den här principen väntar ett slumpmässigt intervall som valts från ett exponentiellt växande intervall innan nästa begäran skickas. |
| **Fast intervall**  | Den här principen väntar det angivna intervallet innan nästa begäran skickas. |
| **Inget**  | Skicka inte begäran igen. |
|||

Information om principbegränsningar för återförsök finns i [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Ändra återförsöksprincip

Så här väljer du en annan återförsöksprincip:

1. Öppna logikappen i Logic App Designer.

1. Öppna **inställningar** för en åtgärd eller utlösare.

1. Om åtgärden eller utlösaren stöder principer för återförsök väljer du den typ du vill använda under **Återförsöksprincip.**

Du kan också manuellt ange återförsöksprincipen `inputs` i avsnittet för en åtgärd eller utlösare som stöder principer för återförsök. Om du inte anger en princip för återförsök används standardprincipen.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*återanvända-principtyp*> | String | Den principtyp för återförsök `default`som `none` `fixed`du vill använda: , , eller`exponential` |
| <*återförsöksintervall*> | String | Det återförsöksintervall där värdet måste använda [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Standardminimum `PT5S` är och det `PT1D`maximala intervallet är . När du använder exponentiell intervallprincip kan du ange olika lägsta och högsta värden. |
| <*försök igen*> | Integer | Antalet försök att försöka igen, som måste vara mellan 1 och 90 |
||||

*Valfri*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*minsta intervall*> | String | För exponentiell intervallprincip, det minsta intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*maximalt intervall*> | String | För exponentiell intervallprincip, det största intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Här finns mer information om de olika principtyperna.

<a name="default-retry"></a>

### <a name="default"></a>Default

Om du inte anger en återförsöksprincip används standardprincipen, som i själva verket är en [exponentiell intervallprincip](#exponential-interval) som skickar upp till fyra återförsök med exponentiellt ökande intervall som skalas med 7,5 sekunder. Intervallet är begränsat till mellan 5 och 45 sekunder.

Även om standardprincipen inte uttryckligen har definierats i åtgärden eller utlösaren är den hur standardprincipen fungerar i ett exempel på HTTP-åtgärd:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Inget

Om du vill ange att åtgärden eller utlösaren inte försöker igen misslyckade begäranden anger du `none`<*återförsöksprinciptyp*> till .

### <a name="fixed-interval"></a>Fast intervall

Om du vill ange att åtgärden eller utlösaren väntar det angivna intervallet innan nästa begäran skickas anger du `fixed`<*återförsöksprinciptyp*> till .

*Exempel*

Den här återförsöksprincipen försöker få de senaste nyheterna två gånger till efter den första misslyckade begäran med en fördröjning på 30 sekunder mellan varje försök:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponentiellt intervall

Om du vill ange att åtgärden eller utlösaren väntar ett slumpmässigt intervall innan nästa begäran skickas anger `exponential`du <*återförsöksprinciptyp*> till . Det slumpmässiga intervallet väljs från ett exponentiellt växande intervall. Du kan också åsidosätta standardintervallen för lägsta och högsta belopp genom att ange dina egna minimi- och maximiintervall.

**Slumpmässiga variabelintervall**

Den här tabellen visar hur Logic Apps genererar en enhetlig slumpmässig variabel i det angivna intervallet för varje nytt försök upp till och med antalet återförsök:

| Nytt nytt antal | Minsta intervall | Maximalt intervall |
|--------------|------------------|------------------|
| 1 | max(0, <*minsta intervall*>) | min(intervall, <*maximalt intervall*>) |
| 2 | max(intervall, <*minsta intervall*>) | min(2 * intervall, <*maximalt intervall*>) |
| 3 | max(2 * intervall, <*minsta intervall*>) | min(4 * intervall, <*maximalt intervall*>) |
| 4 | max(4 * intervall, <*minsta intervall*>) | min(8 * intervall, <*maximalt intervall*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Fånga och hantera fel genom att ändra "kör efter"-beteende

När du lägger till åtgärder i Logic App Designer deklarerar du implicit den ordning som ska användas för att köra dessa åtgärder. När en åtgärd har körts markeras åtgärden `Succeeded`med `Failed` `Skipped`status `TimedOut`som , , eller . I varje åtgärdsdefinition anger egenskapen `runAfter` den föregående åtgärden som först måste avslutas och de statusar som tillåts för den föregående föregående innan den efterföljande åtgärden kan köras. Som standard körs en åtgärd som du lägger till `Succeeded` i designern endast när föregående aktiviteter har slutförts med status.

När en åtgärd genererar ett ohanterat fel eller `Failed`undantag markeras åtgärden `Skipped`och alla efterföljande åtgärder markeras . Om detta inträffar för en åtgärd som har parallella grenar följer logic apps-motorn de andra grenarna för att fastställa deras slutförandestatus. Om till exempel en gren `Skipped` slutar med en åtgärd baseras den grenens slutförandestatus på den föregående åtgärdens föregående status. När logikappkörningen är klar bestämmer motorn hela körningens status genom att utvärdera alla grenstatus. Om någon gren slutar i fel markeras `Failed`hela logikappkörningen .

![Exempel som visar hur körstatus utvärderas](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Om du vill vara säker på att en åtgärd fortfarande kan köras trots föregående status [anpassar du åtgärdens "kör efter"-beteende](#customize-run-after) för att hantera föregångarens misslyckade status.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Anpassa beteendet "kör efter"

Du kan anpassa beteendet "kör efter" så att åtgärden körs när `Succeeded`den `Failed` `Skipped`föregående `TimedOut`aktivitetens status antingen är , , , eller någon av dessa statusar. Om du till exempel vill skicka `Add_a_row_into_a_table` ett e-postmeddelande när föregående Excel Online-åtgärden har markerats `Failed`ändrar du `Succeeded`beteendet "kör efter" genom att följa något av följande steg:

* I designvyn väljer du knappen ellipser (**...**) och väljer sedan **Konfigurera körning efter**.

  ![Konfigurera beteendet "kör efter" för en åtgärd](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Åtgärdsformen visar standardstatus som krävs för den föregående åtgärden, vilket är **Lägg till en rad i en tabell** i det här exemplet:

  ![Standardbeteendet "kör efter" för en åtgärd](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Ändra beteendet "kör efter" till den status du vill ha, vilket **har misslyckats** i det här exemplet:

  ![Ändra "kör efter" beteende till "har misslyckats"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Om du vill ange att åtgärden ska `Failed` `Skipped` köras om den föregående åtgärden ska markeras som eller `TimedOut`väljer du de andra statusarna:

  ![Ändra beteendet "kör efter" för att ha någon annan status](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Redigera egenskapen `runAfter` i kodvyn i åtgärdens JSON-definition:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  I det här `runAfter` exemplet `Succeeded` ändrar du egenskapen från till: `Failed`

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Om du vill ange att åtgärden ska `Failed` `Skipped` köras om den föregående åtgärden ska markeras som eller `TimedOut`lägger du till de andra statusarna:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Utvärdera åtgärder med omfattningar och deras resultat

I likhet med att köra `runAfter` steg efter enskilda åtgärder med egenskapen kan du gruppera åtgärder i ett [scope](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Du kan använda scope när du vill gruppera åtgärder logiskt, bedöma scopets aggregerade status och utföra åtgärder baserat på den statusen. När alla åtgärder i ett scope har körts får själva scopet sin egen status.

Om du vill kontrollera ett scopes status kan du använda samma villkor som du `Succeeded`använder `Failed`för att kontrollera en logikapps körstatus, till exempel , och så vidare.

När alla scopeåtgärder lyckas markeras scopens status som `Succeeded`standard . Om den slutliga åtgärden i `Failed` `Aborted`ett scope resulterar som `Failed`eller markeras scopens status .

Om du vill `Failed` fånga upp undantag i ett scope och `runAfter` köra `Failed` åtgärder som hanterar dessa fel kan du använda egenskapen för det scopet. På så sätt, om *några* åtgärder i `runAfter` omfånget misslyckas, och du använder egenskapen för det omfånget, kan du skapa en enda åtgärd för att fånga fel.

Begränsningar för scope finns i [Gränser och config](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Få sammanhang och resultat för fel

Även om det är användbart att fånga fel från ett scope kanske du också vill att kontexten ska hjälpa dig att förstå exakt vilka åtgärder som misslyckades plus eventuella fel eller statuskoder som returnerades.

Funktionen [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) ger sammanhang om resultaten från alla åtgärder i ett scope. Funktionen `result()` accepterar en enda parameter, som är scopets namn, och returnerar en matris som innehåller alla åtgärdsresultat inifrån det omfånget. Dessa åtgärdsobjekt innehåller samma `actions()` attribut som objektet, till exempel åtgärdens starttid, sluttid, status, indata, korrelations-ID och utdata. Om du vill skicka kontext för alla åtgärder `@result()` som misslyckades `runAfter` i ett scope kan du enkelt para ihop ett uttryck med egenskapen.

Om du vill köra en åtgärd för `Failed` varje åtgärd i ett scope som har ett resultat `@result()` och filtrera matrisen med resultat ned till de misslyckade åtgärderna kan du para ihop ett uttryck med en [**filtermatrisåtgärd**](logic-apps-perform-data-operations.md#filter-array-action) och en [**för varje**](../logic-apps/logic-apps-control-flow-loops.md) slinga. Du kan ta den filtrerade resultatmatrisen och `For_each` utföra en åtgärd för varje fel med hjälp av loopen.

Här är ett exempel, följt av en detaljerad förklaring, som skickar en HTTP POST-begäran med svarstexten för alla åtgärder som misslyckades inom omfånget "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Här är en detaljerad genomgång som beskriver vad som händer i det här exemplet:

1. Om du vill hämta resultatet från alla åtgärder i "My_Scope" använder åtgärden **Filtermatris** det här filteruttrycket:`@result('My_Scope')`

1. Villkoret för **filtermatris** är ett `@result()` objekt `Failed`som har en status som är lika med . Det här villkoret filtrerar matrisen som har alla åtgärdsresultat från "My_Scope" ner till en matris med endast misslyckade åtgärdsresultat.

1. Utför `For_each` en loopåtgärd på de *filtrerade* matrisutgångarna. Det här steget utför en åtgärd för varje misslyckat åtgärdsresultat som tidigare filtrerats.

   Om en enskild åtgärd i omfånget misslyckas körs åtgärderna i loopen `For_each` bara en gång. Flera misslyckade åtgärder orsakar en åtgärd per fel.

1. Skicka ett HTTP-POST på objektsvarstexten, `For_each` som är uttrycket. `@item()['outputs']['body']`

   Objektformen `@result()` är samma `@actions()` som formen och kan tolkas på samma sätt.

1. Inkludera två anpassade huvuden med det`@item()['name']`misslyckade åtgärdsnamnet ( )`@item()['clientTrackingId']`och det misslyckade körningsklientspårnings-ID ().

Som referens är detta ett exempel `@result()` på ett `name` `body`enskilt `clientTrackingId` objekt som visar de , och egenskaper som tolkas i föregående exempel. Utanför `For_each` en `@result()` åtgärd returnerar en matris med dessa objekt.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Om du vill utföra olika mönster för undantagshantering kan du använda de uttryck som tidigare beskrivits i den här artikeln. Du kan välja att köra en enda undantagshanteringsåtgärd utanför scopet som accepterar hela den filtrerade matrisen med fel och ta bort `For_each` åtgärden. Du kan också inkludera andra `\@result()` användbara egenskaper från svaret som tidigare beskrivits.

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor-loggar

De tidigare mönstren är bra sätt att hantera fel och undantag i en körning, men du kan också identifiera och svara på fel oberoende av själva körningen. [Azure Monitor](../azure-monitor/overview.md) är ett enkelt sätt att skicka alla arbetsflödeshändelser, inklusive alla körnings- och åtgärdsstatusar, till en [Log Analytics-arbetsyta,](../azure-monitor/platform/data-platform-logs.md) [Azure-lagringskonto](../storage/blobs/storage-blobs-overview.md)eller [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Om du vill utvärdera körstatusar kan du övervaka loggar och mått eller publicera dem i valfritt övervakningsverktyg som du föredrar. Ett möjligt alternativ är att strömma alla händelser via Event Hubs till [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). I Stream Analytics kan du skriva livefrågor baserat på avvikelser, medelvärden eller fel från diagnostikloggarna. Du kan använda Stream Analytics för att skicka information till andra datakällor, till exempel köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur en kund skapar felhantering med Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta fler exempel och scenarier för Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
