---
title: Fel- och undantagshantering – Azure Logic Apps | Microsoft Docs
description: Lär dig mer om mönster för fel och undantagshantering i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 19a715812f1250523fd050ac8b80dee9ec664be4
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686270"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Det sätt som alla Integreringsarkitektur på lämpligt sätt hanterar driftavbrott eller problem som orsakas av beroende system kan utgöra en utmaning. Som hjälper dig att skapa en stabil och flexibel integrering som smidigt hanterar problem och fel, ger Logic Apps dig en förstklassig upplevelse för hantering av fel och undantag. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Återförsöksprinciper

Du kan använda för den mest grundläggande undantag och felhantering, en *återförsöksprincip* i en åtgärd eller utlösare, där de stöds. En återförsöksprincip som anger om och hur åtgärd eller utlösare försöker skicka in ett när den ursprungliga begäran når sin tidsgräns eller misslyckas, vilket är alla förfrågningar som resulterar i en 408, 429 och 5xx-svar. Om någon annan återförsöksprincip används, används standardprincipen. 

Här följer återförsök principtyper: 

| Typ | Beskrivning | 
|------|-------------| 
| [**Standard**](#default-retry) | Den här principen skickar upp till fyra återförsök på [ *ökar exponentiellt* ](#exponential-retry) mellanrum, som skalas med 7,5 sekunder men begränsas mellan 5 och 45 sekunder. | 
| [**Exponentiellt intervall**](#exponential-retry)  | Den här principen väntar ett slumpmässigt intervall som väljs från en växande exponentiellt intervall innan nästa förfrågan skickas. | 
| [**Fast intervall**](#fixed-retry)  | Den här principen väntar det angivna intervallet innan nästa förfrågan skickas. | 
| [**Ingen**](#no-retry)  | Inte skicka begäran igen. | 
||| 

Information om gränsen för återförsök princip finns i [Logic Apps gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Ändra återförsöksprincipen

Följ dessa steg för att välja en annan återförsöksprincip: 

1. Öppna logikappen i Logic App Designer. 

2. Öppna den **inställningar** för en åtgärd eller utlösare.

3. Om de åtgärd eller utlösare stöder återförsöksprinciper, under **försök princip**, Välj den typ som du vill. 

Du kan också manuellt ange återförsöksprincipen i den `inputs` för en åtgärd eller utlösare som stöder återförsöksprinciper. Om du inte anger en återförsöksprincip, använder standardprincipen för åtgärden.

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
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Krävs*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*återförsöksprincipstyp-*> | Sträng | Den typen av återförsöksprincip du vill använda: `default`, `none`, `fixed`, eller `exponential` | 
| <*återförsöksintervall*> | Sträng | Återförsöksintervallet där värdet måste använda [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Är standardintervallet för minsta `PT5S` och maximalt intervall är `PT1D`. När du använder exponentiell intervallprincip, kan du ange olika lägsta och högsta värden. | 
| <*nya försök*> | Integer | Antal nya försök, vilket måste vara mellan 1 och 90 | 
||||

*Valfritt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*Minsta intervall*> | Sträng | För exponentiell intervallprincip, det minsta intervallet för slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximalt intervall*> | Sträng | För exponentiell intervallprincip, det största intervallet för slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Här finns mer information om de olika principtyperna.

<a name="default-retry"></a>

### <a name="default"></a>Standard

Om du inte anger en återförsöksprincip åtgärden använder standardprincipen, vilket är faktiskt en [exponentiell intervallprincip](#exponential-interval) som skickar upp till fyra återförsök exponentiellt öka intervaller som skalas med 7,5 sekunder. Intervallet är begränsat mellan 5 och 45 sekunder. 

Även om inte explicit definierats i din åtgärd eller utlösare, är här hur standardprincipen beter sig i ett exempel HTTP-åtgärd:

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

### <a name="none"></a>Ingen

Om du vill ange att åtgärden eller utlösaren inte gör misslyckade förfrågningar, ange den <*återförsöksprincipstyp-*> till `none`.

### <a name="fixed-interval"></a>Fast intervall

Om du vill ange att åtgärd eller utlösare väntar det angivna intervallet innan nästa förfrågan skickas, ange den <*återförsöksprincipstyp-*> till `fixed`.

*Exempel*

En sådan återförsöksprincip försöker få de senaste nyheterna två gånger efter den första misslyckade begäranden med en 30 sekunder fördröjning mellan varje försök:

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

Om du vill ange att åtgärd eller utlösare väntar ett slumpmässigt intervall innan nästa förfrågan skickas, ange den <*återförsöksprincipstyp-*> till `exponential`. Slumpmässigt intervall väljs från ett växande exponentiellt intervall. Du kan eventuellt även åsidosätta standard minsta och största intervall genom att ange dina egna minsta och största intervall.

**Variabeln slumpmässiga intervall**

Den här tabellen visar hur Logic Apps genererar en enhetlig slumpmässiga variabel i det angivna intervallet för varje nytt försök till och med antal återförsök:

| Försök nummer | Minsta intervall | Maximalt intervall |
|--------------|------------------|------------------|
| 1 | Max (0, <*minsta intervall*>) | min (intervall, <*maximalt intervall*>) |
| 2 | Max (intervall, <*minsta intervall*>) | min (2 * intervall, <*maximalt intervall*>) |
| 3 | Max (2 * intervall, <*minsta intervall*>) | min (4 * intervall, <*maximalt intervall*>) |
| 4 | Max (4 * intervall, <*minsta intervall*>) | min (8 * intervall, <*maximalt intervall*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Fånga upp och hantera fel med egenskapen RunAfter

Varje åtgärd för logic app deklarerar de åtgärder som måste slutföras innan åtgärden startar liknar hur du anger ordningen på stegen i arbetsflödet. I åtgärdsdefinitionen av en i **runAfter** egenskapen definierar den här ordningen och är ett objekt som beskriver vilka åtgärder och status för åtgärden Kör åtgärden.

Alla åtgärder som du lägger till i Logic App Designer är som standard ska köras efter det föregående steget när det föregående steget resultatet är **lyckades**. Du kan anpassa den **runAfter** värde så att åtgärder som utlöses när tidigare åtgärder blir **misslyckades**, **ignoreras**, eller en kombination av dessa värden. Till exempel att lägga till ett objekt i ett visst Service Bus-ämne efter en specifik **Insert_Row** åtgärden misslyckas, du kan använda det här exemplet **runAfter** definition:

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

Den **runAfter** är inställd på att köras när den **Insert_Row** åtgärdsstatusen är **misslyckades**. Att köra åtgärden om åtgärdsstatusen är **lyckades**, **misslyckades**, eller **ignoreras**, använder du följande syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Åtgärder som körs och slutförs efter en föregående åtgärd har misslyckats, markeras som **lyckades**. Detta innebär att om du har fånga alla fel i ett arbetsflöde, kör själva markeras som **lyckades**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Utvärdera åtgärder med omfattningar och resultaten

Påminner om att köra stegen efter enskilda åtgärder med den **runAfter** egenskapen, du kan gruppera åtgärder i en [omfång](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Du kan använda omfång, när du vill gruppera åtgärder, utvärdera scopets sammanlagd status och utföra åtgärder baserat på statusen logiskt. När alla åtgärder i ett omfång slutföras, hämtar omfånget själva sin egen status. 

Du kan kontrollera status för ett omfång du kan använda samma villkor som används för att kontrollera körningsstatusen för en logikapp, till exempel **lyckades**, **misslyckades**och så vidare. 

Som standard när alla omfattningar åtgärder lyckas scopets status markeras **lyckades**. Om den sista åtgärden i ett omfång resulterar som **misslyckades** eller **avbrutet**, scopets status markeras **misslyckades**. 

Att fånga upp undantag i en **misslyckades** omfång och kör åtgärder som hanterar dessa fel kan du använda den **runAfter** -egenskapen för som **misslyckades** omfång. På så sätt kan om *alla* åtgärder i omfånget misslyckas och du använder den **runAfter** egenskapen för detta omfång som du kan skapa en enda åtgärd för att fånga upp fel.

Gränser för scope för finns i [begränsningar och konfigurationer](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Hämta kontext och resultat för fel

Identifiering av fel från ett scope är användbart, men du kanske också vill kontext för att hjälpa dig att förstå exakt vilka åtgärder kunde inte plus eventuella fel eller statuskoder som returnerades. Den `@result()` uttryck som ger kontext om resultatet av alla åtgärder i ett omfång.

Den `@result()` uttryck accepterar en parameter (scope-namn) och returnerar en matris med alla åtgärd resultat från inom det omfånget. Dessa åtgärder-objekt omfattar samma attribut som den  **@actions()** objekt, till exempel åtgärdens starttid, sluttid, status, indata, Korrelations-ID: N och utdata. Om du vill skicka kontexten för åtgärder som inte ett område som du lätt kan koppla en  **@result()** fungerar med en **runAfter** egenskapen.

Att köra en åtgärd för varje åtgärd i en omfattning som har en **misslyckades** resultatet, och om du vill filtrera matris med resultat till misslyckade åtgärder, kan du koppla  **@result()** med en **[Filtermatris](../connectors/connectors-native-query.md)** åtgärd och en [ **för var och en** ](../logic-apps/logic-apps-control-flow-loops.md) loop. Du kan ta filtrerat resultat matrisen och utföra en åtgärd för varje fel med hjälp av den **för varje** loop. 

Här är ett exempel, följt av en detaljerad förklaring som skickar en HTTP POST-begäran med svarstexten för åtgärder som inte omfattas ”My_Scope”:

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

1. Att få resultat från alla åtgärder i ”My_Scope” den **Filtermatris** åtgärden använder den här filteruttryck: `@result('My_Scope')`

2. Villkoret för **Filtermatris** valfri `@result()` objekt som har statusen lika **misslyckades**. Det här tillståndet filtrerar den matris som har alla åtgärd resultat från ”My_Scope” till en matris med misslyckad åtgärd resultaten.

3. Utföra en **för var och en** loopa åtgärden på den *filtrerade matris* matar ut. Det här steget utför en åtgärd för varje misslyckad Åtgärdsresultat som tidigare har filtrerats.

   Om en enda åtgärd i omfattningen misslyckades åtgärder i den **för varje** loop som bara körs en gång. 
   Flera misslyckade åtgärder gör att en åtgärd per fel.

4. Skicka en HTTP POST den **för var och en** objektet svarstexten, vilket är den `@item()['outputs']['body']` uttryck. 

   Den `@result()` objekt form är samma som den `@actions()` forma och kan parsas på samma sätt.

5. Innehåller två anpassade huvuden med Åtgärdsnamnet misslyckad (`@item()['name']`) och den kör klienten spårnings-ID (`@item()['clientTrackingId']`).

Här är ett exempel på en enda referens `@result()` objektet, som visar den **namn**, **brödtext**, och **clientTrackingId** egenskaper som parsas i föregående exempel. Utanför en **för var och en** åtgärd, `@result()` returnerar en matris med de här objekten.

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

Du kan använda uttrycken som beskrevs tidigare i den här artikeln för att utföra olika undantagshantering mönster. Du kan välja att köra en enda undantagshantering åtgärd utanför omfattningen som accepterar hela filtrerade matris med fel och ta bort den **för var och en** åtgärd. Du kan även inkludera andra användbara egenskaper från den  **@result()** svar enligt beskrivningen ovan.

## <a name="azure-diagnostics-and-metrics"></a>Azure-diagnostik och mått

Föregående mönster är bra sätt att hantera fel och undantag i en körning, men du kan också identifiera och svara på fel som är oberoende av körningen själva. 
[Azure-diagnostik](../logic-apps/logic-apps-monitor-your-logic-apps.md) gör det enkelt att skicka alla arbetsflödeshändelser, inklusive alla körnings- och åtgärden statusar till ett Azure Storage-konto eller en händelsehubb som skapats med Azure Event Hubs. 

Om du vill utvärdera kör statusar, kan du övervaka loggar och mått eller publicera dem i valfri övervakningsverktyg som du föredrar. Ett potentiellt alternativ är att strömma alla händelser via Event Hubs till [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Du kan skriva live-frågor baserat på alla avvikelser, medelvärden eller fel från diagnostikloggarna i Stream Analytics. Du kan använda Stream Analytics för att skicka information till andra datakällor, till exempel köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur kunder bygger felhantering med Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta fler Logic Apps-exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
