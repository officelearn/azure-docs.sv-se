---
title: Fel & undantagshantering - Azure Logic Apps | Microsoft Docs
description: "Mönster för fel- och undantagshantering i Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 4eb6f743479886374692eadcf218b77b4bfcc933
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Med Azure Logikappar innehåller omfattande verktyg och mönster som hjälper dig att kontrollera din integreringar är stabilt och motståndskraftiga mot fel. Alla integration arkitektur utgör utmaningen i att se till att korrekt hantera driftavbrott eller problem från beroende system. Logic Apps gör felhantering en förstklassig miljö, vilket ger dig de verktyg du behöver för att fungera på undantag och fel i dina arbetsflöden.

## <a name="retry-policies"></a>Försök principer

En återförsöksprincip är den mest grundläggande typ av undantag och felhantering. Om en inledande begäran misslyckades eller orsakade timeout (alla förfrågningar som resulterar i en 429 eller 5xx-svar), den här principen definierar om och hur åtgärden ska försöka igen. Det finns tre typer av principer för återförsök `exponential`, `fixed`, och `none`. Om en återförsöksprincip som har angetts i arbetsflödesdefinitionen, används standardprincipen. Du kan konfigurera principer för försök i den **indata** för en särskild funktion eller utlösare om det är återförsökbart. På samma sätt i försöket Logic App Designer principer kan konfigureras (om tillämpligt) under den **inställningar** för ett visst block.

Mer information om begränsningar för återförsök principer finns [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md) och mer information om syntax som stöds finns i [återförsöksprincip avsnittet i arbetsflödesåtgärder och utlösare][retryPolicyMSDN].

### <a name="exponential-interval"></a>Exponentiell intervall
Den `exponential` principtypen försöker en misslyckad begäran efter ett slumpmässigt tidsintervall från ett exponentiellt växande intervall. Varje nytt försök garanterat skickas med slumpmässiga intervall som är större än **minimumInterval** och mindre än **maximumInterval**. En enhetlig slumpmässiga variabel i den under intervallet kommer att genereras för varje försök till och med **antal**:
<table>
<tr><th> Slumpmässiga variabeln intervall </th></tr>
<tr><td>

| Antal försök | Minsta intervall | Maximalt intervall |
| ------------ |  ------------ |  ------------ |
| 1 | Max (0, **minimumInterval**) | Min (intervall, **maximumInterval**) |
| 2 | Max (intervall, **minimumInterval**) | Min (2 * intervall, **maximumInterval**) |
| 3 | Max (2 * intervall, **minimumInterval**) | Min (4 * intervall, **maximumInterval**) |
| 4 | Max (4 * intervall, **minimumInterval**) | Min (8 * intervall, **maximumInterval**) |
| ... |

</td></tr></table>

För `exponential` skriver principer, **antal** och **intervall** krävs när **minimumInterval** och **maximumInterval** kan vara Du kan också ange om du vill åsidosätta standardvärden för PT5S och PT1D respektive.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | `exponential` |
| antal | Ja | Integer | antal omförsök, måste vara mellan 1 och 90  |
| interval | Ja | Sträng | återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), måste vara mellan PT5S och PT1D |
| minimumInterval | Nej| Sträng | minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), måste vara mellan PT5S och **intervall** |
| maximumInterval | Nej| Sträng | minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), måste vara mellan **intervall** och PT1D |

### <a name="fixed-interval"></a>Fasta intervaller

Den `fixed` principtypen försöker en misslyckad begäran genom att vänta med det angivna tidsintervall innan du skickar nästa begäran.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | `fixed`|
| antal | Ja | Integer | antal omförsök, måste vara mellan 1 och 90 |
| interval | Ja | Sträng | återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), måste vara mellan PT5S och PT1D |

### <a name="none"></a>Ingen
Den `none` principtypen försöker inte återställa en misslyckad begäran.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | `none`|

### <a name="default"></a>Standard
Om ingen återförsöksprincip har angetts används standardprincipen. Standardprincipen är en princip för exponentiell intervall som sedan skickar upp till 4 återförsök exponentiellt öka intervall skalas 7.5 sekunder och begränsat till mellan 5 och 45 sekunder. Den här standardprincipen (används när **retryPolicy** är odefinierat) motsvarar principen i det här exemplet HTTP arbetsflödesdefinitionen:

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

## <a name="catch-failures-with-the-runafter-property"></a>Catch-fel med egenskapen RunAfter

Varje logik app åtgärd anger vilka åtgärder som måste slutföras innan åtgärden startar, t.ex. sortera stegen i arbetsflödet. I åtgärdsdefinitionen den här ordningen kallas den `runAfter` egenskapen. Den här egenskapen är ett objekt som beskriver vilka åtgärder och status för åtgärden att utföra åtgärden. Alla åtgärder som har lagts till via logik App Designer är som standard `runAfter` föregående steg om det förra steget `Succeeded`. Du kan anpassa värdet för att utlösa åtgärder när tidigare åtgärder har `Failed`, `Skipped`, eller en möjlig uppsättning med dessa värden. Om du vill lägga till ett objekt till en avsedda Service Bus-ämne efter en viss åtgärd `Insert_Row` misslyckas, kan du använda följande `runAfter` konfiguration:

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

Observera den `runAfter` -egenskapen anges eller om den `Insert_Row` åtgärden är `Failed`. Att köra instruktionen om Åtgärdsstatus är `Succeeded`, `Failed`, eller `Skipped`, använder du följande syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Åtgärder som körs och slutföras efter en föregående åtgärd har misslyckats, markeras som `Succeeded`. Detta innebär att om du har fånga alla fel i ett arbetsflöde, kör själva har markerats som `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Omfång och resultat för att utvärdera åtgärder

Liknar hur du kan köra efter enskilda åtgärder du kan också gruppera åtgärder i en [omfång](../logic-apps/logic-apps-loops-and-scopes.md), som fungerar som en logisk gruppering av åtgärder. Scope är användbara både för att organisera dina logic app åtgärder och för att utföra sammanställd utvärderingar på status för ett omfång. Området själva får status när alla åtgärder i ett scope är klar. Scope-status bestäms med samma kriterier som körs. Om sista åtgärden i en körning grenen `Failed` eller `Aborted`, status är `Failed`.

Du kan använda för att utlösa specifika åtgärder efter fel som inträffade inom omfånget `runAfter` med en omfattning som är markerad `Failed`. Om *alla* det gick inte att utföra åtgärder i omfånget, kör ett scope misslyckas kan du skapa en enda åtgärd för att fånga fel.

### <a name="getting-the-context-of-failures-with-results"></a>Få kontext fel med resultat

Fånga fel från ett scope är användbart, men du kanske också vill kontext för att hjälpa dig att förstå exakt vilka åtgärder som misslyckades, och eventuella fel eller statuskoder som returnerades. Den `@result()` arbetsflödesfunktion ger kontext om resultatet av alla åtgärder i en omfattning.

`@result()`tar en enda parameter, scope-namn och returnerar en matris med alla åtgärd resultat från i omfattningen. Åtgärd eller skriva in samma attribut som den `@actions()` objektet, inklusive åtgärd starttid, sluttid för åtgärd, Åtgärdsstatus, åtgärden indata, åtgärden Korrelations-ID: N och åtgärden matar ut. Om du vill skicka kontexten för alla åtgärder som misslyckades i ett omfång som du lätt kan koppla en `@result()` fungerar med en `runAfter`.

Att köra en åtgärd *för varje* åtgärd i en omfattning som `Failed`, filtrera matris av resultaten till åtgärder som har misslyckats, kan du koppla `@result()` med en  **[Filter matris](../connectors/connectors-native-query.md)**  åtgärd och en  **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)**  loop. Du kan ta matrisen filtrerade resultat och utföra en åtgärd för varje fel med hjälp av den **ForEach** loop. Här är ett exempel, följt av en detaljerad förklaring som skickar en HTTP POST-begäran med brödtext för svar för alla åtgärder som inte omfattas `My_Scope`.

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

Här följer en detaljerad genomgång som beskriver vad som händer:

1. Att hämta resultatet av alla åtgärder inom `My_Scope`, **Filter matris** åtgärdsfilter `@result('My_Scope')`.

2. Villkoret för **Filter matris** valfri `@result()` objekt som har status som är lika med `Failed`. Det här villkoret filtrerar matris med alla åtgärd resultat från `My_Scope` till en matris med endast misslyckade åtgärden resultat.

3. Utföra en **för varje** åtgärd på den **filtrerade matris** matar ut. Det här steget utför en åtgärd *för varje* misslyckades åtgärden resultat som tidigare har filtrerats.

    Om en enda åtgärd i omfånget misslyckats åtgärder i den `foreach` bara körs en gång. 
    Många misslyckade åtgärder gör att en åtgärd per fel.

4. Skicka en HTTP POST på den `foreach` objektet brödtext för svar eller `@item()['outputs']['body']`. Den `@result()` form är samma som den `@actions()` form och kan parsas på samma sätt.

5. Innehåller två anpassade huvuden med misslyckade åtgärdsnamn `@item()['name']` och den kör klienten spårnings-ID `@item()['clientTrackingId']`.

Här är ett exempel på en enda referens `@result()` objektet, visar den `name`, `body`, och `clientTrackingId` egenskaper som parsas i föregående exempel. Utanför en `foreach`, `@result()` returnerar en matris med de här objekten.

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

Du kan använda uttryck som visats för att utföra olika undantagshantering mönster. Du kan välja att utföra en enda undantagshantering åtgärd utanför omfånget som accepterar hela filtrerade matrisen fel och ta bort den `foreach`. Du kan även inkludera andra användbara egenskaper från den `@result()` svar som har visats.

## <a name="azure-diagnostics-and-telemetry"></a>Azure-diagnostik och telemetri

Tidigare mönstren är bra sätt att hantera fel och undantag inom en körning, men du kan också identifiera och svara på fel som är oberoende av körningen sig själv. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) gör det enkelt att skicka alla arbetsflödeshändelser (inklusive status för alla kör och åtgärden) till ett Azure Storage-konto eller ett Azure-Händelsehubb. Om du vill utvärdera kör status, kan du övervaka loggar och mått eller publicera dem i alla övervakningsverktyg som du föredrar. En potentiell alternativ är att strömma alla händelser via Azure Event Hub i [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Du kan skriva live frågor av alla avvikelser, medelvärden och fel från diagnostiska loggar i Stream Analytics. Stream Analytics kan enkelt utdata till andra datakällor som köer, ämnen, SQL, Azure Cosmos DB och Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur en kund bygger felhantering med Azure Logikappar](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta mer Logic Apps exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Lär dig hur du skapar automatiserad distribution för logic apps](../logic-apps/logic-apps-create-deploy-template.md)
* [Skapa och distribuera Logic Apps i Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
