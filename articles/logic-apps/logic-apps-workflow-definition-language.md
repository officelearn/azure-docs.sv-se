---
title: Schemareferens för arbetsflödesdefinitionsspråk
description: Referensguide till JSON-schemat och syntaxen för arbetsflödesdefinitionsspråket som beskriver arbetsflöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283867"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Schemareferensguide för arbetsflödesdefinitionsspråket i Azure Logic Apps

När du skapar en logikapp i [Azure Logic Apps](../logic-apps/logic-apps-overview.md)har logikappen en underliggande arbetsflödesdefinition som beskriver den faktiska logiken som körs i logikappen. Arbetsflödesdefinitionen använder [JSON](https://www.json.org/) och följer en struktur som valideras av schemat För arbetsflödesdefinitionsspråk. Den här referensen ger en översikt över den här strukturen och hur schemat definierar attribut i arbetsflödesdefinitionen.

## <a name="workflow-definition-structure"></a>Struktur för arbetsflödesdefinition

En arbetsflödesdefinition innehåller alltid en utlösare för instansiating din logikapp, plus en eller flera åtgärder som körs efter utlösaraktiveringen.

Här är högnivåstrukturen för en arbetsflödesdefinition:

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
| `definition` | Ja | Startelementet för arbetsflödesdefinitionen |
| `$schema` | Endast när en arbetsflödesdefinition externt refererar till | Platsen för JSON-schemafilen som beskriver versionen för arbetsflödesdefinitionsspråk, som du hittar här: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Inga | Definitionerna för en eller flera åtgärder som ska köras vid arbetsflödeskörning. Mer information finns i [Utlösare och åtgärder](#triggers-actions). <p><p>Maximala åtgärder: 250 |
| `contentVersion` | Inga | Versionsnumret för arbetsflödesdefinitionen, som är "1.0.0.0" som standard. Om du vill identifiera och bekräfta rätt definition när du distribuerar ett arbetsflöde anger du ett värde som ska användas. |
| `outputs` | Inga | Definitionerna för utdata som ska returneras från en arbetsflödeskörning. Mer information finns i [Utdata](#outputs). <p><p>Maximala utgångar: 10 |
| `parameters` | Inga | Definitionerna för en eller flera parametrar som skickar de värden som ska användas vid logikappens körning. Mer information finns i [Parametrar](#parameters). <p><p>Maximala parametrar: 50 |
| `staticResults` | Inga | Definitionerna för ett eller flera statiska resultat som returneras av åtgärder som utkast när statiska resultat aktiveras för dessa åtgärder. I varje åtgärdsdefinition refererar `runtimeConfiguration.staticResult.name` attributet `staticResults`till motsvarande definition inuti . Mer information finns i [Statiska resultat](#static-results). |
| `triggers` | Inga | Definitionerna för en eller flera utlösare som instansierar arbetsflödet. Du kan definiera mer än en utlösare, men bara med arbetsflödesdefinitionsspråket, inte visuellt via Logic Apps Designer. Mer information finns i [Utlösare och åtgärder](#triggers-actions). <p><p>Maximala utlösare: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Utlösare och åtgärder

I en arbetsflödesdefinition definierar `triggers` och `actions` avsnitten de anrop som sker under arbetsflödets körning. Syntax och mer information om dessa avsnitt finns i [Utlösare och åtgärder för arbetsflöde](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametrar

Distributionens livscykel har vanligtvis olika miljöer för utveckling, test, mellanlagring och produktion. När du distribuerar logikappar till olika miljöer vill du förmodligen använda olika värden, till exempel anslutningssträngar, baserat på dina distributionsbehov. Du kan också ha värden som du vill återanvända i hela logikappen utan hårdkodning eller som ändras ofta. I avsnittet arbetsflödesdefinition `parameters` kan du definiera eller redigera parametrar för de värden som logikappen använder under körning. Du måste definiera dessa parametrar först innan du kan referera till dessa parametrar någon annanstans i arbetsflödesdefinitionen.

Här är den allmänna strukturen för en parameterdefinition:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Attribut | Krävs | Typ | Beskrivning |
|-----------|----------|------|-------------|
| <*parameter-namn*> | Ja | String | Namnet på den parameter som du vill definiera |
| <*parametertyp*> | Ja | int, flyta, sträng, bool, array, objekt, securestring, secureobject <p><p>**Obs:** För alla lösenord, nycklar och `securestring` hemligheter, använd eller `secureobject` typer eftersom `GET` åtgärden inte returnerar dessa typer. Mer information om hur du skyddar parametrar finns i [Säkerhetsrekommendationer för åtgärds- och indataparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Typen för parametern |
| <*standard-parameter-värde*> | Ja | Samma som`type` | Standardparametervärdet som ska användas om inget värde anges när arbetsflödet instansierar. Attributet `defaultValue` krävs så att Logic App Designer kan visa parametern korrekt, men du kan ange ett tomt värde. |
| <*matris-med-tillåtna-parameter-värden*> | Inga | Matris | En matris med värden som parametern kan acceptera |
| <*parameterbeskrivning*> | Inga | JSON-objekt | Alla andra parameterdetaljer, till exempel en beskrivning av parametern |
||||

Skapa sedan en [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) för arbetsflödesdefinitionen, definiera mallparametrar som accepterar de värden du vill använda vid distributionen, ersätta hårdkodade värden med referenser till mall- eller arbetsflödesdefinitionsparametrar efter behov och lagra de värden som ska användas vid distributionen i en separat [parameterfil](../azure-resource-manager/templates/parameter-files.md). På så sätt kan du lättare ändra dessa värden genom parameterfilen utan att behöva uppdatera och distribuera om logikappen. Om du vill ha information som är känslig eller måste skyddas, till exempel användarnamn, lösenord och hemligheter, kan du lagra dessa värden i Azure Key Vault och låta parameterfilen hämta dessa värden från nyckelvalvet. Mer information och exempel på hur du definierar parametrar på mall- och arbetsflödesdefinitionsnivåerna finns i [Översikt: Automatisera distributionen för logikappar med Azure Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statiska resultat

I `staticResults` attributet definierar du `outputs` en `status` åtgärds utkast och att åtgärden returnerar när åtgärdens statiska resultatinställning är aktiverad. I åtgärdens definition refererar `runtimeConfiguration.staticResult.name` attributet till namnet för `staticResults`den statiska resultatdefinitionen inuti . Lär dig hur du kan [testa logikappar med utkastdata genom att ställa in statiska resultat](../logic-apps/test-logic-apps-mock-data-static-results.md).

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
| <*statiskt resultat-definition-namn*> | Ja | String | Namnet på en statisk resultatdefinition som en `runtimeConfiguration.staticResult` åtgärdsdefinition kan referera till via ett objekt. Mer information finns i [Konfigurationsinställningar för Körning](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Du kan använda vilket unikt namn du vill. Som standard läggs det här unika namnet till med ett tal som ökas vid behov. |
| <*utdata-attribut-och-värden-returnerade*> | Ja | Varierar | Kraven för dessa attribut varierar beroende på olika villkor. När `outputs` attributet `status` is `Succeeded`till exempel innehåller attribut och värden som returneras som utkast av åtgärden. Om `status` `Failed` `outputs` attributet is innehåller `errors` attributet attributet, som är `message` en matris med ett eller flera felobjekt som har felinformation. |
| <*rubrikvärden*> | Inga | JSON | Alla rubrikvärden som returneras av åtgärden |
| <*status-kod returneras*> | Ja | String | Statuskoden som returneras av åtgärden |
| <*åtgärdsstatus*> | Ja | String | Åtgärdens status, till exempel, `Succeeded` eller`Failed` |
|||||

I den här HTTP-åtgärdsdefinitionen `HTTP0` refererar `staticResults` attributet `runtimeConfiguration.staticResult.name` till exempel i attributet där utkasten för åtgärden definieras. Attributet `runtimeConfiguration.staticResult.staticResultOptions` anger att den statiska resultatinställningen finns `Enabled` på HTTP-åtgärden.

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

HTTP-åtgärden returnerar utdata `HTTP0` i `staticResults`definitionen inuti . I det här exemplet är `OK`det för statuskoden. För rubrikvärden är `"Content-Type": "application/JSON"`utkastet . För åtgärdens status är `Succeeded`utkastet .

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

Med JSON kan du ha bokstavliga värden som finns vid designtillfället, till exempel:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Du kan också ha värden som inte finns förrän på körning. Om du vill representera dessa värden kan du använda *uttryck*som utvärderas vid körning. Ett uttryck är en sekvens som kan innehålla en eller flera [funktioner](#functions), [operatorer,](#operators)variabler, explicita värden eller konstanter. I arbetsflödesdefinitionen kan du använda ett uttryck var som helst i ett JSON-strängvärde genom att prefixera uttrycket med at-sign (\@). När du utvärderar ett uttryck som representerar ett JSON-värde extraheras uttryckstexten \@ genom att tecknet tas bort och resulterar alltid i ett annat JSON-värde.

För den tidigare definierade `customerName` egenskapen kan du till exempel hämta egenskapsvärdet med hjälp av `accountName` funktionen [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) i ett uttryck och tilldela värdet till egenskapen:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Med stränginterpolering* kan du också använda flera uttryck \@ inuti strängar som är{}omrerade med tecknet och klammerparenteser ( ). Här är syntaxen:

```json
@{ "<expression1>", "<expression2>" }
```

Resultatet är alltid en sträng, vilket `concat()` gör den här funktionen liknar funktionen, till exempel: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Om du har en bokstavlig \@ sträng som börjar \@ med \@ tecknet prefix tecknet med ett annat tecken som ett escape-tecken:\@\@

De här exemplen visar hur uttryck utvärderas:

| JSON-värde | Resultat |
|------------|--------|
| "Sophia Owen" | Returnera dessa tecken: "Sophia Owen" |
| "matris[1]" | Returnera dessa tecken: 'array[1]' |
| "\@\@" | Returnera dessa tecken som en sträng\@med ett tecken: ' ' |
| " \@" | Returnera dessa tecken som en sträng \@med två tecken: ' ' |
|||

För dessa exempel, anta att du definierar "myBirthMonth" lika med "Januari" och "myAge" lika med antalet 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

De här exemplen visar hur följande uttryck utvärderas:

| JSON-uttryck | Resultat |
|-----------------|--------|
| "\@parametrar('myBirthMonth')" | Returnera den här strängen: "Januari" |
| "\@{parameters('myBirthMonth')}" | Returnera den här strängen: "Januari" |
| "\@parametrar('myAge')" | Returnera detta nummer: 42 |
| "\@{parameters('myAge')}" | Returnera detta nummer som en sträng: "42" |
| "Min ålder \@är {parameters('myAge')}" | Lämna tillbaka den här strängen: "Min ålder är 42 år" |
| "\@concat('Min ålder är ', sträng(parametrar('myAge'))" | Lämna tillbaka den här strängen: "Min ålder är 42 år" |
| "Min ålder \@ \@är {parameters('myAge')}" | Returnera den här strängen, som innehåller \@uttrycket: "Min ålder är {parameters('myAge')}} |
|||

När du arbetar visuellt i Logic Apps Designer kan du skapa uttryck via uttrycksverktyget, till exempel:

![Logic Apps Designer > Uttrycksverktyget](./media/logic-apps-workflow-definition-language/expression-builder.png)

När du är klar visas uttrycket för motsvarande egenskap i arbetsflödesdefinitionen, till exempel egenskapen `searchQuery` här:

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

<a name="outputs"></a>

## <a name="outputs"></a>Utdata

I `outputs` avsnittet definierar du de data som arbetsflödet kan returnera när det körs. Om du till exempel vill spåra en viss status eller ett visst värde från varje körning anger du att arbetsflödesutdata returnerar dessa data.

> [!NOTE]
> När du svarar på inkommande begäranden från en `outputs`tjänsts REST API ska du inte använda . Använd i `Response` stället åtgärdstypen. Mer information finns i [Arbetsflödesutlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

Här är den allmänna strukturen för en utdatadefinition:

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
| <*nyckelnamn*> | Ja | String | Nyckelnamnet för utdatareturvärdet |
| <*nyckeltyp*> | Ja | int, flyta, sträng, securestring, bool, array, JSON objekt | Typen för utdatareturvärdet |
| <*nyckelvärde*> | Ja | Samma som <*nyckeltyp*> | Avkastningsvärdet för utdata |
|||||

Om du vill hämta utdata från en arbetsflödeskörning granskar du logikappens körhistorik och information i Azure-portalen eller använder [REST-API:et för arbetsflöde](https://docs.microsoft.com/rest/api/logic/workflows). Du kan också skicka utdata till externa system, till exempel Power BI så att du kan skapa instrumentpaneler.

<a name="operators"></a>

## <a name="operators"></a>Operatorer

I [uttryck](#expressions) och [funktioner](#functions)utför operatorer specifika uppgifter, till exempel referens för en egenskap eller ett värde i en matris.

| Operator | Aktivitet |
|----------|------|
| ' | Om du vill använda en stränglitteral som indata eller i uttryck och `'<myString>'`funktioner radbryts strängen endast med enkla citattecken, till exempel . Använd inte dubbla citattecken (""), som står i konflikt med JSON-formateringen runt ett helt uttryck. Ett exempel: <p>**Ja**: längd('Hej') </br>**Nej:** längd("Hej") <p>När du skickar matriser eller tal behöver du inte radbrytande interpunktion. Ett exempel: <p>**Ja**: längd([1, 2, 3]) </br>**Nej**: längd("[1, 2, 3]") |
| [] | Om du vill referera till ett värde till en viss position (index) i en matris använder du hakparenteser. Om du till exempel vill hämta det andra objektet i en matris: <p>`myArray[1]` |
| . | Om du vill referera till en egenskap i ett objekt använder du punktoperatorn. Så här hämtar `name` du till `customer` exempel egenskapen för ett JSON-objekt: <p>`"@parameters('customer').name"` |
| ? | Om du vill referera till null-egenskaper i ett objekt utan körningsfel använder du frågetecknet operatorn. Om du till exempel vill hantera null-utdata från en utlösare kan du använda det här uttrycket: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funktioner

Vissa uttryck hämtar sina värden från körningsåtgärder som kanske ännu inte finns när arbetsflödesdefinitionen börjar köras. Om du vill referera till eller arbeta med dessa värden i uttryck kan du använda [*funktioner*](../logic-apps/workflow-definition-language-functions-reference.md) som arbetsflödesdefinitionsspråket tillhandahåller.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [språkåtgärder och utlösare av arbetsflödesdefinition](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Lär dig mer om att programmässigt skapa och hantera logikappar med [REST-API:et för arbetsflöde](https://docs.microsoft.com/rest/api/logic/workflows)
