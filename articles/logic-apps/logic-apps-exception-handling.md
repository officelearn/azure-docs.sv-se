---
title: "Fel- och undantagshantering för Logic Apps i Azure | Microsoft Docs"
description: "Mönster för fel- och undantagshantering i Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Hantera fel och undantag i Logic Apps

Logic Apps i Azure ger omfattande verktyg och mönster för att säkerställa att din integreringar är stabilt och motståndskraftiga mot fel. Oavsett integration arkitektur utgör utmaningen i att korrekt hantera driftavbrott eller utfärdar från beroende system. Logic Apps gör fel för hantering av en förstklassig miljö. Den ger dig de verktyg du behöver för att fungera på undantag och fel i dina arbetsflöden.

## <a name="retry-policies"></a>Försök principer

En återförsöksprincip är den mest grundläggande typ av undantag och felhantering. Om en inledande begäran når sin tidsgräns eller misslyckas (alla förfrågningar som resulterar i en 429 eller 5xx-svar), en återförsöksprincip som definierar om och hur åtgärden bör provas igen. 

Det finns fyra typer av principer för återförsök: standard, ingen fast intervall och exponentiell intervall. Om en återförsöksprincip som har angetts i arbetsflödesdefinitionen, används standardprincipen som definieras av tjänsten. 

Du kan konfigurera principer för försök i den *indata* för en särskild funktion eller utlösare om det är återförsökbart. Du kan dessutom konfigurera försök igen principer (om tillämpligt) i logik App Designer. Om du vill konfigurera en återförsöksprincip i logik App Designer går du till **inställningar** för en specifik åtgärd.

Information om begränsningar för återförsök principer finns i [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). Mer information om syntax som stöds finns i [försök principavdelningen i arbetsflödesåtgärder och utlösare][retryPolicyMSDN].

### <a name="default"></a>Standard

Om du inte definierar en återförsöksprincip (**retryPolicy** är odefinierat), standardprincipen används. Standardprincipen är en princip för exponentiell intervall som skickar upp till fyra återförsök exponentiellt öka skalas 7.5 sekunders intervall. Intervallet är begränsat till mellan 5 och 45 sekunder. Den här standardprincipen motsvarar principen i det här exemplet HTTP arbetsflödesdefinitionen:

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

### <a name="none"></a>Ingen

Om **retryPolicy** är inställd på **ingen**, en misslyckad begäran är inte ett nytt försök.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | **Ingen** |

### <a name="fixed-interval"></a>Fasta intervaller

Om **retryPolicy** är inställd på **fast**, principen försöker en misslyckad begäran genom att vänta med det angivna intervallet för tid innan nästa begäran skickas.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | **fast** |
| antal | Ja | Integer | Antal nya försök. Måste vara mellan 1 och 90. |
| interval | Ja | Sträng | Återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Måste vara mellan PT5S och PT1D. |

### <a name="exponential-interval"></a>Exponentiell intervall

Om **retryPolicy** är inställd på **exponentiell**, principen försöker en misslyckad begäran efter ett slumpmässigt tidsintervall från ett exponentiellt växande intervall. Varje nytt försök garanterat skickas med slumpmässiga intervall som är större än **minimumInterval** och mindre än **maximumInterval**. En enhetlig slumpmässiga variabel i området som anges i följande tabell skapas för varje försök till och med **antal**:

**Variabeln slumpmässiga intervall**

| Antal försök | Minsta intervall | Maximalt intervall |
| ------------ |  ------------ |  ------------ |
| 1 | Max (0, **minimumInterval**) | Min (intervall, **maximumInterval**) |
| 2 | Max (intervall, **minimumInterval**) | Min (2 * intervall, **maximumInterval**) |
| 3 | Max (2 * intervall, **minimumInterval**) | Min (4 * intervall, **maximumInterval**) |
| 4 | Max (4 * intervall, **minimumInterval**) | Min (8 * intervall, **maximumInterval**) |
| ... |

Exponentiell typen principer för **antal** och **intervall** krävs. Värden för **minimumInterval** och **maximumInterval** är valfria. Du kan lägga till dem om du vill åsidosätta standardvärden för PT5S och PT1D, respektive.

| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| typ | Ja | Sträng | **exponentiell** |
| antal | Ja | Integer | Antal nya försök. Måste vara mellan 1 och 90.  |
| interval | Ja | Sträng | Återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Måste vara mellan PT5S och PT1D. |
| minimumInterval | Nej | Sträng | Minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Måste vara mellan PT5S och **intervall**. |
| maximumInterval | Nej | Sträng | Minsta återförsöksintervall i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Måste vara mellan **intervall** och PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Catch-fel med egenskapen runAfter

Varje logik app åtgärd anger vilka åtgärder som måste slutföras innan åtgärden startar. Den liknar ordning stegen i arbetsflödet. I åtgärdsdefinitionen den här ordningen kallas den **runAfter** egenskapen. 

Den **runAfter** egenskapen är ett objekt som beskriver vilka åtgärder och status för åtgärden att utföra åtgärden. Alla åtgärder som du har lagt till med hjälp av Logic App Designer är som standard ska köras efter det föregående steget, om det föregående steg resultatet är **lyckades**. 

Du kan anpassa den **runAfter** värde som ska utlösa åtgärder när föregående åtgärder har ett resultat av **misslyckades**, **ignoreras**, eller en möjlig uppsättning med dessa värden. Om du vill lägga till ett objekt till ett avsedda Azure Service Bus-ämne efter en viss åtgärd **Insert_Row** misslyckas, kan du använda följande **runAfter** konfiguration:

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

Observera att **runAfter** anges eller om den **Insert_Row** åtgärd resultatet är **misslyckades**. Att köra instruktionen om Åtgärdsstatus är **lyckades**, **misslyckades**, eller **ignoreras**, använder du följande syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Åtgärder som körs och slutförs efter en föregående åtgärd har misslyckats markeras som **lyckades**. Detta innebär att om du har fånga alla fel i ett arbetsflöde, kör själva har markerats som **lyckades**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Omfång och resultat för att utvärdera åtgärder

Du kan gruppera åtgärder i en [omfång](../logic-apps/logic-apps-loops-and-scopes.md), ungefär samma sätt som du kör efter enskilda åtgärder. Ett omfång fungerar en logisk gruppering av åtgärder. 

Scope är användbara för både för att organisera dina Logikapp åtgärder och för att utföra sammanställd utvärderingar på status för ett omfång. Området själva får status när alla åtgärder i ett scope är klar. Scope-status bestäms med samma kriterier som körs. Om sista åtgärden i en körning grenen **misslyckades** eller **avbrutet**, status är **misslyckades**.

Du kan använda för att utlösa specifika åtgärder efter fel som inträffat inom omfånget **runAfter** med en omfattning som har markerats **misslyckades**. Om *alla* åtgärder i omfånget misslyckas om du använder **runAfter** för ett omfång kan du skapa en enda åtgärd för att fånga fel.

### <a name="get-the-context-of-failures-with-results"></a>Hämta kontext fel med resultat

Fånga fel från ett scope är användbart, men du kanske också vill kontext för att förstå exakt vilka åtgärder misslyckades och att förstå fel eller statuskoder som returnerades. Den  **@result()** arbetsflödesfunktion ger kontext om resultatet av alla åtgärder i en omfattning.

Den  **@result()** funktionen tar en parameter (scopenamn) och returnerar en matris med alla åtgärd resultat från i omfattningen. Åtgärd eller skriva in samma attribut som den  **@actions()** objektet, inklusive åtgärd starttid, sluttid för åtgärd, Åtgärdsstatus, åtgärden indata, åtgärden Korrelations-ID: N och åtgärden matar ut. 

Om du vill skicka kontexten för alla åtgärder som misslyckades i ett omfång som du lätt kan koppla en  **@result()** fungerar med en **runAfter** egenskapen.

Att köra en åtgärd *för varje* åtgärden i en omfattning som har en **misslyckades** resultat, och om du vill filtrera matris av resultaten till åtgärder som inte kan kombineras  **@result()** med en [Filter_array](../connectors/connectors-native-query.md) åtgärd och en [foreach](../logic-apps/logic-apps-loops-and-scopes.md) loop. Med filtrerade resultat-matris du utför en åtgärd för varje fel med hjälp av den **foreach** loop. 

Här följer ett exempel som skickar en HTTP POST-begäran med brödtext för svar för alla åtgärder som misslyckades i området My_Scope:

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

Här följer en detaljerad genomgång som beskriver vad som händer i föregående exempel:

1. Att hämta resultatet av alla åtgärder inom My_Scope, den **Filter_array** åtgärdsfilter  **@result(My_Scope)**.

2. Villkoret för **Filter_array** valfri  **@result()** objekt som har en status som är lika med **misslyckades**. Det här villkoret filtrerar matrisen från alla åtgärd resultat från My_Scope, till en matris med endast misslyckad åtgärd resultat.

3. Utföra en **foreach** åtgärd på den *filtrerade matris* matar ut. Det här steget utför en åtgärd *för varje* misslyckades åtgärden resultat som tidigare har filtrerats.

    Om en enda åtgärd i omfånget misslyckades, åtgärder i den **foreach** bara körs en gång. Flera misslyckade åtgärder gör att en åtgärd per fel.

4. Skicka en HTTP POST på den **foreach** objektet brödtext för svar eller  **@item() ['utdata'] [brödtext]**. Den  **@result()** form är samma som den  **@actions()** form. Den kan parsas på samma sätt.

5. Innehåller två anpassade huvuden med misslyckade åtgärdsnamn  **@item() [name]** och den kör klienten spårnings-ID  **@item() [clientTrackingId]**.

Här är ett exempel på en enda referens  **@result()** objekt. Den visar den **namn**, **brödtext**, och **clientTrackingId** egenskaper som parsas i föregående exempel. Utanför en **foreach** åtgärd,  **@result()** returnerar en matris med de här objekten.

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

För olika undantagshantering mönster, kan du använda uttryck beskrivs tidigare i artikeln. Du kan välja att utföra en enda undantagshantering åtgärd utanför omfånget som accepterar hela filtrerade matrisen fel och ta bort den **foreach**. Du kan även inkludera andra användbara egenskaper från den  **@result()** svar, som tidigare beskrivits.

## <a name="azure-diagnostics-and-telemetry"></a>Azure-diagnostik och telemetri

De mönster som beskrivs i den här artikeln ger bra sätt att hantera fel och undantag i en körning, men du kan också identifiera och svara på fel som är oberoende av körningen sig själv. [Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) gör det enkelt att skicka alla arbetsflödeshändelser (inklusive status för alla kör och åtgärden) till ett Azure storage-konto eller till en händelsehubb i Händelsehubbar i Azure. 

Om du vill utvärdera kör status, kan du övervaka loggar och mått eller publicera dem på alla verktyg som du föredrar. En potentiell alternativ är att strömma alla händelser via Händelsehubbar till [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Du kan skriva live frågor baserat på eventuella avvikelser, medelvärden eller fel från diagnostiska loggar i Stream Analytics. Du kan använda Stream Analytics för att skicka information till andra datakällor, som till köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* Se hur en kund [bygger felhantering med Logic Apps i Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Hitta mer [Logic Apps exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md).
* Lär dig hur du skapar [automatiserade distributioner för logikappar](../logic-apps/logic-apps-create-deploy-template.md).
* Lär dig hur du [skapa och distribuera logikappar med Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
