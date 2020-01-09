---
title: Hantera fel och undantag
description: Lär dig hur du hanterar fel och undantag som inträffar i automatiserade uppgifter och arbets flöden som skapats med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: fa197a04b91f398bda2e402b18a638b9bf0ab9a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453398"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Sättet att integrera en integrerings arkitektur på lämpligt sätt hanterar avbrott eller problem som orsakas av beroende system kan medföra en utmaning. För att hjälpa dig att skapa robusta och elastiska integreringar som hanterar problem och fel på ett smidigt sätt ger Logic Apps en förstklassig upplevelse för att hantera fel och undantag. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Principer för nya försök

För flest grundläggande undantag och fel hantering kan du använda en *princip för återförsök* i alla åtgärder eller utlösare där det stöds. En princip för återförsök anger om och hur åtgärden eller utlösaren försöker att utföra en begäran när den ursprungliga tids gränsen för begäran går ut eller Miss lyckas, vilket är en begäran som resulterar i ett 408-, 429-eller 5XX-svar. Om ingen annan princip för återförsök används används standard principen. 

Här är princip typerna för återförsök: 

| Typ | Beskrivning | 
|------|-------------| 
| **Standard** | Den här principen skickar upp till fyra återförsök med *exponentiellt ökande* intervall, som skalas med 7,5 sekunder, men är ett tak mellan 5 och 45 sekunder. | 
| **Exponentiellt intervall**  | Den här principen väntar ett slumpmässigt intervall som väljs från ett exponentiellt växande intervall innan nästa förfrågan skickas. | 
| **Fast intervall**  | Den här principen väntar det angivna intervallet innan nästa förfrågan skickas. | 
| **Ingen**  | Skicka inte begäran igen. | 
||| 

Information om begränsningar för återförsök finns i [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Ändra princip för återförsök

Följ dessa steg om du vill välja en annan princip för återförsök: 

1. Öppna din Logic app i Logic App Designer. 

2. Öppna **inställningarna** för en åtgärd eller utlösare.

3. Om åtgärden eller utlösaren stöder principer för återförsök väljer du den typ som du vill använda under **princip för återförsök**. 

Eller så kan du manuellt ange principen för återförsök i avsnittet `inputs` för en åtgärd eller utlösare som stöder principer för återförsök. Om du inte anger en princip för återförsök använder åtgärden standard principen.

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
| <*retry-policy-type*> | String | Den princip typ för återförsök som du vill använda: `default`, `none`, `fixed`eller `exponential` | 
| <*återförsöksintervall*> | String | Återförsöksintervall där värdet måste använda [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Standard intervallet för minimi intervallet är `PT5S` och det maximala intervallet är `PT1D`. När du använder exponentiell intervall princip kan du ange olika minimi-och max värden. | 
| <*försök-försök*> | Integer | Antalet återförsök som måste vara mellan 1 och 90 | 
||||

*Valfritt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*minimum-interval*> | String | För principen för exponentiella intervall, det minsta intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximalt intervall*> | String | För principen för exponentiella intervall är det största intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Här är mer information om de olika princip typerna.

<a name="default-retry"></a>

### <a name="default"></a>Default

Om du inte anger en princip för återförsök använder åtgärden standard principen, vilket faktiskt är en [exponentiell intervall princip](#exponential-interval) som skickar upp till fyra återförsök med exponentiellt ökande intervall som skalas med 7,5 sekunder. Intervallet är ett tak mellan 5 och 45 sekunder. 

Även om det inte uttryckligen definieras i din åtgärd eller utlösare, så är det här hur standard principen beter sig i ett exempel på HTTP-åtgärd:

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

Om du vill ange att åtgärden eller utlösaren inte ska försöka utföra misslyckade begär Anden, anger du <*återförsök-princip-typ*> att `none`.

### <a name="fixed-interval"></a>Fast intervall

Om du vill ange att åtgärden eller utlösaren ska vänta det angivna intervallet innan du skickar nästa begäran, anger du <*återförsök-princip-typ*> för att `fixed`.

*Exempel*

Den här principen för återförsök försöker hämta de senaste nyheterna två gånger efter den första misslyckade begäran med 30 sekunders fördröjning mellan varje försök:

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

Om du vill ange att åtgärden eller utlösaren ska vänta ett slumpmässigt intervall innan nästa förfrågan skickas, anger du <*återförsök-princip-typ*> för att `exponential`. Det slumpmässiga intervallet väljs från ett exponentiellt växande intervall. Alternativt kan du åsidosätta standard minimi-och Max intervallen genom att ange egna minsta och högsta intervall.

**Slumpmässiga variabel intervall**

I den här tabellen visas hur Logic Apps genererar en enhetlig slumpmässig variabel i det angivna intervallet för varje nytt försök upp till och med antalet återförsök:

| Antal återförsök | Minsta intervall | Maximalt intervall |
|--------------|------------------|------------------|
| 1 | Max (0, <*lägsta intervall*>) | min (intervall, <*maximalt intervall*>) |
| 2 | Max (intervall, <*lägsta intervall*>) | min (2 * intervall, <*maximalt intervall*>) |
| 3 | Max (2 * intervall, <*lägsta-intervall*>) | minsta (4 * intervall, <*maximalt intervall*>) |
| 4 | Max (4 * intervall, <*lägsta-intervall*>) | min (8 * intervall, <*maximalt intervall*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Fånga och hantera problem med egenskapen RunAfter

Varje Logic app-åtgärd deklarerar de åtgärder som måste slutföras innan den här åtgärden startar, på samma sätt som du anger ordningen på stegen i arbets flödet. I en åtgärds definition definierar egenskapen **runAfter** den här ordningen och är ett objekt som beskriver vilka åtgärder och åtgärds status som utför åtgärden.

Som standard är alla åtgärder som du lägger till i Logic App Designer inställda på att köras efter föregående steg när resultatet av föregående steg har **slutförts**. Du kan dock anpassa **runAfter** -värdet så att åtgärder som utlöses när föregående åtgärder resulterar i **fel**, **hoppas över**eller någon kombination av dessa värden. Om du till exempel vill lägga till ett objekt i en speciell Service Bus ämne efter en bestämd **Insert_Row** åtgärd kan du använda den här exempel **runAfter** -definitionen:

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

Egenskapen **runAfter** är inställd på att köras när **Insert_Row** åtgärds status **misslyckades**. Om du vill köra åtgärden om åtgärds statusen har **slutförts**, **misslyckats**eller **hoppats över**, använder du följande syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Åtgärder som körs och slutförs när en föregående åtgärd har misslyckats markeras som **slutförda**. Det innebär att om du har fångat alla fel i ett arbets flöde, markeras själva körningen som **slutförd**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Utvärdera åtgärder med omfattningar och deras resultat

Precis som när du kör steg efter enskilda åtgärder med egenskapen **runAfter** kan du gruppera åtgärder tillsammans i ett [omfång](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Du kan använda omfattningar när du vill gruppera åtgärder logiskt, utvärdera Omfattningens sammanställda status och utföra åtgärder baserat på denna status. När alla åtgärder i en omfattning har slutförts, får själva omfattningen sin egen status. 

Om du vill kontrol lera Omfattningens status kan du använda samma villkor som du använder för att kontrol lera appens körnings status, till exempel **lyckad**, **misslyckad**och så vidare. 

Som standard markeras omfångets status som standard när alla åtgärder i omfånget **lyckades.** Om den slutliga åtgärden i ett omfång resulterar i en **misslyckad** eller **avbruten**, markeras omfångets status som **misslyckad**. 

Om du vill fånga undantag i ett **felaktigt** omfång och köra åtgärder som hanterar dessa fel kan du använda egenskapen **RunAfter** för det **misslyckade** omfånget. På så sätt kan du skapa en enskild åtgärd för att fånga upp felen om *några* åtgärder i omfånget Miss lyckas och du använder egenskapen **runAfter** för den omfattningen.

Begränsningar för omfång finns i [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Få kontext och resultat för problem

Även om det är praktiskt att fånga fel från ett omfång, kan du också behöva kontext för att förstå exakt vilka åtgärder som misslyckats plus eventuella fel eller status koder som returnerades.

Funktionen [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) ger kontext för resultatet från alla åtgärder i ett omfång. Funktionen `result()` accepterar en enda parameter, som är omfångets namn och returnerar en matris som innehåller alla åtgärds resultat inom det omfånget. Dessa åtgärds objekt innehåller samma attribut som `@actions()`-objektet, till exempel start tid, slut tid, status, indata, korrelations-ID och utdata. Om du vill skicka kontext för åtgärder som misslyckats inom ett omfång kan du enkelt para ihop ett `@result()` uttryck med egenskapen `runAfter`.

Om du vill köra en åtgärd för varje åtgärd i en omfattning som har ett **misslyckat** resultat, och för att filtrera matrisen med resultat nedåt till de misslyckade åtgärderna, kan du para ihop ett `@result()` uttryck med en [**filter mat ris**](../connectors/connectors-native-query.md) åtgärd och en [**for each**](../logic-apps/logic-apps-control-flow-loops.md) -slinga. Du kan ta den filtrerade resultat mat ris och utföra en åtgärd för varje haveri med hjälp av **for each** -slingan.

Här är ett exempel, följt av en detaljerad förklaring, som skickar en HTTP POST-begäran med svars texten för åtgärder som misslyckats inom omfånget "My_Scope":

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

Här är en detaljerad genom gång som beskriver vad som händer i det här exemplet:

1. För att få resultatet från alla åtgärder i "My_Scope" använder **filter mat ris** åtgärden detta filter uttryck: `@result('My_Scope')`

2. Villkoret för **filter mat ris** är ett `@result()` objekt som har en status som är lika med **misslyckad**. Det här villkoret filtrerar matrisen som innehåller alla åtgärds resultat från "My_Scope" ned till en matris med endast misslyckade åtgärds resultat.

3. Utför en **för varje** loop-åtgärd på *filtrerade mat ris* utdata. Det här steget utför en åtgärd för varje misslyckat åtgärds resultat som tidigare har filtrerats.

   Om en enskild åtgärd i omfånget misslyckades körs åtgärderna i **för varje** slinga bara en gång. 
   Flera misslyckade åtgärder orsakar en åtgärd per fel.

4. Skicka ett HTTP-inlägg **för varje** objekt svars text, vilket är `@item()['outputs']['body']`-uttrycket. 

   Formen `@result()` objekt är samma som `@actions()`-formen och kan parsas på samma sätt.

5. Inkludera två anpassade huvuden med det misslyckade åtgärds namnet (`@item()['name']`) och det misslyckade körnings klient spårnings-ID: t (`@item()['clientTrackingId']`).

För referens är här ett exempel på ett enda `@result()` objekt, som visar **namn**, **brödtext**och **clientTrackingId** egenskaper som är parsade i föregående exempel. Utanför en **för varje** åtgärd returnerar `@result()` en matris med dessa objekt.

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

Om du vill utföra olika mönster för undantags hantering kan du använda de uttryck som tidigare beskrivits i den här artikeln. Du kan välja att köra en enskild undantags hanterings åtgärd utanför den omfattning som godkänner hela den filtrerade matrisen med fel och ta bort **för varje** åtgärd. Du kan också inkludera andra användbara egenskaper från **\@result ()** -svaret enligt beskrivningen ovan.

## <a name="azure-diagnostics-and-metrics"></a>Azure-diagnostik och mått

De tidigare mönstren är ett bra sätt att hantera fel och undantag i en körning, men du kan också identifiera och svara på fel oberoende av själva körningen. 
[Azure-diagnostik](../logic-apps/logic-apps-monitor-your-logic-apps.md) är ett enkelt sätt att skicka alla arbets flödes händelser, inklusive körnings-och åtgärds status, till ett Azure Storage konto eller en Event Hub som skapats med Azure Event Hubs. 

Om du vill utvärdera körnings status kan du övervaka loggarna och måtten eller publicera dem i alla övervaknings verktyg som du föredrar. Ett möjligt alternativ är att strömma alla händelser genom att Event Hubs i [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). I Stream Analytics kan du skriva Live-frågor baserat på eventuella avvikelser, genomsnitt eller fel från diagnostikloggar. Du kan använda Stream Analytics för att skicka information till andra data källor, till exempel köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur en kund skapar fel hantering med Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta fler Logic Apps exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
