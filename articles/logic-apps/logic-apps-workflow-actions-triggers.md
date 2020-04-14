---
title: Schemareferens för utlösare och åtgärdstyper
description: Schemareferensguide för utlösare och åtgärdstyper för arbetsflödesdefinition i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 7e14cc00d1bd716b3e4880e585b05447d2e55e2b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257444"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Schemareferensguide för utlösare och åtgärdstyper i Azure Logic Apps

Den här referensen beskriver de allmänna typer som används för att identifiera utlösare och åtgärder i logikappens underliggande arbetsflödesdefinition, som beskrivs och valideras av [arbetsflödesdefinitionsspråket](../logic-apps/logic-apps-workflow-definition-language.md). Information om hur du hittar specifika anslutningsutlösare och åtgärder som du kan använda i logikapparna finns i listan under [översikten Kopplingar](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Översikt över utlösare

Varje arbetsflöde innehåller en utlösare som definierar de anrop som instansierar och startar arbetsflödet. Här är de allmänna utlösarkategorierna:

* En *polling* avsökningsutlösare som kontrollerar en tjänsts slutpunkt med jämna mellanrum

* En *push-utlösare* som skapar en prenumeration på en slutpunkt och tillhandahåller en *motringnings-URL* så att slutpunkten kan meddela utlösaren när den angivna händelsen inträffar eller data är tillgängliga. Utlösaren väntar sedan på slutpunktens svar innan bränningen.

Utlösare har dessa element på den översta nivån, även om vissa är valfria:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*utlösarnamn*> | Sträng | Namnet på utlösaren | 
| <*trigger-typ*> | Sträng | Utlösartypen "Http" eller "ApiConnection" | 
| <*trigger-ingångar*> | JSON-objekt | De indata som definierar utlösarens beteende | 
| <*tidsenheten*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren avfyras: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*antal tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här är de lägsta och högsta intervallen: <p>- Månad: 1-16 månader </br>- Dag: 1-500 dagar </br>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*matris-med-villkor*> | Matris | En matris som innehåller ett eller flera [villkor](#trigger-conditions) som avgör om arbetsflödet ska köras. Endast tillgängligt för utlösare. | 
| <*runtime-config-alternativ*> | JSON-objekt | Du kan ändra utlösarkörningsbeteendet genom att ange `runtimeConfiguration` egenskaper. Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options). | 
| <*splitOn-uttryck*> | Sträng | För utlösare som returnerar en matris kan du ange ett uttryck som [delar *ellerbaterar* ](#split-on-debatch) matrisobjekt i flera arbetsflödesinstanser för bearbetning. | 
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista över utlösaretyper

Varje utlösartyp har ett annat gränssnitt och indata som definierar utlösarens beteende. 

### <a name="built-in-triggers"></a>Inbyggda utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Kontrollerar eller *av omröstningar* av en slutpunkt. Den här slutpunkten måste överensstämma med ett specifikt utlösarkontrakt antingen med hjälp av ett "202" asynkront mönster eller genom att returnera en matris. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Skapar en samtalsbar slutpunkt för logikappen men anropar den angivna URL:en för att registrera eller avregistrera. |
| [**Upprepning**](#recurrence-trigger) | Bränder baserat på ett definierat schema. Du kan ange ett framtida datum och tid för att avfyra den här utlösaren. Baserat på frekvensen kan du också ange tider och dagar för att köra arbetsflödet. | 
| [**Förfrågan**](#request-trigger)  | Skapar en samtalsbar slutpunkt för logikappen och kallas även en "manuell" utlösare. Se till exempel [Anropa, utlösare eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Hanterade API-utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**ApiConnection (ApiConnection)**](#apiconnection-trigger) | Kontrollerar eller *av söker* efter en slutpunkt med hjälp av [Microsoft-hanterade API:er](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Skapar en samtalsbar slutpunkt för logikappen genom att anropa [Microsoft-hanterade API:er](../connectors/apis-list.md) för att prenumerera och avsluta prenumerationen. | 
||| 

## <a name="triggers---detailed-reference"></a>Utlösare – detaljerad referens

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection-utlösare  

Den här utlösaren kontrollerar eller *av söker* efter en slutpunkt med hjälp av [Microsoft-hanterade API:er](../connectors/apis-list.md) så att parametrarna för den här utlösaren kan skilja sig beroende på slutpunkten. Många avsnitt i den här utlösardefinitionen är valfria. Utlösarens beteende beror på om avsnitt ingår eller inte.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Sträng | Namnet på utlösaren |
| <*anslutningsnamn*> | Sträng | Namnet på anslutningen till det hanterade API:et som används i arbetsflödet |
| <*metodtyp*> | Sträng | HTTP-metoden för kommunikation med det hanterade API:et: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-drift*> | Sträng | API-åtgärden att anropa |
| <*tidsenheten*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren avfyras: "Second", "Minute", "Hour", "Day", "Week", "Month" |
| <*antal tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här är de lägsta och högsta intervallen: <p>- Månad: 1-16 månader </br>- Dag: 1-500 dagar </br>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. |
||||

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som ska inkluderas med API-anropet. `"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. | 
| <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). | 
| <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | 
| <*splitOn-uttryck*> | Sträng | För utlösare som returnerar matriser refererar det här uttrycket till den matris som ska användas så att du kan skapa och köra en arbetsflödesinstans för varje matrisobjekt i stället för att använda en "för varje"-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras inom utlösarens brödtextinnehåll:`@triggerbody()?['value']` |
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). |
||||

*Utdata*
 
| Element | Typ | Beskrivning |
|---------|------|-------------|
| Headers | JSON-objekt | Rubrikerna från svaret |
| body | JSON-objekt | Kroppen från svaret |
| statuskod | Integer | Statuskoden från svaret |
|||| 

*Exempel*

Den här utlösardefinitionen söker efter e-post varje dag i inkorgen för ett Office 365 Outlook-konto:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook-utlösare

Den här utlösaren skickar en prenumerationsbegäran till en slutpunkt med hjälp av ett [Microsoft-hanterat API](../connectors/apis-list.md), tillhandahåller en *motringnings-URL* till den plats där slutpunkten kan skicka ett svar och väntar på att slutpunkten ska svara. Mer information finns i [Slutpunktsprenumerationer](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*anslutningsnamn*> | Sträng | Namnet på anslutningen till det hanterade API:et som används i arbetsflödet | 
| <*kroppsinnehåll*> | JSON-objekt | Allt meddelandeinnehåll som ska skickas som nyttolast till det hanterade API:et | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som ska inkluderas med API-anropet <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. | 
| <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). | 
| <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | 
| <*splitOn-uttryck*> | Sträng | För utlösare som returnerar matriser refererar det här uttrycket till den matris som ska användas så att du kan skapa och köra en arbetsflödesinstans för varje matrisobjekt i stället för att använda en "för varje"-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras inom utlösarens brödtextinnehåll:`@triggerbody()?['value']` |
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

*Exempel*

Den här utlösardefinitionen prenumererar på Office 365 Outlook API, tillhandahåller en motringnings-URL till API-slutpunkten och väntar på att slutpunkten ska svara när ett nytt e-postmeddelande anländer.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-utlösare

Den här utlösaren skickar en begäran till den angivna HTTP- eller HTTPS-slutpunkten baserat på det angivna upprepningsschemat. Utlösaren kontrollerar sedan svaret för att avgöra om arbetsflödet körs.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `method` | <*metodtyp*> | Sträng | Den metod som ska användas för att skicka den utgående begäran: "GET", "PUT", "POST", "PATCH" eller "DELETE" |
| `uri` | <*HTTP-eller-HTTPS-slutpunkt-URL*> | Sträng | URL:en för HTTP- eller HTTPS-slutpunkt där du vill skicka den utgående begäran. Maximal strängstorlek: 2 KB <p>För en Azure-tjänst eller -resurs innehåller den här URI-syntaxen resurs-ID och sökvägen till den resurs som du vill komma åt. |
| `frequency` | <*tidsenheten*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren avfyras: "Second", "Minute", "Hour", "Day", "Week", "Month" |
| `interval` | <*antal tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här är de lägsta och högsta intervallen: <p>- Månad: 1-16 månader </br>- Dag: 1-500 dagar </br>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. |
|||||

*Valfri*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `headers` | <*rubrikinnehåll*> | JSON-objekt | Alla rubriker som du måste inkludera med begäran <p>Om du till exempel vill ange språk och typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som du behöver använda i begäran <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i begäran. |
| `body` | <*kroppsinnehåll*> | JSON-objekt | Meddelandeinnehållet som ska skickas som nyttolast med begäran |
| `authentication` | <*autentiseringstyp-och-egenskapsvärden*> | JSON-objekt | Autentiseringsmodellen som begäran använder för att autentisera utgående begäranden. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Utöver Scheduler `authority` stöds egenskapen. När det inte anges är `https://management.azure.com/`standardvärdet , men du kan använda ett annat värde. |
| `retryPolicy` > `type` | <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). |
| `operationOptions` | <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). |
|||||

*Utdata*

| Element | Typ | Beskrivning |
|---------|------|-------------| 
| Headers | JSON-objekt | Rubrikerna från svaret | 
| body | JSON-objekt | Kroppen från svaret | 
| statuskod | Integer | Statuskoden från svaret | 
|||| 

*Krav för inkommande begäranden*

För att kunna fungera bra med logikappen måste slutpunkten överensstämma med ett specifikt utlösarmönster eller kontrakt och känna igen dessa egenskaper:  
  
| Svar | Krävs | Beskrivning | 
|----------|----------|-------------| 
| Statuskod | Ja | Statuskoden "200 OK" startar en körning. En annan statuskod startar inte en körning. | 
| Försök igen efter rubrik | Inga | Antalet sekunder tills logikappen avstämmer slutpunkten igen | 
| Platshuvud | Inga | URL:en som ska anropas vid nästa avsökningsintervall. Om inget anges används den ursprungliga WEBBADRESSEN. | 
|||| 

*Exempel på beteenden för olika begäranden*

| Statuskod | Försök igen efter | Beteende | 
|-------------|-------------|----------|
| 200 | {ingen} | Kör arbetsflödet och kontrollera sedan mer data igen efter den definierade upprepningen. | 
| 200 | 10 sekunder | Kör arbetsflödet och kontrollera sedan mer data igen efter 10 sekunder. |  
| 202 | 60 sekunder | Utlösa inte arbetsflödet. Nästa försök sker på en minut, med förbehåll för den definierade upprepningen. Om den definierade upprepningen är mindre än en minut har det efter-huvudet företräde. Annars används den definierade upprepningen. | 
| 400 | {ingen} | Felaktig begäran, kör inte arbetsflödet. Om `retryPolicy` inget har definierats används standardprincipen. När antalet återförsök har nåtts söker utlösaren igen efter data efter den definierade upprepningen. | 
| 500 | {ingen}| Serverfel, kör inte arbetsflödet. Om `retryPolicy` inget har definierats används standardprincipen. När antalet återförsök har nåtts söker utlösaren igen efter data efter den definierade upprepningen. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook-utlösare  

Den här utlösaren gör logikappen uppringbar genom att skapa en slutpunkt som kan registrera en prenumeration genom att anropa den angivna slutpunkts-URL:en. När du skapar den här utlösaren i arbetsflödet ringer en utgående begäran samtalet för att registrera prenumerationen. På så sätt kan utlösaren börja lyssna efter händelser. När en åtgärd gör den här utlösaren ogiltig, ringer en utgående begäran automatiskt för att avbryta prenumerationen. Mer information finns i [Slutpunktsprenumerationer](#subscribe-unsubscribe).

Du kan också ange [asynkrona gränser för](#asynchronous-limits) en **HTTPWebhook-utlösare.** Utlösarens beteende beror på de avsnitt som du använder eller utelämnar.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Vissa värden, till exempel <*metodtyp*>, är tillgängliga `"subscribe"` för `"unsubscribe"` både objekt och objekt.

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | Sträng | HTTP-metoden som ska användas för prenumerationsbegäran: "GET", "PUT", "POST", "PATCH" eller "DELETE" | 
| <*url till slutpunkt-prenumerera*> | Sträng | Slutpunkts-URL:en för att skicka prenumerationsbegäran | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | Sträng | HTTP-metoden som ska användas för begäran om annullering: "GET", "PUT", "POST", "PATCH" eller "DELETE" | 
| <*url för att avsluta prenumerationen på slutpunkt*> | Sträng | Slutpunkts-URL:en för att skicka begäran om annullering | 
| <*kroppsinnehåll*> | Sträng | Allt meddelandeinnehåll som ska skickas i prenumerations- eller annulleringsbegäran | 
| <*autentiseringstyp*> | JSON-objekt | Autentiseringsmodellen som begäran använder för att autentisera utgående begäranden. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). | 
| <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | 
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

*Utdata* 

| Element | Typ | Beskrivning |
|---------|------|-------------| 
| Headers | JSON-objekt | Rubrikerna från svaret | 
| body | JSON-objekt | Kroppen från svaret | 
| statuskod | Integer | Statuskoden från svaret | 
|||| 

*Exempel*

Den här utlösaren skapar en prenumeration på den angivna slutpunkten, tillhandahåller en unik motringnings-URL och väntar på nyligen publicerade teknikartiklar.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
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
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Utlösare för återkommande  

Den här utlösaren körs baserat på det angivna upprepningsschemat och är ett enkelt sätt att skapa ett arbetsflöde som körs regelbundet.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
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
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*tidsenheten*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren avfyras: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*antal tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här är de lägsta och högsta intervallen: <p>- Månad: 1-16 månader </br>- Dag: 1-500 dagar </br>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*startdatum-tid-med-format-YYY-MM-DDThh:mm:ss*> | Sträng | Startdatum och starttid i det här formatet: <p>YYYY-MM-DDThh:mm:ss om du anger en tidszon <p>ELLER <p>Å ÅYY-MM-DDThh:mm:ssZ om du inte anger en tidszon <p>Så om du till exempel vill ha 18 september 2017 klockan 14:00 anger du "2017-09-18T14:00:00" och anger en tidszon som "Pacific Standard Time", eller anger "2017-09-18T14:00:00Z" utan en tidszon. <p>**Anm.:** Denna starttid har högst 49 år i framtiden och måste följa [ISO 8601 datum tidsspecifikationen](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC datumtid format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC offset](https://en.wikipedia.org/wiki/UTC_offset). Om du inte anger någon tidszon måste du lägga till bokstaven "Z" i slutet utan blanksteg. Detta "Z" avser motsvarande [nautisk tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är starttiden den första förekomsten, medan utlösaren inte utlöses tidigare än starttiden för komplexa scheman. Mer information om startdatum och starttider finns i [Skapa och schemalägga aktiviteter som körs regelbundet](../connectors/connectors-native-recurrence.md). | 
| <*tidszon*> | Sträng | Gäller endast när du anger en starttid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Ange den tidszon som du vill använda. | 
| <*en eller flera timmars märken*> | Heltals- eller heltalsmatris | Om du anger "Dag" eller `frequency`"Vecka" för kan du ange ett eller flera heltal från 0 till 23, avgränsade med kommatecken, som timmar på dagen när du vill köra arbetsflödet. <p>Om du till exempel anger "10", "12" och "14" får du 10,00, 12.00 och 14.00 som timmarkeringar. | 
| <*en eller flera minuters märken*> | Heltals- eller heltalsmatris | Om du anger "Dag" eller `frequency`"Vecka" för kan du ange ett eller flera heltal från 0 till 59, avgränsade med kommatecken, som minuterna i timmen när du vill köra arbetsflödet. <p>Du kan till exempel ange "30" som minutmarkering och använda föregående exempel under timmar på dagen får du 10:30, 12:30 och 14:30. | 
| weekDays | Sträng- eller strängmatris | Om du anger "Vecka" för `frequency`kan du ange en eller flera dagar, avgränsade med kommatecken, när du vill köra arbetsflödet: "Måndag", "Tisdag", "Onsdag", "Torsdag", "Fredag", "Lördag" och "Söndag" | 
| <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). | 
| <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | 
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

*Exempel 1*

Den här grundläggande återkommande utlösaren körs dagligen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exempel 2*

Du kan ange ett startdatum och en starttid för att avfyra utlösaren. Den här återkommande utlösaren startar på det angivna datumet och utlöses sedan dagligen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exempel 3*

Denna återkommande utlösare börjar den 9 september 2017 klockan 14:00 och avfyras varje vecka klockan 10:30, 12:30 och 14:30 Pacific Standard Time:

``` json
"Recurrence": {
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

Mer information plus exempel för den här utlösaren finns i [Skapa och schemalägga aktiviteter som körs regelbundet](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Utlösare för begäran

Den här utlösaren gör din logikapp uppringbar genom att skapa en slutpunkt som kan acceptera inkommande begäranden. För den här utlösaren anger du ett JSON-schema som beskriver och validerar nyttolasten eller indata som utlösaren tar emot från den inkommande begäran. Schemat gör också utlösaregenskaper enklare att referera till från senare åtgärder i arbetsflödet.

Om du vill anropa den `listCallbackUrl` här utlösaren måste du använda API:et, som beskrivs i [REST-API:et för arbetsflödestjänsten](https://docs.microsoft.com/rest/api/logic/workflows). Mer information om hur du använder den här utlösaren som en HTTP-slutpunkt finns i [Anropa, utlösare eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*egenskapsnamn*> | Sträng | Namnet på en egenskap i JSON-schemat, som beskriver nyttolasten | 
| <*egenskapstyp*> | Sträng | Egenskapens typ | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | Sträng | Den metod som inkommande begäranden måste använda för att anropa logikappen: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relativ sökväg för accepterad parameter*> | Sträng | Den relativa sökvägen för parametern som slutpunktens URL kan acceptera | 
| <*obligatoriska egenskaper*> | Matris | En eller flera egenskaper som kräver värden | 
| <*max-körningar*> | Integer | Som standard körs arbetsflödesinstanser samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Information om hur du ändrar den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra utlösarkonsitency](#change-trigger-concurrency). | 
| <*max-runs-kö*> | Integer | När arbetsflödet redan kör det maximala antalet instanser, som `runtimeConfiguration.concurrency.runs` du kan ändra baserat på egenskapen, läggs alla nya körningar i den här kön upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | 
| <*funktion-alternativ*> | Sträng | Du kan ändra standardbeteendet `operationOptions` genom att ange egenskapen. Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

*Exempel*

Den här utlösaren anger att en inkommande begäran måste använda HTTP POST-metoden för att anropa utlösaren och innehåller ett schema som validerar indata från den inkommande begäran:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Utlösarvillkor

För alla utlösare och endast utlösare kan du inkludera en matris som innehåller ett eller flera uttryck för villkor som avgör om arbetsflödet ska köras. Om du `conditions` vill lägga till egenskapen i en utlösare i arbetsflödet öppnar du logikappen i kodvyredigeraren.

Du kan till exempel ange att en utlösare bara utlöses när en webbplats returnerar ett `conditions` internt serverfel genom att referera till utlösarens statuskod i egenskapen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Som standard utlöses en utlösare först efter att ha fått ett "200 OK"-svar. När ett uttryck refererar till en utlösarens statuskod ersätts utlösarens standardbeteende. Om du vill att utlösaren ska aktiveras för mer än en statuskod, till exempel statuskoden "200" och "201", måste du inkludera det här uttrycket som ditt villkor:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Utlösa flera körningar

Om utlösaren returnerar en matris för logikappen att bearbeta kan det ibland ta för lång tid att bearbeta varje matrisobjekt. I stället kan du använda egenskapen **SplitOn** i utlösaren för att *debattera* matrisen. Debatt delar upp matrisobjekten och startar en ny arbetsflödesinstans som körs för varje matrisobjekt. Den här metoden är användbar, till exempel när du vill avsöka en slutpunkt som kan returnera flera nya objekt mellan avsökningsintervallen. För det maximala antalet matrisobjekt som **SplitOn** kan bearbeta i en enda logikappkörning finns [i Gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Du kan inte använda **SplitOn** med ett synkront svarsmönster. Alla arbetsflöden som använder **SplitOn** och innehåller en svarsåtgärd `202 ACCEPTED` körs asynkront och skickar omedelbart ett svar.
>
> När utlösarkonkursen är aktiverad minskas [SplitOn-gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) avsevärt. Om antalet objekt överskrider den här gränsen inaktiveras SplitOn-funktionen.
 
Om utlösarens Swagger-fil beskriver en nyttolast som är en matris läggs **egenskapen SplitOn** automatiskt till i utlösaren. Annars lägger du till den här egenskapen i svarsnyttolasten som har den matris som du vill debattera.

*Exempel*

Anta att du har ett API som returnerar det här svaret: 
  
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

Logikappen behöver bara innehållet från `Rows`matrisen i , så att du kan skapa en utlösare som det här exemplet:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Om du `SplitOn` använder kommandot kan du inte hämta de egenskaper som finns utanför matrisen. Så för det här exemplet kan `status` du inte hämta egenskapen i svaret som returneras från API:et.
> 
> För att undvika `Rows` ett fel om egenskapen inte `?` finns använder operatorn det här exemplet.

Arbetsflödesdefinitionen `@triggerBody().name` `name` kan nu användas för `"customer-name-one"` att hämta värdena, som kommer från den första körningen och `"customer-name-two"` från den andra körningen. Så, din trigger utgångar ser ut så här exempel:

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

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Översikt över åtgärder

Azure Logic Apps innehåller olika åtgärdstyper – var och en med olika indata som definierar en åtgärds unika beteende. Åtgärderna har dessa element på hög nivå, även om vissa är valfria:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*åtgärd-namn*> | Sträng | Namnet på åtgärden | 
| <*åtgärdstyp*> | Sträng | Åtgärdstypen, till exempel "Http" eller "ApiConnection"| 
| <*indatanamn*> | Sträng | Namnet på en indata som definierar åtgärdens beteende | 
| <*indatavärde*> | Olika | Indatavärdet, som kan vara en sträng, heltal, JSON-objekt och så vidare | 
| <*föregående-trigger-eller-åtgärd-status*> | JSON-objekt | Namnet och den resulterande statusen för utlösaren eller åtgärden som måste köras omedelbart innan den aktuella åtgärden kan köras | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i Principer för försök igen. | 
| <*runtime-config-alternativ*> | JSON-objekt | För vissa åtgärder kan du ändra åtgärdens beteende `runtimeConfiguration` vid körning genom att ange egenskaper. Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options). | 
| <*funktion-alternativ*> | Sträng | För vissa åtgärder kan du ändra standardbeteendet genom att ange egenskapen. `operationOptions` Mer information finns i [Åtgärdsalternativ](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista över åtgärdstyper

Här är några vanliga åtgärdstyper: 

* [Inbyggda åtgärdstyper](#built-in-actions) som dessa exempel och mycket mer: 

  * [**HTTP**](#http-action) för att anropa slutpunkter via HTTP eller HTTPS

  * [**Svar på**](#response-action) förfrågningar

  * [**Köra JavaScript-kod**](#run-javascript-code) för att köra JavaScript-kodavsnitt

  * [**Funktion**](#function-action) för att anropa Azure-funktioner

  * Åtgärder för dataåtgärder som [**Koppla till,**](#join-action) [**Komponera**](#compose-action), [**Tabell**](#table-action), [**Markera**](#select-action)och andra som skapar eller omvandlar data från olika indata

  * [**Arbetsflöde**](#workflow-action) för att anropa ett annat arbetsflöde för logikapp

* [Hanterade API-åtgärdstyper](#managed-api-actions) som [**ApiConnection**](#apiconnection-action) och [**ApiConnectionWebHook**](#apiconnectionwebhook-action) som anropar olika kopplingar och API:er som hanteras av Microsoft, till exempel Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub med mera

* [Styra arbetsflödesåtgärdstyper](#control-workflow-actions) som [**Om**](#if-action), [**Foreach**](#foreach-action), [**Växla**](#switch-action), [**Omfattning**](#scope-action)och [**Till**](#until-action), som innehåller andra åtgärder och hjälper dig att organisera arbetsflödeskörning

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Inbyggda åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**Komponera**](#compose-action) | Skapar en enda utdata från indata, som kan ha olika typer. | 
| [**Köra JavaScript-kod**](#run-javascript-code) | Kör JavaScript-kodavsnitt som passar inom specifika villkor. Kodkrav och mer information finns [i Lägga till och kör kodavsnitt med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funktion**](#function-action) | Anropar en Azure-funktion. | 
| [**HTTP**](#http-action) | Anropar en HTTP-slutpunkt. | 
| [**Ansluta sig till**](#join-action) | Skapar en sträng från alla objekt i en matris och separerar de objekt med ett angivet avgränsaretecken. | 
| [**Mer från Tolka JSON**](#parse-json-action) | Skapar användarvänliga token från egenskaper i JSON-innehåll. Du kan sedan referera till dessa egenskaper genom att inkludera token i logikappen. | 
| [**Söka i data**](#query-action) | Skapar en matris från objekt i en annan matris baserat på ett villkor eller filter. | 
| [**Svar**](#response-action) | Skapar ett svar på ett inkommande samtal eller en begäran. | 
| [**Välj**](#select-action) | Skapar en matris med JSON-objekt genom att omvandla objekt från en annan matris baserat på den angivna kartan. | 
| [**Tabell**](#table-action) | Skapar en CSV- eller HTML-tabell från en matris. | 
| [**Avsluta**](#terminate-action) | Stoppar ett arbetsflöde som körs aktivt. | 
| [**Vänta**](#wait-action) | Pausar arbetsflödet under en angiven varaktighet eller tills det angivna datumet och tiden. | 
| [**Arbetsflöde**](#workflow-action) | Kapslar ett arbetsflöde i ett annat arbetsflöde. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Hanterade API-åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------|  
| [**ApiConnection (ApiConnection)**](#apiconnection-action) | Anropar en HTTP-slutpunkt med hjälp av ett [Microsoft-hanterat API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Fungerar som HTTP Webhook men använder ett [Microsoft-hanterat API](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Styra arbetsflödesåtgärder

Dessa åtgärder hjälper dig att kontrollera arbetsflödeskörning och inkludera andra åtgärder. Från en kontrollarbetsflödesåtgärd kan du direkt referera till åtgärder i arbetsflödesåtgärden. Om du till exempel `Http` har en åtgärd i `@body('Http')` ett scope kan du referera till uttrycket var som helst i arbetsflödet. Åtgärder som finns i en kontrollarbetsflödesåtgärd kan dock bara "köra efter" andra åtgärder som finns i samma kontrollarbetsflödesstruktur.

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**Foreach**](#foreach-action) | Kör samma åtgärder i en loop för varje objekt i en matris. | 
| [**Om**](#if-action) | Kör åtgärder baserat på om det angivna villkoret är sant eller falskt. | 
| [**Omfattning**](#scope-action) | Kör åtgärder baserat på gruppstatus från en uppsättning åtgärder. | 
| [**Växel**](#switch-action) | Kör åtgärder ordnade i ärenden där värden från uttryck, objekt eller token matchar de värden som anges av varje enskilt fall. | 
| [**Tills**](#until-action) | Kör åtgärder i en loop tills det angivna villkoret är sant. | 
|||  

## <a name="actions---detailed-reference"></a>Åtgärder - Detaljerad referens

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>ÅTGÄRDEN APIConnection

Den här åtgärden skickar en HTTP-begäran till ett [Microsoft-hanterat API](../connectors/apis-list.md) och kräver information om API:et och parametrarna samt en referens till en giltig anslutning. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärd-namn*> | Sträng | Namnet på åtgärden som tillhandahålls av kopplingen | 
| <*api-namn*> | Sträng | Namnet på det Microsoft-hanterade API som används för anslutningen | 
| <*metodtyp*> | Sträng | HTTP-metoden för att anropa API: "GET", "PUT", "POST", "PATCH" eller "DELETE" | 
| <*api-drift*> | Sträng | API-åtgärden att anropa | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*andra åtgärdsspecifika indataegenskaper*> | JSON-objekt | Alla andra indataegenskaper som gäller för den här specifika åtgärden | 
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som ska inkluderas med API-anropet. <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. | 
| <*andra åtgärdsspecifika egenskaper*> | JSON-objekt | Alla andra egenskaper som gäller för den här specifika åtgärden | 
|||| 

*Exempel*

Den här definitionen beskriver åtgärden **Skicka ett e-postmeddelande** för Office 365 Outlook-anslutningsappen, som är ett Microsoft-hanterat API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>ÅTGÄRDEN APIConnectionWebhook

Den här åtgärden skickar en prenumerationsbegäran via HTTP till en slutpunkt med hjälp av ett [Microsoft-hanterat API](../connectors/apis-list.md), tillhandahåller en *motringnings-URL* till den plats där slutpunkten kan skicka ett svar och väntar på att slutpunkten ska svara. Mer information finns i [Slutpunktsprenumerationer](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Vissa värden, till exempel <*metodtyp*>, är tillgängliga `"subscribe"` för `"unsubscribe"` både objekt och objekt.

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärd-namn*> | Sträng | Namnet på åtgärden som tillhandahålls av kopplingen | 
| <*metodtyp*> | Sträng | HTTP-metoden som ska användas för att prenumerera eller avregistrera från en slutpunkt: "GET", "PUT", "POST", "PATCH" eller "DELETE" | 
| <*api-prenumerera-URL*> | Sträng | Den URI som ska användas för att prenumerera på API:et | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*api-avsluta prenumeration-URL*> | Sträng | Den URI som ska användas för att avregistrera sig från API:et | 
| <*rubrikinnehåll*> | JSON-objekt | Alla rubriker som ska skickas i begäran <p>Om du till exempel vill ange språk och typ på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*kroppsinnehåll*> | JSON-objekt | Allt meddelandeinnehåll som ska skickas i begäran | 
| <*autentiseringstyp*> | JSON-objekt | Autentiseringsmodellen som begäran använder för att autentisera utgående begäranden. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som ska inkluderas med API-anropet <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. | 
| <*andra åtgärdsspecifika indataegenskaper*> | JSON-objekt | Alla andra indataegenskaper som gäller för den här specifika åtgärden | 
| <*andra åtgärdsspecifika egenskaper*> | JSON-objekt | Alla andra egenskaper som gäller för den här specifika åtgärden | 
|||| 

Du kan också ange begränsningar för en **ApiConnectionWebhook-åtgärd** på samma sätt [som HTTP-asynkrona gränser](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Komponera åtgärd

Den här åtgärden skapar en enda utdata från flera indata, inklusive uttryck. Både utdata och indata kan ha alla typer som Azure Logic Apps internt stöder, till exempel matriser, JSON-objekt, XML och binära. Du kan sedan använda åtgärdens utdata i andra åtgärder. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*ingångar-till-komponera*> | Alla | Ingångarna för att skapa en enda utdata | 
|||| 

*Exempel 1*

<!-- markdownlint-disable MD038 -->
Den här åtgärdsdefinitionen sammanfogas `abcdefg ` med `1234`ett avslutande utrymme och värdet:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Här är utdata som den här åtgärden skapar:

`abcdefg 1234`

*Exempel 2*

Den här åtgärdsdefinitionen sammanfogar `abcdefg` en strängvariabel som `1234`innehåller och en heltalsvariabel som innehåller:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Här är utdata som den här åtgärden skapar:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Utföra åtgärden JavaScript-kod

Den här åtgärden kör ett JavaScript-kodavsnitt `Result` och returnerar resultaten via en token som senare åtgärder kan referera till.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*JavaScript-kod-kodavsnitt*> | Varierar | JavaScript-koden som du vill köra. Kodkrav och mer information finns [i Lägga till och kör kodavsnitt med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). <p>I `code` attributet kan kodavsnittet använda det `workflowContext` skrivskyddade objektet som indata. Det här objektet har underegenskaper som ger din kod åtkomst till resultaten från utlösaren och tidigare åtgärder i arbetsflödet. Mer information om `workflowContext` objektet finns [i Referensutlösare och åtgärdsresultat i koden](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Krävs i vissa fall*

Attributet `explicitDependencies` anger att du uttryckligen vill inkludera resultat från utlösaren, tidigare åtgärder eller båda som beroenden för kodavsnittet. Mer information om hur du lägger till dessa beroenden finns [i Lägga till parametrar för infogad kod](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

För `includeTrigger` attributet kan `true` du `false` ange eller värden.

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*tidigare åtgärder*> | Strängmatris | En matris med angivna åtgärdsnamn. Använd de åtgärdsnamn som visas i arbetsflödesdefinitionen där åtgärdsnamn använder understreck (_), inte blanksteg (" "). |
||||

*Exempel 1*

Den här åtgärden kör kod som får logikappens namn \<och returnerar texten "Hello world from logic-app-name>" som följd. I det här exemplet refererar koden till arbetsflödets namn genom att komma åt egenskapen `workflowContext.workflow.name` via det skrivskyddade `workflowContext` objektet. Mer information om `workflowContext` hur du använder objektet finns [i Referensutlösare och åtgärdsresultat i koden](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Exempel 2*

Den här åtgärden kör kod i en logikapp som utlöses när ett nytt e-postmeddelande anländer till ett Office 365 Outlook-konto. Logikappen använder också en e-poståtgärd för skicka godkännande som vidarebefordrar innehållet från det mottagna e-postmeddelandet tillsammans med en begäran om godkännande.

Koden extraherar e-postadresserna från `Body` utlösarens egenskap och `SelectedOption` returnerar adresserna tillsammans med egenskapsvärdet från godkännandeåtgärden. Åtgärden innehåller uttryckligen åtgärden skicka godkännande e-post `explicitDependencies`  >  `actions` som ett beroende i attributet.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Funktionsåtgärd

Den här åtgärden anropar en tidigare skapad [Azure-funktion](../azure-functions/functions-create-first-azure-function.md).

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*Azure-funktion-ID*> | Sträng | Resurs-ID för den Azure-funktion som du vill anropa. Här är formatet för det här värdet:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name-name*>" | 
| <*metodtyp*> | Sträng | HTTP-metoden som ska användas för att anropa funktionen: "GET", "PUT", "POST", "PATCH" eller "DELETE" <p>Om inget anges är standardmetoden "POST". | 
||||

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*rubrikinnehåll*> | JSON-objekt | Alla rubriker som ska skickas med samtalet <p>Om du till exempel vill ange språk och typ på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*kroppsinnehåll*> | JSON-objekt | Allt meddelandeinnehåll som ska skickas i begäran | 
| <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som ska inkluderas med API-anropet <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. | 
| <*andra åtgärdsspecifika indataegenskaper*> | JSON-objekt | Alla andra indataegenskaper som gäller för den här specifika åtgärden | 
| <*andra åtgärdsspecifika egenskaper*> | JSON-objekt | Alla andra egenskaper som gäller för den här specifika åtgärden | 
||||

När du sparar logikappen utför Logic Apps-motorn dessa kontroller på den refererade funktionen:

* Arbetsflödet måste ha åtkomst till funktionen.

* Arbetsflödet kan bara använda en vanlig HTTP-utlösare eller en allmän JSON-webbkrokutlösare. 

  Logic Apps-motorn hämtar och cachelagrar utlösarens URL, som används vid körning. Om någon åtgärd ogiltigförklarar den cachelagrade URL:en misslyckas dock **funktionsåtgärden** vid körning. Lös problemet genom att spara logikappen igen så att logikappen hämtar och cachelagrar utlösar-URL:en igen.

* Funktionen kan inte ha någon rutt definierad.

* Endast "funktion" och "anonyma" auktoriseringsnivåer är tillåtna. 

*Exempel*

Den här åtgärdsdefinitionen anropar den tidigare skapade funktionen GetProductID:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-åtgärd

Den här åtgärden skickar en begäran till den angivna HTTP- eller HTTPS-slutpunkten och kontrollerar svaret för att avgöra om arbetsflödet körs.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `method` | <*metodtyp*> | Sträng | Den metod som ska användas för att skicka den utgående begäran: "GET", "PUT", "POST", "PATCH" eller "DELETE" |
| `uri` | <*HTTP-eller-HTTPS-slutpunkt-URL*> | Sträng | URL:en för HTTP- eller HTTPS-slutpunkt där du vill skicka den utgående begäran. Maximal strängstorlek: 2 KB <p>För en Azure-tjänst eller -resurs innehåller den här URI-syntaxen resurs-ID och sökvägen till den resurs som du vill komma åt. |
|||||

*Valfri*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `headers` | <*rubrikinnehåll*> | JSON-objekt | Alla rubriker som du måste inkludera med begäran <p>Om du till exempel vill ange språk och typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*frågeparametrar*> | JSON-objekt | Alla frågeparametrar som du behöver använda i begäran <p>`"queries": { "api-version": "2018-01-01" }` Objektet läggs `?api-version=2018-01-01` till exempel till i anropet. |
| `body` | <*kroppsinnehåll*> | JSON-objekt | Meddelandeinnehållet som ska skickas som nyttolast med begäran |
| `authentication` | <*autentiseringstyp-och-egenskapsvärden*> | JSON-objekt | Autentiseringsmodellen som begäran använder för att autentisera utgående begäranden. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Utöver Scheduler `authority` stöds egenskapen. När det inte anges är `https://management.azure.com/`standardvärdet , men du kan använda ett annat värde. |
| `retryPolicy` > `type` | <*återförsök-beteende*> | JSON-objekt | Anpassar återförsöksbeteendet för återkommande fel, som har statuskoden 408, 429 och 5XX och eventuella anslutningsundantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*andra åtgärdsspecifika indataegenskaper*> | <*input-egenskap*> | JSON-objekt | Alla andra indataegenskaper som gäller för den här specifika åtgärden |
| <*andra åtgärdsspecifika egenskaper*> | <*egenskapsvärde*> | JSON-objekt | Alla andra egenskaper som gäller för den här specifika åtgärden |
|||||

*Exempel*

Den här åtgärdsdefinitionen får de senaste nyheterna genom att skicka en begäran till den angivna slutpunkten:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Delta i åtgärd

Den här åtgärden skapar en sträng från alla objekt i en matris och separerar de objekt med det angivna avgränsaren. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Array*> | Matris | Matrisen eller uttrycket som tillhandahåller källobjekten. Om du anger ett uttryck omsluter du uttrycket med dubbla citattecken. | 
| <*Avgränsare*> | En teckensträng | Tecknet som skiljer varje objekt i strängen | 
|||| 

*Exempel*

Anta att du har en tidigare skapad "myIntegerArray"-variabel som innehåller den här heltalsmatrisen: 

`[1,2,3,4]` 

Den här åtgärdsdefinitionen hämtar värdena från variabeln med hjälp av `variables()` funktionen i ett uttryck och skapar den här strängen med dessa värden, som avgränsas med ett kommatecken:`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Tolka JSON-åtgärd

Den här åtgärden skapar användarvänliga fält eller *token* från egenskaperna i JSON-innehåll. Du kan sedan komma åt dessa egenskaper i logikappen med hjälp av token i stället. När du till exempel vill använda JSON-utdata från tjänster som Azure Service Bus och Azure Cosmos DB kan du inkludera den här åtgärden i logikappen så att du enklare kan referera till data i utdata.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*JSON-källa*> | JSON-objekt | Det JSON-innehåll som du vill tolka | 
| <*JSON-schema*> | JSON-objekt | JSON-schemat som beskriver det underliggande JSON-innehållet, som åtgärden använder för att tolka källans JSON-innehåll. <p>**Tips:** I Logic Apps Designer kan du antingen ange schemat eller ange ett exempel nyttolast så att åtgärden kan generera schemat. | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen skapar dessa token som du kan använda i arbetsflödet men bara i åtgärder som körs efter åtgärden **Parse JSON:**

`FirstName`, `LastName`och`Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

I det här exemplet anger egenskapen "innehåll" JSON-innehåll för åtgärden som ska tolkas. Du kan också ange detta JSON-innehåll som exempelnyttolast för att generera schemat.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Egenskapen "schema" anger det JSON-schema som används för att beskriva JSON-innehållet:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Frågeåtgärd

Den här åtgärden skapar en matris från objekt i en annan matris baserat på ett angivet villkor eller filter.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Array*> | Matris | Matrisen eller uttrycket som tillhandahåller källobjekten. Om du anger ett uttryck omsluter du uttrycket med dubbla citattecken. |
| <*villkora-eller-filtrera*> | Sträng | Villkoret som används för filtrering av objekt i källmatrisen <p>**Om**inga värden uppfyller villkoret skapar åtgärden en tom matris. |
|||| 

*Exempel*

Den här åtgärdsdefinitionen skapar en matris som har värden som är större än det angivna värdet, vilket är två:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Åtgärd för svar  

Den här åtgärden skapar nyttolasten för svaret på en HTTP-begäran. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*svarsstatus-kod*> | Integer | HTTP-statuskoden som skickas till den inkommande begäran. Standardkoden är "200 OK", men koden kan vara valfri giltig statuskod som börjar med 2xx, 4xx eller 5xx, men inte med 3xxx. | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*svarshuvuden*> | JSON-objekt | En eller flera rubriker som ska inkluderas med svaret | 
| <*svar-kropp*> | Olika | Svarstexten, som kan vara en sträng, JSON-objekt eller till och med binärt innehåll från en tidigare åtgärd | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen skapar ett svar på en HTTP-begäran med den angivna statuskoden, meddelandetexten och meddelanderubrikerna:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Begränsningar*

Till skillnad från andra åtgärder har **svarsåtgärden** särskilda begränsningar: 

* Arbetsflödet kan bara använda åtgärden **Svar** när arbetsflödet startar med en HTTP-begärandeutlösare, vilket innebär att arbetsflödet måste utlösas av en HTTP-begäran.

* Arbetsflödet kan använda åtgärden **Svar** var som helst *utom* i **Foreach-loopar,** Tills-loopar, inklusive sekventiella loopar och parallella grenar. **Until** 

* Den ursprungliga HTTP-begäran får arbetsflödets svar först när alla åtgärder som krävs av **svarsåtgärden** är klara inom [http-tidsgränsen för begäran.](../logic-apps/logic-apps-limits-and-config.md#request-limits)

  Men om arbetsflödet anropar en annan logikapp som ett kapslat arbetsflöde väntar det överordnade arbetsflödet tills det kapslade arbetsflödet är klart, oavsett hur mycket tid som går innan det kapslade arbetsflödet är klart.

* När arbetsflödet använder åtgärden **Svar** och ett synkront svarsmönster kan arbetsflödet inte också använda **kommandot splitOn** i utlösardefinitionen eftersom kommandot skapar flera körningar. Sök efter det här fallet när PUT-metoden används, och om den är sann, returnera ett "dåligt begäran" svar.

  Annars, om arbetsflödet använder **kommandot splitOn** och en **svarsåtgärd,** körs arbetsflödet asynkront och returnerar omedelbart ett "202 ACCEPTED"-svar.

* När arbetsflödets körning når åtgärden **Svar,** men den inkommande begäran redan har fått ett svar, **markeras svarsåtgärden** som "Misslyckades" på grund av konflikten. Och som ett resultat är din logikappkörning också markerad med statusen "Misslyckades".

<a name="select-action"></a>

### <a name="select-action"></a>Välj åtgärd

Den här åtgärden skapar en matris med JSON-objekt genom att omvandla objekt från en annan matris baserat på den angivna kartan. Utdatamatrisen och källmatrisen har alltid samma antal objekt. Även om du inte kan ändra antalet objekt i utdatamatrisen kan du lägga till eller ta bort egenskaper och deras värden för dessa objekt. Egenskapen `select` anger minst ett nyckelvärdespar som definierar kartan för att omvandla objekt i källmatrisen. Ett nyckelvärdespar representerar en egenskap och dess värde för alla objekt i utdatamatrisen.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Array*> | Matris | Matrisen eller uttrycket som tillhandahåller källobjekten. Se till att du omsluter ett uttryck med dubbla citattecken. <p>**Om**källmatrisen är tom skapar åtgärden en tom matris. | 
| <*nyckelnamn*> | Sträng | Egenskapsnamnet som tilldelats resultatet från <*uttryck*> <p>Om du vill lägga till en ny egenskap för alla objekt i utdatamatrisen anger du ett <*nyckelnamn*> för den egenskapen och ett <*uttryck*> för egenskapsvärdet. <p>Om du vill ta bort en egenskap från alla objekt i matrisen utelämnar du <*nyckelnamn*> för den egenskapen. | 
| <*Uttryck*> | Sträng | Uttrycket som omvandlar objektet i källmatrisen och tilldelar resultatet till <*nyckelnamn*> | 
|||| 

Åtgärden **Välj** skapar en matris som utdata, så alla åtgärder som vill använda utdata måste antingen acceptera en matris eller konvertera matrisen till den typ som konsumentåtgärden accepterar. Om du till exempel vill konvertera utdatamatrisen till en sträng kan du skicka matrisen till åtgärden **Komponera** och sedan referera till utdata från åtgärden **Komponera** i dina andra åtgärder.

*Exempel*

Den här åtgärdsdefinitionen skapar en JSON-objektmatris från en heltalsmatris. Åtgärden itererar genom källmatrisen, hämtar varje `@item()` heltalsvärde med hjälp`number`av uttrycket och tilldelar varje värde till egenskapen " " i varje JSON-objekt:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Här är matrisen som den här åtgärden skapar:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Om du vill använda den här matrisutdatan i andra åtgärder skickar du utdata till en **Compose-åtgärd:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Du kan sedan använda utdata från åtgärden **Komponera** i dina andra åtgärder, till exempel **Office 365 Outlook - Skicka en e-poståtgärd:**

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Åtgärd för tabell

Den här åtgärden skapar en CSV- eller HTML-tabell från en matris. För matriser med JSON-objekt skapar den här åtgärden automatiskt kolumnrubrikerna från objektens egenskapsnamn. För matriser med andra datatyper måste du ange kolumnrubriker och värden. Den här matrisen innehåller till exempel egenskaperna "ID" och "Product_Name" som den här åtgärden kan använda för kolumnrubriknamnen:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| \<*CSV- eller* HTML->| Sträng | Formatet för den tabell som du vill skapa | 
| <*Array*> | Matris | Matrisen eller uttrycket som tillhandahåller källobjekten för tabellen <p>**Om**källmatrisen är tom skapar åtgärden en tom tabell. | 
|||| 

*Valfri*

Om du vill ange eller anpassa `columns` kolumnrubriker och värden använder du matrisen. När `header-value` par har samma rubriknamn visas deras värden i samma kolumn under rubriknamnet. Annars definierar varje unik rubrik en unik kolumn.

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*kolumnnamn*> | Sträng | Rubriknamnet för en kolumn | 
| <*kolumnvärde*> | Alla | Värdet i den kolumnen | 
|||| 

*Exempel 1*

Anta att du har en tidigare skapad "myItemArray"-variabel som för närvarande innehåller den här matrisen:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Den här åtgärdsdefinitionen skapar en CSV-tabell från variabeln "myItemArray". Uttrycket som `from` används av egenskapen hämtar matrisen från "myItemArray" med hjälp av `variables()` funktionen:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Här är CSV-tabellen som den här åtgärden skapar: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exempel 2*

Den här åtgärdsdefinitionen skapar en HTML-tabell från variabeln "myItemArray". Uttrycket som `from` används av egenskapen hämtar matrisen från "myItemArray" med hjälp av `variables()` funktionen:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Här är HTML-tabellen som den här åtgärden skapar: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Äpplen</td></tr><tr><td>1</td><td>Apelsiner</td></tr></tbody></table>

*Exempel 3*

Den här åtgärdsdefinitionen skapar en HTML-tabell från variabeln "myItemArray". Det här exemplet åsidosätter dock standardrubriknamnen för kolumnrubriker med "Stock_ID" och "Beskrivning", och ordet "Organisk" läggs till i värdena i kolumnen "Beskrivning".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Här är HTML-tabellen som den här åtgärden skapar: 

<table><thead><tr><th>Stock_ID</th><th>Beskrivning</th></tr></thead><tbody><tr><td>0</td><td>Ekologiska äpplen</td></tr><tr><td>1</td><td>Ekologiska apelsiner</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Avsluta åtgärd

Den här åtgärden stoppar körningen för en arbetsflödesinstans, avbryter alla pågående åtgärder, hoppar över alla återstående åtgärder och returnerar den angivna statusen. Du kan till exempel använda åtgärden **Avsluta** när logikappen måste avslutas helt från ett feltillstånd. Den här åtgärden påverkar inte redan slutförda åtgärder och kan inte visas i **Foreach** och **Tills-loopar,** inklusive sekventiella loopar.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Status*> | Sträng | Statusen för att returnera körningen: "Misslyckades", "Annullerad" eller "Lyckades" |
|||| 

*Valfri*

Egenskaperna för objektet "runStatus" gäller endast när egenskapen "runStatus" har angetts till statusen "Misslyckades".

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*felkod-eller-namn*> | Sträng | Koden eller namnet på felet |
| <*felmeddelande*> | Sträng | Meddelandet eller texten som beskriver felet och eventuella åtgärder som appanvändaren kan vidta | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen stoppar en arbetsflödeskörning, anger körstatusen till "Misslyckades" och returnerar status, en felkod och ett felmeddelande:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Vänta åtgärd

Den här åtgärden pausar arbetsflödeskörningen för det angivna intervallet eller fram till den angivna tiden, men inte båda.

*Angivet intervall*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Angiven tid*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*antal enheter*> | Integer | För åtgärden **Fördröjning** är antalet enheter som ska vänta | 
| <*Intervall*> | Sträng | För åtgärden **Fördröjning** väntar intervallet: "Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", | 
| <*datum-tid-stämpel*> | Sträng | För åtgärden **Fördröjning tills,** datum och tid för att återuppta körningen. Det här värdet måste använda [UTC-datumtidsformatet](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exempel 1*

Den här åtgärdsdefinitionen pausar arbetsflödet i 15 minuter:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Exempel 2*

Den här åtgärdsdefinitionen pausar arbetsflödet tills den angivna tiden:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Åtgärden Arbetsflöde

Den här åtgärden anropar en annan tidigare skapad logikapp, vilket innebär att du kan inkludera och återanvända andra logikapparbetsflöden. Du kan också använda utdata från den underordnade eller *kapslade* logikappen i åtgärder som följer den kapslade logikappen, förutsatt att den underordnade logikappen returnerar ett svar.

Logic Apps-motorn kontrollerar åtkomsten till den utlösare du vill anropa, så se till att du kan komma åt utlösaren. Dessutom måste den kapslade logikappen uppfylla följande kriterier:

* En utlösare gör den kapslade logikappen callable, till exempel en [begäran eller](#request-trigger) [HTTP-utlösare](#http-trigger)

* Samma Azure-prenumeration som din överordnade logikapp

* Om du vill använda utdata från den kapslade logikappen i den överordnade logikappen måste den kapslade logikappen ha en [svarsåtgärd](#response-action)

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*kapslade-logik-app-namn*> | Sträng | Namnet på den logikapp som du vill ringa | 
| <*utlösarnamn*> | Sträng | Namnet på utlösaren i den kapslade logikappen som du vill anropa | 
| <*Azure-prenumeration-ID*> | Sträng | Azure-prenumerations-ID för den kapslade logikappen |
| <*Azure-resurs-grupp*> | Sträng | Azure-resursgruppsnamnet för den kapslade logikappen |
| <*kapslade-logik-app-namn*> | Sträng | Namnet på den logikapp som du vill ringa |
||||

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*rubrikinnehåll*> | JSON-objekt | Alla rubriker som ska skickas med samtalet | 
| <*kroppsinnehåll*> | JSON-objekt | Allt meddelandeinnehåll som ska skickas med samtalet | 
||||

*Utdata*

Den här åtgärdens utdata varierar beroende på den kapslade logikappens svarsåtgärd. Om den kapslade logikappen inte innehåller en svarsåtgärd är utgångarna tomma.

*Exempel*

När åtgärden "Start_search" har slutförts anropar den här arbetsflödesåtgärdsdefinitionen en annan logikapp med namnet "Get_product_information", som skickar in de angivna ingångarna:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Information om kontroll av arbetsflödesåtgärder

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Föraningsåtgärd

Den här loopningsåtgärden itererar genom en matris och utför åtgärder på varje matrisobjekt. Som standard körs "för varje" slinga parallellt upp till ett maximalt antal slingor. För detta maximum, se [Gränser och config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Lär dig hur du [skapar "för varje" loopar](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärd-1... N*> | Sträng | Namnen på de åtgärder som körs på varje matrisobjekt | 
| <*åtgärd-definition-1... N*> | JSON-objekt | Definitionerna av de åtgärder som körs | 
| <*för-varje-uttryck*> | Sträng | Uttrycket som refererar till varje objekt i den angivna matrisen | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Räkna*> | Integer | Som standard körs "för varje" loopiterationer samtidigt (samtidigt eller parallellt) upp till [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*> värde finns i [Ändra "för varje" loop samtidighet](#change-for-each-concurrency). | 
| <*funktion-alternativ*> | Sträng | Om du vill köra en "för varje" loop sekventiellt, i stället för parallellt, `Sequential` ställer du in antingen <*operation-alternativ*> till eller <*räkna*> till `1`, men inte båda. Mer information finns i [Kör "för varje" loopar sekventiellt](#sequential-for-each). | 
|||| 

*Exempel*

Denna "för varje" loop skickar ett e-postmeddelande för varje objekt i matrisen, som innehåller bilagor från ett inkommande e-postmeddelande. Loopen skickar ett e-postmeddelande, inklusive bilagan, till en person som granskar bilagan.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Om du bara vill ange en matris som skickas som utdata från utlösaren hämtar det här uttrycket <*matrisnamn*> matris från utlösartexten. För att undvika ett fel om matrisen inte `?` finns använder uttrycket operatorn:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Om åtgärd

Den här åtgärden, som är en *villkorssats,* utvärderar ett uttryck som representerar ett villkor och kör en annan gren baserat på om villkoret är sant eller falskt. Om villkoret är sant markeras villkoret med statusen "Lyckades". Läs om hur du [skapar villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Villkor*> | JSON-objekt | Villkoret, som kan vara ett uttryck, för att utvärdera | 
| <*åtgärd-1*> | JSON-objekt | Åtgärden som ska köras när <*villkor*> utvärderas till true | 
| <*åtgärdsdefinition*> | JSON-objekt | Definitionen av åtgärden | 
| <*åtgärd-2*> | JSON-objekt | Åtgärden som ska köras när <*villkor*> utvärderas till falskt | 
|||| 

Åtgärderna i `actions` objekten eller `else` får dessa statusar:

* "Lyckades" när de kör och lyckas
* "Misslyckades" när de körs och misslyckas
* "Hoppas över" när respektive gren inte körs

*Exempel*

Det här villkoret anger att när heltalsvariabeln har ett värde som är större än noll, kontrollerar arbetsflödet en webbplats. Om variabeln är noll eller mindre kontrollerar arbetsflödet en annan webbplats.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
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
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Hur villkor använder uttryck

Här är några exempel som visar hur du kan använda uttryck under förhållanden:
  
| JSON | Resultat | 
|------|--------| 
| "uttryck":@parameters" ('<*hasSpecialAction*>')" | Endast för booleska uttryck skickas villkoret för alla värden som utvärderas till true. <p>Om du vill konvertera andra typer `empty()` till `equals()`boolesk använder du dessa funktioner: eller . | 
| "expression":@greater" (åtgärder('<*åtgärd*>').output.value, parameters('<*threshold*>')"" | För jämförelsefunktioner körs åtgärden endast när utdata från <*åtgärd*> är mer än <*tröskelvärdet*> värdet. | 
| "expression":@or" (greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | För logikfunktioner och skapa kapslade booleska uttryck körs åtgärden när utdata från <*åtgärd*> är mer än <*tröskelvärdet*> värde eller under 100. | 
| "expression":@equals" (length(actions('<*action*>').outputs.errors), 0))" | Du kan använda matrisfunktioner för att kontrollera om matrisen har några objekt. Åtgärden körs när `errors` matrisen är tom. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Åtgärd för omfattning

Den här åtgärden *grupperar*logiskt åtgärder i scope , som får sin egen status när åtgärderna i det omfånget har körts. Du kan sedan använda scopets status för att avgöra om andra åtgärder körs. Läs om hur du [skapar scope](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*inre-action-1... N*> | JSON-objekt | En eller flera åtgärder som körs i omfånget |
| <*åtgärdsinmatningar*> | JSON-objekt | Ingångarna för varje åtgärd |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Växelåtgärd

Den här åtgärden, även känd som en *switch-sats,* organiserar andra åtgärder i *ärenden*och tilldelar ett värde till varje ärende, förutom standardfallet om det finns ett sådant. När arbetsflödet körs jämför **switch-åtgärden** värdet från ett uttryck, objekt eller en token mot de värden som angetts för varje ärende. Om åtgärden **Växla** hittar ett matchande ärende körs bara åtgärderna för det ärendet. Varje gång åtgärden **Växla** körs finns det antingen bara ett matchande ärende eller så finns det inga matchningar. Om det inte **Switch** finns några matchningar körs standardåtgärderna. Läs om hur du [skapar växelsatser](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Varierar | Uttrycket, JSON-objektet eller token som ska utvärderas | 
| <*åtgärd-namn*> | Sträng | Namnet på åtgärden som ska köras för det matchande ärendet | 
| <*åtgärdsdefinition*> | JSON-objekt | Definitionen för åtgärden som ska köras för det matchande ärendet | 
| <*matchande värde*> | Varierar | Värdet som ska jämföras med det utvärderade resultatet | 
|||| 

*Valfri*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*standard-åtgärd-namn*> | Sträng | Namnet på standardåtgärden som ska köras när det inte finns något matchande ärende | 
| <*standard-åtgärd-definition*> | JSON-objekt | Definitionen för åtgärden som ska köras när det inte finns något matchande ärende | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen utvärderar om den person som svarar på e-postmeddelandet för godkännandebegäran har valt alternativet "Godkänn" eller alternativet "Avvisa". Baserat på det här valet kör **switch-åtgärden** åtgärderna för matchningsfallet, som är att skicka ett annat e-postmeddelande till svararen men med olika ordalydelse i varje enskilt fall. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Fram till åtgärder

Den här loopåtgärden innehåller åtgärder som körs tills det angivna villkoret är sant. Loopen kontrollerar villkoret som det sista steget efter att alla andra åtgärder har körts. Du kan inkludera mer än `"actions"` en åtgärd i objektet och åtgärden måste definiera minst en gräns. Lär dig hur du [skapar "tills" loopar](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärd-namn*> | Sträng | Namnet på den åtgärd som du vill köra inuti loopen | 
| <*åtgärdstyp*> | Sträng | Den åtgärdstyp som du vill köra | 
| <*åtgärdsinmatningar*> | Olika | Ingångarna för åtgärden att köra | 
| <*Villkor*> | Sträng | Villkoret eller uttrycket som ska utvärderas när alla åtgärder i loopen har körts klart | 
| <*loop-antal*> | Integer | Gränsen för flest antal loopar som åtgärden kan köra. Mer information om standardgränsen och maxgränsen finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*loop-timeout*> | Sträng | Gränsen för den längsta tiden som loopen kan köras. Standardvärdet `timeout` är `PT1H`, vilket är det nödvändiga [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Exempel*

Den här loopåtgärdsdefinitionen skickar en HTTP-begäran till den angivna URL:en tills något av dessa villkor är uppfyllt:

* Begäran får ett svar med statuskoden "200 OK".
* Slingan har körts 60 gånger.
* Slingan har pågått i en timme.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks och prenumerationer

Webhook-baserade utlösare och åtgärder kontrollerar inte regelbundet slutpunkter, men vänta på specifika händelser eller data vid dessa slutpunkter i stället. Dessa utlösare och åtgärder *prenumererar på* slutpunkterna genom att tillhandahålla en *motringnings-URL* där slutpunkten kan skicka svar.

Anropet `subscribe` sker när arbetsflödet ändras på något sätt, till exempel när autentiseringsuppgifter förnyas eller när indataparametrarna ändras för en utlösare eller åtgärd. Det här anropet använder samma parametrar som standard HTTP-åtgärder. 

Anropet `unsubscribe` sker automatiskt när en åtgärd gör utlösaren eller åtgärden ogiltig, till exempel:

* Ta bort eller inaktivera utlösaren. 
* Ta bort eller inaktivera arbetsflödet. 
* Ta bort eller inaktivera prenumerationen. 

För att stödja `@listCallbackUrl()` dessa anrop returnerar uttrycket en unik "motringnings-URL" för utlösaren eller åtgärden. Den här URL:en representerar en unik identifierare för slutpunkter som använder tjänstens REST API. Parametrarna för den här funktionen är desamma som webhook-utlösaren eller åtgärden.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Ändra asynkron varaktighet

För både utlösare och åtgärder kan du begränsa varaktigheten för det asynkrona `limit.timeout` mönstret till ett visst tidsintervall genom att lägga till egenskapen. På så sätt, om åtgärden inte har slutförts när intervallet förfaller, markeras åtgärdens status som `Cancelled` med `ActionTimedOut` koden. Egenskapen `timeout` använder [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Konfigurationsinställningar för körning

Du kan ändra standardkörningsbeteendet för utlösare `runtimeConfiguration` och åtgärder genom att lägga till dessa egenskaper i utlösaren eller åtgärdsdefinitionen.

| Egenskap | Typ | Beskrivning | Utlösare eller åtgärd | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Ändra [*standardgränsen för*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) antalet arbetsflödesinstanser som kan köras samtidigt (samtidigt eller parallellt). Om du justerar det här värdet kan du begränsa antalet begäranden som serverd-system får. <p>Ange `runs` att `1` egenskapen fungerar på `operationOptions` samma `SingleInstance`sätt som att ange egenskapen på . Du kan ange någon av egendomerna, men inte båda. <p>Information om hur du ändrar standardgränsen finns i [Ändra utlösar samtidighet](#change-trigger-concurrency) eller [utlösa instanser sekventiellt](#sequential-trigger). | Alla utlösare | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Ändra [*standardgränsen för*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) antalet arbetsflödesinstanser som måste vänta för att köras när logikappen redan kör de maximala samtidiga instanserna. <p>Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](#change-waiting-runs). | Alla utlösare | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Ändra [*standardgränsen*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) för antalet "för varje" loopiterationer som kan köras samtidigt (samtidigt eller parallellt). <p>Ange `repetitions` att `1` egenskapen fungerar på `operationOptions` samma `SingleInstance`sätt som att ange egenskapen på . Du kan ange någon av egendomerna, men inte båda. <p>Om du vill ändra standardgränsen finns i [Ändra "för varje" samtidighet](#change-for-each-concurrency) eller [Kör "för varje" loopar sekventiellt](#sequential-for-each). | Åtgärd: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | För specifika åtgärder som stöder och har sidnumrering aktiverat anger det här värdet det *minsta* antalet resultat som ska hämtas. <p>Information om hur du aktiverar sidnumrering finns i [Hämta massdata, objekt eller resultat med hjälp av sidnumrering](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Åtgärd: Varierad |
| `runtimeConfiguration.secureData.properties` | Matris | På många utlösare och åtgärder döljer dessa inställningar indata, utdata eller båda från logikappens körningshistorik. <p>Mer information om hur du skyddar dessa data finns i [Dölj indata och utdata från körningshistoriken](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | De flesta utlösare och åtgärder |
| `runtimeConfiguration.staticResult` | JSON-objekt | För åtgärder som stöder och har den [statiska resultatinställningen](../logic-apps/test-logic-apps-mock-data-static-results.md) `staticResult` aktiverad har objektet följande attribut: <p>- `name`, som refererar till den aktuella åtgärdens statiska resultatdefinitionsnamn, som visas inuti `staticResults` attributet i logikapparbetsflödets `definition` attribut. Mer information finns i [Statiska resultat - Schemareferens för arbetsflödesdefinitionsspråk](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, som anger om `Enabled` statiska resultat är eller inte för den aktuella åtgärden. <p>Information om hur du aktiverar statiska resultat finns i [Testa logikappar med utkastdata genom att ställa in statiska resultat](../logic-apps/test-logic-apps-mock-data-static-results.md) | Åtgärd: Varierad |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Alternativ för operation

Du kan ändra standardbeteendet för utlösare och åtgärder med egenskapen `operationOptions` i utlösaren eller åtgärdsdefinitionen.

| Alternativet Åtgärd | Typ | Beskrivning | Utlösare eller åtgärd | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Sträng | Kör HTTP-baserade åtgärder synkront, i stället för asynkront. <p><p>Det här alternativet finns i [Kör åtgärder synkront](#asynchronous-patterns). | Åtgärder: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Svar](#response-action) | 
| `OptimizedForHighThroughput` | Sträng | Ändra [standardgränsen för](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) antalet åtgärdskörningar per 5 minuter till [den maximala gränsen](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Det här alternativet finns [i Kör i högt dataflödesläge](#run-high-throughput-mode). | Alla åtgärder | 
| `Sequential` | Sträng | Kör "för varje" loop iterationer en i taget, snarare än alla samtidigt parallellt. <p>Det här alternativet fungerar på `runtimeConfiguration.concurrency.repetitions` samma `1`sätt som att ange egenskapen till . Du kan ange någon av egendomerna, men inte båda. <p><p>Det här alternativet finns i [Kör "för varje" loopar sekventiellt](#sequential-for-each).| Åtgärd: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Sträng | Kör utlösaren för varje logikappinstans sekventiellt och vänta på att den tidigare aktiva körningen ska slutföras innan du utlöser nästa logikappinstans. <p><p>Det här alternativet fungerar på `runtimeConfiguration.concurrency.runs` samma `1`sätt som att ange egenskapen till . Du kan ange någon av egendomerna, men inte båda. <p>Information om hur du anger det här alternativet finns [i Utlösa förekomster sekventiellt](#sequential-trigger). | Alla utlösare | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Ändra samtidighet för utlösare

Som standard körs logikapparbetsflödesinstanser alla samtidigt (samtidigt eller parallellt). Det här beteendet innebär att varje utlösarinstans utlöses innan den tidigare aktiva arbetsflödesinstansen körs. Antalet instanser som körs samtidigt har dock en [standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). När antalet arbetsflödesinstanser som körs samtidigt når den här gränsen måste alla andra nya instanser vänta med att köras. Den här gränsen hjälper till att kontrollera antalet begäranden som backend-system får.

När du aktiverar utlösarens samtidighetskontroll körs utlösarinstanser parallellt med [standardgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här standardgränsen för samtidighet kan du använda antingen kodvyredigeraren eller `runtimeConfiguration.concurrency.runs` Logic Apps Designer eftersom du ändrar samtidighetsinställningen via designern lägger till eller uppdaterar egenskapen i den underliggande utlösardefinitionen och vice versa. Den här egenskapen styr det maximala antalet nya arbetsflödesinstanser som kan köras parallellt.

Här är några funderingar när du vill aktivera samtidighet på en utlösare:

* När samtidighet är aktiverat minskas [SplitOn-gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) avsevärt för [att diskutera matriser](#split-on-debatch). Om antalet objekt överskrider den här gränsen inaktiveras SplitOn-funktionen.

* Du kan inte inaktivera samtidighet när du har aktiverat samtidighetskontrollen.

* När samtidighet är aktiverat minskas [SplitOn-gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) avsevärt för [att diskutera matriser](#split-on-debatch). Om antalet objekt överskrider den här gränsen inaktiveras SplitOn-funktionen.

* När samtidighet är aktiverat kan en tidskrävande logikappinstans orsaka att nya logikappinstanser går in i ett vänteläge. Det här tillståndet förhindrar att Azure Logic Apps skapar nya instanser och inträffar även när antalet samtidiga körningar är mindre än det angivna maximala antalet samtidiga körningar.

  * Om du vill avbryta det här tillståndet avbryter du de tidigaste instanserna som *fortfarande körs*.

    1. På logikappens meny väljer du **Översikt**.

    1. I avsnittet **Körs historik** väljer du den tidigaste instans som fortfarande körs, till exempel:

       ![Välj tidigaste gående instans](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Om du bara vill visa instanser som fortfarande körs öppnar du listan **Alla** och väljer **Kör**.

    1. Under **Logikappkörning**väljer du **Avbryt kör**.

       ![Hitta tidigaste instans som körs](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Om du vill undvika den här möjligheten lägger du till en timeout till alla åtgärder som kan hålla upp dessa körningar. Om du arbetar i kodredigeraren läser du [Ändra asynkron varaktighet](#asynchronous-limits). Om du använder designern gör du så här om du använder designern:

    1. I logikappen, i den åtgärd där du vill lägga till en timeout, markerar du ellipserna (**...**) i det övre högra hörnet och väljer sedan **Inställningar**.

       ![Öppna åtgärdsinställningar](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Under **Timeout**anger du timeout-varaktigheten i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Ange timeout-varaktighet](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Om du vill köra logikappen sekventiellt ställer du `1` in utlösarens samtidighet på antingen med hjälp av kodvyredigeraren eller designern. Se till att du inte också ställer `operationOptions` in `SingleInstance` utlösarens egenskap i kodvyredigeraren. Annars får du ett valideringsfel. Mer information finns i [Utlösa förekomster sekventiellt](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Redigera i kodvyn 

I den underliggande utlösardefinitionen lägger du till egenskapen, `runtimeConfiguration.concurrency.runs` som kan ha ett värde som sträcker sig från `1` till `50`.

Här är ett exempel som begränsar samtidiga körningar till 10 instanser:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I avtryckarens övre högra hörn väljer du ellipserna (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighetskontroll**anger du **Gräns** till **På**. 

1. Dra **skjutreglaget Grad av parallellitet** till önskat värde. Om du vill köra logikappen sekventiellt drar du skjutreglaget till **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Ändra "för varje" samtidighet

Som standard "för varje" loop iterationer alla körs samtidigt (samtidigt eller parallellt). Det här beteendet innebär att varje iteration börjar köras innan den föregående iterationen körs. Antalet iterationer som körs samtidigt har dock en [standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). När antalet iterationer som körs samtidigt når den här gränsen måste alla andra iterationer vänta med att köras.

Om du vill ändra standardgränsen kan du använda antingen kodvyredigeraren eller Logic Apps Designer `runtimeConfiguration.concurrency.repetitions` eftersom du lägger till eller uppdaterar egenskapen i den underliggande åtgärdsdefinitionen "för varje" åtgärd och vice versa genom att ändra samtidighetsinställningen via designern. Den här egenskapen styr det maximala antalet iterationer som kan köras parallellt.

> [!NOTE] 
> Om du anger att åtgärden "för varje" ska köras sekventiellt antingen med hjälp av designern eller kodvyredigeraren ska du inte ange åtgärdens `operationOptions` egenskap till `Sequential` i kodvyredigeraren. Annars får du ett valideringsfel. Mer information finns i [Kör "för varje" loopar sekventiellt](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Redigera i kodvyn 

I den underliggande definitionen "för varje" `runtimeConfiguration.concurrency.repetitions` lägger du till eller uppdaterar `1` `50`egenskapen, som kan ha ett värde som sträcker sig från och .

Här är ett exempel som begränsar samtidiga körningar till 10 iterationer:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I **för varje** åtgärd, i det övre högra hörnet, väljer du ellipserna (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighetskontroll**ställer du in **Samtidighetskontroll** **på På**.

1. Dra **skjutreglaget Grad av parallellitet** till önskat värde. Om du vill köra logikappen sekventiellt drar du skjutreglaget till **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Ändra gränsen för väntekörningar

Som standard körs logikapparbetsflödesinstanser alla samtidigt (samtidigt eller parallellt). Det här beteendet innebär att varje utlösarinstans utlöses innan den tidigare aktiva arbetsflödesinstansen körs. Antalet instanser som körs samtidigt har dock en [standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). När antalet arbetsflödesinstanser som körs samtidigt når den här gränsen måste alla andra nya instanser vänta med att köras.

Antalet väntande körningar har också en [standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). När antalet väntande körningar når den här gränsen accepterar Logic Apps-motorn inte längre nya körningar. Begäran och webhook-utlösare returnerar 429 fel och återkommande utlösare börjar hoppa över avsökningsförsök.

Du kan inte bara [ändra standardgränsen för utlösar samtidighet,](#change-trigger-concurrency)men du kan också ändra standardgränsen för väntande körningar. I den underliggande utlösardefinitionen lägger du till egenskapen, `runtimeConfiguration.concurrency.maximumWaitingRuns` som kan ha ett värde som sträcker sig från `1` till `100`.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Utlösa förekomster sekventiellt

Om du bara vill köra varje logikapparbetsflödesinstans när föregående instans har körts anger du att utlösaren ska köras sekventiellt. Du kan använda antingen kodvyredigeraren eller Logic Apps Designer eftersom det `runtimeConfiguration.concurrency.runs` också lägger till eller uppdaterar egenskapen i den underliggande utlösardefinitionen genom att ändra samtidighetsinställningen via designern.

> [!NOTE] 
> När du ställer in en utlösare att köras sekventiellt antingen med hjälp av `operationOptions` designern `Sequential` eller kodvyredigeraren ska du inte ange utlösarens egenskap till i kodvyredigeraren. Annars får du ett valideringsfel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvyn

Ange någon av dessa egenskaper i utlösardefinitionen, men inte båda. 

Ställ `runtimeConfiguration.concurrency.runs` in `1`egenskapen på:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-eller...*

Ställ `operationOptions` in `SingleInstance`egenskapen på:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options) och [Åtgärdsalternativ](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I avtryckarens övre högra hörn väljer du ellipserna (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighetskontroll**anger du **Gräns** till **På**. 

1. Dra reglaget **Parallellitetsgrad** `1`till talet . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Kör "för varje" slingor sekventiellt

Om du vill köra en "för varje" loopiteration först efter att föregående iteration har körts anger du åtgärden "för varje" som ska köras sekventiellt. Du kan använda antingen kodvyredigeraren eller Logic Apps Designer eftersom det också `runtimeConfiguration.concurrency.repetitions` lägger till eller uppdaterar egenskapen i den underliggande åtgärdsdefinitionen och vice versa genom att ändra åtgärdens samtidighet genom designer.

> [!NOTE] 
> När du anger att en "för varje" åtgärd ska köras sekventiellt antingen med hjälp `operationOptions` av `Sequential` designern eller kodvyredigeraren ska du inte ange åtgärdens egenskap till i kodvyredigeraren. Annars får du ett valideringsfel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvyn

Ange någon av dessa egenskaper i åtgärdsdefinitionen, men inte båda. 

Ställ `runtimeConfiguration.concurrency.repetitions` in `1`egenskapen på:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-eller...*

Ställ `operationOptions` in `Sequential`egenskapen på:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Mer information finns i [Konfigurationsinställningar för Körning](#runtime-config-options) och [Åtgärdsalternativ](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I den övre högra hörnet **för varje** åtgärd markerar du knappen ellipser (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighetskontroll**ställer du in **Samtidighetskontroll** **på På**.

1. Dra reglaget **Parallellitetsgrad** `1`till talet .

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Kör åtgärder synkront

Som standard följer alla HTTP-baserade åtgärder standardasynkronåtgärdsmönstret. Det här mönstret anger att när en HTTP-baserad åtgärd skickar en begäran till den angivna slutpunkten skickar fjärrservern tillbaka ett "202 ACCEPTED"-svar. Det här svaret innebär att servern accepterade begäran om bearbetning. Logic Apps-motorn fortsätter att kontrollera webbadressen som anges av svarets platshuvud tills bearbetningen stoppas, vilket är ett svar som inte är 202.

Begäranden har dock en tidsgräns, så för tidskrävande åtgärder kan du inaktivera det asynkrona beteendet genom att lägga till och ange egenskapen `operationOptions` till `DisableAsyncPattern` under åtgärdens indata.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

Mer information finns i [Åtgärdsalternativ](#operation-options).

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Kör i högt dataflödesläge

För en enda logikappdefinition har antalet åtgärder som körs var femte minut en [standardgräns](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Om du vill höja den här `operationOptions` gränsen `OptimizedForHighThroughput`till [största](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) möjliga inställning ställer du in egenskapen på . Den här inställningen försätter logikappen i "högt dataflöde"-läge.

> [!NOTE]
> Läget för högt dataflöde är förhandsgranskning. Du kan också distribuera en arbetsbelastning över mer än en logikapp om det behövs.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Autentisera utlösare och åtgärder

HTTP- och HTTPS-slutpunkter stöder olika typer av autentisering. Baserat på den utlösare eller åtgärd som du använder för att ringa utgående samtal eller begäranden för att komma åt dessa slutpunkter kan du välja mellan olika autentiseringstyper. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [språk för arbetsflödesdefinition](../logic-apps/logic-apps-workflow-definition-language.md)
