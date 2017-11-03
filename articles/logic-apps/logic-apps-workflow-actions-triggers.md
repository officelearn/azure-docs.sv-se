---
title: "Arbetsflödet utlösare och åtgärder - Azure Logic Apps | Microsoft Docs"
description: "Mer information om vilka typer av utlösare och åtgärder som du kan använda för att skapa och automatisera arbetsflöden och processer med Azure Logikappar"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 7e0266cdc477715a5d2f9067c6dcea73da9ba763
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Utlösare och åtgärder för logik app arbetsflöden

Alla logikappar börja med en utlösare följt av åtgärder. Det här avsnittet beskrivs typerna av utlösare och åtgärder som du kan använda för att skapa integreringar system och automatisera verksamheten arbetsflöden eller processer genom att skapa logikappar. 
  
## <a name="triggers-overview"></a>Utlösare: översikt 

Alla logikappar börja med en utlösare som anger de anrop som kan starta en logikapp som körs. Här följer två sätt som du kan börja initiera en körning av arbetsflödet:  

* En avsökning utlösare  
* En push-utlösare som anropar den [arbetsflöde Service REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Alla utlösare innehålla de översta elementen:  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>Typer av utlösare och indata  

Varje typ av utlösare som har ett annat nätverkskort och annan *indata* som definierar sitt beteende. 

| Typ av utlösare | Beskrivning | 
| ------------ | ----------- | 
| **Upprepning** | Utlöses baserat på ett definierat schema. Du kan ange ett framtida datum och tid för startar den här utlösaren. Baserat på hur ofta du kan också ange tider och dagar för att köra arbetsflödet. | 
| **Förfrågan**  | Gör din logikapp till en slutpunkt som du kan anropa, även kallat en ”manuell” utlösare. | 
| **HTTP** | Kontrollerar, eller *polls*, en HTTP-slutpunkt för webbprogram. HTTP-slutpunkten måste överensstämma med en specifik utlösande kontrakt med hjälp av en ”202” asynkront mönster eller returnerar en matris. | 
| **ApiConnection** | Avsöker som en HTTP-utlösare, men använder [Microsoft-hanterade API: er](../connectors/apis-list.md). | 
| **HTTPWebhook** | Gör din logikapp i en anropsbar slutpunkt som utlösaren begäran men anropar en angiven URL för registrering och avregistrering. |
| **ApiConnectionWebhook** | Fungerar som den **HTTPWebhook** utlösare, men använder Microsoft-hanterade API: er. | 
||| 

Information om annan information, se [språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md). 
  
## <a name="recurrence-trigger"></a>Utlösare för upprepning  

Den här utlösaren körs beroende på upprepning och schema som du anger och ger ett enkelt sätt för att regelbundet köra ett arbetsflöde. Här är ett exempel på grundläggande upprepning utlösare som körs varje dag:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```
Du kan också schemalägga ett startdatum och tidpunkt för startar utlösaren. Till exempel om du vill starta en vecka rapport varje måndag, kan du schemalägga logikappen att starta på en specifik måndag som det här exemplet: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

Här är definitionen för denna utlösare: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Ja | Sträng | Tidsenhet för hur ofta utlösaren utlöses. Använd bara ett av följande värden: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| interval | Ja | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på hur ofta. <p>Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
| Tidszon | Nej | Sträng | Gäller endast när du anger en starttid eftersom utlösaren inte acceptera [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Ange den tidszon som du vill använda. | 
| startTime | Nej | Sträng | Ange startdatum och tidpunkt i det här formatet: <p>ÅÅÅÅ-MM-ddTHH om du anger en tidszon <p>ELLER <p>ÅÅÅÅ-MM-ddTHH om du inte anger en tidszon <p>Exempelvis om du vill 18 September 2017 2:00 PM, anger du ”2017-09-18T14:00:00” och ange en tidszon, till exempel ”Pacific Standard Time”. Alternativt kan du ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs:** starttiden måste följa den [ISO 8601 dag tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum tidsformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Om du inte anger en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. ”Z” refererar till motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkel scheman starttiden är den första förekomsten medan för komplexa scheman utlösaren inte utlösa en snabbare än starttiden. Läs mer om startdatum och gånger [skapa och aktiviteter som körs regelbundet schema](../connectors/connectors-native-recurrence.md). | 
| weekDays | Nej | Sträng eller strängmatris | Om du anger ”vecka” för `frequency`, kan du ange en eller flera dagar, avgränsade med kommatecken, när du vill köra arbetsflödet: ”måndag”, ”tisdag”, ”onsdag”, ”torsdag”, ”fredag”, ”lördag” och ”söndag” | 
| hours | Nej | Heltal eller heltalsmatris | Om du anger ”dag” eller ”vecka” för `frequency`, kan du ange en eller flera heltal mellan 0 och 23, avgränsade med kommatecken, som tidpunkter på dagen när du vill köra arbetsflödet. <p>Till exempel, om du anger ”10”, ”12” och ”14”, får du 10 AM och 12 PM 14: 00 som timme markerar. | 
| minutes | Nej | Heltal eller heltalsmatris | Om du anger ”dag” eller ”vecka” för `frequency`, kan du ange en eller flera heltal mellan 0 och 59, avgränsade med kommatecken, minuter för den när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut märket och använder det föregående exemplet för tidpunkter på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. | 
|||||| 

Till exempel utlösaren återkommande anger att din logikapp körs varje vecka varje måndag kl 10:30, 12:30:00 och 14:30:00 Pacific Standard Time, Starta tidigast 9 September 2017 14:00:00:

``` json
{
    "triggers": {
        "myRecurrenceTrigger": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

Mer information med återkommande och starta tid exempel för denna utlösare finns [skapa och aktiviteter som körs regelbundet schema](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Begäran utlösare

Den här utlösaren fungerar som en slutpunkt som du kan använda för att anropa logikappen via en HTTP-begäran. Det ser ut som i det här exemplet en utlösare för begäran:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Den här utlösaren har en valfri egenskap som kallas *schemat*:
  
| Elementnamn | Krävs | Typ | Beskrivning |
| ------------ | -------- | ---- | ----------- |
| Schemat | Nej | Objekt | En JSON-schema som kontrollerar den inkommande begäranden. Användbart för att hjälpa efterföljande arbetsflödessteg veta vilka egenskaper som ska referera till. | 
||||| 

Om du vill anropa den här slutpunkten måste du anropa den *listCallbackUrl* API. Se [arbetsflöde Service REST API](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>HTTP-utlösare  

HTTP-utlösare avsöker den angivna slutpunkten och kontrollera svaret för att avgöra om arbetsflödet ska köras. Här är den `inputs` objektet följer dessa parametrar som krävs för att konstruera ett HTTP-anrop:  

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| Metoden | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| URI: N | Ja| Sträng | HTTP eller HTTPs slutpunkten som söker av utlösaren. Maximal strängens storlek: 2 KB | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | Objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. | 
| Autentisering | Nej | Objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). <p>Utöver Scheduler, är en mer stöds egenskap: `authority`. Det här värdet är som standard `https://login.windows.net` om anges, men du kan använda ett annat värde som`https://login.windows\-ppe.net`. | 
||||| 

En *standardpolicy* gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:
  
```json
"retryPolicy": {
    "type": "retry-policy-type",
    "interval": retry-interval,
    "count": number-of-retry-attempts
}
```
 
Om du vill arbeta med din logikapp kräver HTTP-utlösaren HTTP-API för att överensstämma med ett specifikt mönster. Utlösaren identifierar dessa egenskaper:  
  
| Svar | Krävs | Beskrivning | 
| -------- | -------- | ----------- |  
| Statuskod | Ja | Statuskod 200 (”OK”) orsakar en körning. Andra statuskod göra inte en körning. | 
| Försök igen efter rubrik | Nej | Antalet sekunder tills logikappen avsöker slutpunkten igen. | 
| Platshuvud | Nej | URL till anropa vid nästa avsökningsintervall. Om inget anges används den ursprungliga URL: en. | 
|||| 

Här följer några exempel beteenden för olika typer av begäranden:
  
| Svarskod | Försök igen efter | Beteende | 
| ------------- | ----------- | -------- | 
| 200 | {Ingen} | Köra arbetsflödet och Sök igen efter mer data efter den definierade upprepningen. | 
| 200 | 10 sekunder | Köra arbetsflödet och Sök igen efter mer data efter 10 sekunder. |  
| 202 | 60 sekunder | Inte Utlös arbetsflödet. Nästa försök sker i en minut, kan den definierade upprepningen. Om den definierade upprepningen är mindre än en minut, företräde försök igen efter huvudet. Annars används den definierade upprepningen. | 
| 400 | {Ingen} | Felaktig begäran inte köra arbetsflödet. Om inget `retryPolicy` definieras standardprincipen används. När antalet försök har nåtts kontrollerar utlösaren igen data efter den definierade upprepningen. | 
| 500 | {Ingen}| Serverfel, inte köra arbetsflödet. Om inget `retryPolicy` definieras standardprincipen används. När antalet försök har nåtts kontrollerar utlösaren igen data efter den definierade upprepningen. | 
|||| 

Här är http-utlösaren utdata: 
  
| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| Rubriker | Objekt | Sidhuvuden för HTTP-svar | 
| Brödtext | Objekt | Innehållet i HTTP-svar | 
|||| 

## <a name="api-connection-trigger"></a>Utlösare för API-anslutningen  

Utlösare för API-anslutningen är samma som HTTP-utlösaren i dess grundläggande funktioner. Parametrar för att identifiera åtgärden är dock olika. Här är ett exempel:  
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| värden | Ja | Objekt | Värdbaserade gateway och -ID för API-appen | 
| Metoden | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | Objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. | 
| Autentisering | Nej | Objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

För den `host` objekt följer egenskaper:  
  
| Elementnamn | Krävs | Beskrivning | 
| ------------ | -------- | ----------- | 
| API-runtimeUrl | Ja | Slutpunkten för hanterade API: et | 
| Anslutningens namn |  | Namnet på den hanterade API-anslutningen som används i arbetsflödet. Måste referera till en parameter med namnet `$connection`. |
|||| 

En *standardpolicy* gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:
  
```json
"retryPolicy": {
    "type": "retry-policy-type",
    "interval": retry-interval,
    "count": number-of-retry-attempts
}
```

Här följer utdata för en utlösare för API-anslutningen:
  
| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| Rubriker | Objekt | Sidhuvuden för HTTP-svar | 
| Brödtext | Objekt | Innehållet i HTTP-svar | 
|||| 
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook utlösare  

Utlösaren HTTPWebhook ger en slutpunkt som liknar utlösaren begäran men HTTPWebhook utlösaren anropar också en angiven URL för registrering och avregistrering av. Här är ett exempel på hur en HTTPWebhook-utlösare kan se ut:  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

Många av dessa avsnitt är valfria och HTTPWebhook utlösaren beteende beror på de avsnitt som du tillhandahåller eller utelämna. Här följer egenskaperna för utlösaren HTTPWebhook:
  
| Elementnamn | Krävs | Beskrivning | 
| ------------ | -------- | ----------- |  
| prenumerera på | Nej | Anger den utgående begäranden att anropa när utlösaren skapas och utför registreringen. | 
| avbryta prenumerationen | Nej | Anger den utgående begäranden att anropa när utlösaren tas bort. | 
|||| 

Du kan ange gränserna på en webhook-åtgärd på samma sätt som [HTTP asynkron gränser](#asynchronous-limits). Här finns mer information om den `subscribe` och `unsubscribe` åtgärder:

* `subscribe`kallas så att utlösaren kan börja lyssna på händelser. Utgående anropet börjar med samma parametrar som standard HTTP-åtgärder. Det här anropet sker när arbetsflödet ändras på något sätt, till exempel när autentiseringsuppgifterna samlas eller ändra utlösarens indataparametrar. 
  
  Att stödja det här anropet den `@listCallbackUrl()` funktionen returnerar en unik URL för den här specifika utlösaren i arbetsflödet. Denna URL representerar den unika identifieraren för slutpunkter som använder tjänstens REST API.
  
* `unsubscribe`anropas automatiskt när en åtgärd återgivningar utlösaren är ogiltig, inklusive dessa åtgärder:

  * Ta bort eller inaktivera utlösaren. 
  * Ta bort eller inaktivera arbetsflödet. 
  * Ta bort eller inaktivera prenumerationen. 
  
  Parametrarna för den här funktionen är samma som HTTP-utlösaren.

Här följer utdata från HTTPWebhook utlösa och innehållet i den inkommande begäranden är:
  
| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| Rubriker | Objekt | Sidhuvuden för HTTP-svar | 
| Brödtext | Objekt | Innehållet i HTTP-svar | 
|||| 

## <a name="conditions"></a>Villkor  

Du kan använda ett eller flera villkor för alla utlösare för att avgöra om arbetsflödet ska köras eller inte. Exempel:  

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

I det här fallet rapporten endast utlösare när arbetsflödets `sendReports` parameter är angiven till true. Slutligen kan villkor referera till statuskod för utlösaren. Du kan till exempel starta ett arbetsflöde endast när din webbplats returnerar en statuskod 500, till exempel:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> När ett uttryck som refererar till en utlösare statuskod på något sätt, ersätts standardbeteendet, som är utlösaren endast på 200 ”OK”. Till exempel om du vill aktivera både statuskod 200 och statuskod 201 du behöver ta: `@or(equals(triggers().code, 200),equals(triggers().code,201))` som dina villkor.
  
## <a name="start-multiple-runs-for-a-request"></a>Starta flera körs för en begäran

Att startar flera körningar för en enskild begäran `splitOn` är användbart, till exempel när du vill söka en slutpunkt som kan ha flera nya objekt mellan avsökningsintervall.
  
Med `splitOn`, du anger egenskapen i nyttolasten av svar som innehåller ett antal objekt som du vill använda för att starta en körning av utlösaren. Anta att du har en API som returnerar svaret:  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
Din logikapp behöver bara den `rows` innehåll, så du kan skapa en utlösare som det här exemplet:  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> Om du använder den `SplitOn` kommandot, du inte kan hämta de egenskaper som är utanför matrisen, så du inte kan hämta det här exemplet i `status` i svaret som returnerades från API: et.
> I det här exemplet vi använda den `?` operatorn så att vi kan undvika fel om den `rows` egenskapen finns inte. 

Så i arbetsflödesdefinitionen, `@triggerBody().name` returnerar `myFirstRow` för den första körningen och `mySecondRow` för andra kör. Utlösaren utdata ser ut det här exemplet:  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>Kör instans

Du kan konfigurera återkommande utlösare så att de eller endast när alla aktiva körningar har slutförts. Om en schemalagd upprepning händer när arbetsflödesinstans körs, hoppar över utlösaren och väntar tills nästa schemalagda Upprepningsintervall söka igen.
För att konfigurera den här inställningen, ange den `operationOptions` egenskapen `singleInstance`:

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>Översikt över åtgärder

Det finns många typer av åtgärder med unika beteende. Varje åtgärd har olika indata som definierar beteendet för en åtgärd. Åtgärder för samlingen kan innehålla flera andra åtgärder i själva. 

### <a name="standard-actions"></a>Standardåtgärder  

| Åtgärdstyp | Beskrivning | 
| ----------- | ----------- | 
| **HTTP** | Anropar en HTTP-slutpunkt för webbprogram. | 
| **ApiConnection**  | Fungerar som HTTP-åtgärden, men använder [Microsoft-hanterade API: er](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Fungerar som HTTPWebhook, men använder Microsoft-hanterade API: er. | 
| **Svar** | Anger svaret på ett inkommande samtal. | 
| **Funktionen** | Representerar en Azure-funktion. | 
| **Vänta** | Väntar på ett fast belopp tid eller tills en viss tid. | 
| **Arbetsflöde** | Representerar ett inkapslat arbetsflöde. | 
| **Skriva** | Skapar ett arbitary objekt från åtgärdens indata. | 
| **Fråga** | Filtrerar en matris baserat på ett villkor. | 
| **Välj** | Projekt varje element i en matris till ett nytt värde. Exempelvis kan du konvertera en matris av talen i en matris med objekt. | 
| **Tabell** | Konverterar en matris med-objekt till en CSV- eller HTML-tabell. | 
| **Avsluta** | Stoppar köra ett arbetsflöde. | 
||| 

### <a name="collection-actions"></a>Åtgärder för samlingen

| Åtgärdstyp | Beskrivning | 
| ----------- | ----------- | 
| **Villkor** | Utvärderar ett uttryck baserat på resultatet och kör den motsvarande grenen. | 
| **Omfång** | Används för att gruppera logiskt andra åtgärder. | 
| **ForEach** | Åtgärden slingor upprepas i en matris och utför interna åtgärder på varje element i matrisen. | 
| **Fram till** | Inre åtgärder utförs i åtgärden slinga tills ett villkor resultatet till true. | 
||| 

## <a name="http-action"></a>HTTP-åtgärd  

HTTP-åtgärder anropa den angivna slutpunkten och kontrollera svaret för att avgöra om arbetsflödet ska köras. Exempel:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

Här är den `inputs` objektet följer dessa parametrar som krävs för att konstruera ett HTTP-anrop: 

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| Metoden | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| URI: N | Ja| Sträng | HTTP eller HTTPs slutpunkten som söker av utlösaren. Maximal strängens storlek: 2 KB | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | Objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| Autentisering | Nej | Objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). <p>Utöver Scheduler, är en mer stöds egenskap: `authority`. Det här värdet är som standard `https://login.windows.net` om anges, men du kan använda ett annat värde som`https://login.windows\-ppe.net`. | 
||||| 

HTTP- och APIConnection åtgärder stöder *försök principer*. En återförsöksprincip som gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:
  
```json
"retryPolicy": {
    "type": "retry-policy-type",
    "interval": retry-interval,
    "count": number-of-retry-attempts
}
```
Det här exemplet HTTP-åtgärden försöker hämta de senaste nyheterna två gånger om återkommande fel för totalt tre körningar och en fördröjning på 30 sekunder mellan varje försök:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

Intervallet anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Det här intervallet har standard och minst 20 sekunder medan det maximala värdet är en timme. Standard- och maximalt antal för nya försök är fyra timmar. Om du inte anger principdefinitionen försök en `fixed` strategi som används med försök antal och intervall standardvärden. Om du vill inaktivera återförsöksprincipen sägs dess typ `None`.

### <a name="asynchronous-patterns"></a>Asynkront mönster

Som standard stöder alla HTTP-baserade åtgärder mönstret standard asynkron åtgärd. Så om fjärrservern anger att begäran är godkänd för behandling med ett ”202 godkända” svar, behåller Logic Apps motorn avsöker den URL som anges i svarshuvud plats tills ett avslutat tillstånd, vilket är ett icke-202 svar.
  
För att inaktivera den asynkrona som beskrevs tidigare ange `operationOptions` till `DisableAsyncPattern` i indata för åtgärden. I det här fallet är åtgärdens utdata baserad på det första 202 svaret från servern. Exempel:
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```
<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Asynkron gränser

Du kan begränsa hur länge ett asynkront mönster för ett visst tidsintervall. Om tidsintervallet långa utan att ett avslutat tillstånd åtgärdens status är markerat `Cancelled` med en `ActionTimedOut` kod. Begränsa tidsgränsen har angetts i ISO 8601-format. Du kan ange gränserna som visas här:

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection åtgärd

Åtgärden APIConnection refererar till en Microsoft-hanterad koppling. Den här åtgärden kräver en referens till en giltig anslutning och information om API och parametrar.
Här är ett exempel APIConnection åtgärd:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| värden | Ja | Objekt | Representerar connector information som den `runtimeUrl` och referens till connection-objektet. | 
| Metoden | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| Sökväg | Ja | Sträng | Sökvägen för API-åtgärd | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | Objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| Autentisering | Nej | Objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
||||| 

En återförsöksprincip som gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:
  
```json
"retryPolicy": {
    "type": "retry-policy-type",
    "interval": retry-interval,
    "count": number-of-retry-attempts
}
```

## <a name="apiconnection-webhook-action"></a>APIConnection webhook åtgärd

Åtgärden APIConnectionWebhook refererar till en Microsoft-hanterad koppling. Den här åtgärden kräver en referens till en giltig anslutning och information om API och parametrar. Du kan ange gränserna på en webhook-åtgärd på samma sätt som [HTTP asynkron gränser](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| värden | Ja | Objekt | Representerar connector information som den `runtimeUrl` och referens till connection-objektet. | 
| Sökväg | Ja | Sträng | Sökvägen för API-åtgärd | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | Objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| Autentisering | Nej | Objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Svaret åtgärd  

Den här åtgärden innehåller hela svaret nyttolast från en HTTP-begäran och ett `statusCode`, `body`, och `headers`:
  
```json
"myResponseAction": {
    "type": "response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

Instruktionen svar har särskilda begränsningar som inte gäller för andra åtgärder, speciellt:  
  
* Du kan inte ha responsåtgärder i parallella grenar inom en definition av logik appen eftersom den inkommande begäranden kräver en deterministisk svar.
  
* Om arbetsflödet når en svar åtgärd när den inkommande begäranden redan fått något svar, anses svar-åtgärd misslyckades eller i konflikt. Därför logikappen kör markeras `Failed`.
  
* Ett arbetsflöde med responsåtgärder kan inte använda den `splitOn` kommandot i definitionen för utlösaren eftersom anropet skapar flera körs. Kontrollera därför för det här fallet när arbetsflödesåtgärden PLACERAS och returnerar svaret ”felaktig begäran”.

## <a name="function-action"></a>Funktionen åtgärd   

Den här åtgärden kan du representerar och anropa ett [Azure funktionen](../azure-functions/functions-overview.md), till exempel:

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```
| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- |  
| funktionen id | Ja | Sträng | Resurs-ID för Azure-funktionen som du vill anropa. | 
| Metoden | Nej | Sträng | HTTP-metoden som används för att anropa funktionen. Om inget anges är ”POST” standardmetoden. | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
|||||

När du sparar din logikapp, utför kontroller på den angivna funktionen Azure Logic Apps:

* Du måste ha åtkomst till funktionen.
* Du kan använda endast standard HTTP-utlösare eller allmänna JSON webhook-utlösare.
* Funktionen får inte ha någon väg som har definierats.
* Endast ”fungera” och ”anonym” åtkomstnivå är tillåten.

Utlösaren URL: en hämtas, cachelagras och används vid körning. Så om någon åtgärd upphäver den cachelagra URL, misslyckas åtgärden under körning. Undvik problemet genom att spara logikappen igen, vilket gör att logikappen du hämtar och cachelagrar utlösaren URL: en igen.

## <a name="wait-action"></a>Vänta åtgärd  

Den här åtgärden pausar körningen av arbetsflödet för det angivna intervallet. Det här exemplet körs arbetsflödet 15 minuter:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Om du vill vänta tills en särskild tidpunkt, kan du också använda det här exemplet:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> Vänta varaktighet kan antingen anges med den `until` objekt eller `interval` objekt, men inte båda.
  
| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| fram till | Nej | Objekt | Vänta varaktighet baserat på en punkt i tiden | 
| tills tidsstämpel | Ja | Sträng | Tidpunkten i [UTC-datum tidsformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) när väntetiden upphör att gälla | 
| interval | Nej | Objekt | Vänta varaktighet baserat på intervall och antalet | 
| intervall | Ja | Sträng | Tidsenhet. Använd bara ett av följande värden: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| intervall för antal | Ja | Integer | Ett positivt heltal som representerar antalet intervallenheter som används för vänta varaktighet | 
||||| 

## <a name="workflow-action"></a>Arbetsflödesåtgärd   

Den här åtgärden representerar ett annat arbetsflöde. Logic Apps utför en åtkomstkontroll på arbetsflödet eller mer specifikt utlösaren, vilket innebär att du måste ha åtkomst till arbetsflödet.

Den här åtgärden utdata är baserat på vad du anger i den `response` åtgärd för arbetsflöden. Om du inte har definierat en `response` åtgärd och sedan utdata är tomma.

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- |  
| värd-id | Ja | Sträng| Resurs-ID för det arbetsflöde som du vill anropa | 
| värden Utlösarnamn | Ja | Sträng | Namnet på det som du vill anropa utlöser | 
| frågor | Nej | Objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| Rubriker | Nej | Objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| Brödtext | Nej | Objekt | Representerar nyttolasten som skickas till slutpunkten. | 
|||||   

## <a name="compose-action"></a>Skriv åtgärd

Den här åtgärden kan du skapa ett godtyckligt objekt och utdata är ett resultat av utvärderingen av åtgärdens indata. 

> [!NOTE]
> Du kan använda den `Compose` åtgärd för att konstruera inga utdata, inklusive objekt, matriser och andra typer som stöds av logikappar som XML och binary.

Exempelvis kan du använda åtgärden compose för sammanslagning utdata från flera åtgärder:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="select-action"></a>Välj åtgärd

Du kan projicera varje element i en matris till ett nytt värde.
Till exempel om du vill konvertera en matris av talen i en array med objekt som kan du använda:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| Från | Ja | Matris | Källmatrisen |
| Välj | Ja | Alla | Projektionen som tillämpas på varje element i källmatrisen |
||||| 

Utdata från den `select` åtgärden är en matris som har samma kardinalitet som matrisen som indata. Varje element omvandlas som definieras av den `select` egenskapen. Om indata är en tom matris, är utdata också en tom matris.

## <a name="query-action"></a>Frågeåtgärden

Den här åtgärden kan du filtrera en matris baserat på ett villkor. Det här exemplet väljer tal som är större än två:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

Utdata från den `query` åtgärden är en matris som har element från Indatamatrisen som uppfyller villkoret.

> [!NOTE]
> Om inga värden uppfyller de `where` villkoret, resultatet är en tom matris.

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| Från | Ja | Matris | Källmatrisen |
| där | Ja | Sträng | Villkoret som kopplas till varje element från källmatrisen |
||||| 

## <a name="table-action"></a>Åtgärden för tabellen

Du kan konvertera en matris med-objekt till en **CSV** eller **HTML** tabell. Anta exempelvis att du har en `@triggerBody()` med denna matris:

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

Och du definierar en tabell åtgärder som det här exemplet:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

Resultatet från det här exemplet ser ut som den här HTML-tabellen: 

<table><thead><tr><th>id</th><th>namn</th></tr></thead><tbody><tr><td>0</td><td>Äpplen</td></tr><tr><td>1</td><td>orange</td></tr></tbody></table>

Om du vill anpassa den här tabellen, kan du ange vilka kolumner explicit, till exempel:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Resultatet från det här exemplet ser ut som den här HTML-tabellen: 

<table><thead><tr><th>Skapa ID</th><th>Beskrivning</th></tr></thead><tbody><tr><td>0</td><td>ny äpplen</td></tr><tr><td>1</td><td>ny orange</td></tr></tbody></table>

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| Från | Ja | Matris | Källmatrisen. Om den `from` egenskapsvärdet är en tom matris, utdata är en tom tabell. | 
| Format | Ja | Sträng | Tabellformatet som du vill antingen **CSV** eller **HTML** | 
| Kolumner | Nej | Matris | De tabellkolumner som du vill använda. Används för att åsidosätta standard tabellform. | 
| kolumnrubrik | Nej | Sträng | Kolumnrubriken | 
| värde i kolumnen | Ja | Sträng | Värdet i kolumnen | 
||||| 

## <a name="terminate-action"></a>Åtgärden Avbryt

Den här åtgärden stoppar kör arbetsflödet, avbryter alla pågående åtgärder och hoppar över eventuella återstående åtgärder. Åtgärden Avsluta påverkar inte klar åtgärder.

Du kan till exempel använda det här exemplet för att stoppa ett kör som ”misslyckades” status:

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Ja | Sträng | Målet kör har status, vilket antingen är `Failed` eller`Cancelled` |
| runError | Nej | Objekt | Felinformation. Stöds endast när `runStatus` är inställd på `Failed`. |
| runError kod | Nej | Sträng | Det kör felkod: |
| runError meddelande | Nej | Sträng | Det kör felmeddelande |
||||| 

## <a name="collection-actions-overview"></a>Samling åtgärder översikt

Vissa åtgärder kan innehålla åtgärder i själva. Referens-åtgärder i en samling kan refereras direkt utanför samlingen. Till exempel om du definierar `Http` i en `scope`, sedan `@body('http')` fortfarande är giltigt var som helst i arbetsflödet. Du kan ha åtgärder i en samling `runAfter` endast med andra åtgärder i samma samling.

## <a name="condition-if-action"></a>Villkor: Om åtgärden

Du kan utvärdera ett villkor och köra en gren baserat på om uttrycket utvärderas till `true`. 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | Objekt | De inre åtgärderna för att köras när `expression` utvärderas till`true` | 
| uttryck | Ja | Sträng | Uttrycket som ska utvärderas |
| annan | Nej | Objekt | De inre åtgärderna för att köras när `expression` utvärderas till`false` |
||||| 

Om villkoret utvärderas har, villkor har markerats som `Succeeded`. Åtgärder i antingen den `actions` eller `else` objekt utvärderas till: 

* `Succeeded`När de körs och lyckas
* `Failed`När de körs och misslyckas
* `Skipped`När grenen respektive kan inte köras

Här följer exempel på hur villkor kan använda uttryck i en åtgärd:
  
| JSON-värde | Resultat | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | Ett värde som utvärderas till true kommer detta att skicka. Stöder endast booleska uttryck. Om du vill konvertera andra typer till Boolean använder dessa funktioner: `empty` och`equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | Stöder funktioner för jämförelse. I det här exemplet åtgärden körs bara när resultatet av `act1` är större än tröskelvärdet. | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | Stöder funktioner för logik för att skapa kapslade booleska uttryck. I det här exemplet körs åtgärden när resultatet av `act1` tröskelvärdet eller under 100. | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | Om du vill kontrollera om en matris har alla objekt som kan du använda matrisen. I det här exemplet körs åtgärden när den `errors` matrisen är tom. | 
| `"expression": "parameters('hasSpecialAction')"` | Fel, inte ett giltigt tillstånd eftersom @ krävs för villkor. |  
|||

## <a name="scope-action"></a>Scope-åtgärd

Den här åtgärden kan du logiskt gruppera åtgärder i ett arbetsflöde.

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- |  
| åtgärder | Ja | Objekt | Inre åtgärder för att köra inuti scope |
||||| 

## <a name="foreach-action"></a>ForEach-åtgärd

Åtgärden slingor upprepas i en matris och utför interna åtgärder på varje element i matrisen. Som standard den `foreach` loop körs parallellt och kan köras 20 körningar parallellt på samma gång. Ange regler för körning av `operationOptions` parameter.

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | Objekt | Inre åtgärder för att köra i den här slingan | 
| foreach | Ja | Sträng | Matrisen för att gå igenom | 
| operationOptions | Nej | Sträng | Anger åtgärden alternativ för att anpassa beteende. Stöder för närvarande endast `Sequential` för att köra sekventiellt iterationer där standardbeteendet är parallellt. |
||||| 

## <a name="until-action"></a>Förrän åtgärden

Åtgärden slingor körs inre åtgärder förrän ett villkor resultat till true.

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | Objekt | Inre åtgärder för att köra i den här slingan | 
| uttryck | Ja | Sträng | Uttrycket som ska utvärderas efter varje iteration | 
| Gränsen | Ja | Objekt | Gränser för loopen. Måste ange minst en gräns. | 
| Antal | Nej | Integer | Gränsen för antalet iterationer för att utföra | 
| timeout | Nej | Sträng | Timeout-gränsen i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger hur länge slingan ska köras |
||||| 

## <a name="next-steps"></a>Nästa steg

* [Språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md)
* [Arbetsflöde för REST-API](https://docs.microsoft.com/rest/api/logic/workflows)
