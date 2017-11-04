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
ms.openlocfilehash: 9af2f71b3d288cc6f4e271d0915545d43a1249bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Med Azure Logikappar innehåller omfattande verktyg och mönster som hjälper dig att kontrollera din integreringar är stabilt och motståndskraftiga mot fel. Alla integration arkitektur utgör utmaningen i att se till att korrekt hantera driftavbrott eller problem från beroende system. Logic Apps gör felhantering en förstklassig miljö, vilket ger dig de verktyg du behöver för att fungera på undantag och fel i dina arbetsflöden.

## <a name="retry-policies"></a>Försök principer

En återförsöksprincip är den mest grundläggande typ av undantag och felhantering. Om en inledande begäran misslyckades eller orsakade timeout (alla förfrågningar som resulterar i en 429 eller 5xx-svar), den här principen definierar om åtgärden bör försöka. Som standard gör alla åtgärder 4 ytterligare gånger över 20 sekunders intervall. Om den första begäranden tar emot en `500 Internal Server Error` svar, arbetsflödesmotorn pausar i 20 sekunder och försöker begäran igen. Om efter alla försök det fortfarande är undantag eller fel, arbetsflödet fortsätter och markerar Åtgärdsstatus som `Failed`.

Du kan konfigurera principer för försök i den **indata** för en viss åtgärd. Du kan till exempel konfigurera en återförsöksprincip för upp till 4 gånger över 1 timme intervall. Fullständig information om indataparametrar finns [arbetsflödesåtgärder och utlösare][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Om du vill att HTTP-åtgärd försök 4 gånger och vänta i 10 minuter mellan varje försök, använder du följande definition:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Mer information om syntax som stöds finns i [återförsöksprincip avsnittet i arbetsflödesåtgärder och utlösare][retryPolicyMSDN].

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
