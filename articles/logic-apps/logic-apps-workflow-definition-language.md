---
title: Schema för språk för arbets flödes definition – Azure Logic Apps
description: Schema referens för språk för arbets flödes definition i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 64c01baf0852e7e09ce9ffed2d079b47e95f7190
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680084"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Schema referens guide för språket för arbets flödes definition i Azure Logic Apps

När du skapar en Logic app i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), har din Logic app en underliggande arbets flödes definition som beskriver den faktiska logiken som körs i din Logic app. Den arbets flödes definitionen använder [JSON](https://www.json.org/) och följer en struktur som verifieras av språk schema för arbets flödes definition. Den här referensen ger en översikt över den här strukturen och hur schemat definierar attribut i arbets flödes definitionen.

## <a name="workflow-definition-structure"></a>Struktur för arbets flödes definition

En arbets flödes definition innehåller alltid en utlösare för att instansiera din Logic app, plus en eller flera åtgärder som körs när utlösaren utlöses.

Här är en övergripande struktur för en arbets flödes definition:

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
| `definition` | Ja | Start element för arbets flödes definitionen |
| `$schema` | Endast när externt refererar till en arbets flödes definition | Platsen för JSON-schemafilen som beskriver språk versionen för arbets flödes definitionen, som du hittar här: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nej | Definitionerna för en eller flera åtgärder som ska köras vid arbets flödets körning. Mer information finns i [utlösare och åtgärder](#triggers-actions). <p><p>Högsta antal åtgärder: 250 |
| `contentVersion` | Nej | Versions numret för arbets flödes definitionen, som är "1.0.0.0" som standard. Ange ett värde som ska användas för att identifiera och bekräfta rätt definition när du distribuerar ett arbets flöde. |
| `outputs` | Nej | Definitionerna för de utdata som ska returneras från en arbets flödes körning. Mer information finns i [utdata](#outputs). <p><p>Högsta antal utdata: 10 |
| `parameters` | Nej | Definitionerna för en eller flera parametrar som skickar värdena som ska användas i din Logic Apps-körning. Mer information finns i [parametrar](#parameters). <p><p>Högsta antal parametrar: 50 |
| `staticResults` | Nej | Definitionerna för ett eller flera statiska resultat som returneras av åtgärder som modellerade utdata när statiska resultat är aktiverade för dessa åtgärder. I varje åtgärds definition refererar `runtimeConfiguration.staticResult.name`-attributet till motsvarande definition i `staticResults`. Mer information finns i [statiska resultat](#static-results). |
| `triggers` | Nej | Definitionerna för en eller flera utlösare som instansierar arbets flödet. Du kan definiera mer än en utlösare, men endast med arbets flödets definitions språk, inte visuellt via Logic Apps designer. Mer information finns i [utlösare och åtgärder](#triggers-actions). <p><p>Högsta antal utlösare: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Utlösare och åtgärder

I en arbets flödes definition definierar avsnitten `triggers` och `actions` de anrop som inträffar under arbets flödets körning. Syntax och mer information om dessa avsnitt finns i [arbets flödes utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametrar

Distributions livs cykeln har vanligt vis olika miljöer för utveckling, testning, mellanlagring och produktion. När du distribuerar Logi Kap par till olika miljöer vill du förmodligen använda olika värden, t. ex. anslutnings strängar, baserat på dina distributions behov. Eller så kanske du har värden som du vill återanvända i din Logic app utan hårdkoda eller som ändras ofta. I arbets flödets definitions `parameters` avsnittet kan du definiera eller redigera parametrar för de värden som din Logic app använder vid körning. Du måste definiera dessa parametrar först innan du kan referera till parametrarna någon annan stans i arbets flödes definitionen.

Här är den allmänna strukturen för en parameter definition:

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
| <*parameter-name* > | Ja | Sträng | Namnet på den parameter som du vill definiera |
| <*parameter-type* > | Ja | int, Float, String, bool, array, Object, SecureString, secureobject <p><p>**Obs**: för alla lösen ord, nycklar och hemligheter använder du `securestring`-eller `secureobject`s typerna eftersom `GET` åtgärden inte returnerar dessa typer. Mer information om hur du skyddar parametrar finns i [säkerhets rekommendationer för åtgärder och indataparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Parameterns typ |
| <*standard parameter-värde* > | Ja | Samma som `type` | Det standard parameter värde som ska användas om inget värde anges när arbets flödet instansieras. Attributet `defaultValue` krävs så att Logic Apps Designer kan visa parametern korrekt, men du kan ange ett tomt värde. |
| <*matris – med-tillåtet parameter värde* > | Nej | Matris | En matris med värden som parametern kan acceptera |
| <*parameter-description* > | Nej | JSON-objekt | Alla andra parameter uppgifter, till exempel en beskrivning av parametern |
||||

Skapa sedan en [Azure Resource Manager mall](../azure-resource-manager/resource-group-overview.md) för arbets flödes definitionen, definiera mallparametrar som godkänner de värden som du vill ha vid distributionen, Ersätt hårdkodad-värden med referenser till mall-eller arbets flödes definitions parametrar som lämpligt och lagra de värden som ska användas vid distributionen i en separat [parameter fil](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). På så sätt kan du enkelt ändra dessa värden genom parameter filen utan att behöva uppdatera och distribuera om din Logic app. För information som är känslig eller måste skyddas, t. ex. användar namn, lösen ord och hemligheter, kan du lagra dessa värden i Azure Key Vault och låta parameter filen hämta dessa värden från ditt nyckel valv. Mer information och exempel på hur du definierar parametrar på mall-och arbets flödes definitions nivåer finns i [Översikt: Automatisera distribution av logi Kap par med Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statiska resultat

I `staticResults`-attributet definierar du en åtgärds modell `outputs` och `status` att åtgärden returnerar när åtgärdens statiska resultat-inställning är aktive rad. I åtgärdens definition refererar `runtimeConfiguration.staticResult.name` attribut till namnet på den statiska resultat definitionen i `staticResults`. Lär dig hur du kan testa Logi Kap par [med blå data genom att konfigurera statiska resultat](../logic-apps/test-logic-apps-mock-data-static-results.md).

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
| <*statiskt resultat-definition-namn* > | Ja | Sträng | Namnet på en statisk resultat definition som en åtgärds definition kan referera till via ett `runtimeConfiguration.staticResult`-objekt. Mer information finns i [konfigurations inställningar för körning](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Du kan använda valfritt unikt namn. Som standard läggs det här unika namnet till med ett nummer, vilket ökar efter behov. |
| <*utdata-attribut-och-Values-returnerade* > | Ja | Varierar | Kraven för dessa attribut varierar beroende på olika villkor. Om `status` till exempel är `Succeeded`, innehåller attributet `outputs` attribut och värden som returneras som bildutdata av åtgärden. Om `status` är `Failed` innehåller attributet `outputs` attributet `errors`, som är en matris med ett eller flera fel `message` objekt med fel information. |
| <*rubrik – värden* > | Nej | JSON | Alla rubrik värden som returneras av åtgärden |
| <*status-kod returnerad* > | Ja | Sträng | Status koden som returnerades av åtgärden |
| <*åtgärd – status* > | Ja | Sträng | Åtgärdens status, till exempel `Succeeded` eller `Failed` |
|||||

I den här definitionen av HTTP-åtgärden refererar exempelvis `runtimeConfiguration.staticResult.name`-attributet `HTTP0` inuti `staticResults`-attributet där de blå utmatningarna för åtgärden definieras. Attributet `runtimeConfiguration.staticResult.staticResultOptions` anger att den statiska resultat inställningen är `Enabled` för HTTP-åtgärden.

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

HTTP-åtgärden returnerar utdata i `HTTP0` definition i `staticResults`. I det här exemplet är den blå utdata `OK`. För rubrik värden är de blå utdata `"Content-Type": "application/JSON"`. När det gäller åtgärdens status är den blå utmatningen `Succeeded`.

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

Med JSON kan du ha exakta värden som finns i design läge, till exempel:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Du kan också ha värden som inte finns förrän kör tid. För att representera dessa värden kan du använda *uttryck*som utvärderas vid körning. Ett uttryck är en sekvens som kan innehålla en eller flera [funktioner](#functions), [operatorer](#operators), variabler, explicita värden eller konstanter. I arbets flödes definitionen kan du använda ett uttryck var som helst i ett JSON-sträng värde genom att prefixet med at-sign (\@). När du utvärderar ett uttryck som representerar ett JSON-värde extraheras uttrycks texten genom att \@s symbolen tas bort och alltid resulterar i ett annat JSON-värde.

För den tidigare definierade `customerName`-egenskapen kan du till exempel hämta egenskap svärdet genom att använda funktionen [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) i ett uttryck och tilldela värdet till egenskapen `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

Med hjälp av *String-interpolation* kan du också använda flera uttryck inuti strängar som omsluts av \@ tecken och klammerparenteser ({}). Här är syntaxen:

```json
@{ "<expression1>", "<expression2>" }
```

Resultatet är alltid en sträng, vilket innebär att den här funktionen liknar funktionen `concat()`, till exempel: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Om du har en tecken sträng som börjar med \@-tecken, ska du använda prefixet \@ tecken med ett annat \@ tecken som ett escape-tecken: \@ \@

Följande exempel visar hur uttryck utvärderas:

| JSON-värde | Resultat |
|------------|--------|
| "Sophia Owen" | Returnera följande tecken: ' Sophia Owen ' |
| "matris [1]" | Returnera följande tecken: ' matris [1] ' |
| "\@ \@" | Returnera dessa tecken som en tecken sträng: "\@" |
| "\@" | Returnera dessa tecken som en sträng med två tecken: "\@" |
|||

I de här exemplen antar vi att du definierar "myBirthMonth" lika med "januari" och "myAge" som motsvarar talet 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Följande exempel visar hur följande uttryck utvärderas:

| JSON-uttryck | Resultat |
|-----------------|--------|
| "\@parameters (' myBirthMonth ')" | Returnera den här strängen: "januari" |
| "\@ {parameters (' myBirthMonth ')}" | Returnera den här strängen: "januari" |
| "\@parameters (' myAge ')" | Returnera det här talet: 42 |
| "\@ {parameters (' myAge ')}" | Returnera det här talet som en sträng: "42" |
| "Min ålder är \@ {parameters (' myAge ')}" | Returnera den här strängen: "min ålder är 42" |
| "\@concat (' min ålder är ', String (parameters (' myAge ')))" | Returnera den här strängen: "min ålder är 42" |
| "Min ålder är \@ \@ {parameters (' myAge ')}" | Returnera den här strängen, som innehåller uttrycket: "min ålder är \@ {parameters (' myAge ')} ' |
|||

När du arbetar visuellt i Logic Apps Designer kan du skapa uttryck genom uttrycks verktyget, till exempel:

![Uttrycks verktyget för Logic Apps designer >](./media/logic-apps-workflow-definition-language/expression-builder.png)

När du är klar visas uttrycket för motsvarande egenskap i arbets flödes definitionen, till exempel egenskapen `searchQuery` här:

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

I avsnittet `outputs` definierar du de data som arbets flödet kan returnera när du har kört klart. Om du till exempel vill spåra en speciell status eller ett värde från varje körning anger du att arbets flödets utdata returnerar dessa data.

> [!NOTE]
> När du svarar på inkommande begär Anden från en tjänsts REST API ska du inte använda `outputs`. Använd i stället `Response` åtgärds typ. Mer information finns i [arbets flödes utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md).

Här är den allmänna strukturen för en utmatnings definition:

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
| <*nyckel namn* > | Ja | Sträng | Nyckel namnet för returvärdet för utdata |
| <*nyckel typ* > | Ja | int, Float, String, SecureString, bool, array, JSON-objekt | Retur värdets typ |
| <*nyckel värde* > | Ja | Samma som <*nyckel typ* > | Retur värde för utdata |
|||||

Om du vill hämta utdata från en arbets flödes körning granskar du din Logic Apps körnings historik och information i Azure Portal eller använder [arbets flödes REST API](https://docs.microsoft.com/rest/api/logic/workflows). Du kan också skicka utdata till externa system, till exempel Power BI så att du kan skapa instrument paneler.

<a name="operators"></a>

## <a name="operators"></a>Operatorer

I [uttryck](#expressions) och [funktioner](#functions)utför operatorer olika uppgifter, t. ex. referenser till en egenskap eller ett värde i en matris.

| Operator | Aktivitet |
|----------|------|
| ' | Om du vill använda en stränglitteral som inmatad eller i uttryck och funktioner radbryts strängen endast med enkla citat tecken, till exempel `'<myString>'`. Använd inte dubbla citat tecken (""), som står i konflikt med JSON-formateringen runt ett helt uttryck. Exempel: <p>**Ja**: längd (' Hej ') </br>**Nej**: längd ("Hej") <p>När du skickar matriser eller tal behöver du inte figursätta interpunktion. Exempel: <p>**Ja**: längd ([1, 2, 3]) </br>**Nej**: längd ("[1, 2, 3]") |
| [] | Om du vill referera till ett värde vid en speciell position (index) i en matris använder du hakparenteser. Till exempel för att hämta det andra objektet i en matris: <p>`myArray[1]` |
| . | Om du vill referera till en egenskap i ett objekt använder du punkt operatorn. Till exempel för att hämta `name`-egenskapen för ett `customer` JSON-objekt: <p>`"@parameters('customer').name"` |
| ? | Om du vill referera till null-egenskaper i ett objekt utan körnings fel använder du operatorn frågetecken. Om du till exempel vill hantera null-utdata från en utlösare kan du använda det här uttrycket: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Vissa uttryck hämtar sina värden från körnings åtgärder som kanske inte redan finns när arbets flödes definitionen börjar köras. Om du vill referera till eller arbeta med dessa värden i uttryck kan du använda [*funktioner*](../logic-apps/workflow-definition-language-functions-reference.md) som definieras av arbets flödes definitions språket.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [språk åtgärder och utlösare för arbets flödes definition](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Lär dig mer om att skapa och hantera Logi Kap par program mässigt med [arbets flödes REST API](https://docs.microsoft.com/rest/api/logic/workflows)
