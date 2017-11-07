---
title: "Arbetsflödet Definition Language schema - Azure Logic Apps | Microsoft Docs"
description: "Definiera arbetsflöden baserat på arbetsflödet definition schemat för Azure Logic Apps"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 36eee42b7b10dfb62e569d665f62a94fc94365be
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Arbetsflödet Definition Language schemat för Azure Logic Apps

En arbetsflödesdefinition innehåller faktiska logik som körs som en del av din logikapp. Den här definitionen innehåller en eller flera utlösare som startar logikappen och en eller flera åtgärder för logikappen att vidta.  
  
## <a name="basic-workflow-definition-structure"></a>Grundläggande arbetsflöde definition struktur

Här är den grundläggande strukturen i en arbetsflödesdefinition:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> Den [arbetsflöde Management REST API](https://docs.microsoft.com/rest/api/logic/workflows) dokumentationen innehåller information om hur du skapar och hanterar logik app arbetsflöden.
  
|Elementnamn|Krävs|Beskrivning|  
|------------------|--------------|-----------------|  
|$schema|Nej|Anger platsen för schemat JSON-fil som beskriver versionen av definition language. Den här platsen måste anges när du refererar till en definition av externt. Platsen är för det här dokumentet: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|Nej|Anger vilken definitionsversion. När du distribuerar ett arbetsflöde med definitionen kan du använda det här värdet för att se till att rätt definition används.|  
|parameters|Nej|Anger parametrar som används för att mata in data i definitionen. Du kan definiera högst 50 parametrar.|  
|Utlösare|Nej|Anger information för utlösare som initierar arbetsflödet. Högst 10 utlösare kan definieras.|  
|åtgärder|Nej|Anger åtgärder som vidtas som flödet kör. Du kan definiera högst 250 åtgärder.|  
|utdata|Nej|Anger information om distribuerade resursen. Du kan definiera högst 10 utdata.|  
  
## <a name="parameters"></a>Parametrar

Det här avsnittet anger de parametrar som används i arbetsflödesdefinitionen vid tidpunkten för distribution. Alla parametrar måste deklareras i det här avsnittet innan de kan användas i andra avsnitt i definitionen.  
  
I följande exempel visar strukturen för en parameterdefinition:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Elementnamn|Krävs|Beskrivning|  
|------------------|--------------|-----------------|  
|typ|Ja|**Typen**: sträng <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "string"}` <p> **Specifikationen**:`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typen**: securestring <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "securestring"}}` <p> **Specifikationen**:`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typen**: int <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "int"}}` <p> **Specifikationen**:`"parameters": {"parameter1": {"value" : 5}}` <p> **Typen**: bool <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "bool"}}` <p> **Specifikationen**:`"parameters": {"parameter1": { "value": true }}` <p> **Typen**: matris <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "array"}}` <p> **Specifikationen**:`"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Typen**: objektet <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikationen**:`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Typen**: secureobject <p> **Deklarationen**:`"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikationen**:`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Obs:** den `securestring` och `secureobject` typer returneras inte i `GET` åtgärder. Alla lösenord, nycklar och hemligheter ska använda den här typen.|  
|Standardvärde|Nej|Anger standardvärdet för parametern när inget värde anges när resursen har skapats.|  
|allowedValues|Nej|Anger en matris med tillåtna värden för parametern.|  
|Metadata|Nej|Anger ytterligare information om parametern, till exempel en läsbar beskrivning eller designläge data som används av Visual Studio eller andra verktyg.|  
  
Det här exemplet visar hur du kan använda en parameter i brödtexten i en åtgärd:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Parametrarna kan också användas i utdata.  
  
## <a name="triggers-and-actions"></a>Utlösare och åtgärder  

Ange de anrop som kan delta i arbetsflödeskörning utlösare och åtgärder. Mer information om det här avsnittet finns [arbetsflödesåtgärder och utlösare](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>utdata  

Utdata ange information som kan returneras från ett arbetsflöde som körs. Om du har en viss status eller ett värde som du vill spåra för varje körning kan inkludera du dessa data i kör utdata. Data visas i Management REST API för att köra och i hanteringsgränssnittet för att köras i Azure-portalen. Du kan också flöda dessa utdata till andra externa system som PowerBI för att skapa instrumentpaneler. Utdata är *inte* används för att svara på inkommande begäranden på REST API-tjänsten. Att svara på en inkommande begäran med hjälp av den `response` åtgärdstyp, här är ett exempel:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Elementnamn|Krävs|Beskrivning|  
|------------------|--------------|-----------------|  
|key1|Ja|Anger nyckelidentifierare för utdata. Ersätt **key1** med ett namn som du vill använda för att identifiera utdata.|  
|värde|Ja|Anger värdet för utdata.|  
|typ|Ja|Anger typen för det värde som har angetts. Möjliga är värden: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Uttryck  

JSON-värden i definitionen kan vara literal eller de kan vara uttryck som utvärderas när definitionen används. Exempel:  
  
```json
"name": "value"
```

 eller  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Vissa uttryck hämta sina värden från runtime-åtgärder som inte kanske finns i början av körningen. Du kan använda **funktioner** för att hämta några av dessa värden.  
  
Uttryck kan finnas var som helst i ett strängvärde i JSON och alltid resultera i en annan JSON-värde. När ett JSON-värde har bedömts vara ett uttryck, brödtexten i uttrycket ska extraheras genom att ta bort @-tecknet (@). Om det krävs en teckensträng som börjar med @, att strängen måste hoppas genom att använda@. I följande exempel visas hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|”parametrar”|Tecken-parametrar, returneras.|  
|”Parametrar [1]”|Tecken-parametrar [1] ”returneras.|  
|"@@"|En 1 tecken-sträng som innehåller ”@” returneras.|  
|" @"|En 2 teckensträng som innehåller ”@” returneras.|  
  
Med *string interpolerade*, uttryck kan även visas i strängar där uttryck omslutas i `@{ ... }`. Exempel: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

Resultatet är alltid en sträng, vilket gör den här funktionen liknar den `concat` funktion. Anta att du har definierat `myNumber` som `42` och `myString` som `sampleString`:  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|”@parameters(minSträng)”|Returnerar `sampleString` som en sträng.|  
|”@{parameters('myString')}”|Returnerar `sampleString` som en sträng.|  
|”@parameters(myNumber)”|Returnerar `42` som en *nummer*.|  
|”@{parameters('myNumber')}”|Returnerar `42` som en *sträng*.|  
|”Svaret är: @{parameters('myNumber')}”|Returnerar strängen `Answer is: 42`.|  
|”@concat(' Svaret är: ', string(parameters('myNumber')))”|Returnerar strängen`Answer is: 42`|  
|”Svaret är: @@ {parameters('myNumber')}”|Returnerar strängen `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operatorer  

Operatorer är de tecken som du kan använda i uttryck eller funktioner. 
  
|Operatorn|Beskrivning|  
|--------------|-----------------|  
|.|Punktoperatorn kan du referera till egenskaperna för ett objekt|  
|?|Operatorn frågetecken kan du referera till null egenskaper för ett objekt utan ett körningsfel. Du kan till exempel använda det här uttrycket för att hantera null utlösaren utdata: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|Enkelt citattecken är det enda sättet att omsluta stränglitteraler. Du kan inte använda dubbla citattecken i uttryck eftersom detta skiljetecken står i konflikt med JSON-offert som omsluter hela uttrycket.|  
|[]|Hakparentes kan användas för att hämta ett värde från en matris med ett visst index. Om du har en åtgärd som skickar till exempel `range(0,10)`i att den `forEach` funktion, du kan använda den här funktionen för att hämta objekt utanför matriser:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funktioner  

Du kan också kontakta funktioner i uttryck. I följande tabell visas de funktioner som kan användas i ett uttryck.  
  
|uttryck|Utvärdering|  
|----------------|----------------|  
|”@function(” Hello ”)”|Anropar funktionen tillhör definition med den exakta strängen Hello som den första parametern.|  
|”@function(' Den '' s lågfrekvent!')”|Anropar funktionen tillhör definition med den exakta strängen 'Är lågfrekvent!' som den första parametern|  
|”@function.prop1 ()”|Returnerar värdet för egenskapen egenskap1 för den `myfunction` medlem i definitionen.|  
|”@function(” Hello ”) .prop1”|Anropar funktionen tillhör definition med strängen ”Hello” som den första parametern och returnerar egenskapen egenskap1 för objektet.|  
|”@function(parameters('Hello'))”|Utvärderar Hello-parametern och skickar värdet till funktion|  
  
### <a name="referencing-functions"></a>Refererar till funktioner  

Du kan använda dessa funktioner för att referera till utdata från andra åtgärder i logikapp eller värden som skickades när logikappen skapades. Du kan till exempel referera data från ett steg till att använda den i en annan.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|parameters|Returnerar ett parametervärde som har definierats i definitionen. <p>`parameters('password')` <p> **Parameternummer**: 1 <p> **Namnet**: parametern <p> **Beskrivning**: krävs. Namnet på den parameter vars värden som du vill.|  
|Åtgärden|Gör ett uttryck att erhålla dess värde från andra JSON-namn och värdepar eller utdata från den aktuella runtime-åtgärden. Egenskapen som representeras av propertyPath i följande exempel är valfri. Om propertyPath inte anges är referensen till hela action-objekt. Den här funktionen kan bara användas i-tills villkoren i en åtgärd. <p>`action().outputs.body.propertyPath`|  
|åtgärder|Gör ett uttryck att erhålla dess värde från andra JSON-namn och värdepar eller resultatet av åtgärden runtime. Dessa uttryck deklarera uttryckligen att en åtgärd är beroende av en annan åtgärd. Egenskapen som representeras av propertyPath i följande exempel är valfri. Om propertyPath inte anges är referensen till hela action-objekt. Du kan använda det här elementet eller elementet villkor för att ange beroenden, men du behöver inte använda både för samma beroende resurs. <p>`actions('myAction').outputs.body.propertyPath` <p> **Parameternummer**: 1 <p> **Namnet**: åtgärdsnamn <p> **Beskrivning**: krävs. Namnet på åtgärden vars värden som du vill. <p> Tillgängliga egenskaper för åtgärdsobjektet är: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Finns det [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646) mer information om dessa egenskaper.|
|Utlösare|Gör ett uttryck att erhålla dess värde från andra JSON-namn och värdepar eller utdata för runtime-utlösare. Egenskapen som representeras av propertyPath i följande exempel är valfri. Om propertyPath inte anges är referensen till objektet hela utlösare. <p>`trigger().outputs.body.propertyPath` <p>När den används i en utlösare indata returnerar funktionen utdata för tidigare körningen. Men, när den används i en utlösarvillkor den `trigger` funktionen returnerar utdata för aktuella körningen. <p> Tillgängliga egenskaper i objektet för utlösare är: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Finns det [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644) mer information om dessa egenskaper.|
|actionOutputs|Den här funktionen är snabbformen för`actions('actionName').outputs` <p> **Parameternummer**: 1 <p> **Namnet**: åtgärdsnamn <p> **Beskrivning**: krävs. Namnet på åtgärden vars värden som du vill.|  
|actionBody och brödtext|Dessa funktioner är snabbformen för`actions('actionName').outputs.body` <p> **Parameternummer**: 1 <p> **Namnet**: åtgärdsnamn <p> **Beskrivning**: krävs. Namnet på åtgärden vars värden som du vill.|  
|triggerOutputs|Den här funktionen är snabbformen för`trigger().outputs`|  
|triggerBody|Den här funktionen är snabbformen för`trigger().outputs.body`|  
|Objektet|När den används i en upprepande åtgärd returnerar funktionen objekt som finns i matrisen för den här iteration av åtgärden. Om du har en åtgärd som körs för varje objekt en matris av meddelanden som kan du använda följande syntax: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Samlingen funktioner  

Dessa funktioner fungerar på samlingar som vanligtvis gäller för matriser, strängar, och ibland ordlistor.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|Innehåller|Returnerar true om uppslagslistan innehåller en nyckel, lista innehåller värdet eller strängen innehåller delsträngen. Den här funktionen returnerar till exempel `true`: <p>`contains('abacaba','aca')` <p> **Parameternummer**: 1 <p> **Namnet**: inom samlingen <p> **Beskrivning**: krävs. Att söka i samlingen. <p> **Parameternummer**: 2 <p> **Namnet**: Sök objekt <p> **Beskrivning**: krävs. Objektet för att hitta i den **inom samlingen**.|  
|Längd|Returnerar antalet element i en matris eller sträng. Den här funktionen returnerar till exempel `3`:  <p>`length('abc')` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. Samlingen som du vill hämta längden.|  
|tom|Returnerar true om objektet, matris eller sträng är tom. Den här funktionen returnerar till exempel `true`: <p>`empty('')` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. Samlingen för att kontrollera om den är tom.|  
|skärningspunkten|Returnerar en enda matris eller ett objekt som har gemensamma element mellan matriser eller objekt som överförts. Den här funktionen returnerar till exempel `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Parametrar för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av båda). Om det finns två objekt med samma namn, visas det sista objektet med det namnet i det sista objektet. <p> **Parameternummer**: 1...*n* <p> **Namnet**: samlingen*n* <p> **Beskrivning**: krävs. Samlingar att utvärdera. Ett objekt måste vara i alla samlingar som skickades till visas i resultatet.|  
|Union|Returnerar en enda matris eller ett objekt med alla element i matrisen eller objekt överförs till den här funktionen. Den här funktionen returnerar till exempel `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Parametrar för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av dessa). Om det finns två objekt med samma namn i det slutgiltiga resultatet, visas det sista objektet med det namnet i det sista objektet. <p> **Parameternummer**: 1...*n* <p> **Namnet**: samlingen*n* <p> **Beskrivning**: krävs. Samlingar att utvärdera. Ett objekt som visas i någon av samlingarna visas också i resultatet.|  
|första|Returnerar det första elementet i matrisen eller sträng skickades. Den här funktionen returnerar till exempel `0`: <p>`first([0,2,3])` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. Göra det första objektet från samlingen.|  
|senaste|Returnerar det sista elementet i matrisen eller sträng skickades. Den här funktionen returnerar till exempel `3`: <p>`last('0123')` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. Göra det sista objektet från samlingen.|  
|ta|Returnerar först **antal** element från en matris eller sträng skickades. Den här funktionen returnerar till exempel `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. I samlingen från först plats där **antal** objekt. <p> **Parameternummer**: 2 <p> **Namnet**: antal <p> **Beskrivning**: krävs. Antal objekt som ska ta den **samling**. Måste vara ett positivt heltal.|  
|Hoppa över|Returnerar element i matrisen med början vid index **antal**. Den här funktionen returnerar till exempel `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Parameternummer**: 1 <p> **Namnet**: samlingen <p> **Beskrivning**: krävs. Samlingen för att hoppa över först **antal** objekt från. <p> **Parameternummer**: 2 <p> **Namnet**: antal <p> **Beskrivning**: krävs. Antalet objekt som ska tas bort från framför **samling**. Måste vara ett positivt heltal.|  
|join|Returnerar en sträng med varje objekt i en matris som kopplas med avgränsare, till exempel returneras `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: samlingen<br /><br /> **Beskrivning**: krävs. Att ansluta till objekt från samlingen.<br /><br /> **Parameternummer**: 2<br /><br /> **Namnet**: avgränsare<br /><br /> **Beskrivning**: krävs. Strängen för att avgränsa poster.|  
  
### <a name="string-functions"></a>Strängfunktioner

Följande funktioner gäller endast för strängar. Du kan också använda vissa funktioner i samlingen i strängar.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|concat|Kombinerar valfritt antal strängar tillsammans. Om parametern 1 är till exempel `p1`, den här funktionen returnerar `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Parameternummer**: 1...*n* <p> **Namnet**: sträng*n* <p> **Beskrivning**: krävs. Strängar att kombinera till en sträng.|  
|delsträngen|Returnerar en delmängd av tecken från en sträng. Den här funktionen returnerar till exempel `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen där delsträngen tas. <p> **Parameternummer**: 2 <p> **Namnet**: startIndex <p> **Beskrivning**: krävs. Index för där delsträngen börjar i parameter 1. <p> **Parameternummer**: 3 <p> **Namnet**: längd <p> **Beskrivning**: krävs. Längden på delsträngen.|  
|Ersätt|Ersätter en sträng med en given sträng. Den här funktionen returnerar till exempel `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som genomsöks för parametern 2 och uppdateras med parametern 3, när parametern 2 hittas i parameter 1. <p> **Parameternummer**: 2 <p> **Namnet**: gamla sträng <p> **Beskrivning**: krävs. Strängen som ska ersätta med parametern 3, när en matchning hittas i parameter 1 <p> **Parameternummer**: 3 <p> **Namnet**: ny sträng <p> **Beskrivning**: krävs. Den sträng som används för att ersätta strängen i parametern 2 när en matchning hittas i parameter 1.|  
|GUID|Den här funktionen genererar ett globalt unik sträng (GUID). Den här funktionen kan till exempel generera detta GUID:`c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Parameternummer**: 1 <p> **Namnet**: Format <p> **Beskrivning**: valfria. En enda Formatspecificeraren som anger [formatera värdet för detta Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Formatparametern kan vara ”N”, ”D”, ”B”, ”P” eller ”X”. Om formatet inte är anges, används ”D”.|  
|toLower|Konverterar en sträng till gemener. Den här funktionen returnerar till exempel `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som ska konverteras till lägre skiftläge. Om ett tecken i strängen inte har en motsvarighet på gemener ingår tecknet oförändrad i den returnerade strängen.|  
|toUpper|Konverterar en sträng till versaler. Den här funktionen returnerar till exempel `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som ska konverteras till övre skiftläge. Om ett tecken i strängen inte har motsvarande versaler ingår tecknet oförändrad i den returnerade strängen.|  
|indexOf|Hitta index för ett värde inom strängen fall insensitively. Den här funktionen returnerar till exempel `7`: <p>`indexof('hello, world.', 'world')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som kan innehålla värdet. <p> **Parameternummer**: 2 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Värdet att söka index.|  
|lastIndexOf|Hitta senaste index för ett värde inom strängen fall insensitively. Den här funktionen returnerar till exempel `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som kan innehålla värdet. <p> **Parameternummer**: 2 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Värdet att söka index.|  
|StartsWith|Kontrollerar om strängen börjar med ett värde ärende insensitively. Den här funktionen returnerar till exempel `true`: <p>`startswith('hello, world', 'hello')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som kan innehålla värdet. <p> **Parameternummer**: 2 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Värdet för strängen kan börja med.|  
|EndsWith|Kontrollerar om strängen slutar med ett värde ärende insensitively. Den här funktionen returnerar till exempel `true`: <p>`endswith('hello, world', 'world')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som kan innehålla värdet. <p> **Parameternummer**: 2 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Värdet för strängen kan avslutas med.|  
|split|Delar upp strängen med avgränsare. Den här funktionen returnerar till exempel `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som delas. <p> **Parameternummer**: 2 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Avgränsare.|  
  
### <a name="logical-functions"></a>Logiska funktioner  

Dessa funktioner är användbara i villkor och kan användas för att utvärdera någon typ av logik.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|är lika med|Returnerar true om två värden är lika. Till exempel om parameter1 är someValue, returnerar funktionen `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Parameternummer**: 1 <p> **Namnet**: objekt-1 <p> **Beskrivning**: krävs. Det objekt som jämför med **objekt 2**. <p> **Parameternummer**: 2 <p> **Namnet**: objekt 2 <p> **Beskrivning**: krävs. Det objekt som jämför med **objekt 1**.|  
|mindre|Returnerar true om det första argumentet är mindre än andra. Observera att värden kan bara vara av typen heltal, flyttal eller string. Den här funktionen returnerar till exempel `true`: <p>`less(10,100)` <p> **Parameternummer**: 1 <p> **Namnet**: objekt-1 <p> **Beskrivning**: krävs. Objektet för att kontrollera om det är mindre än **objekt 2**. <p> **Parameternummer**: 2 <p> **Namnet**: objekt 2 <p> **Beskrivning**: krävs. Objektet för att kontrollera om det är större än **objekt 1**.|  
|lessOrEquals|Returnerar true om det första argumentet är mindre än eller lika med andra. Observera att värden kan bara vara av typen heltal, flyttal eller string. Den här funktionen returnerar till exempel `true`: <p>`lessOrEquals(10,10)` <p> **Parameternummer**: 1 <p> **Namnet**: objekt-1 <p> **Beskrivning**: krävs. Objektet som ska kontrollera om det är mindre eller lika med **objekt 2**. <p> **Parameternummer**: 2 <p> **Namnet**: objekt 2 <p> **Beskrivning**: krävs. Objektet för att kontrollera om den är större än eller lika med **objekt 1**.|  
|större|Returnerar true om det första argumentet är större än andra. Observera att värden kan bara vara av typen heltal, flyttal eller string. Den här funktionen returnerar till exempel `false`:  <p>`greater(10,10)` <p> **Parameternummer**: 1 <p> **Namnet**: objekt-1 <p> **Beskrivning**: krävs. Objektet för att kontrollera om det är större än **objekt 2**. <p> **Parameternummer**: 2 <p> **Namnet**: objekt 2 <p> **Beskrivning**: krävs. Objektet för att kontrollera om det är mindre än **objekt 1**.|  
|greaterOrEquals|Returnerar true om det första argumentet är större än eller lika med andra. Observera att värden kan bara vara av typen heltal, flyttal eller string. Den här funktionen returnerar till exempel `false`: <p>`greaterOrEquals(10,100)` <p> **Parameternummer**: 1 <p> **Namnet**: objekt-1 <p> **Beskrivning**: krävs. Objektet för att kontrollera om den är större än eller lika med **objekt 2**. <p> **Parameternummer**: 2 <p> **Namnet**: objekt 2 <p> **Beskrivning**: krävs. Objektet för att kontrollera om det är mindre än eller lika med **objekt 1**.|  
|och|Returnerar true om båda parametrarna är true. Båda argument måste vara booleska värden. Den här funktionen returnerar till exempel `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Parameternummer**: 1 <p> **Namnet**: booleskt 1 <p> **Beskrivning**: krävs. Det första argumentet måste vara `true`. <p> **Parameternummer**: 2 <p> **Namnet**: booleskt 2 <p> **Beskrivning**: krävs. Det andra argumentet måste vara `true`.|  
|eller|Returnerar true om endera parametern är true. Båda argument måste vara booleska värden. Den här funktionen returnerar till exempel `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Parameternummer**: 1 <p> **Namnet**: booleskt 1 <p> **Beskrivning**: krävs. Det första argumentet som kan vara `true`. <p> **Parameternummer**: 2 <p> **Namnet**: booleskt 2 <p> **Beskrivning**: krävs. Det andra argumentet får vara `true`.|  
|inte|Returnerar true om parametrarna som är `false`. Båda argument måste vara booleska värden. Den här funktionen returnerar till exempel `true`: <p>`not(contains('200 Success','Fail'))` <p> **Parameternummer**: 1 <p> **Namnet**: booleskt <p> **Beskrivning**: returnerar true om parametrarna som är `false`. Båda argument måste vara booleska värden. Den här funktionen returnerar `true`:`not(contains('200 Success','Fail'))`|  
|Om|Returnerar ett angivet värde baserat på om uttrycket resulterade i `true` eller `false`.  Den här funktionen returnerar till exempel `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Parameternummer**: 1 <p> **Namnet**: uttryck <p> **Beskrivning**: krävs. Uttrycket måste returnera ett booleskt värde som avgör vilket värde. <p> **Parameternummer**: 2 <p> **Namnet**: True <p> **Beskrivning**: krävs. Värdet som returneras om uttrycket är `true`. <p> **Parameternummer**: 3 <p> **Namnet**: False <p> **Beskrivning**: krävs. Värdet som returneras om uttrycket är `false`.|  
  
### <a name="conversion-functions"></a>Konverteringsfunktioner  

Dessa funktioner används för att konvertera mellan var och en av de inbyggda typerna för språk:  
  
- Sträng  
  
- heltal  
  
- flyttal  
  
- Booleskt värde  
  
- matriser  
  
- ordlistor  

-   Formulär  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|int|Konvertera parametern till ett heltal. Den här funktionen returnerar till exempel 100 som ett tal i stället för en sträng: <p>`int('100')` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som konverteras till ett heltal.|  
|Sträng|Konvertera parametern till en sträng. Den här funktionen returnerar till exempel `'10'`: <p>`string(10)` <p>Du kan också konvertera ett objekt till en sträng. Till exempel om den `myPar` parametern är ett objekt med en egenskap `abc : xyz`, och sedan på den här funktionen returnerar `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som konverteras till en sträng.|  
|JSON|Konvertera parametern till ett värde för typ av JSON och är motsatsen till `string()`. Den här funktionen returnerar till exempel `[1,2,3]` som en matris i stället för en sträng: <p>`json('[1,2,3]')` <p>På samma sätt kan du konvertera en sträng till ett objekt. Den här funktionen returnerar till exempel `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Den sträng som konverteras till en inbyggd typ-värde. <p>Den `json()` har stöd för XML som indata för funktionen. Till exempel parametervärdet för: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>konverteras till den här JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|flyttal|Konvertera argumentet parametern till ett flyttal. Den här funktionen returnerar till exempel `10.333`: <p>`float('10.333')` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som har konverterats till ett flyttal.|  
|bool|Konvertera parametern till ett booleskt värde. Den här funktionen returnerar till exempel `false`: <p>`bool(0)` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som har konverterats till ett booleskt värde.|  
|Base64|Returnerar base64-representation av Indatasträngen. Den här funktionen returnerar till exempel `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Parameternummer**: 1 <p> **Namnet**: String 1 <p> **Beskrivning**: krävs. Strängen som koda till base64-representation.|  
|base64ToBinary|Returnerar en a binär representation av en base64-kodad sträng. Den här funktionen returnerar till exempel binär representation av `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Base64-kodad sträng.|  
|base64ToString|Returnerar en strängrepresentation av en based64 kodad sträng. Den här funktionen returnerar till exempel `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Base64-kodad sträng.|  
|Binär|Returnerar en a binär representation av ett värde.  Den här funktionen returnerar till exempel en a binär representation av `some string`: <p>`binary('some string')` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som konverteras till binär.|  
|dataUriToBinary|Returnerar en a binär representation av en data-URI. Den här funktionen returnerar till exempel binär representation av `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Data-URI att konvertera till binär representation.|  
|dataUriToString|Returnerar en strängrepresentation av en data-URI. Den här funktionen returnerar till exempel `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng<p> **Beskrivning**: krävs. URI för att konvertera till sträng som innehåller data.|  
|dataUri|Returnerar en data-URI för ett värde. Den här funktionen returnerar till exempel data URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Parameternummer**: 1<p> **Namnet**: värde<p> **Beskrivning**: krävs. Värdet som ska konverteras till data-URI.|  
|decodeBase64|Returnerar en strängrepresentation av en inkommande based64-sträng. Den här funktionen returnerar till exempel `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: returnerar en strängrepresentation av en inkommande based64-sträng.|  
|encodeUriComponent|Visar URL: en sträng som skickas. Den här funktionen returnerar till exempel `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som escape-URL-osäkra tecken från.|  
|decodeUriComponent|UN-URL-visar den sträng som skickas. Den här funktionen returnerar till exempel `You Are:Cool/Awesome`: <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som avkoda URL-osäkra tecken från.|  
|decodeDataUri|Returnerar en a binär representation av ett indata-URI-strängen. Den här funktionen returnerar till exempel binär representation av `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng <p> **Beskrivning**: krävs. DataURI att avkoda i en a binär representation.|  
|uriComponent|Returnerar en URI kodad representation av ett värde. Den här funktionen returnerar till exempel `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Parameternummer**: 1<p> **Namnet**: sträng <p> **Beskrivning**: krävs. Strängen som ska vara URI-kodad.|  
|uriComponentToBinary|Returnerar en a binär representation av en URI-kodad sträng. Den här funktionen returnerar till exempel en a binär representation av `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Parameternummer**: 1 <p> **Namnet**: sträng<p> **Beskrivning**: krävs. URI-kodad sträng.|  
|uriComponentToString|Returnerar en strängrepresentation av en URI-kodad sträng. Den här funktionen returnerar till exempel `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Parameternummer**: 1<p> **Namnet**: sträng<p> **Beskrivning**: krävs. URI-kodad sträng.|  
|xml|Returnera ett XML-representation av värdet. Den här funktionen returnerar exempelvis XML innehåll representeras av `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>Den `xml()` har stöd för JSON-objekt som indata för funktionen. Till exempel parametern `{ "abc": "xyz" }` konverteras till XML-innehåll:`\<abc>xyz\</abc>` <p> **Parameternummer**: 1<p> **Namnet**: värde<p> **Beskrivning**: krävs. Värdet som ska konverteras till XML.|  
|matris|Konvertera parametern till en matris. Den här funktionen returnerar till exempel `["abc"]`: <p>`array('abc')` <p> **Parameternummer**: 1 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Det värde som konverteras till en matris.|
|createArray|Skapar en matris av parametrarna. Den här funktionen returnerar till exempel `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Parameternummer**: 1...*n* <p> **Namnet**: alla*n* <p> **Beskrivning**: krävs. Värdena som används för att kombinera till en matris.|
|triggerFormDataValue|Returnerar ett värde matchar nyckelnamnet från formulärdata eller utdata för formuläret-kodade utlösare.  Om det finns flera matchar det kommer fel.  Exempelvis returnerar följande `bar`:`triggerFormDataValue('foo')`<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: nyckelnamn<br /><br />**Beskrivning**: krävs. Nyckelnamnet värdets formuläret data att returnera.|
|triggerFormDataMultiValues|Returnerar en matris med värden som matchar nyckelnamnet från formulärdata eller utdata för formuläret-kodade utlösare.  Exempelvis returnerar följande `["bar"]`:`triggerFormDataValue('foo')`<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: nyckelnamn<br /><br />**Beskrivning**: krävs. Nyckelnamnet formulärvärden för data att returnera.|
|triggerMultipartBody|Returnerar innehållet för en del i en multipart utdata för utlösaren.<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: Index<br /><br />**Beskrivning**: krävs. Index för en del ska hämtas.|
|formDataValue|Returnerar ett värde matchar nyckelnamnet från formulärdata eller formulär-kodade utdata.  Om det finns flera matchar det kommer fel.  Exempelvis returnerar följande `bar`:`formDataValue('someAction', 'foo')`<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: åtgärdsnamn<br /><br />**Beskrivning**: krävs. Namnet på åtgärden med ett formulär-data eller en form-kodad.<br /><br />**Parameternummer**: 2<br /><br />**Namnet**: nyckelnamn<br /><br />**Beskrivning**: krävs. Nyckelnamnet värdets formuläret data att returnera.|
|formDataMultiValues|Returnerar en matris med värden som matchar nyckelnamnet från formulärdata eller formulär-kodade utdata.  Exempelvis returnerar följande `["bar"]`:`formDataMultiValues('someAction', 'foo')`<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: åtgärdsnamn<br /><br />**Beskrivning**: krävs. Namnet på åtgärden med ett formulär-data eller en form-kodad.<br /><br />**Parameternummer**: 2<br /><br />**Namnet**: nyckelnamn<br /><br />**Beskrivning**: krävs. Nyckelnamnet formulärvärden för data att returnera.|
|multipartBody|Returnerar innehållet för en del i en multipart resultatet av en åtgärd.<br /><br />**Parameternummer**: 1<br /><br />**Namnet**: åtgärdsnamn<br /><br />**Beskrivning**: krävs. Namnet på åtgärden med ett multipart svar.<br /><br />**Parameternummer**: 2<br /><br />**Namnet**: Index<br /><br />**Beskrivning**: krävs. Index för en del ska hämtas.|

### <a name="manipulation-functions"></a>Funktioner för datahantering
 
Dessa funktioner gäller XML och objekt.
 
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------| 
|Slå samman|Returnerar det första icke-null-objektet i argument som skickas. **Obs**: en tom sträng inte är null. Till exempel om parametrar 1 och 2 inte har definierats, returnerar funktionen `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Parameternummer**: 1...*n* <p> **Namnet**: objektet*n* <p> **Beskrivning**: krävs. Objekt att söka efter null.|
|addProperty|Returnerar ett objekt med en annan egenskap. Om egenskapen finns redan vid körning genereras ett fel. Den här funktionen returnerar till exempel objektet `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Parameternummer**: 1 <p> **Namnet**: objektet <p> **Beskrivning**: krävs. Att lägga till en ny egenskap i objektet. <p> **Parameternummer**: 2 <p> **Namnet**: egenskapsnamn <p> **Beskrivning**: krävs. Namnet på den nya egenskapen. <p> **Parameternummer**: 3 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Värdet som tilldelas den nya egenskapen.|
|setProperty|Returnerar ett objekt med en annan egenskap eller en befintlig egenskap anges till det angivna värdet. Den här funktionen returnerar till exempel objektet `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Parameternummer**: 1 <p> **Namnet**: objektet <p> **Beskrivning**: krävs. Objektet att ange egenskapen.<p> **Parameternummer**: 2 <p> **Namnet**: egenskapsnamn<p> **Beskrivning**: krävs. Namnet på egenskapen ny eller befintlig. <p> **Parameternummer**: 3 <p> **Namnet**: värde <p> **Beskrivning**: krävs. Värdet för att tilldela egenskapen.|
|removeProperty|Returnerar ett objekt med en egenskap tas bort. Om egenskapen för att ta bort inte finns returneras det ursprungliga objektet. Den här funktionen returnerar till exempel objektet `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Parameternummer**: 1 <p> **Namnet**: objektet <p> **Beskrivning**: krävs. Objekt att ta bort egenskapen från.<p> **Parameternummer**: 2 <p> **Namnet**: egenskapsnamn <p> **Beskrivning**: krävs. Namnet på egenskapen för att ta bort. <p>|
|XPath|Returnera en matris med XML-noder som matchar ett värde som xpath-uttryck som utvärderas till det xpath-uttrycken. <p> **Exempel 1** <p>Anta att värdet för parametern `p1` är en strängrepresentation av den här XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Den här koden:`xpath(xml(parameters('p1')), '/lab/robot/name')` <p>Returnerar <p>`[ <name>R1</name>, <name>R2</name> ]` <p>När den här koden: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>Returnerar <p>`13` <p> <p> **Exempel 2** <p>Anges i följande XML-innehåll: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Den här koden:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>eller den här koden: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>Returnerar <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>Och koden:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>Returnerar <p>``xyz`` <p> **Parameternummer**: 1 <p> **Namnet**: Xml <p> **Beskrivning**: krävs. XML-filen som du vill utvärdera XPath-uttryck. <p> **Parameternummer**: 2 <p> **Namnet**: XPath <p> **Beskrivning**: krävs. XPath-uttrycket ska utvärderas.|

### <a name="math-functions"></a>Matematiska funktioner  

Dessa funktioner kan användas för båda typer av siffror: **heltal** och **flyter**.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|Lägg till|Returnerar resultatet från att lägga till de två talen. Den här funktionen returnerar till exempel `20.333`: <p>`add(10,10.333)` <p> **Parameternummer**: 1 <p> **Namnet**: genom att hålla 1 <p> **Beskrivning**: krävs. Det tal som ska lägga till **genom att hålla 2**. <p> **Parameternummer**: 2 <p> **Namnet**: genom att hålla 2 <p> **Beskrivning**: krävs. Det tal som ska lägga till **genom att hålla 1**.|  
|Sub|Returnerar resultatet från att subtrahera två tal. Den här funktionen returnerar till exempel `-0.333`: <p>`sub(10,10.333)` <p> **Parameternummer**: 1 <p> **Namn på**: Minuend <p> **Beskrivning**: krävs. Numret som **Subtrahend** tas bort från. <p> **Parameternummer**: 2 <p> **Namn på**: Subtrahend <p> **Beskrivning**: krävs. Det tal som ska ta bort från den **Minuend**.|  
|mul|Returnerar resultatet från att multiplicera de två talen. Den här funktionen returnerar till exempel `103.33`: <p>`mul(10,10.333)` <p> **Parameternummer**: 1 <p> **Namn på**: Multiplicand 1 <p> **Beskrivning**: krävs. Talet som multipliceras **Multiplicand 2** med. <p> **Parameternummer**: 2 <p> **Namn på**: Multiplicand 2 <p> **Beskrivning**: krävs. Talet som multipliceras **Multiplicand 1** med.|  
|div|Returnerar resultatet från divisionen av de två talen. Den här funktionen returnerar till exempel `1.0333`: <p>`div(10.333,10)` <p> **Parameternummer**: 1 <p> **Namnet**: täljaren <p> **Beskrivning**: krävs. Det tal som du delar med den **Divisor**. <p> **Parameternummer**: 2 <p> **Namnet**: Divisor <p> **Beskrivning**: krävs. Det tal som ska divideras med det **täljaren** av.|  
|MOD|Returnerar resten efter att dela de två talen (modulo). Den här funktionen returnerar till exempel `2`: <p>`mod(10,4)` <p> **Parameternummer**: 1 <p> **Namnet**: täljaren <p> **Beskrivning**: krävs. Det tal som du delar med den **Divisor**. <p> **Parameternummer**: 2 <p> **Namnet**: Divisor <p> **Beskrivning**: krävs. Det tal som ska divideras med det **täljaren** av. Efter att divisionen tas resten.|  
|min.|Det finns två olika mönster för att anropa den här funktionen. <p>Här `min` tar en matris och funktionen returnerar `0`: <p>`min([0,1,2])` <p>Den här funktionen kan också ta en kommaavgränsad lista med värden och returnerar `0`: <p>`min(0,1,2)` <p> **Obs**: alla värden måste vara ett tal, så om parametern är en matris, matrisen måste endast innehålla siffror. <p> **Parameternummer**: 1 <p> **Namnet**: samlingen eller -värde <p> **Beskrivning**: krävs. Antingen en matris med värden för att hitta det minsta värdet eller det första värdet i en mängd. <p> **Parameternummer**: 2...*n* <p> **Namnet**: värde*n* <p> **Beskrivning**: valfria. Om den första parametern är ett värde, kan du skicka ytterligare värden och minimum för alla överförda värden returneras.|  
|Max|Det finns två olika mönster för att anropa den här funktionen. <p>Här `max` tar en matris och funktionen returnerar `2`: <p>`max([0,1,2])` <p>Den här funktionen kan också ta en kommaavgränsad lista med värden och returnerar `2`: <p>`max(0,1,2)` <p> **Obs**: alla värden måste vara ett tal, så om parametern är en matris, matrisen måste endast innehålla siffror. <p> **Parameternummer**: 1 <p> **Namnet**: samlingen eller -värde <p> **Beskrivning**: krävs. Antingen en matris med värden för att hitta det största värdet eller det första värdet i en mängd. <p> **Parameternummer**: 2...*n* <p> **Namnet**: värde*n* <p> **Beskrivning**: valfria. Om den första parametern är ett värde, kan du skicka ytterligare värden och maxvärdet för alla överförda värden returneras.|  
|intervallet|Genererar en heltalsmatris som startar från ett visst antal. Du kan definiera returnerade Matrislängden. <p>Den här funktionen returnerar till exempel `[3,4,5,6]`: <p>`range(3,4)` <p> **Parameternummer**: 1 <p> **Namnet**: startIndex <p> **Beskrivning**: krävs. Det första heltal i matrisen. <p> **Parameternummer**: 2 <p> **Namnet**: antal <p> **Beskrivning**: krävs. Det här värdet är antalet heltal som är i matrisen.|  
|SLUMP|Genererar ett slumpmässigt heltal inom det angivna intervallet (sammanlagt endast på första sidan). Den här funktionen kan till exempel returnera antingen `0` eller '1': <p>`rand(0,2)` <p> **Parameternummer**: 1 <p> **Namnet**: minsta <p> **Beskrivning**: krävs. Ett heltal som kan returneras. <p> **Parameternummer**: 2 <p> **Namnet**: högsta <p> **Beskrivning**: krävs. Det här värdet är nästa heltal efter det största heltal som kan returneras.|  
 
### <a name="date-functions"></a>Datumfunktioner  

|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|utcnow|Returnerar aktuell tidsstämpel som en sträng, till exempel: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Parameternummer**: 1 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|lägg_till_sekunder|Lägger till ett heltal sekunder en tidsstämpel för sträng skickades. Antalet sekunder som kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”), om inte en Formatspecificeraren tillhandahålls som standard. Till exempel: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Parameternummer**: 1 <p> **Namnet**: tidsstämpel <p> **Beskrivning**: krävs. En sträng som innehåller tidpunkten. <p> **Parameternummer**: 2 <p> **Namnet**: sekunder <p> **Beskrivning**: krävs. Antal sekunder att lägga till. Kan vara negativt subtrahera sekunder. <p> **Parameternummer**: 3 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|addminutes|Lägger till ett heltal antal minuter en tidsstämpel för sträng skickades. Antalet minuter som kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”), om inte en Formatspecificeraren tillhandahålls som standard. Till exempel: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Parameternummer**: 1 <p> **Namnet**: tidsstämpel <p> **Beskrivning**: krävs. En sträng som innehåller tidpunkten. <p> **Parameternummer**: 2 <p> **Namnet**: minuter <p> **Beskrivning**: krävs. Antal minuter att lägga till. Kan vara negativt subtrahera minuter. <p> **Parameternummer**: 3 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|addhours|Lägger till ett heltal timmar en tidsstämpel för sträng skickades. Antalet timmar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”), om inte en Formatspecificeraren tillhandahålls som standard. Till exempel: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Parameternummer**: 1 <p> **Namnet**: tidsstämpel <p> **Beskrivning**: krävs. En sträng som innehåller tidpunkten. <p> **Parameternummer**: 2 <p> **Namnet**: timmar <p> **Beskrivning**: krävs. Antal timmar att lägga till. Kan vara negativt subtrahera timmar. <p> **Parameternummer**: 3 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|adddays|Lägger till ett heltal antal dagar till en sträng tidsstämpel skickades. Antalet dagar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”), om inte en Formatspecificeraren tillhandahålls som standard. Till exempel: `2015-02-23T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Parameternummer**: 1 <p> **Namnet**: tidsstämpel <p> **Beskrivning**: krävs. En sträng som innehåller tidpunkten. <p> **Parameternummer**: 2 <p> **Namnet**: dagar <p> **Beskrivning**: krävs. Antal dagar att lägga till. Kan vara negativt subtrahera dagar. <p> **Parameternummer**: 3 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|FormatDateTime|Returnerar en sträng i datumformat. Resultatet är en sträng i ISO 8601-format (”o”), om inte en Formatspecificeraren tillhandahålls som standard. Till exempel: `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Parameternummer**: 1 <p> **Namnet**: datum <p> **Beskrivning**: krävs. En sträng som innehåller datum. <p> **Parameternummer**: 2 <p> **Namnet**: Format <p> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|startOfHour|Returnerar början på timmen till en sträng tidsstämpel skickades. Till exempel `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.<br /><br />**Parameternummer**: 2<br /><br /> **Namnet**: Format<br /><br /> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).|  
|startOfDay|Returnerar början på dagen och en tidsstämpel för sträng skickades. Till exempel `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.<br /><br />**Parameternummer**: 2<br /><br /> **Namnet**: Format<br /><br /> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).| 
|startOfMonth|Returnerar början på månaden till en sträng tidsstämpel skickades. Till exempel `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.<br /><br />**Parameternummer**: 2<br /><br /> **Namnet**: Format<br /><br /> **Beskrivning**: valfria. Antingen en [enstaka format specificerare tecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat formatmönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som anger att formatera värdet för den här tidsstämpel. Om formatet inte är anges, används ISO 8601-format (”o”).| 
|DayOfWeek|Returnerar en dag i veckan komponent i en sträng tidsstämpel.  Söndag är 0, måndag är 1 och så vidare. Till exempel `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.| 
|Dag i månaden|Returnerar dagen i månaden komponent i en sträng tidsstämpel. Till exempel `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.| 
|DayOfYear|Returnerar dagen på året komponent i en sträng tidsstämpel. Till exempel `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.| 
|skalstreck|Returnerar skalstrecken-egenskapen för ett sträng-tidsstämpel. Till exempel `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namnet**: tidsstämpel<br /><br /> **Beskrivning**: krävs. Detta är en sträng som innehåller tiden.| 
  
### <a name="workflow-functions"></a>Funktioner för arbetsflöde  

Dessa funktioner för att få information om själva arbetsflödet vid körning.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|listCallbackUrl|Returnerar en sträng att anropa för att anropa utlösare eller åtgärd. <p> **Obs**: den här funktionen kan endast användas i en **httpWebhook** och **apiConnectionWebhook**, inte i en **manuell**, **återkommande**, **http**, eller **apiConnection**. <p>Till exempel den `listCallbackUrl()` fungerar returnerar: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|arbetsflöde|Den här funktionen innehåller all information om själva arbetsflödet vid körningen. <p> Tillgängliga egenskaper för objektet arbetsflödet är: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> Värdet för den `run` egenskapen är ett objekt med följande egenskaper: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Finns det [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617) mer information om dessa egenskaper.<p> Till exempel namnet på den aktuella kör får använda den `"@workflow().run.name"` uttryck. |

## <a name="next-steps"></a>Nästa steg

[Åtgärder och utlösare för arbetsflöde](logic-apps-workflow-actions-triggers.md)
