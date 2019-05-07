---
title: Referens för utlösare och åtgärd typer i Definitionsspråk för arbetsflödet – Azure Logic Apps
description: Referensguide för utlösare och åtgärd typer i Definitionsspråk för arbetsflödet för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 503bd6cfee1c19d2342ec9f535b3945178ab3ea0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65136607"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Referens för utlösare och åtgärd typer i Definitionsspråk för arbetsflödet för Azure Logic Apps

Den här referensen beskriver allmänna typer som används för att identifiera utlösare och åtgärder i din logikapp underliggande arbetsflödesdefinitionen, som beskrivs och godkänt den [Definitionsspråk för arbetsflödet](../logic-apps/logic-apps-workflow-definition-language.md).
Viss koppling utlösare och åtgärder som du kan använda i dina logic apps finns i listan under den [översikt över Anslutningsappar](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Utlösare: översikt

Varje arbetsflöde innehåller en utlösare som definierar de anrop som kan skapa en instans av och starta arbetsflödet. Här följer allmänna utlösaren kategorier:

* En *avsökning* utlösare som kontrollerar en tjänstslutpunkt med jämna mellanrum

* En *push* utlösare, som skapar en prenumeration på en slutpunkt och tillhandahåller en *Motringnings-URL för* så att slutpunkten kan meddela utlösaren när den angivna händelsen inträffar eller data är tillgängliga. Utlösaren väntar sedan slutpunkten svaret innan den utlösts. 

Utlösare har de här översta elementen, även om vissa är valfria:  
  
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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*trigger-name*> | String | Namnet på utlösaren | 
| <*typ av utlösare*> | String | Typ av utlösare, till exempel ”Http” eller ”ApiConnection” | 
| <*Utlösarens indata*> | JSON-objekt | Indata som definierar utlösarens beteende | 
| <*time-unit*> | String | Tidsenheten som beskriver hur ofta utlösaren utlöses: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”Month” | 
| <*number-of-time-units*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på åtkomstfrekvensen, vilket är antalet tidsenheter ska vänta tills den utlöses igen <p>Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*matris med villkor*> | Matris | En matris som innehåller en eller flera [villkor](#trigger-conditions) som avgör om du vill köra arbetsflödet. Endast tillgängligt för utlösare. | 
| <*runtime-config-options*> | JSON-objekt | Du kan ändra utlösaren runtime beteendet genom att ange `runtimeConfiguration` egenskaper. Mer information finns i [Runtime konfigurationsinställningar](#runtime-config-options). | 
| <*splitOn-expression*> | String | Du kan ange ett uttryck för utlösare som returnerar en matris, som [delar upp eller *debatches* ](#split-on-debatch) matrisen objekt till flera arbetsflödesinstanser för bearbetning. | 
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista över typer av utlösare

Varje Utlösartyp av har ett annat gränssnitt och indata som definierar utlösarens beteende. 

### <a name="built-in-triggers"></a>Inbyggda utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Kontrollerar eller *enkäter* valfri slutpunkt. Den här slutpunkten måste följa en utlösare kontrakt med hjälp av en ”202” asynkront mönster eller genom att returnera en matris. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Skapar en anropningsbara slutpunkt för din logikapp men anropar den angivna URL: en för att registrera eller avregistrera. |
| [**Upprepning**](#recurrence-trigger) | Utlöses enligt ett definierat schema. Du kan ange ett framtida datum och tid för aktiveringen av den här utlösaren. Baserat på åtkomstfrekvensen, du kan också ange tider och dagar för att köra arbetsflödet. | 
| [**Request**](#request-trigger)  | Skapar en anropningsbara slutpunkt för din logikapp och kallas även en ”manuell” utlösare. Se exempelvis [anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Hanterade API-utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Kontrollerar eller *polls* en slutpunkt med hjälp av [Microsoft-hanterade API: er](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Skapar en anropningsbara slutpunkt för din logikapp genom att anropa [Microsoft-hanterade API: er](../connectors/apis-list.md) ska prenumerera på och avbryta prenumerationen. | 
||| 

## <a name="triggers---detailed-reference"></a>Utlösare – detaljerad

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection utlösare  

Den här utlösaren kontrollerar eller *polls* en slutpunkt med hjälp av [Microsoft-hanterade API: er](../connectors/apis-list.md) så parametrarna för den här utlösaren kan skilja sig åt baserat på slutpunkten. Många avsnitt i den här utlösardefinition är valfria. Utlösarens beteende beror på om huruvida avsnitt är infogade.

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | String | Namnet på utlösaren | 
| <*connection-name*> | String | Namn för anslutningen till den hanterade API: N som används i arbetsflödet | 
| <*metodtyp*> | String | HTTP-metoden för att kommunicera med den hanterade API: N: ”HÄMTA”, ”PLACERA”, ”EFTER”, ”UPPDATERA”, ”TA BORT” | 
| <*api-operation*> | String | API-åtgärden att anropa | 
| <*time-unit*> | String | Tidsenheten som beskriver hur ofta utlösaren utlöses: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”Month” | 
| <*number-of-time-units*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på åtkomstfrekvensen, vilket är antalet tidsenheter ska vänta tills den utlöses igen <p>Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska inkluderas med API-anrop. Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*max-runs*> | Integer | Som standard arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*splitOn-expression*> | String | För utlösare som returnerar matriser, refererar det här uttrycket till matrisen att använda så att du kan skapa och köra en arbetsflödesinstans av för varje objekt i matrisen i stället använda en ”för var och en”-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras i utlösarens brödtext: `@triggerbody()?['value']` |
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). |
||||

*Utdata*
 
| Element | Typ | Beskrivning |
|---------|------|-------------|
| Rubriker | JSON-objekt | Rubrikerna från svaret |
| brödtext | JSON-objekt | Text från svaret |
| Statuskod | Integer | Statuskoden från svaret |
|||| 

*Exempel*

Den här utlösardefinitionen som kontrollerar för e-post varje dag i Inkorgen för ett Office 365 Outlook-konto: 

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

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook utlösare

Den här utlösaren skickar begäran om en prenumeration till en slutpunkt med hjälp av en [Microsoft-hanterade API: et](../connectors/apis-list.md), ger en *Motringnings-URL för* där slutpunkten kan skicka ett svar och väntar på slutpunkten så att den svarar. Mer information finns i [Endpoint prenumerationer](#subscribe-unsubscribe).

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*connection-name*> | String | Namn för anslutningen till den hanterade API: N som används i arbetsflödet | 
| <*body-content*> | JSON-objekt | Alla meddelandeinnehållet att skicka som nyttolast hanterade API: et | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska inkluderas med API-anrop <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*max-runs*> | Integer | Som standard arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*splitOn-expression*> | String | För utlösare som returnerar matriser, refererar det här uttrycket till matrisen att använda så att du kan skapa och köra en arbetsflödesinstans av för varje objekt i matrisen i stället använda en ”för var och en”-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras i utlösarens brödtext: `@triggerbody()?['value']` |
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

*Exempel*

Den här utlösardefinition prenumererar på Office 365 Outlook-API, ger en Motringnings-URL för API-slutpunkten och väntar tills slutpunkten som svarar när ett nytt e-postmeddelande.

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

Den här utlösaren kontrollerar eller genomsöker den angivna slutpunkten baserat på angivna upprepningsschemat. Slutpunktens svaret anger om arbetsflödet körs.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | String | HTTP-metod du använder för avsökning av den angivna slutpunkten: ”HÄMTA”, ”PLACERA”, ”EFTER”, ”UPPDATERA”, ”TA BORT” | 
| <*endpoint-URL*> | String | HTTP eller HTTPS-URL för slutpunkten för att göra en avsökning <p>Maximal strängstorlek: 2 KB | 
| <*time-unit*> | String | Tidsenheten som beskriver hur ofta utlösaren utlöses: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”Month” | 
| <*number-of-time-units*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på åtkomstfrekvensen, vilket är antalet tidsenheter ska vänta tills den utlöses igen <p>Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*header-content*> | JSON-objekt | Rubriker ska skicka med begäran <p>Till exempel vill ange språk och typ för en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | Meddelandeinnehållet att skicka som nyttolasten med begäran | 
| <*metod för autentisering*> | JSON-objekt | Metoden begäran används för autentisering. Mer information finns i [utgående autentisering i Scheduler](../scheduler/scheduler-outbound-authentication.md). Utöver Scheduler, den `authority` egenskapen stöds. Om inget värde anges är standardvärdet `https://login.windows.net`, men du kan använda ett annat värde, till exempel`https://login.windows\-ppe.net`. |
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska ingå i begäran <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` på begäran. | 
| <*max-runs*> | Integer | Som standard arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

*Utdata*

| Element | Typ | Beskrivning |
|---------|------|-------------| 
| Rubriker | JSON-objekt | Rubrikerna från svaret | 
| brödtext | JSON-objekt | Text från svaret | 
| Statuskod | Integer | Statuskoden från svaret | 
|||| 

*Krav för inkommande begäranden*

För att fungera bra med din logikapp måste slutpunkten följer ett mönster för utlösare eller kontrakt och känna igen de här egenskaperna:  
  
| Svar | Krävs | Beskrivning | 
|----------|----------|-------------| 
| Statuskod | Ja | Den ”200 OK”-statuskod: startar en körning. Alla andra statuskod Påbörja inte en körning. | 
| Rubrik för återförsök | Nej | Antalet sekunder tills logikappen avsöker slutpunkten igen | 
| Location-huvudet | Nej | URL till anropa vid nästa avsökningsintervall. Om den inte anges används den ursprungliga URL: en. | 
|||| 

*Exempel beteenden för olika förfrågningar*

| Statuskod | Försök igen efter | Beteende | 
|-------------|-------------|----------|
| 200 | {none} | Köra arbetsflödet och Sök igen efter mer data när du har definierat upprepningen. | 
| 200 | 10 sekunder | Köra arbetsflödet och Sök igen efter mer data efter 10 sekunder. |  
| 202 | 60 sekunder | Inte utlösa arbetsflödet. Nästa försök sker i en minut, omfattas av definierade upprepningen. Om definierade upprepningen är mindre än en minut, företräde sidhuvudet retry-after. I annat fall används definierade upprepningen. | 
| 400 | {none} | Felaktig begäran inte köra arbetsflödet. Om ingen `retryPolicy` definieras standardprincipen används. När antalet försök har uppnåtts, kontrollerar utlösaren igen data när du har definierat upprepningen. | 
| 500 | {none}| Serverfel, inte köra arbetsflödet. Om ingen `retryPolicy` definieras standardprincipen används. När antalet försök har uppnåtts, kontrollerar utlösaren igen data när du har definierat upprepningen. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook utlösare  

Den här utlösaren gör logikappen anropningsbara genom att skapa en slutpunkt som kan registrera en prenumeration genom att anropa angivna slutpunkts-URL. När du skapar den här utlösaren i ditt arbetsflöde gör en utgående begäran anropet för att registrera prenumerationen. På så sätt kan utlösaren kan börja lyssna efter händelser. När en åtgärd gör att den här utlösaren är ogiltigt, gör en utgående begäran automatiskt anropet för att säga upp prenumerationen. Mer information finns i [Endpoint prenumerationer](#subscribe-unsubscribe).

Du kan också ange [asynkrona gränser](#asynchronous-limits) på en **HTTPWebhook** utlösaren.
Utlösarens beteende beror på de avsnitt som du använder eller utelämna. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
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

Vissa värden, till exempel <*metodtyp*>, är tillgängliga för både den `"subscribe"` och `"unsubscribe"` objekt.

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | String | HTTP-metoden ska användas för prenumerationsbegäran: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” | 
| <*endpoint-subscribe-URL*> | String | Slutpunkts-URL att skicka prenumerationsbegäran | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | String | HTTP-metoden för begäran om annullering: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” | 
| <*endpoint-unsubscribe-URL*> | String | Slutpunkts-URL att skicka begäran om annullering | 
| <*body-content*> | String | Alla meddelanden som innehåll när du vill skicka i prenumeration eller avbryta begäran | 
| <*metod för autentisering*> | JSON-objekt | Metoden begäran används för autentisering. Mer information finns i [utgående autentisering i Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Integer | Som standard alla arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

*Utdata* 

| Element | Typ | Beskrivning |
|---------|------|-------------| 
| Rubriker | JSON-objekt | Rubrikerna från svaret | 
| brödtext | JSON-objekt | Text från svaret | 
| Statuskod | Integer | Statuskoden från svaret | 
|||| 

*Exempel*

Den här utlösaren skapar en prenumeration på den angivna slutpunkten, tillhandahåller en unik Motringnings-URL och väntar på teknik för nyligen publicerade artiklar.

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

### <a name="recurrence-trigger"></a>Utlösare för upprepning  

Den här utlösaren körs baserat på angivna upprepningsschemat och ger ett enkelt sätt för att skapa ett arbetsflöde som körs regelbundet. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*time-unit*> | String | Tidsenheten som beskriver hur ofta utlösaren utlöses: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”Month” | 
| <*number-of-time-units*> | Integer | Ett värde som anger hur ofta utlösaren utlöses baserat på åtkomstfrekvensen, vilket är antalet tidsenheter ska vänta tills den utlöses igen <p>Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | Datum och tid i följande format: <p>ÅÅÅÅ-MM-ddTHH om du anger en tidszon <p>ELLER <p>ÅÅÅÅ-MM-: ssZ om du inte anger en tidszon <p>Till exempel om du vill 18 September 2017 kl 2:00, sedan ange ”2017-09-18T14:00:00” och ange en tidszon, till exempel ”Pacific Standard Time”, eller ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs!** Starttiden måste följa den [ISO 8601 datum tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte anger en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. Den här ”Z” avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>Starttiden är den första förekomsten för enkla scheman och för komplexa scheman inte utlösaren utlöses alla snabbare än starttiden. Läs mer om startdatum och tider, [skapa och schemalägga aktiviteter som körs regelbundet](../connectors/connectors-native-recurrence.md). | 
| <*time-zone*> | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte acceptera [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Ange den tidszon som du vill använda. | 
| <*one-or-more-hour-marks*> | Heltal eller heltalsmatris | Om du anger ”Day” eller ”Week” för `frequency`, kan du ange en eller flera heltal mellan 0 och 23, avgränsade med kommatecken, som timmar på dagen när du vill köra arbetsflödet. <p>Exempel: Om du anger ”10”, ”12” och ”14”, får du 10 AM, 12 PM och 14: 00 som timme markerar. | 
| <*ett-eller-fler-minut-märken*> | Heltal eller heltalsmatris | Om du anger ”Day” eller ”Week” för `frequency`, kan du ange en eller flera heltal mellan 0 och 59, avgränsade med kommatecken, minuter på den timma som när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut mark och använder exemplet ovan för timmar på dagen, får du 10:30 AM, 12:30:00 och 14:30:00. | 
| weekDays | Sträng eller strängmatris | Om du anger ”Week” för `frequency`, kan du ange en eller flera dagar, avgränsade med kommatecken, när du vill köra arbetsflödet: ”Måndag”, ”tisdag”, ”onsdag”, ”torsdag”, ”fredag”, ”lördag” och ”Sunday” | 
| <*max-runs*> | Integer | Som standard alla arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

*Exempel 1*

Den här grundläggande återkommande utlösaren körs varje dag:

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

Du kan ange ett startdatum och starttid för aktiveringen av utlösaren. Den här upprepningsutlösare startar på det angivna datumet och sedan utlöses varje dag:

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

Den här upprepningsutlösare startar den 9 September 2017 kl 2:00 och utlöses varje vecka varje måndag kl. 10:30, 12:30:00 och 14:30:00 Pacific Standard Time:

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

Mer information och exempel för den här utlösaren finns i [skapa och schemalägga aktiviteter som körs regelbundet](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Begäran-utlösare

Den här utlösaren gör logikappen anropningsbara genom att skapa en slutpunkt som kan acceptera inkommande begäranden. Ange en JSON-schema som beskriver och verifierar nyttolast eller indata som utlösaren tar emot från den inkommande begäranden för den här utlösaren. Schemat är även egenskaper för utlösare enklare referensen från senare åtgärder i arbetsflödet. 

För att anropa den här utlösaren, måste du använda den `listCallbackUrl` API, som beskrivs i den [REST API för arbetsflödet](https://docs.microsoft.com/rest/api/logic/workflows). Läs hur du använder den här utlösaren som en HTTP-slutpunkt i [anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*property-name*> | String | Namnet på en egenskap i JSON-schema som beskriver nyttolasten | 
| <*typ vlastnosti*> | String | Egenskapens typ | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | String | Den metod som inkommande begäranden måste använda för att anropa logikappen: ”HÄMTA”, ”PLACERA”, ”EFTER”, ”UPPDATERA”, ”TA BORT” |
| <*relative-path-for-accepted-parameter*> | String | Den relativa sökvägen för den parameter som kan acceptera din slutpunkts-URL | 
| <*required-properties*> | Matris | En eller flera egenskaper som kräver värden | 
| <*max-runs*> | Integer | Som standard alla arbetsflödesinstanser körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra utlösaren samtidighet](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | När arbetsflödet körs redan det maximala antalet instanser, som du kan ändra baserat på den `runtimeConfiguration.concurrency.runs` egenskapen några nya körningar sätts i den här kön den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | 
| <*åtgärden-alternativet*> | String | Du kan ändra standardinställningen genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

*Exempel*

Den här utlösaren anger att en inkommande begäran måste använda HTTP POST-metoden för att anropa utlösaren och innehåller ett schema som verifierar indata från den inkommande begäranden: 

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

För alla utlösare och endast utlösare, kan du inkludera en matris som innehåller en eller flera uttryck för villkor som bestämmer om arbetsflödet ska köras. Att lägga till den `conditions` egenskapen till en utlösare i arbetsflödet, öppna logikappen i kodredigeraren för vyn.

Du kan till exempel ange att en utlösaren utlöses när en webbplats returnerar endast ett internt serverfel genom att referera till utlösarens-statuskod i den `conditions` egenskapen:

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

Som standard en utlösaren aktiveras endast när en ”200 OK” svar. När ett uttryck som refererar till en utlösare statuskod, ersätts utlösarens standardbeteendet. Därför måste du inkludera det här uttrycket som villkoret om du vill att utlösaren ska för mer än en statuskod, till exempel ”200” och ”201” statuskod: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Utlösa flera körningar

Om utlösaren returnerar en matris för din logikapp att bearbeta, ta ibland ”för var och en” loop för lång tid att bearbeta varje objekt i matrisen. Använd i stället de **SplitOn** -egenskapen i utlösaren till *debatch* matrisen. Debatching delar upp matrisobjekt och startar en ny arbetsflödesinstans som körs för varje objekt i matrisen. Den här metoden är användbar, till exempel när du vill fråga en slutpunkt som kan returnera flera nya objekt mellan frågeintervallen.
För det maximala antalet matris objekt som **SplitOn** kan bearbeta i en enda logic app-körning, se [gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Du kan inte använda **SplitOn** med ett mönster för synkron svar. Alla arbetsflöden som använder **SplitOn** och innehåller ett svar åtgärd körs asynkront och omedelbart skickar en `202 ACCEPTED` svar.

Om din utlösare Swagger-fil som beskriver en nyttolast som är en matris, den **SplitOn** egenskapen läggs automatiskt till utlösaren. Annars lägger du till den här egenskapen i svarets nyttolast som har den matris som du vill debatch. 

*Exempel*

Anta att du har ett API som returnerar svaret: 
  
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
 
Logikappen behöver bara innehåll från en matris i `Rows`, så att du kan skapa en utlösare som i följande exempel:

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
> Om du använder den `SplitOn` kommandot, du inte kan hämta de egenskaper som är utanför matrisen. Så det här exemplet som du inte kan hämta den `status` -egenskapen i svaret som returnerades från API: et.
> 
> Att undvika ett fel om de `Rows` egenskapen finns inte, det här exemplet används den `?` operator.

Nu kan du använda din arbetsflödesdefinitionen `@triggerBody().name` att hämta den `name` värden för `"customer-name-one"` från den första körningen och `"customer-name-two"` från den andra körningen. Därför utlösaren matar ut utseende som de här exemplen:

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

Azure Logic Apps ger olika åtgärdstyper – var och en med olika indata som definierar hur en åtgärd. 

Åtgärder ha elementen på hög nivå om vissa är valfria:

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*action-name*> | String | Namn för åtgärden | 
| <*typ av åtgärd*> | String | Åtgärdstyp, till exempel ”Http” eller ”ApiConnection”| 
| <*input-name*> | String | Namnet på indata som definierar beteendet för den åtgärden | 
| <*input-value*> | Olika | Indatavärdet, vilket kan vara en sträng, heltal, JSON-objekt och så vidare | 
| <*previous-trigger-or-action-status*> | JSON-objekt | Namn och resulterande status för utlösaren eller åtgärden som måste köras omedelbart innan åtgärden aktuella kan köra | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i principer för återförsök. | 
| <*runtime-config-options*> | JSON-objekt | För vissa åtgärder, kan du ändra åtgärdens beteende under körning med `runtimeConfiguration` egenskaper. Mer information finns i [Runtime konfigurationsinställningar](#runtime-config-options). | 
| <*åtgärden-alternativet*> | String | För vissa åtgärder, kan du ändra standardbeteendet genom att ange den `operationOptions` egenskapen. Mer information finns i [åtgärdsalternativen](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Listan över typer

Här följer några vanliga åtgärdstyper: 

* [Inbyggda åtgärdstyper](#built-in-actions) som de här exemplen och mer: 

  * [**HTTP** ](#http-action) för att anropa slutpunkter via HTTP eller HTTPS

  * [**Svaret** ](#response-action) för att svara på begäranden

  * [**Köra JavaScript-kod** ](#run-javascript-code) för att köra JavaScript-kodfragment

  * [**Funktionen** ](#function-action) för att anropa Azure Functions

  * Åtgärden dataåtgärder som [ **ansluta**](#join-action), [ **Compose**](#compose-action), [ **tabell** ](#table-action), [ **Välj**](#select-action), och andra som skapar eller Transformera data från olika indata

  * [**Arbetsflödet** ](#workflow-action) för att anropa en annan logikappens arbetsflöde

* [Hanterade API: et åtgärdstyper](#managed-api-actions) som [ **ApiConnection** ](#apiconnection-action) och [ **ApiConnectionWebHook** ](#apiconnectionwebhook-action) som anropar olika kopplingar och API: er hanteras av Microsoft, till exempel Azure Service Bus, Office 365 Outlook, Powerbi, Azure Blob Storage, OneDrive, GitHub och mer

* [Kontrollera åtgärdstyper för arbetsflödet](#control-workflow-actions) som [ **om**](#if-action), [ **Foreach**](#foreach-action), [ **växel**  ](#switch-action), [ **Omfång**](#scope-action), och [ **tills**](#until-action), som innehåller andra åtgärder och hjälper dig att organisera arbetsflödeskörning

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Inbyggda åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Skapar ett enda utflöde från indata som kan ha olika typer. | 
| [**Köra JavaScript-kod**](#run-javascript-code) | Köra JavaScript-kodavsnitt som passar för specifika villkor. Kodkrav och mer information finns i [Lägg till och köra kodfragment med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funktionen**](#function-action) | Anropar en Azure-funktion. | 
| [**HTTP**](#http-action) | Anropar en HTTP-slutpunkt. | 
| [**Join**](#join-action) | Skapar en sträng från alla objekt i en matris och delar upp dessa objekt med ett tecken för angiven avgränsare. | 
| [**Parsa JSON**](#parse-json-action) | Skapar användarvänliga token från egenskaper i JSON-innehåll. Du kan sedan referera dessa egenskaper genom att inkludera token i din logikapp. | 
| [**Fråga**](#query-action) | Skapar en matris av objekt i en annan matris baserat på ett villkor eller filter. | 
| [**Svar**](#response-action) | Skapar ett svar på ett inkommande samtal eller begäran. | 
| [**Välj**](#select-action) | Skapar en matris med JSON-objekt genom att transformera objekt från en annan matris baserat på den angivna mappningen. | 
| [**Tabell**](#table-action) | Skapar en CSV- eller HTML-tabell från en matris. | 
| [**Avsluta**](#terminate-action) | Stoppar ett arbetsflöde som aktivt som körs. | 
| [**Vänta**](#wait-action) | Pausar ditt arbetsflöde under en angiven period eller tills angivet datum och tid. | 
| [**Arbetsflöde**](#workflow-action) | Omsluter ett arbetsflöde i ett annat arbetsflöde. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Hanterade API-åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Anropar en HTTP-slutpunkt med hjälp av en [Microsoft-hanterade API: et](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Fungerar som HTTP-Webhook, men använder en [Microsoft-hanterade API: et](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Kontrollen arbetsflödesåtgärder

De här åtgärderna hjälper dig att kontrollera arbetsflödeskörning och innehålla andra åtgärder. Från utanför en kontroll i arbetsflödesåtgärd referera du direkt till åtgärder i den kontrollen arbetsflödesåtgärden. Exempel: Om du har en `Http` åtgärd inuti ett omfång kan du referera till den `@body('Http')` uttryck var som helst i arbetsflödet. Åtgärder som finns i en kontroll arbetsflödesåtgärd kan dock endast ”köra” andra åtgärder som finns i samma kontroll Arbetsflödesstruktur.

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Kör samma åtgärder i en loop för alla objekt i en matris. | 
| [**If**](#if-action) | Kör åtgärder baserat på om det angivna villkoret är SANT eller FALSKT. | 
| [**Omfång**](#scope-action) | Kör åtgärder baserat på Gruppstatus för från en uppsättning åtgärder. | 
| [**Switch**](#switch-action) | Köra åtgärder som är ordnade i fall när värden från uttryck, objekt eller token matchar de värden som anges av varje fall. | 
| [**Until**](#until-action) | Kör åtgärder i en loop tills det angivna villkoret är sant. | 
|||  

## <a name="actions---detailed-reference"></a>Åtgärder – detaljerad

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection åtgärd

Den här åtgärden skickar en HTTP-begäran till en [Microsoft-hanterade API: et](../connectors/apis-list.md) och kräver information om API: et och parametrar plus en referens till en giltig anslutning. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*action-name*> | String | Namnet på den åtgärd som tillhandahålls av anslutningen | 
| <*api-name*> | String | Namnet på Microsoft-hanterade API: et som används för anslutningen | 
| <*metodtyp*> | String | HTTP-metoden för att anropa API: et: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” | 
| <*api-operation*> | String | API-åtgärden att anropa | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*andra-åtgärd-specifika-indata-properties*> | JSON-objekt | Andra indata egenskaper som gäller för den här specifika åtgärden | 
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska inkluderas med API-anrop. <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*other-action-specific-properties*> | JSON-objekt | Andra egenskaper som gäller för den här specifika åtgärden | 
|||| 

*Exempel*

Den här definitionen beskrivs den **skicka ett e-postmeddelande** åtgärd för Office 365 Outlook-anslutningstjänsten, som är ett Microsoft-hanterade API: 

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

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook åtgärd

Den här åtgärden skickar en begäran om prenumeration via HTTP till en slutpunkt med hjälp av en [Microsoft-hanterade API: et](../connectors/apis-list.md), ger en *Motringnings-URL för* där slutpunkten kan skicka ett svar och väntar på slutpunkten till svara. Mer information finns i [Endpoint prenumerationer](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Vissa värden, till exempel <*metodtyp*>, är tillgängliga för både den `"subscribe"` och `"unsubscribe"` objekt.

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*action-name*> | String | Namnet på den åtgärd som tillhandahålls av anslutningen | 
| <*metodtyp*> | String | HTTP-metoden ska användas för prenumerera eller prenumerationen på en slutpunkt: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” | 
| <*api-subscribe-URL*> | String | URI: N ska användas för att prenumerera på API: et | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | URI: N för prenumerationen på API: et | 
| <*header-content*> | JSON-objekt | Alla rubriker för att skicka i begäran <p>Till exempel vill ange språket och på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objekt | Alla meddelandeinnehållet att skicka i begäran | 
| <*metod för autentisering*> | JSON-objekt | Metoden begäran används för autentisering. Mer information finns i [utgående autentisering i Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska inkluderas med API-anrop <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*andra-åtgärd-specifika-indata-properties*> | JSON-objekt | Andra indata egenskaper som gäller för den här specifika åtgärden | 
| <*other-action-specific-properties*> | JSON-objekt | Andra egenskaper som gäller för den här specifika åtgärden | 
|||| 

Du kan också ange gränser på en **ApiConnectionWebhook** åtgärd på samma sätt som [HTTP asynkrona gränser](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Åtgärden Skriv

Den här åtgärden skapar ett enda utflöde från flera inmatningar, inklusive uttryck. Både indata och utdata kan ha någon typ som Azure Logic Apps har inbyggt stöd, till exempel matriser, JSON-objekt, XML och binära.
Du kan sedan använda åtgärdens utdata i andra åtgärder. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Krävs* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*indata till compose*> | Alla | Indata för att skapa ett enda utflöde | 
|||| 

*Exempel 1*

<!-- markdownlint-disable MD038 -->
Den här åtgärdsdefinitionen sammanfogar `abcdefg ` med ett avslutande blanksteg och värdet `1234`:
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

Den här åtgärdsdefinitionen sammanfogar en strängvariabel som innehåller `abcdefg` och en heltalsvariabel som innehåller `1234`:

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

### <a name="execute-javascript-code-action"></a>Köra JavaScript-kod åtgärd

Den här åtgärden kör ett JavaScript-kodfragment och returnerar resultat via en `Result` token som kan referera till senare åtgärder.

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

*Krävs*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | Varierar | JavaScript-koden som du vill köra. Kodkrav och mer information finns i [Lägg till och köra kodfragment med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). <p>I den `code` attribut, din kodfragmentet kan använda den skrivskyddade `workflowContext` objekt som indata. Det här objektet har subegenskaper som ger din kodåtkomst till resultaten från utlösaren och den tidigare åtgärder i arbetsflödet. Mer information om den `workflowContext` objekt, se [referera till utlösare och åtgärd resultat i din kod](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*I vissa fall krävs*

Den `explicitDependencies` attributet anger att du vill uttryckligen resultaten från utlösaren, tidigare åtgärder eller båda som beroenden för din kodfragmentet. Läs mer om att lägga till dessa beroenden, [lägga till parametrar för infogad kod](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

För den `includeTrigger` attribut, som du kan ange `true` eller `false` värden.

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*previous-actions*> | Strängmatris | En matris med angiven åtgärd-namn. Använd åtgärdsnamn som visas i din arbetsflödesdefinitionen där åtgärdsnamn använder understreck (_), inte blanksteg (””). |
||||

*Exempel 1*

Den här åtgärden körs koden som hämtar logikappens namn och returnerar texten ”Hello world från < logic-app-name >” som ett resultat. I det här exemplet refererar koden arbetsflödets namn genom att öppna den `workflowContext.workflow.name` egenskapen genom den skrivskyddade `workflowContext` objekt. Mer information om hur du använder den `workflowContext` objekt, se [referera till utlösare och åtgärd resultat i din kod](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Den här åtgärden körs koden i en logikapp som utlöses när ett nytt e-postmeddelande tas emot i ett Office 365 Outlook-konto. Logikappen använder även en Skicka godkännande e-poståtgärd som vidarebefordrar innehållet från mottagna e-postmeddelandet tillsammans med en begäran om godkännande. 

Koden extraherar e-postadresser från utlösarens `Body` egenskapen och returnerar de e-postadresser tillsammans med den `SelectedOption` egenskapsvärdet från Godkännandeåtgärden. Åtgärden bland annat omfattar Skicka godkännande e-poståtgärden som ett beroende i den `explicitDependencies`  >  `actions` attribut.

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

### <a name="function-action"></a>Funktionen åtgärd

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | Resurs-ID för Azure-funktion som du vill anropa. Här är formatet för det här värdet:<p>”/subscriptions/ <*azure-prenumerations-ID*> /resourceGroups/ <*Azure-resursgrupp*> /providers/Microsoft.Web/sites/ <*Azure function-appens namn-*> /Functions/ <*azure funktionsnamn*> ” | 
| <*metodtyp*> | String | HTTP-metod du använder för att anropa funktionen: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” <p>Om inte anges är standardvärdet ”POST”-metoden. | 
||||

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*header-content*> | JSON-objekt | Rubriker ska skicka med anropet <p>Till exempel vill ange språket och på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objekt | Alla meddelandeinnehållet att skicka i begäran | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska inkluderas med API-anrop <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*andra-åtgärd-specifika-indata-properties*> | JSON-objekt | Andra indata egenskaper som gäller för den här specifika åtgärden | 
| <*other-action-specific-properties*> | JSON-objekt | Andra egenskaper som gäller för den här specifika åtgärden | 
||||

När du sparar din logikapp utför de här kontrollerna på den angivna funktionen Logic Apps-motorn:

* Arbetsflödet måste ha åtkomst till funktionen.

* Ditt arbetsflöde kan använda endast en standard HTTP-utlösare eller en generisk JSON webhook-utlösaren. 

  Logic Apps-motorn hämtar och cachelagrar utlösarens URL som används vid körning. Men om alla åtgärder som upphäver den cachelagra URL, den **funktionen** åtgärden misslyckas vid körning. För att åtgärda problemet, spara logikappen igen så att logikappen hämtar och cachelagrar utlösaren URL: en igen.

* Funktionen kan inte ha någon väg som definierats.

* Endast ”funktionen” och ”anonym” åtkomstnivåer tillåts. 

*Exempel*

Den här åtgärdsdefinitionen anropar funktionen tidigare skapade ”GetProductID”:

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

Den här åtgärden skickar en begäran till den angivna slutpunkten och kontrollerar svaret för att avgöra om arbetsflödet ska köras. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metodtyp*> | String | Metoden som ska användas för att skicka begäran: ”Hämta”, ”se”, ”POST”, ”uppdatera” eller ”ta bort” | 
| <*HTTP-or-HTTPS-endpoint-URL*> | String | HTTP eller HTTPS-slutpunkt att anropa. Maximal strängstorlek: 2 KB | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*header-content*> | JSON-objekt | Rubriker ska skicka med begäran <p>Till exempel vill ange språk och typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objekt | Alla meddelandeinnehållet att skicka i begäran | 
| <*retry-behavior*> | JSON-objekt | Anpassar återförsöksbeteendet för tillfälliga fel, som har 408, 429, och 5XX-statuskoden och eventuella undantag. Mer information finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-objekt | Alla frågeparametrar ska ingå i begäran <p>Till exempel den `"queries": { "api-version": "2018-01-01" }` objektet lägger till `?api-version=2018-01-01` till anropet. | 
| <*andra-åtgärd-specifika-indata-properties*> | JSON-objekt | Andra indata egenskaper som gäller för den här specifika åtgärden | 
| <*other-action-specific-properties*> | JSON-objekt | Andra egenskaper som gäller för den här specifika åtgärden | 
|||| 

*Exempel*

Åtgärdsdefinitionen för den här hämtar de senaste nyheterna genom att skicka en begäran till den angivna slutpunkten:

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

### <a name="join-action"></a>Ansluta till åtgärd

Den här åtgärden skapar en sträng från alla objekt i en matris och delar upp dessa objekt med angiven avgränsare tecknet. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Matris*> | Matris | Den matris eller ett uttryck som ger källobjekt. Om du anger ett uttryck, skriva uttrycket med dubbla citattecken. | 
| <*delimiter*> | Enkel teckensträng | Tecknet som åtskiljer varje objekt i strängen | 
|||| 

*Exempel*

Anta att du har en tidigare skapad ”myIntegerArray”-variabel som innehåller den här heltalsmatris: 

`[1,2,3,4]` 

Den här åtgärdsdefinitionen får värdena från variabeln genom att använda den `variables()` fungera i ett uttryck och skapar den här strängen med de värden som är avgränsade med semikolon: `"1,2,3,4"`

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

### <a name="parse-json-action"></a>Parsa JSON-åtgärd

Den här åtgärden skapar användarvänliga fält eller *token* från egenskaper i JSON-innehåll. Du kan sedan komma åt dessa egenskaper i din logikapp med hjälp av token i stället. Till exempel när du vill använda JSON-utdata från tjänster som Azure Service Bus och Azure Cosmos DB kan inkludera du den här åtgärden i din logikapp så att du enkelt kan referera till data i dessa utdata. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*JSON-source*> | JSON-objekt | JSON-innehåll som du vill parsa | 
| <*JSON-schema*> | JSON-objekt | JSON-schema som beskriver den underliggande JSON-innehåll som åtgärden använder för parsning av källan JSON-innehåll. <p>**Tips!** I Logic Apps Designer kan du ange schemat eller ange en exempelnyttolast så att åtgärden kan generera schemat. | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen skapar dessa token som du kan använda i ditt arbetsflöde, men endast i åtgärder att köra följande den **parsa JSON** åtgärd: 

`FirstName`, `LastName`, och `Email`

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

I det här exemplet anger egenskapen ”innehåll” för åtgärden att parsa JSON-innehåll. Du kan också ange det här JSON-innehållet som exempelnyttolast för att generera schemat.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Egenskapen ”schema” anger JSON-schema används för att beskriva JSON-innehåll:

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

### <a name="query-action"></a>Frågeåtgärden

Den här åtgärden skapar en matris från objekten i en annan matris baserat på ett angivet villkor eller filter.

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Matris*> | Matris | Den matris eller ett uttryck som ger källobjekt. Om du anger ett uttryck, skriva uttrycket med dubbla citattecken. |
| <*condition-or-filter*> | String | Villkor som används för att filtrera objekt i matrisen källa <p>**Obs!** Om inga värden uppfyller villkoret, skapar en tom matris med åtgärden. |
|||| 

*Exempel*

Åtgärdsdefinitionen för den här skapar en matris med värden som är större än det angivna värdet är två:

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

### <a name="response-action"></a>Svarsåtgärd  

Den här åtgärden skapar nyttolast för svar på en HTTP-begäran. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*response-status-code*> | Integer | HTTP-statuskoden som skickas till den inkommande begäranden. Standardkoden ”200 OK”, men kod som kan vara valfri giltig statuskod som börjar med 2xx, 4xx eller 5xx, men inte med 3xxx. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*svarshuvuden*> | JSON-objekt | En eller flera sidhuvuden som ska ingå i svaret | 
| <*brödtext för svar*> | Olika | Svarstexten, vilket kan vara en sträng, JSON-objekt eller även binärt innehåll från en tidigare åtgärd | 
|||| 

*Exempel*

Åtgärdsdefinitionen för den här skapar ett svar till en HTTP-begäran med den angivna statuskoden och meddelandetexten meddelandehuvudena:

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

Till skillnad från andra åtgärder i **svar** åtgärd har särskilda begränsningar: 

* Ditt arbetsflöde kan använda den **svar** åtgärd bara när arbetsflödet som börjar med en HTTP-begäran-utlösare, vilket innebär att ditt arbetsflöde måste aktiveras via en HTTP-begäran.

* Ditt arbetsflöde kan använda den **svar** åtgärden var som helst *utom* inuti **Foreach** loopar **tills** slingor, inklusive sekventiella slingor och parallella grenar. 

* Den ursprungliga HTTP-begäran får ditt arbetsflöde svar endast när alla åtgärder som krävs av den **svar** åtgärd är färdiga inom den [tidsgränsen för HTTP-begäran](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Men om ditt arbetsflöde anrop till en annan logikapp som ett kapslat arbetsflöde kan väntar det överordnade arbetsflödet du tills det inkapslade arbetsflödet har slutförts, oavsett hur lång tid tar innan det inkapslade arbetsflödet har slutförts.

* När arbetsflödet använder den **svar** åtgärden och ett mönster för synkron svar arbetsflödet också kan inte använda den **splitOn** kommandot i utlösardefinitionen eftersom kommandot skapar flera körningar. Kontrollera för det här fallet när PUT-metoden används och om värdet är true och returnerar ett ”felaktig begäran”-svar.

  Om arbetsflödet använder den **splitOn** kommandot och en **svar** åtgärd, arbetsflödet körs asynkront och returnerar omedelbart ett svar med ”202-ACCEPTERAD”.

* När din arbetsflödeskörning når den **svar** åtgärd, men den inkommande begäranden redan har tagit emot ett svar på **svar** åtgärd har markerats som ”misslyckades” på grund av konflikten. Och därmed logikappen körs också att markeras med statusen ”misslyckades”.

<a name="select-action"></a>

### <a name="select-action"></a>Välj åtgärd

Den här åtgärden skapar en matris med JSON-objekt genom att transformera objekt från en annan matris baserat på den angivna mappningen. Utdata matris och källmatris har alltid samma antal objekt. Men du inte kan ändra antalet objekt i matrisen utdata, kan du lägga till eller ta bort egenskaper och deras värden för dessa objekt. Den `select` egenskap anger minst ett nyckel / värde-par som definierar kartan för omvandling av objekt i matrisen källa. Ett nyckel / värde-par representerar en egenskap och dess värde för alla objekt i matrisen utdata. 

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

*Krävs* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Matris*> | Matris | Den matris eller ett uttryck som ger källobjekt. Kontrollera att du skriva ett uttryck med dubbla citattecken. <p>**Obs!** Om källmatrisen är tom skapar åtgärden en tom matris. | 
| <*key-name*> | String | Egenskapsnamnet som tilldelats resultatet från <*uttryck*> <p>Om du vill lägga till en ny egenskap för alla objekt i matrisen utdata, ger en <*nyckelnamn*> för den egenskapen och en <*uttryck*> för egenskapens värde. <p>Ta bort en egenskap från alla objekt i matrisen genom att utelämna den <*nyckelnamn*> för den egenskapen. | 
| <*uttryck*> | String | Det uttryck som omvandlar objekt i matrisen källa och tilldelar resultat som ska <*-nyckelnamn*> | 
|||| 

Den **Välj** åtgärden skapar en matris som utdata, så att alla åtgärder som vill använda denna utdata måste antingen acceptera en matris eller måste du konvertera matrisen till den typ som accepterar konsument-åtgärd. Till exempel om du vill konvertera utdata-matris till en sträng, du kan skicka den matrisen till den **Compose** åtgärd, och sedan referera till utdata från den **Compose** åtgärd i dina andra åtgärder.

*Exempel*

Åtgärdsdefinitionen för den här skapar en JSON-matris för objekt från en heltalsmatris. Åtgärden upprepas källmatris, får varje heltalsvärde genom att använda den `@item()` uttryck och tilldelar varje värde till den ”`number`”-egenskapen i varje JSON-objekt: 

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

Här är den matris som den här åtgärden skapar:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Om du vill använda denna matris som utdata i andra åtgärder, skicka dessa utdata till en **Compose** åtgärd:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Du kan sedan använda utdata från den **Compose** åtgärd i dina andra åtgärder, till exempel den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd:

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

### <a name="table-action"></a>Åtgärden-tabell

Den här åtgärden skapar en CSV- eller HTML-tabell från en matris. För matriser med JSON-objekt skapar den här åtgärden automatiskt kolumnrubrikerna från objektens egenskapsnamn. Du måste ange kolumnrubriker och värden för matriser med andra datatyper. Den här matrisen innehåller till exempel egenskaperna ”ID” och ”Product_Name” som den här åtgärden kan använda för kolumnrubrikerna:

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

*Krävs* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| < CSV *eller* HTML >| String | Formatet för den tabell som du vill skapa | 
| <*Matris*> | Matris | Den matris eller ett uttryck som ger källobjekt för tabellen <p>**Obs!** Om källmatrisen är tom skapar åtgärden en tom tabell. | 
|||| 

*Valfritt*

Om du vill ange eller anpassa kolumnrubriker och värden, använder de `columns` matris. När `header-value` par har samma rubriknamn, deras värden visas i samma kolumn under det rubriknamnet. I annat fall definierar varje unik sidhuvud en unik kolumn.

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*column-name*> | String | Rubriknamn för en kolumn | 
| <*column-value*> | Alla | Värdet i kolumnen | 
|||| 

*Exempel 1*

Anta att du har en tidigare skapad ”myItemArray”-variabel som innehåller den här matrisen: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Åtgärdsdefinitionen för den här skapar en CSV-tabell från variabeln ”myItemArray”. Uttrycket som används av den `from` egenskapen hämtar matrisen från ”myItemArray” med hjälp av den `variables()` funktionen: 

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

Här är den CSV-tabell som den här åtgärden skapar: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exempel 2*

Åtgärdsdefinitionen för den här skapar en HTML-tabell från variabeln ”myItemArray”. Uttrycket som används av den `from` egenskapen hämtar matrisen från ”myItemArray” med hjälp av den `variables()` funktionen: 

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

Här är den HTML-tabell som den här åtgärden skapar: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Äpplen</td></tr><tr><td>1</td><td>Apelsiner</td></tr></tbody></table>

*Exempel 3*

Åtgärdsdefinitionen för den här skapar en HTML-tabell från variabeln ”myItemArray”. Men det här exemplet åsidosätter standard kolumnrubrikerna med ”Stock_ID” och ”beskrivning” och lägger till ordet ”organisk” för värdena i kolumnen ”Beskrivning”.

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

Här är den HTML-tabell som den här åtgärden skapar: 

<table><thead><tr><th>Stock_ID</th><th>Beskrivning</th></tr></thead><tbody><tr><td>0</td><td>Organisk äpplen</td></tr><tr><td>1</td><td>Organisk apelsiner</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Avbryt

Den här åtgärden stoppar körningen av en arbetsflödesinstans, avbryter alla åtgärder som pågår, hoppar över alla återstående åtgärder och returnerar angiven status. Du kan till exempel använda den **avsluta** åtgärd när din logikapp måste avsluta helt från ett feltillstånd. Den här åtgärden påverkar inte redan utförda åtgärder och kan inte förekomma inuti **Foreach** och **tills** slingor, inklusive sekventiella slingor. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*status*> | String | Status ska returneras för körningen: ”Misslyckades”, ”avbröt” eller ”lyckades” |
|||| 

*Valfritt*

Egenskaper för objektet ”runStatus” gäller endast när egenskapen ”runStatus” anges till statusen ”misslyckades”.

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | Kod eller namn för felet |
| <*error-message*> | String | Meddelande- eller text som beskriver felet och åtgärder som appanvändaren kan ta | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen stoppar ett arbetsflöde kör anger körningsstatusen till ”misslyckades” och returnerar status, en felkod och ett felmeddelande visas:

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

Den här åtgärden pausar arbetsflödeskörning för det angivna intervallet, eller tills den angivna tiden, men inte båda. 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*number-of-units*> | Integer | För den **fördröjning** åtgärd, hur många enheter vänta | 
| <*intervall*> | String | För den **fördröjning** åtgärd, intervall vänta: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”Month” | 
| <*date-time-stamp*> | String | För den **fördröjning tills** åtgärd, datum och tid att återuppta körningen. Det här värdet måste använda den [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exempel 1*

Den här åtgärdsdefinitionen stoppar arbetsflödet i 15 minuter:

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

Den här åtgärdsdefinitionen stoppar arbetsflödet fram till den angivna tiden:

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

### <a name="workflow-action"></a>Arbetsflödesåtgärden

Den här åtgärden anropar en annan tidigare skapade logic app, vilket innebär att du kan inkludera och återanvända andra logikappens arbetsflöde. Du kan också använda utdata från underordnat eller *kapslade* logikapp i åtgärder som följer kapslad logikapp, förutsatt att logikappen underordnade returnerar ett svar.

Logic Apps-motorn kontrollerar åtkomst till utlösaren som du vill anropa, så se till att du kan komma åt den utlösaren. Kapslad logikapp måste också uppfylla dessa kriterier:

* En utlösare gör kapslad logikapp anropningsbara, till exempel en [begära](#request-trigger) eller [HTTP](#http-trigger) utlösare

* Samma Azure-prenumeration som din logikapp för överordnad

* Om du vill använda utdata från kapslad logikapp i logikappen överordnade kapslad logikapp måste ha en [svar](#response-action) åtgärd 

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | String | Namn för logikappen som du vill anropa | 
| <*trigger-name*> | String | Namnet för utlösaren i kapslade logikappen som du vill anropa | 
| <*Azure-subscription-ID*> | String | Kapslade logic app-ID i Azure-prenumeration |
| <*Azure-resource-group*> | String | Azure resursgruppens namn för kapslad logikapp |
| <*nested-logic-app-name*> | String | Namn för logikappen som du vill anropa |
||||

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*header-content*> | JSON-objekt | Rubriker ska skicka med anropet | 
| <*body-content*> | JSON-objekt | Alla meddelandeinnehåll ska skicka med anropet | 
||||

*Utdata*

Utdata för den här åtgärden varierar beroende på den kapslad logikapp svarsåtgärd. Om kapslad logikapp inte innehåller en svarsåtgärd kan är utdata tomma.

*Exempel*

När ”Start_search”-åtgärd har slutförts anropar en annan logic app med namnet ”Get_product_information” som passerar i angivna indata i den här åtgärden arbetsflödesdefinitionen: 

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

## <a name="control-workflow-action-details"></a>Information om åtgärd för kontroll arbetsflöde

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach-åtgärd

Den här slingan åtgärden upprepas en matris och utför åtgärder på varje objekt i matrisen. Som standard ”för var och en”-loop som körs parallellt upp till ett högsta antal loopar. Den här maximum, se [begränsningar och konfigurationer](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Lär dig [skapa ”för var och en” slingor](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

*Krävs* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*action-1...n*> | String | Namnen på de åtgärder som körs på varje matrisen | 
| <*action-definition-1...n*> | JSON-objekt | Definitioner av åtgärder som körs | 
| <*för varje uttryck*> | String | Det uttryck som refererar till varje objekt i den angivna matrisen | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Antal*> | Integer | Som standard ”för var och en” loop som iterationer körs samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ändra den här gränsen genom att ange en ny <*antal*> värde, se [ändra ”för var och en” loop samtidighet](#change-for-each-concurrency). | 
| <*åtgärden-alternativet*> | String | Om du vill köra en ”för var och en” loop sekventiellt i stället parallellt, ange antingen <*åtgärdsalternativ*> till `Sequential` eller <*antal*> till `1`, men inte båda. Mer information finns i [kör ”för var och en” loopar sekventiellt](#sequential-for-each). | 
|||| 

*Exempel*

Den här ”för var och en” loop skickar ett e-postmeddelande för varje objekt i den matris som innehåller bifogade filer från ett inkommande e-postmeddelande. Loopen skickar ett e-postmeddelande, inklusive den bifogade filen till en person som går igenom den bifogade filen.

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

Om du vill ange en matris som skickas som utdata från utlösaren hämtar det här uttrycket i <*matrisnamnet*> matris från brödtexten i utlösaren. För att undvika ett fel om matrisen inte finns kan uttrycket använder den `?` operator:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Om åtgärden

Den här åtgärden, vilket är en *villkorlig instruktionen*, utvärderar ett uttryck som representerar ett villkor och kör en annan gren baserat på om villkoret är SANT eller FALSKT. Om villkoret är SANT markeras villkoret med ”Succeeded” status. Lär dig [så här skapar du villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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
| <*villkor*> | JSON-objekt | Villkoret, vilket kan vara ett uttryck att utvärdera | 
| <*action-1*> | JSON-objekt | Åtgärden som ska köras när <*villkor*> utvärderas till SANT | 
| <*action-definition*> | JSON-objekt | Definitionen för åtgärden | 
| <*åtgärd 2*> | JSON-objekt | Åtgärden som ska köras när <*villkor*> utvärderas till false | 
|||| 

Åtgärder i den `actions` eller `else` objekt får dessa statusar:

* ”Lyckades” när de körs och lyckas
* ”Misslyckades” när de körs och misslyckas
* ”Skipped” när grenen respektive inte körs

*Exempel*

Det här tillståndet anger att arbetsflödet när heltalsvariabeln har ett värde som är större än noll, kontrollerar en webbplats. Om variabeln är noll eller mindre, kontrollerar arbetsflödet en annan webbplats.

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

#### <a name="how-conditions-use-expressions"></a>Hur villkor använda uttryck

Här följer några exempel som visar hur du kan använda uttryck i villkor:
  
| JSON | Resultat | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | För booleska uttryck uppfylls villkoret för alla värden som utvärderas till SANT. <p>Andra typer konvertera till booleskt värde med dessa funktioner: `empty()` eller `equals()`. | 
| ”uttryck” ”:@greater(actions('<*action*>').output.value, parametrar (” <*tröskelvärdet*> ”))” | För jämförelse av funktioner, åtgärden körs endast när utdata från <*åtgärd*> är mer än den <*tröskelvärdet*> värde. | 
| ”uttryck” ”:@or(större (actions('<*action*>').output.value, parametrar (” <*tröskelvärdet*> ”)), mindre (åtgärder ('<*samma åtgärd*>').Output.Value 100)) ” | För logiska funktioner och skapa kapslade booleska uttryck, åtgärden körs när utdata från <*åtgärd*> är mer än den <*tröskelvärdet*> värde eller under 100. | 
| ”uttryck” ”:@equals(längd (actions('<*action*>').outputs.errors), 0))” | Du kan använda matrisfunktioner för att kontrollera om matrisen har alla objekt. Åtgärden körs när den `errors` matrisen är tom. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Scope-åtgärd

Den här åtgärden logiskt grupperar åtgärder i *scope*, som får sina egna status när åtgärderna eftersom omfånget slutföras. Du kan sedan använda scopets status för att avgöra om andra åtgärder ska köras. Lär dig [så här skapar du scope](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*inner-action-1...n*> | JSON-objekt | En eller flera åtgärder som körs inom |
| <*indata för åtgärden*> | JSON-objekt | Indata för varje åtgärd |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch-åtgärden

Den här åtgärden, även känt som en *växla instruktionen*, organiserar andra åtgärder i *fall*, och tilldelar ett värde till varje fall såvida standard om en sådan finns. När arbetsflödet körs, **växel** åtgärd jämför värdet från ett uttryck, objekt eller token mot de värden som anges för varje scenario. Om den **växel** åtgärden hittar en matchande fallet, arbetsflödet körs bara åtgärder för detta. Varje gång den **växel** körs, vilket endast en matchande fall finns eller om inga matchningar finns. Om inga matchningar finns den **växel** åtgärden körs standardåtgärderna som. Lär dig [så här skapar du switch-satser](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

*Krävs*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*uttryck-objekt-eller-token*> | Varierar | Det uttryck, JSON-objekt eller token för att utvärdera | 
| <*action-name*> | String | Namnet på åtgärden som ska köras för matchande | 
| <*action-definition*> | JSON-objekt | Definitionen för åtgärden som ska köras för matchande | 
| <*matching-value*> | Varierar | Värde att jämföra med det beräknade resultatet | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*standard åtgärdsnamn*> | String | Namnet på standardåtgärden som körs när inget matchande fall finns | 
| <*default-action-definition*> | JSON-objekt | Definitionen för åtgärden som ska köras när det finns inget matchande fall | 
|||| 

*Exempel*

Den här åtgärdsdefinitionen utvärderar huruvida den person som svarar på e-post för godkännande begäran valde alternativet ”Godkänn” eller ”avvisa”-alternativet. Baserat på det här alternativet den **växel** åtgärden kör åtgärderna för det matchande ärendet, vilket är att skicka en annan e-postmeddelande till svarande men med olika formulering i båda fallen. 

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

### <a name="until-action"></a>Fram till åtgärd

Åtgärden loopen innehåller åtgärder som körs tills villkoret är sant. Loopen utvärderas villkoret som det sista steget när alla andra åtgärder har körts. Du kan inkludera mer än en åtgärd i den `"actions"` objektet och åtgärden måste ange minst en gräns. Lär dig [så här skapar du ”till” slingor](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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
| <*action-name*> | String | Namn för åtgärden som du vill köra i den här slingan | 
| <*typ av åtgärd*> | String | Åtgärdstyp som du vill köra | 
| <*indata för åtgärden*> | Olika | Indata för åtgärden att köra | 
| <*villkor*> | String | Villkoret eller uttryck som ska utvärderas när alla åtgärder i loopen slutföras | 
| <*Antal loopar*> | Integer | Gränsen för flest antal loopar som åtgärden kan köras. Standard `count` värdet är 60. | 
| <*loop-timeout*> | String | Gränsen för den längsta tid som loopen kan köras. Standard `timeout` värdet är `PT1H`, vilket är de nödvändiga [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Exempel*

Den här slingan åtgärdsdefinitionen skickar en HTTP-begäran till den angivna URL: en tills någon av dessa villkor är uppfyllt: 

* Begäran får ett svar med den ”200 OK”-statuskod.
* Loopen har körts 60 gånger.
* Loopen har körts under en timme.

```json
 "Run_until_loop_succeeds_or_expires": {
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
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks och prenumerationer

Webhook-baserade utlösare och åtgärder regelbundet kontrollera inte slutpunkter, men vänta i stället för specifika händelser eller data på dessa slutpunkter. Dessa utlösare och åtgärder *prenumerera* till slutpunkterna genom att tillhandahålla en *Motringnings-URL för* där slutpunkten kan skicka svar.

Den `subscribe` anrop sker när arbetsflödet ändras på något sätt, till exempel när autentiseringsuppgifter förnyas, eller när indataparametrarna som ändras för en utlösare eller åtgärd. Det här anropet använder samma parametrar som standard HTTP-åtgärder. 

Den `unsubscribe` anrop sker automatiskt när en åtgärd gör utlösaren eller åtgärden ogiltig, till exempel:

* Ta bort eller inaktivera utlösaren. 
* Ta bort eller inaktivera arbetsflödet. 
* Ta bort eller inaktivera prenumerationen. 

Stöd för dessa anrop den `@listCallbackUrl()` uttrycket returnerar ett unikt ”Motringnings-URL” utlösaren eller åtgärden. Denna URL representerar en unik identifierare för slutpunkter som använder tjänstens REST API. Parametrarna för den här funktionen är samma som webhooksutlösaren eller åtgärden.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Ändra asynkron varaktighet

För både utlösare och åtgärder du kan begränsa hur länge asynkront mönster till ett visst tidsintervall genom att lägga till den `limit.timeout` egenskapen. På så sätt kan om åtgärden inte har slutförts när intervallet-uppnås åtgärdens status har markerats som `Cancelled` med den `ActionTimedOut` kod. Den `timeout` egenskapen använder [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

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

## <a name="runtime-configuration-settings"></a>Runtime-konfigurationsinställningar

Du kan ändra standardinställningen för utlösare och åtgärder med dessa runtime `runtimeConfiguration` egenskaper i definitionen för utlösare eller åtgärd.

| Egenskap  | Typ | Beskrivning | Utlösare eller åtgärd | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Ändra den [ *Standardgräns* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) på antalet arbetsflödesinstanser som kan köras samtidigt eller parallellt. Det här värdet kan hjälpa att begränsa antalet begäranden som tar emot backend-system. <p>Ange den `runs` egenskap `1` fungerar på samma sätt som den `operationOptions` egenskap `SingleInstance`. Du kan ange antingen egenskap, men inte båda. <p>Om du vill ändra Standardgränsen [ändra utlösaren samtidighet](#change-trigger-concurrency) eller [utlösa instanser sekventiellt](#sequential-trigger). | Alla utlösare | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Ändra den [ *Standardgräns* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) på antalet arbetsflödesinstanser som kan vänta med att köra när arbetsflödet körs redan det högsta antalet samtidiga instanser. Du kan ändra samtidighetsgräns i den `concurrency.runs` egenskapen. <p>Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](#change-waiting-runs). | Alla utlösare | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Ändra den [ *Standardgräns* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) på antalet ”för var och en” loop iterationer som kan köras samtidigt eller parallellt. <p>Ange den `repetitions` egenskap `1` fungerar på samma sätt som den `operationOptions` egenskap `SingleInstance`. Du kan ange antingen egenskap, men inte båda. <p>Om du vill ändra Standardgränsen [ändra ”för var och en” samtidighet](#change-for-each-concurrency) eller [kör ”för var och en” loopar sekventiellt](#sequential-for-each). | Åtgärd: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | För specifika åtgärder som stöder och har aktiverat sidbrytning, det här värdet anger den *minsta* antal resultat ska hämtas. <p>Om du vill aktivera sidnumrering Se [hämta stora mängder data, objekt eller resultat med sidbrytning](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Åtgärd: Olika |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Alternativ för åtgärden

Du kan ändra standardinställningen för utlösare och åtgärder med den `operationOptions` egenskapen i definitionen för utlösare eller åtgärd.

| Åtgärdsvärdet | Typ | Beskrivning | Utlösare eller åtgärd | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Kör HTTP-baserade åtgärder synkront, snarare än asynkront. <p><p>Om du vill ange det här alternativet, se [kör åtgärder synkront](#asynchronous-patterns). | Åtgärder: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Svar](#response-action) | 
| `OptimizedForHighThroughput` | String | Ändra den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) för antalet körningar per 5 minuter till den [maxgränsen](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Om du vill ange det här alternativet, se [körs med högt dataflöde](#run-high-throughput-mode). | Alla åtgärder | 
| `Sequential` | String | Kör ”för var och en” loop iterationer en i taget, i stället för på samma gång parallellt. <p>Det här alternativet fungerar på samma sätt som den `runtimeConfiguration.concurrency.repetitions` egenskap `1`. Du kan ange antingen egenskap, men inte båda. <p><p>Om du vill ange det här alternativet, se [kör ”för var och en” loopar sekventiellt](#sequential-for-each).| Åtgärd: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | Köra utlösaren för varje logikappinstans sekventiellt och vänta tills den tidigare active körningen ska slutföras innan du aktiverar den nästa logic app-instansen. <p><p>Det här alternativet fungerar på samma sätt som den `runtimeConfiguration.concurrency.runs` egenskap `1`. Du kan ange antingen egenskap, men inte båda. <p>Om du vill ange det här alternativet, se [utlösa instanser sekventiellt](#sequential-trigger). | Alla utlösare | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Ändra utlösaren samtidighet

Som standard logic app-instanserna körs på samma gång samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Varje instans som utlösaren utlöses så innan föregående arbetsflödesinstansen körningen är klar. Den här gränsen kan du styra hur många begäranden som tar emot backend-system. 

Om du vill ändra Standardgränsen, du kan använda antingen kod Vyredigeraren eller Logic Apps Designer eftersom ändra inställningen för samtidighet via designern lägger till eller uppdaterar den `runtimeConfiguration.concurrency.runs` egenskapen i den underliggande utlösardefinitionen och vice versa. Den här egenskapen styr det maximala antalet arbetsflödesinstanser som kan köras parallellt. 

> [!NOTE] 
> Om du ställer in att utlösaren ska köras sekventiellt antingen med hjälp av designern eller visa Kodredigeraren inte ställer in utlösarens `operationOptions` egenskap `SingleInstance` i kodredigeraren för vyn. Annars kan få du ett valideringsfel. Mer information finns i [utlösa instanser sekventiellt](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Redigera i kodvy 

I den underliggande utlösa definition, lägga till eller uppdatera den `runtimeConfiguration.concurrency.runs` egenskap med ett värde mellan `1` och `50` portintervallet.

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

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I utlösarens övre högra hörnet väljer du ellipserna (...)-knappen och väljer sedan **inställningar**.

2. Under **samtidighetskontroll**anger **gränsen** till **på**. 

3. Dra den **grad av parallellitet** skjutreglaget till önskat värde. Om du vill köra din logikapp sekventiellt dra skjutreglaget värdet till **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Ändra ”för var och en” samtidighet

Som standard ”för var och en” loop iterationer körs samtidigt eller parallellt, upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra Standardgränsen, du kan använda antingen kod Vyredigeraren eller Logic Apps Designer eftersom ändra inställningen för samtidighet via designern lägger till eller uppdaterar den `runtimeConfiguration.concurrency.repetitions` -egenskapen i den underliggande åtgärd ”för var och en” definition och tvärtom. Den här egenskapen styr det maximala antalet upprepningar som kan köras parallellt.

> [!NOTE] 
> Om du anger ”för var och en”-åtgärd att köra sekventiellt antingen med hjälp av designern eller visa Kodredigeraren, inte anger åtgärdens `operationOptions` egenskap `Sequential` i kodredigeraren för vyn. Annars kan få du ett valideringsfel. Mer information finns i [kör ”för var och en” loopar sekventiellt](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Redigera i kodvy 

I den underliggande ”för var och en” definition, lägga till eller uppdatera den `runtimeConfiguration.concurrency.repetitions` egenskap med ett värde mellan `1` och `50` portintervallet. 

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

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I den **för var och en** åtgärd i det övre högra hörnet, väljer du ellipserna (...)-knappen och välj sedan **inställningar**.

2. Under **samtidighetskontroll**anger **samtidighetskontroll** till **på**. 

3. Dra den **grad av parallellitet** skjutreglaget till önskat värde. Om du vill köra din logikapp sekventiellt dra skjutreglaget värdet till **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Ändra väntar på gränsen för körningar

Körs som standard logic app arbetsflödesinstanser alla på samma gång samtidigt eller parallellt upp till den [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Varje instans som utlösaren utlöses innan tidigare active arbetsflödesinstansen körningen är klar. Även om du kan [ändra den här Standardgräns](#change-trigger-concurrency), när antalet arbetsflödesinstanser når den nya gränsen för samtidighet, nya instanser måste vänta med att köra. 

Antal körningar som kan vänta har också en [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), som du kan ändra. Men när logikappen når gränsen för att vänta körningar, accepterar Logic Apps-motorn inte längre nya körningar. Begäran och webhook-utlösare returnera 429-fel och återkommande utlösare starta hoppar över avsökningen försök.

Om du vill ändra Standardgräns för att vänta körs i den underliggande utlösa definition, lägga till den `runtimeConfiguration.concurency.maximumWaitingRuns` egenskap med ett värde mellan `0` och `100`. 

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

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Utlösa instanser sekventiellt

Om du vill köra varje logic ange app arbetsflödesinstansen först efter föregående instans har slutförts kör, att utlösaren ska köras sekventiellt. Du kan använda antingen kod Vyredigeraren eller Logic Apps Designer eftersom också ändra inställningen för samtidighet via designer lägger till eller uppdaterar den `runtimeConfiguration.concurrency.runs` egenskapen i den underliggande utlösardefinitionen och vice versa. 

> [!NOTE] 
> När du ställer in en utlösare för att köra sekventiellt antingen med hjälp av designern eller visa Kodredigeraren inte ställer in utlösarens `operationOptions` egenskap `Sequential` i kodredigeraren för vyn. Annars kan få du ett valideringsfel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvy

Ange något av dessa egenskaper, men inte båda i definitionen för utlösare. 

Ange den `runtimeConfiguration.concurrency.runs` egenskap `1`:

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

*-or-*

Ange den `operationOptions` egenskap `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I utlösarens övre högra hörnet väljer du ellipserna (...)-knappen och väljer sedan **inställningar**.

2. Under **samtidighetskontroll**anger **gränsen** till **på**. 

3. Dra den **grad av parallellitet** skjutreglaget för hur många `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Kör ”för var och en” loopar sekventiellt

Om du vill köra en ”för var och en” loop anger körs, iteration förrän den föregående iterationen är klar ”för var och en”-åtgärd att köra sekventiellt. Du kan använda antingen kod Vyredigeraren eller Logic Apps Designer eftersom också ändra åtgärdens samtidighet via designer lägger till eller uppdaterar den `runtimeConfiguration.concurrency.repetitions` egenskapen i den underliggande åtgärdsdefinitionen och vice versa. 

> [!NOTE] 
> När du anger en ”för var och en”-åtgärd för att köra sekventiellt antingen genom att använda designer eller visa Kodredigerare kan inte ange åtgärdens `operationOptions` egenskap `Sequential` i kodredigeraren för vyn. Annars kan få du ett valideringsfel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvy

Ange något av dessa egenskaper, men inte båda i åtgärdsdefinitionen av. 

Ange den `runtimeConfiguration.concurrency.repetitions` egenskap `1`:

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

*-or-*

Ange den `operationOptions` egenskap `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps Designer

1. I den **för var och en** åtgärdens övre högra hörnet väljer du ellipserna (...)-knappen och välj sedan **inställningar**.

2. Under **samtidighetskontroll**anger **samtidighetskontroll** till **på**. 

3. Dra den **grad av parallellitet** skjutreglaget för hur många `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Kör åtgärder synkront

Som standard följer alla HTTP-baserade åtgärder standard asynkron åtgärd mönster. Det här mönstret anger att när en HTTP-baserade åtgärd skickar en begäran till den angivna slutpunkten, fjärrservern skickar tillbaka ett svar med ”202-ACCEPTERAD”. Det här svaret innebär att servern har accepterat för bearbetning. Logic Apps-motorn kontrollerar den URL som anges av svarets platsrubrik förrän bearbetningen stoppas, vilket är alla icke-202-svaret.

Dock förfrågningar har en tidsgräns som begränsar, vilket för långvariga åtgärder, kan du inaktivera asynkrona beteendet genom att lägga till och ställa in den `operationOptions` egenskap `DisableAsyncPattern` under åtgärdens indata.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Kör i läget för hög genomströmning

För en enkel logikapp-körningen, antalet åtgärder som utförs var femte minut har en [Standardgräns](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Att höja gränsen till den [maximala](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) möjliga, ange den `operationOptions` egenskap `OptimizedForHighThroughput`. Den här inställningen placerar logikappen i ”dataflöden” läge. 

> [!NOTE]
> Högt dataflöde läge finns i förhandsversion. Du kan även distribuera en arbetsbelastning i mer än en logikapp efter behov.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Autentisera HTTP-utlösare och åtgärder

HTTP-slutpunkter har stöd för olika typer av autentisering. Du kan konfigurera autentisering för dessa HTTP-utlösare och åtgärder:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP-webhook](../connectors/connectors-native-webhook.md)

Här är typerna av autentisering som du kan ställa in:

* [Grundläggande autentisering](#basic-authentication)
* [Autentisering av klientcertifikat](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth-autentisering](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Kontrollera att du skyddar känslig information som ditt arbetsflöde för logikappsdefinitionen hanterar. Använd säkra parametrar och koda data om det behövs. Läs mer om att använda och skydda parametrar [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Grundläggande autentisering

För [grundläggande autentisering](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) med hjälp av Azure Active Directory, din utlösare eller åtgärd definition kan innehålla en `authentication` JSON-objekt som har egenskaper som anges av tabellen nedan. Du kan använda för att komma åt parametervärdena vid körning i `@parameters('parameterName')` uttryck, som tillhandahålls av den [Definitionsspråk för arbetsflödet](https://aka.ms/logicappsdocs). 

| Egenskap  | Krävs | Value | Beskrivning | 
|----------|----------|-------|-------------| 
| **type** | Ja | "Basic" | Den autentiseringstyp som använder, vilket är ”Basic” här | 
| **användarnamn** | Ja | "@parameters('userNameParam')" | Användarnamnet för att autentisera åtkomst till mål-tjänstslutpunkt |
| **Lösenord** | Ja | ”@parameters(passwordParam)” | Lösenord för att autentisera åtkomst till mål-tjänstslutpunkt |
||||| 

I det här exemplet definition för HTTP-åtgärd, den `authentication` anger `Basic` autentisering. Läs mer om att använda och skydda parametrar [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Kontrollera att du skyddar känslig information som ditt arbetsflöde för logikappsdefinitionen hanterar. Använd säkra parametrar och koda data om det behövs. Mer information om hur du skyddar parametrar finns i [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autentisering av klientcertifikat

För [certifikatbaserad autentisering](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) med Azure Active Directory kan din utlösare eller åtgärd definition kan innehålla en `authentication` JSON-objekt som har egenskaper som anges av tabellen nedan. Du kan använda för att komma åt parametervärdena vid körning i `@parameters('parameterName')` uttryck, som tillhandahålls av den [Definitionsspråk för arbetsflödet](https://aka.ms/logicappsdocs). Gränser för hur många klientcertifikat som du kan använda för finns i [gränser och konfigurering för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

| Egenskap  | Krävs | Value | Beskrivning |
|----------|----------|-------|-------------|
| **type** | Ja | "ClientCertificate" | Autentiseringstypen som ska användas för Secure Sockets Layer (SSL)-klientcertifikat. Självsignerade certifikat för SSL stöds inte finns stöd för självsignerade certifikat. |
| **pfx** | Ja | "@parameters('pfxParam') | Base64-kodad innehållet från en Personal Information Exchange (PFX)-fil |
| **Lösenord** | Ja | ”@parameters(passwordParam)” | Lösenord för åtkomst till PFX-filen |
||||| 

I det här exemplet definition för HTTP-åtgärd, den `authentication` anger `ClientCertificate` autentisering. Läs mer om att använda och skydda parametrar [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Kontrollera att du skyddar känslig information som ditt arbetsflöde för logikappsdefinitionen hanterar. Använd säkra parametrar och koda data om det behövs. Mer information om hur du skyddar parametrar finns i [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Azure Active Directory (AD) OAuth-autentisering

För [Azure AD OAuth-autentisering](../active-directory/develop/authentication-scenarios.md), din utlösare eller åtgärd definition kan innehålla en `authentication` JSON-objekt som har egenskaper som anges av tabellen nedan. Du kan använda för att komma åt parametervärdena vid körning i `@parameters('parameterName')` uttryck, som tillhandahålls av den [Definitionsspråk för arbetsflödet](https://aka.ms/logicappsdocs).

| Egenskap  | Krävs | Value | Beskrivning |
|----------|----------|-------|-------------|
| **type** | Ja | `ActiveDirectoryOAuth` | Den autentiseringstyp som använder, vilket är ”ActiveDirectoryOAuth” för Azure AD OAuth |
| **utfärdare** | Nej | <*URL-for-authority-token-issuer*> | URL-Adressen för utfärdaren som tillhandahåller autentiseringstoken |
| **klient** | Ja | <*tenant-ID*> | Klient-ID för Azure AD-klient |
| **Målgrupp** | Ja | <*resurs att auktorisera*> | Den resurs som du vill använda för auktorisering, till exempel `https://management.core.windows.net/` |
| **clientId** | Ja | <*client-ID*> | Klient-ID för appen begär auktorisering |
| **credentialType** | Ja | ”Certifikat” eller ”hemlighet” | Typ av autentiseringsuppgift klienten använder för att begära tillstånd. Den här egenskapen och värdet visas inte i den underliggande definitionen, men anger de obligatoriska parametrarna för typ av autentiseringsuppgift. |
| **pfx** | Ja, endast under ”certifikat” typ av autentiseringsuppgift | "@parameters('pfxParam') | Base64-kodad innehållet från en Personal Information Exchange (PFX)-fil |
| **Lösenord** | Ja, endast under ”certifikat” typ av autentiseringsuppgift | ”@parameters(passwordParam)” | Lösenord för åtkomst till PFX-filen |
| **secret** | Ja, endast för ”hemligheten” autentiseringstyp | "@parameters('secretParam')" | Klienthemlighet för att begära tillstånd |
|||||

I det här exemplet definition för HTTP-åtgärd, den `authentication` anger `ActiveDirectoryOAuth` autentisering och ”hemligheten” autentiseringstyp. Läs mer om att använda och skydda parametrar [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Kontrollera att du skyddar känslig information som ditt arbetsflöde för logikappsdefinitionen hanterar. Använd säkra parametrar och koda data om det behövs. Mer information om hur du skyddar parametrar finns i [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Definitionsspråk för arbetsflödet](../logic-apps/logic-apps-workflow-definition-language.md)
