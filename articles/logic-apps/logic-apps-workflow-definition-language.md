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
ms.date: 04/30/2018
ms.openlocfilehash: d80ffa862546f56e93a338a7a1db031e2cb55990
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845752"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Schemareferens för Definitionsspråk för arbetsflödet i Azure Logic Apps

När du skapar en logikapp i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), din logikapp har en underliggande arbetsflödesdefinitionen som beskriver den faktiska logik som körs i din logikapp. Den arbetsflödesdefinitionen använder [JSON](https://www.json.org/) och följer en struktur som verifieras av schemat Definitionsspråk för arbetsflödet. Den här referensen innehåller en översikt om den här strukturen och hur schemat definierar element i din arbetsflödesdefinitionen.

## <a name="workflow-definition-structure"></a>Arbetsflöde-definitionsstruktur

En arbetsflödesdefinition alltid innehåller en utlösare för kontrollanten din logikapp och en eller flera åtgärder som körs när utlösaren aktiveras.

Här är den övergripande strukturen för en arbetsflödesdefinition:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Element | Krävs | Beskrivning |
|---------|----------|-------------|
| definition | Ja | Från elementet för din arbetsflödesdefinition |
| $schema | Endast när externt refererar till en arbetsflödesdefinition | Plats för schemat JSON-fil som beskriver den Definitionsspråk för arbetsflödet-versionen som du hittar här: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| contentVersion | Nej | Versionsnumret för din arbetsflödesdefinitionen som är ”1.0.0.0” som standard. Ange ett värde som ska användas för att identifiera och verifiera rätt definitionen när du distribuerar ett arbetsflöde. |
| parameters | Nej | Definitioner för en eller flera parametrar som skickar data i ditt arbetsflöde <p><p>Maximal parametrar: 50 |
| utlösare | Nej | Definitioner för en eller flera utlösare som instansierar arbetsflödet. Du kan definiera mer än en utlösare, men endast med Definitionsspråk för arbetsflödet, inte visuellt med Logikappdesignern. <p><p>Maximal utlösare: 10 |
| Åtgärder | Nej | Definitioner för en eller flera åtgärder att köra vid körning av arbetsflödet <p><p>Antal åtgärder: 250 |
| utdata | Nej | Definitioner för utdata som returneras från ett arbetsflöde som kör <p><p>Maximal utdata: 10 |
||||

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

| Element | Krävs | Typ | Beskrivning |
|---------|----------|------|-------------|
| typ | Ja | int, float, string, securestring, bool, matris, JSON-objekt, secureobject <p><p>**Obs!** Alla lösenord, nycklar och hemligheter kan du använda den `securestring` och `secureobject` skriver eftersom den `GET` åtgärden inte returnerar de här typerna. Mer information om hur du skyddar parametrar finns i [skydda din logikapp](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Typen för parametern |
| Standardvärde | Ja | Samma som `type` | Standard-parametervärdet när inget värde anges när du skapar en instans av arbetsflödet |
| allowedValues | Nej | Samma som `type` | En matris med värden som parametern kan acceptera |
| metadata | Nej | JSON-objekt | Övriga parametern detaljer, till exempel namnet eller en läsbar beskrivning för logikappen eller flödet designfasen data som används av Visual Studio eller andra verktyg |
||||

## <a name="triggers-and-actions"></a>Utlösare och åtgärder

I en arbetsflödesdefinition den `triggers` och `actions` avsnitt definierar de anrop som sker under körning av ditt arbetsflöde. Mer information om dessa avsnitt och syntax finns i [utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

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

| Element | Krävs | Typ | Beskrivning |
|---------|----------|------|-------------|
| <*key-name*> | Ja | String | Nyckelnamn för utdata returvärde |
| typ | Ja | int, float, string, securestring, bool, matris, JSON-objekt | Typen för det returnera värdet för utdata |
| value | Ja | Samma som `type` | Det returnera värdet för utdata |
|||||

Om du vill hämta utdata från ett arbetsflöde som kör granska din logikapps körningshistorik och information i Azure portal eller Använd den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows). Du kan även skicka utdata till externa system, till exempel Power BI så att du kan skapa instrumentpaneler.

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

| Operator | Aktivitet |
|----------|------|
| ' | Om du vill använda en strängliteral som indata eller i uttryck och funktioner, omsluta strängen bara med enkla citattecken, till exempel `'<myString>'`. Använd inte dubbla citattecken (””), som står i konflikt med JSON-formatering runt ett helt uttryck. Exempel: <p>**Ja**: length('Hello') </br>**Inte**: length("Hello") <p>När du skickar matriser eller siffror, behöver du inte wrapping skiljetecken. Exempel: <p>**Ja**: längden ([1, 2, 3]) </br>**Inte**: längden (”[1, 2, 3]”) |
| [] | Om du vill referera till ett värde på en specifik plats (index) i en matris, använder du hakparenteser. Till exempel för att hämta objektet på andra i en matris: <p>`myArray[1]` |
| . | Om du vill referera till en egenskap i ett objekt, använder du punktoperatorn. Till exempel för att hämta den `name` -egenskapen för en `customer` JSON-objekt: <p>`"@parameters('customer').name"` |
| ? | Om du vill referera till null egenskaper i ett objekt utan ett körningsfel, använder du operatorn frågetecken. Du kan till exempel använda det här uttrycket för att hantera null utdata från en utlösare: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Vissa uttryck få deras värden från runtime-åtgärder som inte kanske finns ännu när din arbetsflödesdefinitionen börjar köras. Du kan använda för att referera till eller arbeta med dessa värden i uttryck, [ *functions* ](../logic-apps/workflow-definition-language-functions-reference.md) som tillhandahåller Definitionsspråk för arbetsflödet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Definitionsspråk för arbetsflödet åtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Lär dig mer om programmässigt skapa och hantera logikappar med den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows)
