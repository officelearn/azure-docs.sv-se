---
title: Schemareferens för Definitionsspråk för arbetsflödet – Azure Logic Apps
description: Referensguide för Definitionsspråk för arbetsflödet schemat i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 3b0ad33ea6348f24079b3c88f972437244c0bc93
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596756"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Schemareferens för Definitionsspråk för arbetsflödet i Azure Logic Apps

När du skapar en logikapp i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), din logikapp har en underliggande arbetsflödesdefinitionen som beskriver den faktiska logik som körs i din logikapp. Den arbetsflödesdefinitionen använder [JSON](https://www.json.org/) och följer en struktur som verifieras av schemat Definitionsspråk för arbetsflödet. Den här referensen innehåller en översikt om den här strukturen och hur schemat definierar attribut i din arbetsflödesdefinitionen.

## <a name="workflow-definition-structure"></a>Arbetsflöde-definitionsstruktur

En arbetsflödesdefinition alltid innehåller en utlösare för kontrollanten din logikapp och en eller flera åtgärder som körs när utlösaren aktiveras.

Här är den övergripande strukturen för en arbetsflödesdefinition:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attribut | Krävs | Beskrivning |
|-----------|----------|-------------|
| `definition` | Ja | Från elementet för din arbetsflödesdefinition |
| `$schema` | Endast när externt refererar till en arbetsflödesdefinition | Plats för schemat JSON-fil som beskriver den Definitionsspråk för arbetsflödet-versionen som du hittar här: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nej | Definitioner för en eller flera åtgärder att köra vid körning av arbetsflödet. Mer information finns i [utlösare och åtgärder](#triggers-actions). <p><p>Antal åtgärder: 250 |
| `contentVersion` | Nej | Versionsnumret för din arbetsflödesdefinitionen som är ”1.0.0.0” som standard. Ange ett värde som ska användas för att identifiera och verifiera rätt definitionen när du distribuerar ett arbetsflöde. |
| `outputs` | Nej | Definitioner för utdata som returneras från ett arbetsflöde som körs. Mer information finns i [utdata](#outputs). <p><p>Maximal utdata: 10 |
| `parameters` | Nej | Definitioner för en eller flera parametrar som skickar data i ditt arbetsflöde. Mer information finns i [parametrar](#parameters). <p><p>Maximal parametrar: 50 |
| `staticResults` | Nej | Definitioner för en eller flera statiska resultat som returneras av åtgärder som fingerad utdata när statiska resultat är aktiverade på dessa åtgärder. I åtgärdsdefinitionen av varje i `runtimeConfiguration.staticResult.name` attribut refererar till den motsvarande definitionen i `staticResults`. Mer information finns i [Statiska resultat](#static-results). |
| `triggers` | Nej | Definitioner för en eller flera utlösare som instansierar arbetsflödet. Du kan definiera mer än en utlösare, men endast med Definitionsspråk för arbetsflödet, inte visuellt med Logikappdesignern. Mer information finns i [utlösare och åtgärder](#triggers-actions). <p><p>Maximal utlösare: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Utlösare och åtgärder

I en arbetsflödesdefinition den `triggers` och `actions` avsnitt definierar de anrop som sker under körning av ditt arbetsflöde. Mer information om dessa avsnitt och syntax finns i [utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="outputs"></a>

## <a name="outputs"></a>Utdata

I den `outputs` avsnittet, definierar vilka data som ditt arbetsflöde kan returnera när du är klar körs. Exempel: Om du vill spåra en viss status eller ett värde från varje körning, anger du att arbetsflödets utdata returnerar dessa data.

> [!NOTE]
> Använd inte när svarar på inkommande begäranden från en service REST API, `outputs`. Använd i stället de `Response` åtgärdstyp. Mer information finns i [utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

Här är den allmänna strukturen för en utdata-definition:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attribut | Krävs | Typ | Beskrivning |
|-----------|----------|------|-------------|
| <*key-name*> | Ja | String | Nyckelnamn för utdata returvärde |
| <*typ av nyckel*> | Ja | int, float, string, securestring, bool, matris, JSON-objekt | Typen för det returnera värdet för utdata |
| <*key-value*> | Ja | Samma som <*nyckel-typ*> | Det returnera värdet för utdata |
|||||

Om du vill hämta utdata från ett arbetsflöde som kör granska din logikapps körningshistorik och information i Azure portal eller Använd den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows). Du kan även skicka utdata till externa system, till exempel Power BI så att du kan skapa instrumentpaneler.

<a name="parameters"></a>

## <a name="parameters"></a>Parametrar

I den `parameters` avsnittet, definiera alla arbetsflödesparametrar som din arbetsflödesdefinitionen använder vid distribution för att acceptera indata. Både parametern och parametervärden måste anges vid distributionen. Innan du kan använda dessa parametrar i andra avsnitt i arbetsflödet, se till att du deklarerar alla parametrar i dessa avsnitt. 

Här är den allmänna strukturen för en parameterdefinition:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Attribut | Obligatoriskt | Typ | Beskrivning |
|-----------|----------|------|-------------|
| <*Parametern-typ*> | Ja | int, float, string, securestring, bool, matris, JSON-objekt, secureobject <p><p>**Obs!** Alla lösenord, nycklar och hemligheter kan du använda den `securestring` och `secureobject` skriver eftersom den `GET` åtgärden inte returnerar de här typerna. Mer information om hur du skyddar parametrar finns i [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Typen för parametern |
| <*default-parameter-values*> | Ja | Samma som `type` | Standard-parametervärdet när inget värde anges när du skapar en instans av arbetsflödet |
| <*array-with-permitted-parameter-values*> | Nej | Matris | En matris med värden som parametern kan acceptera |
| `metadata` | Nej | JSON-objekt | Övriga parametern detaljer, till exempel namnet eller en läsbar beskrivning för logikappen eller flödet designfasen data som används av Visual Studio eller andra verktyg |
||||

<a name="static-results"></a>

## <a name="static-results"></a>Statisk resultat

I den `staticResults` attributet, definiera en åtgärd utkast `outputs` och `status` som åtgärden returnerar när den åtgärden statiska resultatet är aktiverad. I definitionen av åtgärdens den `runtimeConfiguration.staticResult.name` attribut refererar till namnet på definitionen för statiska resultatet i `staticResults`. Lär dig hur du kan [testa logic apps med fingerade data genom att ställa in statisk resultat](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attribut | Krävs | Typ | Beskrivning |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | Ja | String | Namnet på en definition av statiska resultatet som en åtgärdsdefinition kan hänvisa till via en `runtimeConfiguration.staticResult` objekt. Mer information finns i [Runtime konfigurationsinställningar](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Du kan använda ett unikt namn som du vill. Som standard läggs den här unika namnet med ett tal, ökas vid behov. |
| <*output-attributes-and-values-returned*> | Ja | Varierar | Kraven för dessa attribut varierar beroende på olika villkor. Till exempel när den `status` är `Succeeded`, `outputs` attribut innehåller attribut och värden returneras som fingerad utdata för åtgärden. Om den `status` är `Failed`, `outputs` attribut innehåller den `errors` attribut, som är en matris med en eller flera fel `message` objekt som har information om felet. |
| <*huvud-värden*> | Nej | JSON | Alla värden i huvudet som returneras av instruktionen |
| <*status för kod returnerat*> | Ja | String | Statuskoden för åtgärden |
| <*action-status*> | Ja | String | Åtgärdens status, till exempel `Succeeded` eller `Failed` |
|||||

I den här definitionen för HTTP-åtgärden, till exempel den `runtimeConfiguration.staticResult.name` attributet referenser `HTTP0` inuti den `staticResults` attribut där till fingerad utdatan för åtgärden definieras. Den `runtimeConfiguration.staticResult.staticResultOptions` attributet anger att inställningen statisk resultatet är `Enabled` på HTTP-åtgärden.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP-åtgärden returnerar utdata i den `HTTP0` definition i `staticResults`. I det här exemplet för statuskoden, fingerad utdata är `OK`. Huvud-värden fingerad utdata är `"Content-Type": "application/JSON"`. För åtgärdens status fingerad utdata är `Succeeded`.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Uttryck

Med JSON, kan du ha exakta värden som finns vid designtillfället, till exempel:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Du kan också har värden som inte finns tills körningstid. Du kan använda för att representera värdena *uttryck*, som utvärderas vid körning. Ett uttryck är en sekvens som kan innehålla en eller flera [functions](#functions), [operatörer](#operators), variabler, explicita värden eller konstanter. I din arbetsflödesdefinitionen, du kan använda ett uttryck var som helst i ett JSON-strängvärde genom uttryck med @-tecknet (\@). När du utvärderar ett uttryck som representerar ett JSON-värde, uttryck brödtext ska extraheras genom att ta bort den \@ tecken och alltid resultat i en annan JSON-värde.

Till exempel för den tidigare definierade `customerName` egenskap, kan du hämta egenskapens värde med hjälp av den [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) fungera i ett uttryck och tilldela detta värde till den `accountName` egenskapen:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*String interpolation* också kan du använda flera uttryck i strängar som är omslutna av den \@ tecken- och klammerparenteser ({}). Här är syntaxen:

```json
@{ "<expression1>", "<expression2>" }
```

Resultatet är alltid en sträng, vilket gör den här funktionen liknar den `concat()` fungerar, till exempel: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Om du har en teckensträng som börjar med den \@ tecken, prefix i \@ tecknet med ett annat \@ tecknet som escape-tecken: \@\@

De här exemplen visar hur uttryck utvärderas:

| JSON-värde | Resultat |
|------------|--------|
| "Sophia Owen" | Returnera följande tecken: 'Sophia Owen' |
| ”matris [1]” | Returnera följande tecken: 'array [1]' |
| "\@\@" | Returnerar tecknen som en sträng med ett tecken: '\@' |
| " \@" | Returnerar tecknen som en sträng med två tecken: ' \@' |
|||

Anta att du har definierat ”myBirthMonth” lika med ”January” och ”myAge” lika med antalet 42 för de här exemplen:

```json
"myBirthMonth": "January",
"myAge": 42
```

De här exemplen visar hur följande uttryck utvärderas:

| JSON-uttryck | Resultat |
|-----------------|--------|
| ”\@parameters('myBirthMonth')” | Returnera den här strängen: ”January” |
| ”\@{parameters('myBirthMonth')}” | Returnera den här strängen: ”January” |
| ”\@parameters('myAge')” | Returnera det här talet: 42 |
| ”\@{parameters('myAge')}” | Returnera det här talet som en sträng: "42" |
| ”Min ålder är \@{parameters('myAge')}” | Returnera den här strängen: ”Min ålder är 42” |
| ”\@sammanfoga (min ålder är, string(parameters('myAge')))” | Returnera den här strängen: ”Min ålder är 42” |
| ”Min ålder är \@ \@{parameters('myAge')}” | Returnera den här strängen, som innehåller uttrycket: ”Min ålder är \@{parameters('myAge')}' |
|||

När du arbetar visuellt i Logic Apps Designer, kan du skapa uttryck via Uttrycksverktyget, till exempel:

![Logic Apps Designer > Uttrycksverktyget](./media/logic-apps-workflow-definition-language/expression-builder.png)

När du är klar uttrycket visas för motsvarande egenskap i din arbetsflödesdefinitionen, till exempel, `searchQuery` egenskapen här:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operatorer

I [uttryck](#expressions) och [functions](#functions), operatorer för att utföra specifika uppgifter, till exempel en egenskap eller ett värde i en matris.

| Operator | Uppgift |
|----------|------|
| ' | Om du vill använda en strängliteral som indata eller i uttryck och funktioner, omsluta strängen bara med enkla citattecken, till exempel `'<myString>'`. Använd inte dubbla citattecken (””), som står i konflikt med JSON-formatering runt ett helt uttryck. Exempel: <p>**Ja**: length('Hello') </br>**Inte**: length("Hello") <p>När du skickar matriser eller siffror, behöver du inte wrapping skiljetecken. Exempel: <p>**Ja**: längden ([1, 2, 3]) </br>**Inte**: längden (”[1, 2, 3]”) |
| [] | Om du vill referera till ett värde på en specifik plats (index) i en matris, använder du hakparenteser. Till exempel för att hämta objektet på andra i en matris: <p>`myArray[1]` |
| . | Om du vill referera till en egenskap i ett objekt, använder du punktoperatorn. Till exempel för att hämta den `name` -egenskapen för en `customer` JSON-objekt: <p>`"@parameters('customer').name"` |
| ? | Om du vill referera till null egenskaper i ett objekt utan ett körningsfel, använder du operatorn frågetecken. Du kan till exempel använda det här uttrycket för att hantera null utdata från en utlösare: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funktioner

Vissa uttryck få deras värden från runtime-åtgärder som inte kanske finns ännu när din arbetsflödesdefinitionen börjar köras. Du kan använda för att referera till eller arbeta med dessa värden i uttryck, [ *functions* ](../logic-apps/workflow-definition-language-functions-reference.md) som tillhandahåller Definitionsspråk för arbetsflödet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Definitionsspråk för arbetsflödet åtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Lär dig mer om programmässigt skapa och hantera logikappar med den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows)
