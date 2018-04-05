---
title: Fel- och undantagshantering för Logic Apps i Azure | Microsoft Docs
description: Mönster för fel- och undantagshantering i Logic Apps.
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Hantera fel och undantag i Logic Apps

Korrekt kan hantering driftavbrott eller problem från beroende system utgöra en utmaning för alla integration arkitektur. Om du vill skapa en stabil integrering som är elastiska mot problem och fel, erbjuder Logic Apps en förstklassig miljö för hantering av fel och undantag. 

## <a name="retry-policies"></a>Försök principer

Du kan använda återförsöksprincipen för den mest grundläggande undantag och felhantering. Om en inledande begäran misslyckades eller orsakade timeout, som är en begäran att resulterar i en 429 eller 5xx-svar, principen definierar om och hur åtgärden försöker begäran. 

Det finns fyra typer av principer för återförsök: standard, ingen fast intervall och exponentiell intervall. Om din arbetsflödesdefinitionen inte har en återförsöksprincip, används standardprincipen, som definieras av tjänsten, i stället.

Om du vill konfigurera försök principer, om tillämpligt, öppna logik App Designer för din logikapp och gå till **inställningar** för en specifik åtgärd i din logikapp. Eller så kan du definiera principer för försök i den **indata** avsnittet för en specifik åtgärd eller utlösare, om återförsökbart i din arbetsflödesdefinitionen. Här är den allmänna syntaxen:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Mer information om syntax och **indata** avsnittet, finns det [återförsöksprincip avsnittet i arbetsflödesåtgärder och utlösare][retryPolicyMSDN]. Information om begränsningar för återförsök princip finns [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Standardvärde

När du inte definierar en återförsöksprincip i den **retryPolicy** avsnittet logikappen använder standardprincipen, som är en [exponentiell intervall princip](#exponential-interval) som skickar upp till fyra återförsök vid exponentiellt öka intervall som skalas 7.5 sekunder. Intervallet är begränsat mellan 5 och 45 sekunder. Den här principen motsvarar principen i det här exemplet HTTP arbetsflödesdefinitionen:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Inget

Om du ställer in **retryPolicy** till **ingen**, misslyckade begäranden försök inte i den här principen.

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| typ | Ja | Sträng | **none** | 
||||| 

### <a name="fixed-interval"></a>Fasta intervaller

Om du ställer in **retryPolicy** till **fast**, den här principen försöker en misslyckad begäran genom att vänta med det angivna intervallet för tid innan nästa begäran skickas.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | **Fast** |
| antal | Ja | Heltal | Antal nya försök, vilket måste vara mellan 1 och 90 | 
| intervall | Ja | Sträng | Återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), som måste vara mellan PT5S och PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponentiell intervall

Om du ställer in **retryPolicy** till **exponentiell**, den här principen försöker en misslyckad begäran efter ett slumpmässigt tidsintervall från ett exponentiellt växande intervall. Principen garanterar även att skicka varje nytt försök med ett slumpmässigt intervall som är större än **minimumInterval** och mindre än **maximumInterval**. Exponentiell principer kräver **antal** och **intervall**, medan värden för **minimumInterval** och **maximumInterval** är valfria. Om du vill åsidosätta standardvärden PT5S och PT1D respektive kan du lägga till dessa värden.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | **exponential** |
| antal | Ja | Heltal | Antal nya försök, vilket måste vara mellan 1 och 90  |
| intervall | Ja | Sträng | Återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), som måste vara mellan PT5S och PT1D. |
| minimumInterval | Nej | Sträng | Minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), som måste vara mellan PT5S och **intervall** |
| maximumInterval | Nej | Sträng | Minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), som måste vara mellan **intervall** och PT1D | 
||||| 

Den här tabellen visar hur en enhetlig slumpmässiga variabel i det angivna intervallet genereras för varje nytt försök till och med **antal**:

**Variabeln slumpmässiga intervall**

| Antal försök | Minsta intervall | Maximalt intervall |
| ------------ | ---------------- | ---------------- |
| 1 | Max (0, **minimumInterval**) | Min (intervall, **maximumInterval**) |
| 2 | Max (intervall, **minimumInterval**) | Min (2 * intervall, **maximumInterval**) |
| 3 | Max (2 * intervall, **minimumInterval**) | Min (4 * intervall, **maximumInterval**) |
| 4 | Max (4 * intervall, **minimumInterval**) | Min (8 * intervall, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Fånga och hantera fel med egenskapen RunAfter

Varje logik app åtgärd deklarerar de åtgärder som måste slutföras innan åtgärden startar liknar hur du anger ordningen på stegen i arbetsflödet. I en i åtgärdsdefinitionen av **runAfter** egenskapen definierar den här ordningen och är ett objekt som beskriver vilka åtgärder och status för åtgärden att utföra åtgärden.

Alla åtgärder som du lägger till i logik App Designer är som standard ska köras efter det föregående steget när resultatet av föregående steg är **lyckades**. Du kan dock anpassa det **runAfter** värdet så att åtgärder eller när tidigare åtgärder resulterar som **misslyckades**, **ignoreras**, eller en kombination av dessa värden. Till exempel för att lägga till ett objekt till ett visst Service Bus-ämne efter en specifik **Insert_Row** åtgärden misslyckas, kan du använda det här exemplet **runAfter** definition:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Den **runAfter** egenskap är inställd på att köras när den **Insert_Row** Åtgärdsstatus är **misslyckades**. Att köra instruktionen om Åtgärdsstatus är **lyckades**, **misslyckades**, eller **ignoreras**, använder du följande syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Åtgärder som körs och slutförs efter en föregående åtgärd har misslyckats, markeras som **lyckades**. Detta innebär att om du har fånga alla fel i ett arbetsflöde, kör själva har markerats som **lyckades**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Utvärdera åtgärder med scope och deras resultat

Liknar köra steg efter enskilda åtgärder med den **runAfter** egenskap, du kan gruppera åtgärder i en [omfång](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Du kan använda scope när du vill gruppera åtgärder, utvärdera scopets sammanlagd status och utföra åtgärder baserat på denna status logiskt. När alla åtgärder i en omfattning klara så hämtar omfånget själva sin egen status. 

Om du vill kontrollera status för ett omfång kan du använda samma villkor som används för att kontrollera status för en logikapp kör som **lyckades**, **misslyckades**och så vidare. 

Som standard när alla omfattningar åtgärder lyckas scopets status markeras **lyckades**. Om den sista åtgärden i en omfattning som **misslyckades** eller **avbrutet**, scopets status markeras **misslyckades**. 

Fånga undantag i en **misslyckades** omfång och kör åtgärder som ska hantera dessa fel, kan du använda den **runAfter** -egenskapen för som **misslyckades** omfång. På så sätt kan om *alla* åtgärder i omfånget misslyckas och du använder den **runAfter** -egenskapen för det omfånget som du kan skapa en enda åtgärd för att fånga fel.

Gränserna för scope finns [gränser och config](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Hämta kontext och resultat för fel

Fånga fel från ett scope är användbart, men du kanske också vill kontext för att hjälpa dig att förstå exakt vilka åtgärder misslyckades plus fel eller statuskoder som returnerades. Den  **@result()** arbetsflödesfunktion ger kontext om resultatet av alla åtgärder i en omfattning.

Den  **@result()** funktionen accepterar en parameter (scope-namn) och returnerar en matris med alla åtgärd resultat från i omfattningen. Åtgärd eller skriva in samma attribut som den  **@actions()** objekt, t.ex åtgärdens starttid, sluttid, status, indata, Korrelations-ID: N och utdata. Om du vill skicka kontext för åtgärder som inte godkänts i ett omfång som du lätt kan koppla en  **@result()** fungerar med en **runAfter** egenskapen.

Att köra en åtgärd *för varje* åtgärden i en omfattning som har en **misslyckades** resultat, och om du vill filtrera matris av resultaten till misslyckade åtgärder du kan koppla  **@result()** med en **[Filter matris](../connectors/connectors-native-query.md)** åtgärd och en **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** loop. Du kan ta matrisen filtrerade resultat och utföra en åtgärd för varje fel med hjälp av den **ForEach** loop. 

Här är ett exempel, följt av en detaljerad förklaring som skickar en HTTP POST-begäran med brödtext för svar för alla åtgärder som inte omfattas ”My_Scope”:

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Här följer en detaljerad genomgång som beskriver vad som händer i det här exemplet:

1. Att hämta resultatet av alla åtgärder inom ”My_Scope” den **Filter matris** åtgärdsfilter  **@result(My_Scope)**.

2. Villkoret för **Filter matris** valfri  **@result()** objekt som har en status som är lika med **misslyckades**. Det här villkoret filtrerar matris med alla åtgärd resultat från ”My_Scope” till en matris med endast misslyckad åtgärd resultat.

3. Utföra en **för varje** loop-åtgärden på den *filtrerade matris* matar ut. Det här steget utför en åtgärd *för varje* misslyckades åtgärden resultat som tidigare har filtrerats.

   Om en enda åtgärd i omfånget misslyckades, åtgärder i den **foreach** bara körs en gång. 
   Flera misslyckade åtgärder gör att en åtgärd per fel.

4. Skicka en HTTP POST på den **foreach** objektet svarstexten är  **@item() ['utdata'] [brödtext]**. Den  **@result()** form är samma som den  **@actions()** form och kan parsas på samma sätt.

5. Innehåller två anpassade huvuden med misslyckade åtgärdsnamn  **@item() [name]** och den kör klienten spårnings-ID  **@item() [clientTrackingId]**.

Här är ett exempel på en enda referens  **@result()** objektet, visar den **namn**, **brödtext**, och **clientTrackingId** egenskaper som parsas i föregående exempel. Utanför en **foreach** åtgärd,  **@result()** returnerar en matris med de här objekten.

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

Du kan använda uttryck som beskrevs tidigare i den här artikeln för att utföra olika undantagshantering mönster. Du kan välja att utföra en enda undantagshantering åtgärd utanför omfånget som accepterar hela filtrerade matrisen fel och ta bort den **foreach** åtgärd. Du kan även inkludera andra användbara egenskaper från den  **@result()** svar enligt beskrivningen ovan.

## <a name="azure-diagnostics-and-telemetry"></a>Azure-diagnostik och telemetri

Tidigare mönstren är bra sätt att hantera fel och undantag inom en körning, men du kan också identifiera och svara på fel som är oberoende av körningen sig själv. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) gör det enkelt att skicka alla arbetsflödeshändelser, inklusive alla kör och åtgärden status till ett Azure Storage-konto eller en händelsehubb som skapats med Händelsehubbar i Azure. 

Du kan övervaka loggar och mått eller publicera dem i alla verktyg som du föredrar för att utvärdera kör status. En potentiell alternativ är att strömma alla händelser med Händelsehubbar i [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Du kan skriva live frågor baserat på eventuella avvikelser, medelvärden eller fel från diagnostiska loggar i Stream Analytics. Du kan använda Stream Analytics för att skicka information till andra datakällor, till exempel köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur en kund bygger felhantering med Azure Logikappar](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta mer Logic Apps exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9