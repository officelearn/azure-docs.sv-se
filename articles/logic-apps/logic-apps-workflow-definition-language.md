---
title: Schemareferens för Definitionsspråk för arbetsflödet – Azure Logic Apps | Microsoft Docs
description: Skriva anpassade arbetsflödesdefinitioner för Azure Logic Apps med Definitionsspråk för arbetsflödet
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 0ac191f1191357ecc7292d51b298567f7f4e4786
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391232"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Schemareferens för Definitionsspråk för arbetsflödet i Azure Logic Apps

När du skapar ett logic app-arbetsflöde med [Azure Logic Apps](../logic-apps/logic-apps-overview.md), ditt arbetsflöde underliggande definitionen beskrivs den faktiska logik som körs i din logikapp. Den här beskrivningen följer en struktur som har definierats och godkänts av schemat Definitionsspråk för arbetsflödet som använder [JavaScript Object Notation (JSON)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Arbetsflöde-definitionsstruktur

En arbetsflödesdefinition har minst en utlösare som skapar en instans av din logikapp, plus en eller flera åtgärder som din logikapp körs. 

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
| $schema | Endast när externt refererar till en arbetsflödesdefinition | Plats för schemat JSON-fil som beskriver den Definitionsspråk för arbetsflödet-versionen som du hittar här: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Nej | Versionsnumret för din arbetsflödesdefinitionen som är ”1.0.0.0” som standard. Ange ett värde som ska användas för att identifiera och verifiera rätt definitionen när du distribuerar ett arbetsflöde. | 
| parameters | Nej | Definitioner för en eller flera parametrar som skickar data i ditt arbetsflöde <p><p>Maximal parametrar: 50 | 
| utlösare | Nej | Definitioner för en eller flera utlösare som instansierar arbetsflödet. Du kan definiera mer än en utlösare, men endast med Definitionsspråk för arbetsflödet, inte visuellt med Logikappdesignern. <p><p>Maximal utlösare: 10 | 
| Åtgärder | Nej | Definitioner för en eller flera åtgärder att köra vid körning av arbetsflödet <p><p>Antal åtgärder: 250 | 
| utdata | Nej | Definitioner för utdata som returneras från ett arbetsflöde som kör <p><p>Maximal utdata: 10 |  
|||| 

## <a name="parameters"></a>Parametrar

I den `parameters` avsnittet, definiera alla arbetsflödesparametrar som logikappen använder vid distribution för att acceptera indata. Både parametern och parametervärden måste anges vid distributionen. Innan du kan använda dessa parametrar i andra avsnitt i arbetsflödet, se till att du deklarerar alla parametrar i dessa avsnitt. 

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
| typ | Ja | int, float, string, securestring, bool, matris, JSON-objekt, secureobject <p><p>**Obs**: alla lösenord, nycklar och hemligheter kan du använda den `securestring` och `secureobject` skriver eftersom den `GET` åtgärden inte returnerar de här typerna. | Typen för parametern |
| Standardvärde | Nej | Samma som `type` | Standard-parametervärdet när inget värde anges när du skapar en instans av arbetsflödet | 
| allowedValues | Nej | Samma som `type` | En matris med värden som parametern kan acceptera |  
| metadata | Nej | JSON-objekt | Övriga parametern detaljer, till exempel namnet eller en läsbar beskrivning för din logikapp eller ett designläge data som används av Visual Studio eller andra verktyg |  
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
| <*nyckel-name*> | Ja | Sträng | Nyckelnamn för utdata returvärde |  
| typ | Ja | int, float, string, securestring, bool, matris, JSON-objekt | Typen för det returnera värdet för utdata | 
| värde | Ja | Samma som `type` | Det returnera värdet för utdata |  
||||| 

Om du vill hämta utdata från ett arbetsflöde som kör granska logikappen körningshistorik och information i Azure portal eller Använd den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows). Du kan även skicka utdata till externa system, till exempel Power BI så att du kan skapa instrumentpaneler. 

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
| ”Sophia Owen” | Returnera följande tecken: 'Sophia Owen' |
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
| ”\@parameters('myBirthMonth')” | Returnerar den här strängen: ”January” |  
| ”\@{parameters('myBirthMonth')}” | Returnerar den här strängen: ”January” |  
| ”\@parameters('myAge')” | Returnera det här antalet: 42 |  
| ”\@{parameters('myAge')}” | Returnerar det tal som en sträng: ”42” |  
| ”Min ålder är \@{parameters('myAge')}” | Returnerar den här strängen: ”min ålder är 42” |  
| ”\@sammanfoga (min ålder är, string(parameters('myAge')))” | Returnerar den här strängen: ”min ålder är 42” |  
| ”Min ålder är \@ \@{parameters('myAge')}” | Returnerar den här strängen, som innehåller uttrycket ”: min ålder är \@{parameters('myAge')}' | 
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

Vissa uttryck få deras värden från runtime-åtgärder som inte kanske finns ännu när en logikapp börjar köras. Du kan använda för att referera till eller arbeta med dessa värden i uttryck, [ *functions*](../logic-apps/workflow-definition-language-functions-reference.md). Exempel: du kan använda matematiska funktioner för beräkningar, till exempel den [Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add) som returnerar summan från heltal eller flyttal. Detaljerad information om varje funktion finns i den [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).
Eller lära dig mer om funktioner och deras generell användning.

Här är bara några exempel på uppgifter som du kan utföra med functions: 

| Aktivitet | Funktionens syntax | Resultat | 
| ---- | --------------- | ------ | 
| Returnera en sträng i gemener format. | toLower ('<*text*> ”) <p>Till exempel: toLower('Hello') | ”hello” | 
| Returnera en globalt unik identifierare (GUID). | GUID() |”c2ecc88d-88c8-4096-912c-d6f2e2b138ce” | 
|||| 

Det här exemplet visar hur du kan hämta värdet från den `customerName` parametern och tilldela värdet i `accountName` egenskapen med hjälp av den [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funktion i ett uttryck:

```json
"accountName": "@parameters('customerName')"
```

Följande är några andra allmänna metoder som du kan använda funktioner i uttryck:

| Aktivitet | Funktionens syntax i ett uttryck | 
| ---- | -------------------------------- | 
| Utföra arbete till ett objekt genom att skicka objekt till en funktion. | ”\@<*functionName*> (<*objekt*>)” | 
| 1. Hämta den *parameterName*'s värde med hjälp av den kapslade `parameters()` funktion. </br>2. Utföra arbete med resultatet genom att skicka detta värde till *functionName*. | ”\@<*functionName*> (parametrar (” <*parameterName*> ”))” | 
| 1. Hämta resultatet från den kapslade inre funktionen *functionName*. </br>2. Skicka resultatet till funktionen yttre *functionName2*. | ”\@<*functionName2*> (<*functionName*> (<*objekt*>))” | 
| 1. Få resultat från *functionName*. </br>2. Med hänsyn till att resultatet är ett objekt med egenskapen *propertyName*, hämta egenskapens värde. | ”\@<*functionName*>(<*item*>). <*propertyName*>” | 
||| 

Till exempel den `concat()` funktionen kan ta minst två strängvärden som parametrar. Den här funktionen kombinerar dessa strängar till en sträng. Du kan antingen skicka i stränglitteraler, till exempel ”Sophia” och ”Owen” så att du får en kombinerad sträng, ”SophiaOwen”:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Eller så kan du få strängvärden från parametrar. Det här exemplet används den `parameters()` funktion i varje `concat()` parametern och `firstName` och `lastName` parametrar. Du sedan skicka resulterande strängar till den `concat()` så att du får en kombinerad sträng, till exempel ”SophiaOwen”:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

I båda fallen båda exemplen tilldela resultat som ska den `customerName` egenskapen. 

Detaljerad information om varje funktion finns i den [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).
Eller lära dig mer om funktioner baserat på deras generell användning.

<a name="string-functions"></a>

### <a name="string-functions"></a>Strängfunktioner

Om du vill arbeta med strängar, du kan använda dessa strängfunktioner och även vissa [samling funktioner](#collection-functions). Strängfunktioner fungerar endast för strängar. 

| Strängfunktion | Aktivitet | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinera två eller flera strängar och returnera den kombinerade strängen. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Kontrollera om en sträng som slutar med den angivna delsträngen. | 
| [GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Returnera startpositionen för en delsträng. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Returnera slutpositionen efter en delsträng. | 
| [Ersätt](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersätta en understräng med den angivna strängen och returnerar den uppdaterade strängen. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Returnera en matris som innehåller alla tecken från en sträng, och mellan varje tecken med specifika avgränsningstecken. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontrollera om en sträng som börjar med en viss delsträng. | 
| [delsträngen](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Returnera tecken från en sträng, med början från den angivna positionen. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Returnera en sträng i gemener format. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Returnera en sträng i versaler format. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Ta bort inledande och avslutande blanksteg från en sträng och returnera den uppdaterade strängen. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Samling funktioner

Du kan använda dessa funktioner för samlingen om du vill arbeta med samlingar, vanligtvis matriser, strängar och ibland ordlistor. 

| Funktionen för samlingen | Aktivitet | 
| ------------------- | ---- | 
| [innehåller](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontrollera om en samling har ett specifikt objekt. |
| [tom](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Kontrollera om en samling är tom. | 
| [första](../logic-apps/workflow-definition-language-functions-reference.md#first) | Returnera det första objektet från en samling. | 
| [skärningspunkten](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Returnerar en samling som har *endast* vanliga objekt mellan de angivna samlingarna. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Returnerar en sträng som har *alla* objekt från en matris, avgränsade med det angivna tecknet. | 
| [senaste](../logic-apps/workflow-definition-language-functions-reference.md#last) | Returnera det sista objektet från en samling. | 
| [Längd](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller en matris. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Ta bort objekt från början av en samling och returnerar *alla andra* objekt. | 
| [ta](../logic-apps/workflow-definition-language-functions-reference.md#take) | Returnera objekt från början av en samling. | 
| [Union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Returnerar en samling som har *alla* objekt från de angivna samlingarna. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Jämförelsefunktioner

För att arbeta med villkor, jämför värden och uttryck resultat eller utvärdera olika typer av logik, kan du använda dessa jämförelsefunktioner. Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Jämförelse av funktion | Aktivitet | 
| ------------------- | ---- | 
| [och](../logic-apps/workflow-definition-language-functions-reference.md#and) | Kontrollera om alla uttryck utvärderas som true. | 
| [är lika med](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Kontrollera om båda värdena är likvärdiga. | 
| [större](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Kontrollera om det första värdet är större än det andra värdet. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Kontrollera om det första värdet är större än eller lika med det andra värdet. | 
| [Om](../logic-apps/workflow-definition-language-functions-reference.md#if) | Kontrollera om ett uttryck är SANT eller FALSKT. Baserat på resultatet kan returnera ett angivet värde. | 
| [mindre](../logic-apps/workflow-definition-language-functions-reference.md#less) | Kontrollera om det första värdet är mindre än det andra värdet. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Kontrollera om det första värdet är mindre än eller lika med det andra värdet. | 
| [inte](../logic-apps/workflow-definition-language-functions-reference.md#not) | Kontrollera om ett uttryck är FALSKT. | 
| [eller](../logic-apps/workflow-definition-language-functions-reference.md#or) | Kontrollera om minst en uttrycket är sant. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Konverteringsfunktioner

Om du vill ändra ett värde svarstyp eller, kan du använda dessa konverteringsfunktioner. Du kan till exempel ändra ett värde från ett booleskt värde till ett heltal. Läs hur Logikappar hanterar innehållstyper vid konvertering i [hantera innehållstyper](../logic-apps/logic-apps-content-type.md). Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Konverteringsfunktion | Aktivitet | 
| ------------------- | ---- | 
| [Matris](../logic-apps/workflow-definition-language-functions-reference.md#array) | Returnera en matris från en enda som angetts som indata. Flera inmatningar Se [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [Base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Returnera den base64-kodad versionen efter en sträng. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Returnera den binära versionen för en base64-kodad sträng. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Returnera sträng för en base64-kodad sträng. | 
| [binär](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Returnera den binära versionen för ett indatavärde. | 
| [Bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Returnera booleskt för ett indatavärde. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Returnera en matris från flera inmatningar. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Returnera data-URI för ett indatavärde. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Returnera sträng för en data-URI. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Returnera sträng för en base64-kodad sträng. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Returnera den binära versionen för en data-URI. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Returnerar en sträng som ersätter escape-tecken med avkodade versioner. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecknen med escape-tecken. | 
| [flyttal](../logic-apps/workflow-definition-language-functions-reference.md#float) | Returnerar en flytande peka nummer för ett indatavärde. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Returnera heltal för en sträng. | 
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | Returnera TYPVÄRDE för JavaScript Object Notation (JSON) eller objekt för en sträng eller XML. | 
| [sträng](../logic-apps/workflow-definition-language-functions-reference.md#string) | Returnera sträng för ett indatavärde. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Returnera den URI-kodad versionen för ett indatavärde genom att ersätta URL-osäkra tecknen med escape-tecken. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Returnera sträng för en URI-kodad sträng. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Returnera XML-versionen för en sträng. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Matematikfunktioner

Du kan använda dessa matematiska funktioner för att fungera med heltal och flyttal. Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Matematiska funktion | Aktivitet | 
| ------------- | ---- | 
| [Lägg till](../logic-apps/workflow-definition-language-functions-reference.md#add) | Returnera resultatet från att lägga till två tal. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Returnera resultatet från divisionen av två tal. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Returnera det högsta värdet från en uppsättning siffror eller en matris. | 
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Returnera det lägsta värdet från en uppsättning siffror eller en matris. | 
| [MOD](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Returnera återstoden av att dividera två tal. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Returnera produkten från multiplicera två tal. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Returnera ett slumpvist heltal från ett visst intervall. | 
| [Adressintervall](../logic-apps/workflow-definition-language-functions-reference.md#range) | Returnera en heltalsmatris som startar från en angiven heltal. | 
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Returnera resultatet från att subtrahera den andra siffran från den första siffran. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Datum- och tidsfunktioner

Du kan använda dessa funktioner för datum och tid om du vill arbeta med datum och tid.
Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Funktionen för datum och tid | Aktivitet | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Lägga till ett antal dagar till en tidsstämpel. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Lägga till ett antal timmar till en tidsstämpel. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Lägga till ett antal minuter till en tidsstämpel. | 
| [Lägg_till_sekunder](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Lägga till ett antal sekunder till en tidsstämpel. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Lägga till ett antal tidsenheter till en tidsstämpel. Se även [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertera en tidsstämpel från Universal Coordinated för tid (UTC) till måltidszon. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konvertera en tidsstämpel från källtidszonen tidszon mål. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertera en tidsstämpel från källtidszonen till Coordinated för Universal Time (UTC). | 
| [dag i månaden](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Returnera dagen på månadskomponenten från en tidsstämpel. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Returnera dagen i veckodagskomponenten från en tidsstämpel. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Returnera dagen på årskomponenten från en tidsstämpel. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Returnera datumet från en tidsstämpel. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna. Se även [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Returnera början på dagen för en tidsstämpel. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Returnera början på timmen för en tidsstämpel. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Returnera början på månaden för en tidsstämpel. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Ta bort ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticken](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Returnera den `ticks` egenskapsvärdet för en angiven tidsstämpel. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Returnera den aktuella tidsstämpeln som en sträng. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Arbetsflödesfunktioner

Med hjälp av dessa arbetsflödesfunktioner kan du:

* Få information om en arbetsflödesinstans vid körning. 
* Arbeta med de indata som används för kontrollanten logikappar.
* Referera till utdatan från utlösare och åtgärder.

Du kan till exempel refererar till utdatan från en åtgärd och använda dessa data i en senare åtgärd. Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Arbetsflödesfunktion | Aktivitet | 
| ----------------- | ---- | 
| [Åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action) | Returnera den aktuella åtgärden utdata vid körning, eller värden från andra JSON-namn och värde-par. Se även [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Returnerar en åtgärd `body` utdata vid körning. Se även [brödtext](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Returnera en åtgärd utdata vid körning. Se [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [Åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Returnera en åtgärd utdata vid körning, eller värden från andra JSON-namn och värde-par. Se även [åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [Brödtext](#body) | Returnerar en åtgärd `body` utdata vid körning. Se även [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Skapa en matris med värden som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* åtgärd utdata. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Returnera ett enstaka värde som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulärkodade utdata*. | 
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | Returnera det aktuella objektet i matrisen under åtgärdens aktuella iteration när inuti en upprepningsåtgärd över en matris. | 
| [objekt](../logic-apps/workflow-definition-language-functions-reference.md#items) | När du är i ett för varje eller gör tills loop, returnerar det aktuella objektet från den angivna loopen.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Returnera ”Motringnings-URL” som anropar en utlösare eller åtgärder. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Returnera brödtexten för en viss del i utdata för en åtgärd som har flera delar. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Returnera värdet för en parameter som beskrivs i sina logikapp-definitioner. | 
| [Utlösare](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Returnera en utlösare utdata vid körning, eller från andra JSON-namn och värde-par. Se även [triggerOutputs](#triggerOutputs) och [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Returnerar en utlösare `body` utdata vid körning. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Returnera ett enstaka värde som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösa utdata. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Returnera brödtexten för en viss del i en utlösarens multipart-utdata. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Skapa en matris vars värden matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösa utdata. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Returnera en utlösare utdata vid körning, eller värden från andra JSON-namn och värde-par. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [Variabler](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Returnera värdet för en specifik variabel. | 
| [Arbetsflöde](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Returnera all information om själva arbetsflödet under körningen. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI-parsningsfunktioner

Om du vill arbeta med uniform resource Identifier (URI: er) och få olika värden för dessa URI: er, kan du använda dessa URI-parsningsfunktioner. Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| URI: N parsning av funktionen | Aktivitet | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Returnera den `host` värde för en uniform resource identifier (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Returnera den `path` värde för en uniform resource identifier (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Returnera den `path` och `query` värden för en uniform resource identifier (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Returnera den `port` värde för en uniform resource identifier (URI). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Returnera den `query` värde för en uniform resource identifier (URI). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Returnera den `scheme` värde för en uniform resource identifier (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON och XML-funktioner

Om du vill arbeta med JSON-objekt och XML-noder måste använda du dessa funktioner för datahantering. Fullständig referens om varje funktion finns i [alfabetisk referensartikeln](../logic-apps/workflow-definition-language-functions-reference.md).

| Datahanteringsfunktion | Aktivitet | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Lägg till en egenskap och dess värde eller namn / värde-par, ett JSON-objekt och returnera det uppdaterade objektet. | 
| [Slå samman](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Returnera det första icke-null-värdet från en eller flera parametrar. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Ta bort en egenskap från ett JSON-objekt och returnera det uppdaterade objektet. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ange värdet för egenskapen för ett JSON-objekt och returnera det uppdaterade objektet. | 
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Kontrollera XML för noder eller värden som matchar ett XPath (XML Path Language)-uttryck och returnerar matchande noder eller värden. | 
||| 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Definitionsspråk för arbetsflödet åtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Lär dig mer om programmässigt skapa och hantera logikappar med den [arbetsflöde REST API](https://docs.microsoft.com/rest/api/logic/workflows)
