---
title: Arbetsflödet utlösare och åtgärder - Azure Logic Apps | Microsoft Docs
description: Lär dig mer om utlösare och åtgärder i arbetsflödesdefinitioner för Logic Apps i Azure
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Utlösare och åtgärder för arbetsflödesdefinitioner i Azure Logic Apps

I [Azure Logikappar](../logic-apps/logic-apps-overview.md), alla logik app arbetsflöden som startar med utlösare följt av åtgärder. Den här artikeln beskriver utlösare och åtgärder som du kan använda för att skapa logikappar för automatiskt business arbetsflöden eller processer i lösningar för katalogintegrering. Du kan skapa logikappar visuellt med Logic Apps Designer eller genom direkt redigering av underliggande arbetsflödesdefinitioner med den [språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md). Du kan använda Azure-portalen eller Visual Studio. Lär dig hur [priser fungerar för utlösare och åtgärder](../logic-apps/logic-apps-pricing.md).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Utlösare: översikt

Alla logikappar börja med en utlösare, vilket definierar de anrop som kan skapa en instans av och starta ett arbetsflöde för logik app. Här följer typerna av utlösare som du kan använda:

* En *avsökning* utlösare som kontrollerar HTTP-slutpunkten för en tjänst med jämna mellanrum
* En *push* utlösa, som anropar den [arbetsflöde Service REST API](https://docs.microsoft.com/rest/api/logic/workflows)
 
Alla utlösare har de översta elementen, även om vissa är valfria:  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Utlösarnamn*> | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Utlösaren Skriv till exempel: ”Http” eller ”ApiConnection” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| recurrence | JSON-objekt | Frekvensen och intervall som beskriver hur ofta utlösaren utlöses |  
| frequency | Sträng | Tidsenhet som beskriver hur ofta utlösaren utlöses ”: andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| interval | Integer | Ett positivt heltal som beskriver hur ofta utlösaren utlöses baserat på hur ofta. <p>Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [Villkor](#trigger-conditions) | Matris | Ett eller flera villkor som avgör om att köra arbetsflödet eller inte | 
| [splitOn](#split-on-debatch) | Sträng | Ett uttryck som delar upp, eller *debatches*, matrisen objekt till flera arbetsflödesinstanser för bearbetning. Det här alternativet är tillgängligt för utlösare som returnerar en matris och endast när du arbetar direkt i kodvy. | 
| [operationOptions](#trigger-operation-options) | Sträng | Vissa utlösare ger ytterligare alternativ som kan du ändra standardfunktioner för utlösare | 
||||| 

## <a name="trigger-types-and-details"></a>Typer av utlösare och information  

Varje typ av utlösare har olika gränssnitt och indata som definierar utlösarens beteende. 

| Typ av utlösare | Beskrivning | 
| ------------ | ----------- | 
| [**Upprepning**](#recurrence-trigger) | Utlöses baserat på ett definierat schema. Du kan ange ett framtida datum och tid för startar den här utlösaren. Baserat på hur ofta du kan också ange tider och dagar för att köra arbetsflödet. | 
| [**Begäran**](#request-trigger)  | Gör din logikapp i en anropsbar slutpunkt, även kallat en ”manuell” utlösare. Se exempelvis [anropa utlösare eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md). | 
| [**HTTP**](#http-trigger) | Kontrollerar, eller *polls*, en HTTP-slutpunkt för webbprogram. HTTP-slutpunkten måste motsvara en utlösare kontrakt med hjälp av en ”202” asynkront mönster eller returnerar en matris. | 
| [**ApiConnection**](#apiconnection-trigger) | Fungerar som HTTP-utlösare, men använder [Microsoft-hanterade API: er](../connectors/apis-list.md). | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Fungerar som utlösare för begäran, men en angiven URL-anrop för registrering och avregistrering. |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Fungerar som HTTPWebhook utlösaren, men använder [Microsoft-hanterade API: er](../connectors/apis-list.md). | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Utlösare för upprepning  

Den här utlösaren körs baserat på angivet intervall och schema och ger ett enkelt sätt för att regelbundet köra ett arbetsflöde. 

Här är utlösardefinition:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| Upprepning | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”återkommande” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| recurrence | JSON-objekt | Frekvensen och intervall som beskriver hur ofta utlösaren utlöses |  
| frequency | Sträng | Tidsenhet som beskriver hur ofta utlösaren utlöses ”: andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| interval | Integer | Ett positivt heltal som beskriver hur ofta utlösaren utlöses baserat på hur ofta. <p>Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| startTime | Sträng | Datum och tid i det här formatet: <p>ÅÅÅÅ-MM-ddTHH om du anger en tidszon <p>ELLER <p>ÅÅÅÅ-MM-ddTHH om du inte anger en tidszon <p>Exempelvis om du vill 18 September 2017 2:00 PM, anger du ”2017-09-18T14:00:00” och ange en tidszon, till exempel ”Pacific Standard Time”, eller ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs:** starttiden måste följa den [ISO 8601 dag tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum tidsformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Om du inte anger en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. ”Z” refererar till motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkel scheman starttiden är den första förekomsten medan för komplexa scheman utlösaren inte utlösa en snabbare än starttiden. Läs mer om startdatum och gånger [skapa och aktiviteter som körs regelbundet schema](../connectors/connectors-native-recurrence.md). | 
| Tidszon | Sträng | Gäller endast när du anger en starttid eftersom utlösaren inte acceptera [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Ange den tidszon som du vill använda. | 
| hours | Heltal eller heltalsmatris | Om du anger ”dag” eller ”vecka” för `frequency`, kan du ange en eller flera heltal mellan 0 och 23, avgränsade med kommatecken, som tidpunkter på dagen när du vill köra arbetsflödet. <p>Till exempel, om du anger ”10”, ”12” och ”14”, får du 10 AM och 12 PM 14: 00 som timme markerar. | 
| minutes | Heltal eller heltalsmatris | Om du anger ”dag” eller ”vecka” för `frequency`, kan du ange en eller flera heltal mellan 0 och 59, avgränsade med kommatecken, minuter för den när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut märket och använder det föregående exemplet för tidpunkter på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. | 
| weekDays | Sträng eller strängmatris | Om du anger ”vecka” för `frequency`, kan du ange en eller flera dagar, avgränsade med kommatecken, när du vill köra arbetsflödet: ”måndag”, ”tisdag”, ”onsdag”, ”torsdag”, ”fredag”, ”lördag” och ”söndag” | 
| Concurrency | JSON-objekt | Det här objektet anger återkommande och avsökning utlösare, maximalt antal arbetsflödesinstanser som kan köras samtidigt. Använd det här värdet om du vill begränsa förfrågningar som tar emot serverdelssystem. <p>Detta värde anger gränsen för samtidighet till 10 instanser: `"concurrency": { "runs": 10 }` | 
| operationOptions | Sträng | Den `singleInstance` alternativet anger att utlösaren Utlöses efter att alla aktiva körs är klar. Se [utlösare: eller endast när active körs Slutför](#single-instance). | 
|||| 

*Exempel 1*

Den här grundläggande upprepning utlösaren körs dagligen:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exempel 2*

Du kan ange ett startdatum och tidpunkt för startar utlösaren. Den här utlösaren upprepning startar på det angivna datumet och sedan utlöses varje dag:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exempel 3*

Återkommande utlösaren startar på 9 September 2017 på 2:00 PM och utlöses varje vecka varje måndag 10:30 AM, 12:30:00 och 14:30:00 Pacific, normaltid:

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Mer information och exempel på den här utlösaren finns [skapa och aktiviteter som körs regelbundet schema](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

## <a name="request-trigger"></a>Begäran utlösare

Den här utlösaren gör din logikapp callable genom att skapa en slutpunkt som kan ta emot inkommande HTTP-begäranden. För att anropa den här utlösaren, måste du använda den `listCallbackUrl` API i den [arbetsflöde Service REST API](https://docs.microsoft.com/rest/api/logic/workflows). Information om hur du använder den här utlösaren som en HTTP-slutpunkt finns [anropa utlösare eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| Manuell | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”begär” | 
| typ | Sträng | Typ av begäran som är ”Http” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| metod | Sträng | Metoden som begäranden måste använda för att anropa utlösaren: ”GET”, ”PLACERA”, ”publicera”, ”uppdatera”, ”ta bort” eller ”gå” |
| RelativePath | Sträng | Den relativa sökvägen för den parameter som accepterar HTTP-slutpunktens URL | 
| Schemat | JSON-objekt | JSON-schema som beskriver och validerar nyttolasten eller indata som utlösaren tar emot från den inkommande begäranden. Det här schemat kan efterföljande arbetsflödesåtgärder veta vilka egenskaper som ska referera till. | 
| properties | JSON-objekt | En eller flera egenskaper i JSON-schema som beskriver nyttolasten | 
| Krävs | Matris | En eller flera egenskaper som kräver att värden | 
|||| 

*Exempel*

Den här begäran utlösaren anger att en inkommande begäran använder HTTP POST-metoden för att anropa utlösaren och ett schema som validerar indata från den inkommande begäranden: 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>HTTP-utlösare  

Den här utlösaren genomsöker den angivna slutpunkten och kontrollerar svaret. Svaret avgör om arbetsflödet ska köras eller inte. Den `inputs` JSON-objekt innehåller och kräver den `method` och `uri` parametrar som krävs för att konstruera HTTP-anropet:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| HTTP | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”Http” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| metod | Ja | Sträng | HTTP-metoden för avsökning av den angivna slutpunkten: ”GET”, ”PLACERA”, ”publicera”, ”uppdatera”, ”ta bort” eller ”gå” | 
| URI | Ja| Sträng | HTTP eller HTTPS slutpunkts-URL som utlösaren söker eller avsöker <p>Maximal strängens storlek: 2 KB | 
| recurrence | JSON-objekt | Frekvensen och intervall som beskriver hur ofta utlösaren utlöses |  
| frequency | Sträng | Tidsenhet som beskriver hur ofta utlösaren utlöses ”: andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| interval | Integer | Ett positivt heltal som beskriver hur ofta utlösaren utlöses baserat på hur ofta. <p>Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| frågor | JSON-objekt | Alla frågeparametrar som du vill ta med URL-Adressen <p>Till exempel lägger till i det här elementet i `?api-version=2015-02-01` frågesträng till URL: <p>`"queries": { "api-version": "2015-02-01" }` <p>Resultat: `https://contoso.com?api-version=2015-02-01` | 
| rubriker | JSON-objekt | En eller flera sidhuvuden att skicka till begäran <p>Till exempel ange språk och typ för en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | JSON-objekt | Nyttolasten (data) att skicka till slutpunkten | 
| autentisering | JSON-objekt | Den metod som den inkommande begäranden ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). Utöver Scheduler, den `authority` egenskapen stöds. Om inget värde anges är standardvärdet `https://login.windows.net`, men du kan använda ett annat värde som`https://login.windows\-ppe.net`. | 
| retryPolicy | JSON-objekt | Det här objektet anpassar beteendet för återkommande fel som har 4xx eller 5xx statuskoder försök igen. Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
| Concurrency | JSON-objekt | Det här objektet anger återkommande och avsökning utlösare, maximalt antal arbetsflödesinstanser som kan köras samtidigt. Använd det här värdet om du vill begränsa förfrågningar som tar emot serverdelssystem. <p>Detta värde anger gränsen för samtidighet till 10 instanser: <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | Sträng | Den `singleInstance` alternativet anger att utlösaren Utlöses efter att alla aktiva körs är klar. Se [utlösare: eller endast när active körs Slutför](#single-instance). | 
|||| 

Om du vill arbeta med din logikapp, kräver HTTP-utlösaren att HTTP-API som överensstämmer med ett specifikt mönster. HTTP-utlösaren identifierar dessa egenskaper:  
  
| Svar | Krävs | Beskrivning | 
| -------- | -------- | ----------- |  
| Statuskod | Ja | Den ”200 OK” statuskod startar en körning. Andra statuskod starta inte en körning. | 
| Försök igen efter rubrik | Nej | Antalet sekunder tills logikappen avsöker slutpunkten igen | 
| Platshuvud | Nej | URL till anropa vid nästa avsökningsintervall. Om inget anges används den ursprungliga URL: en. | 
|||| 

*Exempel beteenden för olika begäranden*

| Statuskod | Försök igen efter | Beteende | 
| ----------- | ----------- | -------- | 
| 200 | {Ingen} | Köra arbetsflödet och Sök igen efter mer data efter den definierade upprepningen. | 
| 200 | 10 sekunder | Köra arbetsflödet och Sök igen efter mer data efter 10 sekunder. |  
| 202 | 60 sekunder | Inte Utlös arbetsflödet. Nästa försök sker i en minut, kan den definierade upprepningen. Om den definierade upprepningen är mindre än en minut, företräde försök igen efter huvudet. Annars används den definierade upprepningen. | 
| 400 | {Ingen} | Felaktig begäran inte köra arbetsflödet. Om inget `retryPolicy` definieras standardprincipen används. När antalet försök har nåtts kontrollerar utlösaren igen data efter den definierade upprepningen. | 
| 500 | {Ingen}| Serverfel, inte köra arbetsflödet. Om inget `retryPolicy` definieras standardprincipen används. När antalet försök har nåtts kontrollerar utlösaren igen data efter den definierade upprepningen. | 
|||| 

### <a name="http-trigger-outputs"></a>HTTP-utlösaren matar ut

| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| rubriker | JSON-objekt | Huvuden från HTTP-svar | 
| brödtext | JSON-objekt | Text från HTTP-svar | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection utlösare  

Den här utlösaren fungerar som den [HTTP-utlösaren](#http-trigger), men använder [Microsoft-hanterade API: er](../connectors/apis-list.md) så skiljer sig åt parametrar för den här utlösaren. 

Här är utlösardefinition, även om många avsnitt är valfria, så den utlösaren beteende beror på om huruvida avsnitt är infogade:

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”ApiConnection” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| värd | JSON-objekt | JSON-objekt som beskriver värden gateway och -ID för den hanterade API <p>Den `host` JSON-objekt har dessa element: `api` och `connection` | 
| api | JSON-objekt | Slutpunkts-URL för den hanterade API: <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| anslutning | JSON-objekt | Namn för den hanterade API-anslutningen som används i arbetsflödet, som måste innehålla en referens till en parameter med namnet `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| metod | Sträng | HTTP-metoden för att kommunicera med hanterade API: ”GET”, ”PLACERA”, ”publicera”, ”uppdatera”, ”ta bort” eller ”gå” | 
| recurrence | JSON-objekt | Frekvensen och intervall som beskriver hur ofta utlösaren utlöses |  
| frequency | Sträng | Tidsenhet som beskriver hur ofta utlösaren utlöses ”: andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| interval | Integer | Ett positivt heltal som beskriver hur ofta utlösaren utlöses baserat på hur ofta. <p>Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| frågor | JSON-objekt | Alla frågeparametrar som du vill ta med URL-Adressen <p>Till exempel lägger till i det här elementet i `?api-version=2015-02-01` frågesträng till URL: <p>`"queries": { "api-version": "2015-02-01" }` <p>Resultat: `https://contoso.com?api-version=2015-02-01` | 
| rubriker | JSON-objekt | En eller flera sidhuvuden att skicka till begäran <p>Till exempel ange språk och typ för en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | JSON-objekt | I JSON-objekt som beskriver nyttolasten (data) att skicka till hanterade API: et | 
| autentisering | JSON-objekt | Den metod som en inkommande begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | JSON-objekt | Det här objektet anpassar försök beteendet för återkommande fel som har 4xx eller 5xx statuskoder: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
| Concurrency | JSON-objekt | Det här objektet anger återkommande och avsökning utlösare, maximalt antal arbetsflödesinstanser som kan köras samtidigt. Använd det här värdet om du vill begränsa förfrågningar som tar emot serverdelssystem. <p>Detta värde anger gränsen för samtidighet till 10 instanser: `"concurrency": { "runs": 10 }` | 
| operationOptions | Sträng | Den `singleInstance` alternativet anger att utlösaren Utlöses efter att alla aktiva körs är klar. Se [utlösare: eller endast när active körs Slutför](#single-instance). | 
||||

*Exempel*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>APIConnection utlösaren utdata
 
| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| rubriker | JSON-objekt | Huvuden från HTTP-svar | 
| brödtext | JSON-objekt | Text från HTTP-svar | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>HTTPWebhook utlösare  

Den här utlösaren fungerar som den [begäran utlösaren](#request-trigger) genom att skapa en anropsbar slutpunkten för din logikapp. Den här utlösaren anropar dock också en viss slutpunkts-URL för registrering eller avregistrering av en prenumeration. Du kan ange gränserna på en webhook-utlösare på samma sätt som [HTTP asynkron gränser](#asynchronous-limits). 

Här är utlösardefinition, även om många avsnitt är valfria och utlösarens beteende beror på de avsnitt som du använder eller utelämna:

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”HttpWebhook” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| prenumerera på | JSON-objekt| Utgående begäran att anropa och utföra registreringen när utlösaren har skapats. Det här anropet sker så att utlösaren kan börja lyssna på händelser på slutpunkten. Mer information finns i [prenumerera och avbryta prenumerationen](#subscribe-unsubscribe). | 
| metod | Sträng | HTTP-metod som används för prenumerationsbegäran: ”GET”, ”PLACERA”, ”publicera”, ”uppdatera”, ”ta bort” eller ”gå” | 
| URI | Sträng | Slutpunkts-URL att skicka prenumerationsbegäran | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| avbryta prenumerationen | JSON-objekt | De utgående begäran om att anropa automatiskt och avbryta prenumerationen när en åtgärd gör utlösaren är ogiltig. Mer information finns i [prenumerera och avbryta prenumerationen](#subscribe-unsubscribe). | 
| metod | Sträng | HTTP-metoden ska användas för begäran om att avbryta: ”GET”, ”PLACERA”, ”publicera”, ”uppdatera”, ”ta bort” eller ”gå” | 
| URI | Sträng | Slutpunkts-URL för att skicka begäran om att avbryta | 
| brödtext | JSON-objekt | JSON-objekt som beskriver nyttolasten (data) för prenumerationen eller avbryta begäran | 
| autentisering | JSON-objekt | Den metod som en inkommande begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | JSON-objekt | Det här objektet anpassar försök beteendet för återkommande fel som har 4xx eller 5xx statuskoder: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
|||| 

*Exempel*

```json
"myAppSpotTrigger": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "headers": {},
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` och `unsubscribe`

Den `subscribe` anrop sker när arbetsflödet ändras på något sätt, till exempel när autentiseringsuppgifter förnyas eller utlösaren input parametrar ändringen. Anropet använder samma parametrar som standard HTTP-åtgärder. 
 
Den `unsubscribe` anrop sker automatiskt när en åtgärd gör HTTPWebhook utlösaren ogiltig, till exempel:

* Ta bort eller inaktivera utlösaren. 
* Ta bort eller inaktivera arbetsflödet. 
* Ta bort eller inaktivera prenumerationen. 

Att stödja dessa anrop av `@listCallbackUrl()` returnerar funktionen ett unikt ”återanrop URL” för den här utlösaren. Denna URL representerar en unik identifierare för slutpunkter som använder tjänstens REST API. Parametrarna för den här funktionen är samma som HTTP-utlösaren.

### <a name="httpwebhook-trigger-outputs"></a>HTTPWebhook utlösaren utdata

| Elementnamn | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| rubriker | JSON-objekt | Huvuden från HTTP-svar | 
| brödtext | JSON-objekt | Text från HTTP-svar | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook utlösare

Den här utlösaren fungerar som den [HTTPWebhook utlösaren](#httpwebhook-trigger), men använder [Microsoft-hanterade API: er](../connectors/apis-list.md). 

Här är utlösardefinition:

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*Krävs*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | JSON-objekt | Namn för trigger, vilket är ett objekt som beskrivs i Javascript Object Notation (JSON)-format  | 
| typ | Sträng | Typen av utlösare som är ”ApiConnectionWebhook” | 
| Indata | JSON-objekt | Utlösarens indata som definierar utlösarens beteende | 
| värd | JSON-objekt | JSON-objekt som beskriver värden gateway och -ID för den hanterade API <p>Den `host` JSON-objekt har dessa element: `api` och `connection` | 
| anslutning | JSON-objekt | Namn för den hanterade API-anslutningen som används i arbetsflödet, som måste innehålla en referens till en parameter med namnet `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| brödtext | JSON-objekt | I JSON-objekt som beskriver nyttolasten (data) att skicka till hanterade API: et | 
| NotificationUrl | Sträng | Returnerar en unik ”återanrop URL” för denna utlösare som kan använda för hanterade API: et | 
|||| 

*Valfria*

| Elementnamn | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| frågor | JSON-objekt | Alla frågeparametrar som du vill ta med URL-Adressen <p>Till exempel lägger till i det här elementet i `?folderPath=Inbox` frågesträng till URL: <p>`"queries": { "folderPath": "Inbox" }` <p>Resultat: `https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>Utlösare: villkor

Du kan inkludera en matris med en eller flera villkor som avgör om arbetsflödet ska köras för alla utlösare. I det här exemplet rapporten utlösaren utlöses endast när arbetsflödets `sendReports` parameter är angiven till true. 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

Villkor kan också referera utlösarens statuskod. Anta att du vill starta ett arbetsflöde endast när din webbplats returnerar statuskod ”500”:

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> Som standard utlöses en utlösare bara på Ta emot en ”200 OK” svar. När ett uttryck som refererar till en utlösare statuskod på något sätt, ersätts utlösarens standardbeteendet. Om du vill att utlösaren att utlösas för flera statuskoder, till exempel statuskod 200 och statuskod 201, så måste du inkludera den här instruktionen som dina villkor: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>Utlösare: Dela en matris i flera körs

Om utlösaren returnerar en matris för din logikapp att bearbeta kan ibland ”för var och en” loop ta lång tid att bearbeta varje element i matrisen. Använd i stället de **SplitOn** egenskap i utlösaren till *debatch* matrisen. 

Debatching delar upp matris-objekt och startar en ny instans av logik app som körs för varje element i matrisen. Den här metoden är användbar, till exempel när du vill söka en slutpunkt som kan returnera flera nya objekt mellan avsökningsintervall.
För det maximala antalet matris objekt som **SplitOn** kan bearbeta enkel logik app körs, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Du kan lägga till **SplitOn** bara till utlösare manuellt definiera eller åsidosätta i kodvy för din logikapp JSON-definitionen. Du kan inte använda **SplitOn** när du vill implementera ett mönster för synkron svar. Alla arbetsflöden som använder **SplitOn** och innehåller ett svar åtgärd körs asynkront och skickar omedelbart en `202 ACCEPTED` svar.

Om din utlösaren Swagger-fil som beskriver en nyttolast som är en matris av **SplitOn** egenskapen läggs automatiskt till utlösaren. Lägg till den här egenskapen i svar-nyttolast som har den matris som du vill debatch annars. 

Anta att du har en API som returnerar svaret: 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
Din logikapp behöver bara innehåll från `Rows`, så du kan skapa en utlösare som det här exemplet.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": 1
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Om du använder den `SplitOn` kommandot, du inte kan hämta de egenskaper som är utanför matrisen. Så i det här exemplet kan du hämta den `status` i svaret som returnerades från API: et.
> 
> Att undvika ett fel om den `Rows` egenskapen finns inte, det här exemplet används den `?` operator.

Nu kan du använda din arbetsflödesdefinitionen `@triggerBody().name` att hämta `customer-name-one` från den första körningen och `customer-name-two` från andra kör. Därför utlösaren matar ut utseende som de här exemplen:

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>Utlösare: Åtgärden alternativ

Dessa utlösare tillhandahåller flera alternativ som kan du ändra standardbeteendet.

| Utlösare | Åtgärden alternativet | Beskrivning |
|---------|------------------|-------------|
| [Återkommande](#recurrence-trigger), <br>[HTTP](#http-trigger), <br>[ApiConnection](#apiconnection-trigger) | singleInstance | Fire körs endast när alla aktiva utlösaren har slutförts. |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>Utlösare: Eller endast när active körs Slutför

Du kan ange att utlösaren brand körs endast när alla aktiva är klar för utlösare där du kan ange återkommande. Om en schemalagd upprepning händer när en arbetsflödesinstans som körs, hoppar över utlösaren och väntar tills nästa schemalagda upprepning innan kontrollen igen. Exempel:

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Översikt över åtgärder

Det finns många typer av åtgärder med unika beteende. Varje åtgärd har olika indata som definierar beteendet för en åtgärd. Åtgärder för samlingen kan innehålla flera andra åtgärder i själva. 

### <a name="standard-actions"></a>Standardåtgärder  

| Åtgärdstyp | Beskrivning | 
| ----------- | ----------- | 
| **HTTP** | Anropar en HTTP-slutpunkt för webbprogram. | 
| **ApiConnection**  | Fungerar som HTTP-åtgärden, men använder [Microsoft-hanterade API: er](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Fungerar som HTTPWebhook, men använder Microsoft-hanterade API: er. | 
| **Svar** | Anger svaret på ett inkommande samtal. | 
| **Skriva** | Skapar ett godtyckligt objekt från åtgärdens indata. | 
| **Funktionen** | Representerar en Azure-funktion. | 
| **Vänta** | Väntar på ett fast belopp tid eller tills en viss tid. | 
| **Arbetsflöde** | Representerar ett inkapslat arbetsflöde. | 
| **Skriva** | Skapar ett godtyckligt objekt från åtgärdens indata. | 
| **Fråga** | Filtrerar en matris baserat på ett villkor. | 
| **Välj** | Projekt varje element i en matris till ett nytt värde. Exempelvis kan du konvertera en matris av talen i en matris med objekt. | 
| **Tabell** | Konverterar en matris med-objekt till en CSV- eller HTML-tabell. | 
| **Avsluta** | Stoppar köra ett arbetsflöde. | 
| **Vänta** | Väntar på ett fast belopp tid eller tills en viss tid. | 
| **Arbetsflöde** | Representerar ett inkapslat arbetsflöde. | 
||| 

### <a name="collection-actions"></a>Åtgärder för samlingen

| Åtgärdstyp | Beskrivning | 
| ----------- | ----------- | 
| **Om** | Utvärderar ett uttryck och baserat på resultatet, Kör grenen motsvarande. | 
| **växel** | Utför olika åtgärder baserat på specifika värden i ett objekt. | 
| **ForEach** | Åtgärden slingor upprepas i en matris och utför interna åtgärder på varje element i matrisen. | 
| **fram till** | Inre åtgärder utförs i åtgärden slinga tills ett villkor resultatet till true. | 
| **Omfång** | Används för att gruppera logiskt andra åtgärder. | 
|||  

## <a name="http-action"></a>HTTP-åtgärd  

En HTTP-åtgärd anropar den angivna slutpunkten och kontrollerar svaret för att avgöra om arbetsflödet ska köras eller inte. Exempel:
  
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
| metod | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| URI | Ja| Sträng | HTTP eller HTTPs slutpunkten som söker av utlösaren. Maximal strängens storlek: 2 KB | 
| frågor | Nej | JSON-objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| rubriker | Nej | JSON-objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | Nej | JSON-objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | JSON-objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| autentisering | Nej | JSON-objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). <p>Utöver Scheduler, är en mer stöds egenskap: `authority`. Det här värdet är som standard `https://login.windows.net` om anges, men du kan använda ett annat värde som`https://login.windows\-ppe.net`. | 
||||| 

HTTP- och APIConnection åtgärder stöder *försök principer*. En återförsöksprincip som gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Det här exemplet HTTP-åtgärden försöker hämta de senaste nyheterna två gånger om återkommande fel för totalt tre körningar och en fördröjning på 30 sekunder mellan varje försök:
  
```json
"myLatestNewsAction": {
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

Intervallet anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Det här intervallet har standard och minst 20 sekunder medan det maximala värdet är en timme. Standard- och maximalt antal för nya försök är fyra timmar. Om du inte anger ett nytt försök principdefinitionen en `fixed` strategi som används med försök antal och intervall standardvärden. Om du vill inaktivera återförsöksprincipen sägs dess typ `None`.

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

Du kan begränsa hur länge ett asynkront mönster för ett visst tidsintervall. Om tidsintervallet långa utan att ett avslutat tillstånd åtgärdens status är markerat `Cancelled` med en `ActionTimedOut` kod. Begränsa tidsgränsen har angetts i ISO 8601-format. Det här exemplet visar hur du kan ange gränserna:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection åtgärd

Den här åtgärden refererar till en Microsoft-hanterad koppling som kräver en referens till en giltig anslutning och information om API och parametrar. Här är ett exempel APIConnection åtgärd:

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
| värd | Ja | JSON-objekt | Representerar connector information som den `runtimeUrl` och referens till connection-objektet. | 
| metod | Ja | Sträng | Använder en av metoderna HTTP: ”GET”, ”publicera”, ”PLACERA”, ”ta bort”, ”uppdatera” eller ”chef” | 
| sökväg | Ja | Sträng | Sökvägen för API-åtgärd | 
| frågor | Nej | JSON-objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| rubriker | Nej | JSON-objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | Nej | JSON-objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | JSON-objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| autentisering | Nej | JSON-objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
||||| 

En återförsöksprincip som gäller för återkommande fel som betecknas som HTTP-statuskoder 408 och 429 5xx utöver eventuella undantag. Du kan definiera principen med den `retryPolicy` objekt som visas här:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
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
| värd | Ja | JSON-objekt | Representerar connector information som den `runtimeUrl` och referens till connection-objektet. | 
| sökväg | Ja | Sträng | Sökvägen för API-åtgärd | 
| frågor | Nej | JSON-objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| rubriker | Nej | JSON-objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | Nej | JSON-objekt | Representerar nyttolasten som skickas till slutpunkten. | 
| retryPolicy | Nej | JSON-objekt | Använd det här objektet för att anpassa försök beteendet för 4xx eller 5xx-fel. Mer information finns i [försök principer](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nej | Sträng | Definierar de särskilda beteenden att åsidosätta. | 
| autentisering | Nej | JSON-objekt | Representerar den metod som begäran ska användas för autentisering. Mer information finns i [Scheduler utgående autentisering](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Svaret åtgärd  

Den här åtgärden innehåller hela svaret nyttolast från en HTTP-begäran och ett `statusCode`, `body`, och `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
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

## <a name="compose-action"></a>Skriv åtgärd

Den här åtgärden kan du skapa ett godtyckligt objekt och utdata är ett resultat av utvärderingen av åtgärdens indata. 

> [!NOTE]
> Du kan använda den `Compose` åtgärd för att konstruera inga utdata, inklusive objekt, matriser och andra typer som stöds av logikappar som XML och binary.

Du kan till exempel använda den `Compose` åtgärd för sammanslagning utdata från flera åtgärder:

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

## <a name="function-action"></a>Funktionen åtgärd

Den här åtgärden kan du representerar och anropa ett [Azure funktionen](../azure-functions/functions-overview.md), till exempel:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
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
| metod | Nej | Sträng | HTTP-metoden som används för att anropa funktionen. Om inget anges är ”POST” standardmetoden. | 
| frågor | Nej | JSON-objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| rubriker | Nej | JSON-objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | Nej | JSON-objekt | Representerar nyttolasten som skickas till slutpunkten. | 
|||||

När du sparar din logikapp utför Logic Apps motorn vissa kontroller på den angivna funktionen:

* Du måste ha åtkomst till funktionen.
* Du kan använda en standard HTTP-utlösare eller allmänna JSON Webhook-utlösare.
* Funktionen får inte ha någon väg som har definierats.
* Endast ”funktionen” och ”anonym” åtkomstnivåer tillåts.

> [!NOTE]
> Motorn för Logic Apps hämtar och cachelagrar utlösaren URL-Adressen som används vid körning. Så om någon åtgärd upphäver den cachelagra URL, misslyckas åtgärden under körning. Undvik problemet genom att spara logikappen igen, vilket gör att logikappen du hämtar och cachelagrar utlösaren URL: en igen.

## <a name="select-action"></a>Välj åtgärd

Du kan projicera varje element i en matris till ett nytt värde. Det här exemplet konverterar en matris av talen i en matris av objekt:

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
| från | Ja | Matris | Källmatrisen |
| välj | Ja | Alla | Projektionen som tillämpas på varje element i källmatrisen |
||||| 

Utdata från den `select` åtgärden är en matris som har samma kardinalitet som matrisen som indata. Varje element omvandlas som definieras av den `select` egenskapen. Om indata är en tom matris, är utdata också en tom matris.

## <a name="terminate-action"></a>Åtgärden Avbryt

Den här åtgärden stoppar ett arbetsflöde kör avbryta alla åtgärder pågår och hoppa över eventuella återstående åtgärder. Åtgärden Avsluta påverkar inte redan utförda åtgärder.

Till exempel för att stoppa en körning som har `Failed` status:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Ja | Sträng | Målet kör har status, vilket antingen är `Failed` eller `Cancelled` |
| runError | Nej | JSON-objekt | Felinformation. Stöds endast när `runStatus` är inställd på `Failed`. |
| runError kod | Nej | Sträng | Det kör felkod: |
| runError meddelande | Nej | Sträng | Det kör felmeddelande | 
||||| 

## <a name="query-action"></a>Frågeåtgärden

Den här åtgärden kan du filtrera en matris baserat på ett villkor. 

> [!NOTE]
> Du kan inte använda åtgärden Skriv för att konstruera inga utdata, inklusive objekt, matriser och andra typer som stöds av logikappar som XML och binary.

Till exempel välja tal som är större än två:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| från | Ja | Matris | Källmatrisen |
| där | Ja | Sträng | Villkoret som kopplas till varje element från källmatrisen. Om inga värden uppfyller de `where` villkoret, resultatet är en tom matris. |
||||| 

Utdata från den `query` åtgärden är en matris som har element från Indatamatrisen som uppfyller villkoret.

## <a name="table-action"></a>Åtgärden för tabellen

Du kan konvertera en matris till en CSV- eller HTML-tabell. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| från | Ja | Matris | Källmatrisen. Om den `from` egenskapsvärdet är en tom matris, utdata är en tom tabell. | 
| Format | Ja | Sträng | Tabellformatet som du vill ”CSV” eller ”HTML” | 
| Kolumner | Nej | Matris | De tabellkolumner som du vill använda. Används för att åsidosätta standard tabellform. | 
| kolumnrubrik | Nej | Sträng | Kolumnrubriken | 
| värde i kolumnen | Ja | Sträng | Värdet i kolumnen | 
||||| 

Anta att du definierar en tabell åtgärder som det här exemplet:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

Och använda denna matris för `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Här är utdata från det här exemplet:

<table><thead><tr><th>ID</th><th>Namn</th></tr></thead><tbody><tr><td>0</td><td>Äpplen</td></tr><tr><td>1</td><td>orange</td></tr></tbody></table>

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

Här är utdata från det här exemplet:

<table><thead><tr><th>Skapa ID</th><th>Beskrivning</th></tr></thead><tbody><tr><td>0</td><td>ny äpplen</td></tr><tr><td>1</td><td>ny orange</td></tr></tbody></table>

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
> Du kan ange varaktigheten vänta med antingen den `interval` objekt eller `until` objekt, men inte båda.

| Elementnamn | Krävs | Typ | Beskrivning | 
| ------------ | -------- | ---- | ----------- | 
| tills | Nej | JSON-objekt | Vänta varaktighet baserat på en punkt i tiden | 
| tills tidsstämpel | Ja | Sträng | Tidpunkten i [UTC-datum tidsformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) när väntetiden upphör att gälla | 
| interval | Nej | JSON-objekt | Vänta varaktighet baserat på intervall och antalet | 
| intervall | Ja | Sträng | Tidsenhet. Använd bara ett av följande värden: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka” eller ”månad” | 
| intervall för antal | Ja | Integer | Ett positivt heltal som representerar antalet intervallenheter som används för vänta varaktighet | 
||||| 

## <a name="workflow-action"></a>Arbetsflödesåtgärd

Den här åtgärden låter dig kapsla ett arbetsflöde. Logic Apps-motorn utför en åtkomstkontroll på underordnade arbetsflödet, mer specifikt utlösaren, så du måste ha åtkomst till det underordnade arbetsflödet. Exempel:

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
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
| frågor | Nej | JSON-objekt | Representerar alla frågeparametrar som du vill inkludera i URL: en. <p>Till exempel `"queries": { "api-version": "2015-02-01" }` lägger till `?api-version=2015-02-01` till URL: en. | 
| rubriker | Nej | JSON-objekt | Representerar varje huvud som skickades i begäran. <p>Till exempel ange språk och Skriv begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| brödtext | Nej | JSON-objekt | Representerar nyttolasten som skickas till slutpunkten. | 
||||| 

Den här åtgärden utdata är baserat på vad du anger i den `Response` åtgärd för arbetsflöden. Om arbetsflöden som inte definierar en `Response` , utdata är tom.

## <a name="collection-actions-overview"></a>Samling åtgärder översikt

För att du kan styra arbetsflödeskörning, åtgärder för samlingen kan innehålla andra åtgärder. Du kan direkt referera till refererar till åtgärder i en samling utanför samlingen. Till exempel om du definierar en `Http` åtgärden i en omfattning `@body('http')` fortfarande är giltigt var som helst i ett arbetsflöde. Dessutom kan åtgärder i en samling endast ”kör” andra åtgärder i samma samling.

## <a name="if-action"></a>Om åtgärden

Den här åtgärden, vilket är en villkorlig programsats, kan du utvärdera ett villkor och köra en gren baserat på om uttrycket utvärderas som SANT. Om villkoret har utvärderas som SANT, är villkoret markerad med ”Succeeded” status. Åtgärder som är den `actions` eller `else` objekt utvärderas till dessa värden:

* ”Lyckades” när de körs och lyckas
* ”Misslyckades” när de körs och misslyckas
* ”Hoppas över” när grenen respektive kan inte köras

Lär dig mer om [villkorssatser i logikappar](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | JSON-objekt | De inre åtgärderna för att köras när `expression` utvärderas till `true` | 
| uttryck | Ja | Sträng | Uttrycket som ska utvärderas |
| annan | Nej | JSON-objekt | De inre åtgärderna för att köras när `expression` utvärderas till `false` |
||||| 

Exempel:

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Hur villkor kan använda uttryck i åtgärder

Här följer några exempel på hur du kan använda uttryck i villkor:
  
| JSON-uttryck | Resultat | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Ett värde som utvärderas som SANT gör detta villkor att skicka. Stöder endast booleska uttryck. Om du vill konvertera andra typer till Boolean använder dessa funktioner: `empty` eller `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Stöder funktioner för jämförelse. I det här exemplet åtgärden endast körs när utdata från action1 är större än tröskelvärdet. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Stöder funktioner för logik för att skapa kapslade booleska uttryck. I det här exemplet körs åtgärden när utdata från action1 är mer än tröskelvärdet eller under 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Om du vill kontrollera om en matris har alla objekt som kan du använda matrisen. I det här exemplet körs åtgärden när fel matrisen är tom. | 
| `"expression": "parameters('hasSpecialAction')"` | Det här uttrycket orsakar ett fel och är inte ett giltigt villkor. Villkor måste använda den ”@” symbol. | 
||| 

## <a name="switch-action"></a>Växla åtgärd

Den här åtgärden som är en switch-instruktionen, utför olika åtgärder baserat på specifika värden för ett objekt, uttryck eller token. Den här åtgärden utvärderar objektet, uttryck eller token, väljer fallet som matchar resultatet och kör åtgärder för endast detta. När aldrig matchar resultatet, körs standardåtgärden. När switch-instruktionen körs ska bara ett fall matcha resultatet. Lär dig mer om [växla instruktioner i logikappar](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| uttryck | Ja | Sträng | Objektet, uttryck eller token för att utvärdera | 
| fall | Ja | JSON-objekt | Innehåller uppsättningar med inre åtgärder som körs baserat på uttryckresultatet. | 
| Ärende | Ja | Sträng | Värdet som stämmer överens med resultatet | 
| åtgärder | Ja | JSON-objekt | De inre åtgärder som körs för fall matchar uttryckresultatet | 
| standard | Nej | JSON-objekt | De inre åtgärder som körs när inga fall matchar resultatet | 
||||| 

Exempel:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Foreach-åtgärd

Åtgärden slingor upprepas i en matris och utför interna åtgärder på varje element i matrisen. Som standard körs Foreach-loop parallellt. För det maximala antalet parallella cykler som ”för varje” slingor kan köra, se [gränser och config](../logic-apps/logic-apps-limits-and-config.md). Om du vill köra varje cykel sekventiellt i `operationOptions` parameter till `Sequential`. Lär dig mer om [Foreach körs i en loop i logikappar](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | JSON-objekt | Inre åtgärder för att köra i den här slingan | 
| foreach | Ja | Sträng | Matrisen för att gå igenom | 
| operationOptions | Nej | Sträng | Anger åtgärden alternativ för att anpassa beteende. Stöder för närvarande endast `Sequential` för att köra sekventiellt iterationer där standardbeteendet är parallellt. |
||||| 

Exempel:

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
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Förrän åtgärden

Åtgärden slingor körs inre åtgärder tills ett villkor utvärderas som SANT. Lär dig mer om [”till” loopar i logikappar](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- | 
| åtgärder | Ja | JSON-objekt | Inre åtgärder för att köra i den här slingan | 
| uttryck | Ja | Sträng | Uttrycket som ska utvärderas efter varje iteration | 
| Gränsen | Ja | JSON-objekt | Gränser för loopen. Måste ange minst en gräns. | 
| antal | Nej | Integer | Gränsen för antalet iterationer för att utföra | 
| timeout | Nej | Sträng | Timeout-gränsen i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger hur länge slingan ska köras |
||||| 

Exempel:

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
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Scope-åtgärd

Den här åtgärden kan du logiskt gruppera åtgärder i ett arbetsflöde. Omfånget också hämtar sin egen status när alla åtgärder i detta scope Slutför körs. Lär dig mer om [scope](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Namn | Krävs | Typ | Beskrivning | 
| ---- | -------- | ---- | ----------- |  
| åtgärder | Ja | JSON-objekt | Inre åtgärder för att köra inuti scope |
||||| 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md)
* Lär dig mer om [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows)