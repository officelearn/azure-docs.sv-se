---
title: Referens för funktioner i Definitionsspråk för arbetsflödet – Azure Logic Apps och Microsoft Flow
description: Referensguide för funktioner i uttryck som skapats med Definitionsspråk för arbetsflödet för Azure Logic Apps och Microsoft Flow
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 08/15/2018
ms.openlocfilehash: d7ea62c51065cbe85a905b4ff78743fdc11c1e10
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618217"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps-and-microsoft-flow"></a>Funktionsreferens för Definitionsspråk för arbetsflödet i Azure Logic Apps och Microsoft Flow

För arbetsflödesdefinitioner i [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Microsoft Flow](https://docs.microsoft.com/flow/getting-started), vissa [uttryck](../logic-apps/logic-apps-workflow-definition-language.md#expressions) hämta sina värden från runtime-åtgärder som inte kanske finns ännu när arbetsflödet börjar köras. Du kan använda för att använda dessa värden eller bearbeta värdena i dessa uttryck, *functions* tillhandahålls av den [Definitionsspråk för arbetsflödet](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Den här referenssida gäller både Azure Logic Apps och Microsoft Flow, men finns i dokumentationen för Azure Logic Apps. Även om den här sidan specifikt refererar till logikappar, fungerar dessa funktioner för både flöden och logic apps. Läs mer om funktioner och uttryck i Microsoft Flow, [använda uttryck i villkor](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Du kan till exempel beräkna värden genom att använda matematiska funktioner som den [Add ()-funktionen](../logic-apps/workflow-definition-language-functions-reference.md#add), när du vill summan från heltal eller flyttal. Här följer några andra exempel på uppgifter som du kan utföra med functions:

| Aktivitet | Funktionens syntax | Resultat |
| ---- | --------------- | ------ |
| Returnera en sträng i gemener format. | toLower ('<*text*> ”) <p>Till exempel: toLower('Hello') | "hello" |
| Returnera en globalt unik identifierare (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Funktionerna [baserat på deras allmänna](#ordered-by-purpose), granska följande tabeller. Eller, detaljerad information om varje funktion finns i den [lista i bokstavsordning](#alphabetical-list).

> [!NOTE]
> Ett frågetecken (?) som visas när en parameter innebär att parametern är valfri i syntaxen för parameterdefinitioner.
> Se exempelvis [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funktioner i uttryck

För att visa hur du använder en funktion i ett uttryck, det här exemplet visar hur du kan hämta värdet från den `customerName` parametern och tilldela värdet i `accountName` egenskapen med hjälp av den [parameters()](#parameters) funktion i ett uttryck:

```json
"accountName": "@parameters('customerName')"
```

Följande är några andra allmänna metoder som du kan använda funktioner i uttryck:

| Aktivitet | Funktionens syntax i ett uttryck |
| ---- | -------------------------------- |
| Utföra arbete till ett objekt genom att skicka objekt till en funktion. | "\@<*functionName*>(<*item*>)" |
| 1. Hämta den *parameterName*'s värde med hjälp av den kapslade `parameters()` funktion. </br>2. Utföra arbete med resultatet genom att skicka detta värde till *functionName*. | ”\@<*functionName*> (parametrar (” <*parameterName*> ”))” |
| 1. Hämta resultatet från den kapslade inre funktionen *functionName*. </br>2. Skicka resultatet till funktionen yttre *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. Få resultat från *functionName*. </br>2. Med hänsyn till att resultatet är ett objekt med egenskapen *propertyName*, hämta egenskapens värde. | ”\@<*functionName*>(<*item*>). <*propertyName*>” |
|||

Till exempel den `concat()` funktionen kan ta minst två strängvärden som parametrar. Den här funktionen kombinerar dessa strängar till en sträng.
Du kan antingen skicka i stränglitteraler, till exempel ”Sophia” och ”Owen” så att du får en kombinerad sträng, ”SophiaOwen”:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Eller så kan du få strängvärden från parametrar. Det här exemplet används den `parameters()` funktion i varje `concat()` parametern och `firstName` och `lastName` parametrar. Du sedan skicka resulterande strängar till den `concat()` så att du får en kombinerad sträng, till exempel ”SophiaOwen”:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

I båda fallen båda exemplen tilldela resultat som ska den `customerName` egenskapen.

Här är de tillgängliga funktionerna sorterade efter deras generell användning, eller så kan du bläddra funktioner baserat på [alfabetisk ordning](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Strängfunktioner

Om du vill arbeta med strängar, du kan använda dessa strängfunktioner och även vissa [samling funktioner](#collection-functions).
Strängfunktioner fungerar endast för strängar.

| Strängfunktion | Aktivitet |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinera två eller flera strängar och returnera den kombinerade strängen. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Kontrollera om en sträng som slutar med den angivna delsträngen. |
| [GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Returnera startpositionen för en delsträng. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Returnera startpositionen för den sista förekomsten av en delsträng. |
| [Ersätt](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersätta en understräng med den angivna strängen och returnerar den uppdaterade strängen. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, från en större sträng baserat på en angiven avgränsare tecken i den ursprungliga strängen. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontrollera om en sträng som börjar med en viss delsträng. |
| [delsträngen](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Returnera tecken från en sträng, med början från den angivna positionen. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Returnera en sträng i gemener format. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Returnera en sträng i versaler format. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Ta bort inledande och avslutande blanksteg från en sträng och returnera den uppdaterade strängen. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Samling funktioner

Du kan använda dessa funktioner för samlingen om du vill arbeta med samlingar, vanligtvis matriser, strängar och ibland ordlistor.

| Funktionen för samlingen | Aktivitet |
| ------------------- | ---- |
| [innehåller](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontrollera om en samling har ett specifikt objekt. |
| [tom](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Kontrollera om en samling är tom. |
| [första](../logic-apps/workflow-definition-language-functions-reference.md#first) | Returnera det första objektet från en samling. |
| [skärningspunkten](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Returnerar en samling som har *endast* vanliga objekt mellan de angivna samlingarna. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | Returnera det aktuella objektet i matrisen under åtgärdens aktuella iteration när inuti en upprepningsåtgärd över en matris. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Returnerar en sträng som har *alla* objekt från en matris, avgränsade med det angivna tecknet. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Returnera det sista objektet från en samling. |
| [Längd](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller en matris. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Ta bort objekt från början av en samling och returnerar *alla andra* objekt. |
| [ta](../logic-apps/workflow-definition-language-functions-reference.md#take) | Returnera objekt från början av en samling. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Returnerar en samling som har *alla* objekt från de angivna samlingarna. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logisk jämförelsefunktioner

Du kan använda dessa logiska jämförelse av funktioner för att arbeta med villkor, jämför värden och uttryck resultat eller utvärdera olika typer av logik.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Logisk Jämförelsefunktion | Aktivitet |
| --------------------------- | ---- |
| [och](../logic-apps/workflow-definition-language-functions-reference.md#and) | Kontrollera om alla uttryck utvärderas som true. |
| [är lika med](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Kontrollera om båda värdena är likvärdiga. |
| [större](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Kontrollera om det första värdet är större än det andra värdet. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Kontrollera om det första värdet är större än eller lika med det andra värdet. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Kontrollera om ett uttryck är SANT eller FALSKT. Baserat på resultatet kan returnera ett angivet värde. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Kontrollera om det första värdet är mindre än det andra värdet. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Kontrollera om det första värdet är mindre än eller lika med det andra värdet. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Kontrollera om ett uttryck är FALSKT. |
| [eller](../logic-apps/workflow-definition-language-functions-reference.md#or) | Kontrollera om minst en uttrycket är sant. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverteringsfunktioner

Om du vill ändra ett värde svarstyp eller, kan du använda dessa konverteringsfunktioner.
Du kan till exempel ändra ett värde från ett booleskt värde till ett heltal.
Läs mer om hur Logikappar hanterar innehållstyper under konverteringen, [hantera innehållstyper](../logic-apps/logic-apps-content-type.md).
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Konverteringsfunktion | Aktivitet |
| ------------------- | ---- |
| [Matris](../logic-apps/workflow-definition-language-functions-reference.md#array) | Returnera en matris från en enda som angetts som indata. Flera inmatningar Se [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Returnera den base64-kodad versionen efter en sträng. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Returnera den binära versionen för en base64-kodad sträng. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Returnera sträng för en base64-kodad sträng. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Returnera den binära versionen för ett indatavärde. |
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
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Returnera TYPVÄRDE för JavaScript Object Notation (JSON) eller objekt för en sträng eller XML. |
| [sträng](../logic-apps/workflow-definition-language-functions-reference.md#string) | Returnera sträng för ett indatavärde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Returnera den URI-kodad versionen för ett indatavärde genom att ersätta URL-osäkra tecknen med escape-tecken. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Returnera sträng för en URI-kodad sträng. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Returnera XML-versionen för en sträng. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematikfunktioner

Du kan använda dessa matematiska funktioner för att fungera med heltal och flyttal.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Matematiska funktion | Aktivitet |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Returnera resultatet från divisionen av två tal. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Returnera det högsta värdet från en uppsättning siffror eller en matris. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Returnera det lägsta värdet från en uppsättning siffror eller en matris. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Returnera återstoden av att dividera två tal. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Returnera produkten från multiplicera två tal. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Returnera ett slumpvist heltal från ett visst intervall. |
| [Adressintervall](../logic-apps/workflow-definition-language-functions-reference.md#range) | Returnera en heltalsmatris som startar från en angiven heltal. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Returnera resultatet från att subtrahera den andra siffran från den första siffran. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- och tidsfunktioner

Du kan använda dessa funktioner för datum och tid om du vill arbeta med datum och tid.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funktionen för datum och tid | Aktivitet |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Lägga till ett antal dagar till en tidsstämpel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Lägga till ett antal timmar till en tidsstämpel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Lägga till ett antal minuter till en tidsstämpel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Lägga till ett antal sekunder till en tidsstämpel. |
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

## <a name="workflow-functions"></a>Arbetsflödesfunktioner

Med hjälp av dessa arbetsflödesfunktioner kan du:

* Få information om en arbetsflödesinstans vid körning.
* Arbeta med de indata som används för kontrollanten logic apps eller flöden.
* Referera till utdatan från utlösare och åtgärder.

Du kan till exempel refererar till utdatan från en åtgärd och använda dessa data i en senare åtgärd.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Arbetsflödesfunktion | Aktivitet |
| ----------------- | ---- |
| [Åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action) | Returnera den aktuella åtgärden utdata vid körning, eller värden från andra JSON-namn och värde-par. Se även [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Returnerar en åtgärd `body` utdata vid körning. Se även [brödtext](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Returnera en åtgärd utdata vid körning. Se [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Returnera en åtgärd utdata vid körning, eller värden från andra JSON-namn och värde-par. Se även [åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Returnerar en åtgärd `body` utdata vid körning. Se även [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Skapa en matris med värden som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* åtgärd utdata. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Returnera ett enstaka värde som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulärkodade utdata*. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | Returnera det aktuella objektet i matrisen under åtgärdens aktuella iteration när inuti en upprepningsåtgärd över en matris. |
| [objekt](../logic-apps/workflow-definition-language-functions-reference.md#items) | När du är i ett för varje eller gör tills loop, returnerar det aktuella objektet från den angivna loopen.|
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Returnera ”Motringnings-URL” som anropar en utlösare eller åtgärder. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Returnera brödtexten för en viss del i utdata för en åtgärd som har flera delar. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Returnera värdet för en parameter som beskrivs i din arbetsflödesdefinitionen. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Returnera en utlösare utdata vid körning, eller från andra JSON-namn och värde-par. Se även [triggerOutputs](#triggerOutputs) och [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Returnerar en utlösare `body` utdata vid körning. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Returnera ett enstaka värde som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösa utdata. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Returnera brödtexten för en viss del i en utlösarens multipart-utdata. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Skapa en matris vars värden matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösa utdata. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Returnera en utlösare utdata vid körning, eller värden från andra JSON-namn och värde-par. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variabler](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Returnera värdet för en specifik variabel. |
| [Arbetsflöde](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Returnera all information om själva arbetsflödet under körningen. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-parsningsfunktioner

Om du vill arbeta med uniform resource Identifier (URI: er) och få olika värden för dessa URI: er, kan du använda dessa URI-parsningsfunktioner.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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

## <a name="manipulation-functions-json--xml"></a>Funktioner för strängmanipulering: JSON & XML

Om du vill arbeta med JSON-objekt och XML-noder måste använda du dessa funktioner för datahantering.
Fullständig referens om varje funktion finns i [lista i bokstavsordning](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Datahanteringsfunktion | Aktivitet |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Lägg till en egenskap och dess värde eller namn / värde-par, ett JSON-objekt och returnera det uppdaterade objektet. |
| [Slå samman](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Returnera det första icke-null-värdet från en eller flera parametrar. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Ta bort en egenskap från ett JSON-objekt och returnera det uppdaterade objektet. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ange värdet för egenskapen för ett JSON-objekt och returnera det uppdaterade objektet. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Kontrollera XML för noder eller värden som matchar ett XPath (XML Path Language)-uttryck och returnerar matchande noder eller värden. |
|||

<a name="alphabetical-list"></a>
<a name="action"></a>

### <a name="action"></a>åtgärd

Returnera den *aktuella* åtgärd jobbs effekt vid körning, eller värden från andra JSON namn och värde par som du kan tilldela till ett uttryck.
Den här funktionen refererar till åtgärdsobjektet hela som standard, men du kan du ange en egenskap vars värde du vill.
Se även [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Du kan använda den `action()` funktionen endast på dessa platser:

* Den `unsubscribe` -egenskapen för en webhook-åtgärd så att du kan komma åt resultatet från ursprungligt `subscribe` begäran
* Den `trackedProperties` -egenskapen för en åtgärd
* Den `do-until` loopa villkoret för en åtgärd

```
action()
action().outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Nej | String | Namn på åtgärd-objektets egenskapen vars värde du vill: **namn**, **startTime**, **endTime**, **indata**,  **matar ut**, **status**, **kod**, **trackingId**, och **clientTrackingId**. I Azure-portalen hittar du de här egenskaperna genom att granska information om en specifik körningshistoriken. Mer information finns i [REST-API – kör arbetsflödesåtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-output*> | String | Utdata från den aktuella åtgärden eller egenskapen |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Returnerar en åtgärd `body` utdata vid körning.
Snabbformat för `actions('<actionName>').outputs.body`.
Se [body()](#body) och [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Namn för åtgärden `body` utdata som du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | Den `body` utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar den `body` utdata från åtgärden Twitter `Get user`:

```
actionBody('Get_user')
```

Och returnerar resultatet:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Returnera en åtgärd utdata vid körning.
Snabbformat för `actions('<actionName>').outputs`.
Se [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Namn för åtgärden utdatafiler som du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*output*> | String | Utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar utdata från Twitter-åtgärden `Get user`:

```
actionOutputs('Get_user')
```

Och returnerar resultatet:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>Åtgärder

Returnera en åtgärd utdata vid körning, eller värden från andra JSON namn och värde par som du kan tilldela till ett uttryck. Som standard funktionen refererar till åtgärdsobjektet hela, men du kan du ange en egenskap vars värde som du vill.
Snabbformat versioner, se [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), och [body()](#body).
Den aktuella åtgärden Se [action()](#action).

> [!NOTE]
> Tidigare kunde du använda den `actions()` funktion eller `conditions` element när du anger att en åtgärd kördes baserat på utdata från en annan åtgärd. Men för att deklarera uttryckligen beroenden mellan åtgärder, måste du nu använda den beroende åtgärden `runAfter` egenskapen.
> Mer information om den `runAfter` egenskap, finns i [fånga upp och hantera fel med egenskapen runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Namn för åtgärdsobjektet vars utdata du vill  |
| <*Egenskapen*> | Nej | String | Namn på åtgärd-objektets egenskapen vars värde du vill: **namn**, **startTime**, **endTime**, **indata**,  **matar ut**, **status**, **kod**, **trackingId**, och **clientTrackingId**. I Azure-portalen hittar du de här egenskaperna genom att granska information om en specifik körningshistoriken. Mer information finns i [REST-API – kör arbetsflödesåtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-output*> | String | Utdata från den angivna åtgärden eller egenskapen |
||||

*Exempel*

Det här exemplet hämtar den `status` egenskapsvärdet från Twitter-åtgärden `Get user` vid körning:

```
actions('Get_user').outputs.body.status
```

Och returnerar resultatet: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Returnera resultatet från att lägga till två tal.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Ja | Heltal, flyttal, eller både | Nummer att lägga till |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*result-sum*> | Heltal eller flyttal | Resultatet från att lägga till den angivna värden |
||||

*Exempel*

Det här exemplet lägger till den angivna värden:

```
add(1, 1.5)
```

Och returnerar resultatet: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Lägga till ett antal dagar till en tidsstämpel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*dagar*> | Ja | Integer | Positiv eller negativ antalet dagar att lägga till |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpeln plus det angivna antalet dagar  |
||||

*Exempel 1*

Det här exemplet lägger till 10 dagar till den angivna tidsstämpeln:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Och returnerar resultatet: `"2018-03-25T00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar resultatet: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Lägga till ett antal timmar till en tidsstämpel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*Timmar*> | Ja | Integer | Positiv eller negativ antalet timmar att lägga till |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpeln plus det angivna antalet timmar  |
||||

*Exempel 1*

Det här exemplet lägger till 10 timmar till den angivna tidsstämpeln:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Och returnerar resultatet: `"2018-03-15T10:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar resultatet: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Lägga till ett antal minuter till en tidsstämpel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*minuter*> | Ja | Integer | Positiv eller negativ antalet minuter att lägga till |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpeln plus det angivna antalet minuter |
||||

*Exempel 1*

Det här exemplet lägger till 10 minuter till den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Och returnerar resultatet: `"2018-03-15T00:20:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem minuter från den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Och returnerar resultatet: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Lägg till en egenskap och dess värde eller namn / värde-par, ett JSON-objekt och returnera det uppdaterade objektet. Om objektet finns redan vid körning, genereras ett fel.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Objekt | JSON-objekt där du vill lägga till en egenskap |
| <*Egenskapen*> | Ja | String | Namnet på egenskapen att lägga till |
| <*value*> | Ja | Alla | Värdet för egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Uppdaterade JSON-objekt med den angivna egenskapen |
||||

*Exempel*

Det här exemplet lägger till den `accountNumber` egenskap enligt den `customerProfile` objekt som konverteras till JSON med den [JSON()](#json) funktion.
Funktionen tilldelar ett värde som genereras av den [guid()](#guid) fungera, och returnerar det uppdaterade objektet:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Lägga till ett antal sekunder till en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*seconds*> | Ja | Integer | Positiv eller negativ antalet sekunder att lägga till |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpeln plus det angivna antalet sekunder  |
||||

*Exempel 1*

Det här exemplet lägger till 10 sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Och returnerar resultatet: `"2018-03-15T00:00:10.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem sekunder så att den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Och returnerar resultatet: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Lägga till ett antal tidsenheter till en tidsstämpel.
Se även [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter att lägga till |
| <*timeUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall*: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”månad”, ”år” |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpeln plus det angivna antalet tidsenheter  |
||||

*Exempel 1*

Det här exemplet lägger till en dag till den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Och returnerar resultatet: `"2018-01-02T00:00:00:0000000Z"`

*Exempel 2*

Det här exemplet lägger till en dag till den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet i valfritt ”D”-format: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>och

Kontrollera om alla uttryck utvärderas som true.
Returnerar värdet true när alla uttryck utvärderas som true och returnerar false när minst en uttrycket är FALSKT.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck1*>, <*uttryck2*>,... | Ja | Boolesk | Uttryck för att kontrollera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när alla uttryck utvärderas som true. Returnera false när minst en uttrycket är FALSKT. |
||||

*Exempel 1*

De här exemplen kontrollera om de angivna booleska värdena är uppfyllda:

```
and(true, true)
and(false, true)
and(false, false)
```

Och returnerar följande resultat:

* Det första exemplet: Båda uttrycken är uppfyllda, så returnerar `true`.
* Andra exempel: Ett uttryck är FALSKT, så returnerar `false`.
* Tredje exempel: Båda uttrycken är FALSKT, så returnerar `false`.

*Exempel 2*

De här exemplen kontrollera om de angivna uttrycken är uppfyllda:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Det första exemplet: Båda uttrycken är uppfyllda, så returnerar `true`.
* Andra exempel: Ett uttryck är FALSKT, så returnerar `false`.
* Tredje exempel: Båda uttrycken är FALSKT, så returnerar `false`.

<a name="array"></a>

### <a name="array"></a>matris

Returnera en matris från en enda som angetts som indata.
Flera inmatningar Se [createArray()](#createArray).

```
array('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Strängen för att skapa en matris |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*värdet*>] | Matris | En matris som innehåller den enda angivna indata |
||||

*Exempel*

Det här exemplet skapar en matris från strängen ”hello”:

```
array('hello')
```

Och returnerar resultatet: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>Base64

Returnera den base64-kodad versionen efter en sträng.

```
base64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Indatasträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | Base64-kodad versionen för Indatasträngen |
||||

*Exempel*

Det här exemplet konverterar strängen ”hello” till en base64-kodad sträng:

```
base64('hello')
```

Och returnerar resultatet: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Returnera den binära versionen för en base64-kodad sträng.

```
base64ToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Base64-kodad sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär för base64-sträng*> | String | Den binära versionen för base64-kodad sträng |
||||

*Exempel*

Det här exemplet konverterar den ”aGVsbG8 =” base64-kodad sträng till en binär sträng:

```
base64ToBinary('aGVsbG8=')
```

Och returnerar resultatet:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Returnera sträng för en base64-kodad sträng effektivt avkodning base64-sträng.
Använd den här funktionen snarare än [decodeBase64()](#decodeBase64).
Även om båda funktionerna fungerar på samma sätt `base64ToString()` är att föredra.

```
base64ToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Base64-kodade strängen att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Strängversion för en base64-kodad sträng |
||||

*Exempel*

Det här exemplet konverterar den ”aGVsbG8 =” base64-kodad sträng till bara en sträng:

```
base64ToString('aGVsbG8=')
```

Och returnerar resultatet: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binär

Returnera den binära versionen för en sträng.

```
binary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | Den binära versionen för den angivna strängen |
||||

*Exempel*

Det här exemplet konverterar strängen ”hello” till en binär sträng:

```
binary('hello')
```

Och returnerar resultatet:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>brödtext

Returnerar en åtgärd `body` utdata vid körning.
Snabbformat för `actions('<actionName>').outputs.body`.
Se [actionBody()](#actionBody) och [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Namn för åtgärden `body` utdata som du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | Den `body` utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar den `body` utdata från den `Get user` Twitter-åtgärd:

```
body('Get_user')
```

Och returnerar resultatet:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Returnera booleskt efter ett värde.

```
bool(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Alla | Värdet att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Den booleska versionen för det angivna värdet |
||||

*Exempel*

De här exemplen konvertera de angivna värdena till booleska värden:

```
bool(1)
bool(0)
```

Och returnerar följande resultat:

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Slå samman

Returnera det första icke-null-värdet från en eller flera parametrar.
Tomma strängar, tomma matriser och tomma objekt är inte null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Ja | Eventuella kan blanda typer | Ett eller flera objekt att söka efter null |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Alla | Den första posten eller -värde som inte är null. Den här funktionen returnerar null om alla parametrarna är null. |
||||

*Exempel*

De här exemplen returnerar det första icke-null-värdet från den angivna värden eller nullvärden när alla värden är null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Och returnerar följande resultat:

* Det första exemplet: `true`
* Andra exempel: `"hello"`
* Tredje exempel: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Kombinera två eller flera strängar och returnera den kombinerade strängen.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text1*>, <*text2*>,... | Ja | String | Minst två strängar att kombinera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | Den sträng som har skapats från kombinerade inkommande strängar |
||||

*Exempel*

Det här exemplet kombinerar strängar ”Hello” och ”World”:

```
concat('Hello', 'World')
```

Och returnerar resultatet: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>innehåller

Kontrollera om en samling har ett specifikt objekt.
Returnerar värdet true när hitta objektet, eller returnera false när hittades inte.
Den här funktionen är skiftlägeskänslig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Den här funktionen fungerar mer specifikt om den här typen av samling:

* En *sträng* att hitta en *delsträng*
* En *matris* att hitta en *värde*
* En *ordlista* att hitta en *nyckel*

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matrisen eller ordlista | Samlingen som ska kontrolleras |
| <*value*> | Ja | Sträng, matrisen eller ordboken, respektive | Objektet du vill hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera sant när objektet har hittats. Returnera false när hittades inte. |
||||

*Exempel 1*

Det här exemplet kontrollerar strängen ”hello world” för delsträngen ”world” och returnerar true:

```
contains('hello world', 'world')
```

*Exempel 2*

Det här exemplet kontrollerar strängen ”hello world” för delsträngen ”universe” och returnerar false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konvertera en tidsstämpel från Universal Coordinated för tid (UTC) till måltidszon.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*destinationTimeZone*> | Ja | String | Namnet på måltidszon. Mer information finns i [tidszon-ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Tidsstämpeln konverteras till måltidszon |
||||

*Exempel 1*

Det här exemplet konverterar en tidsstämpel till den angivna tidszonen:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Och returnerar resultatet: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

Det här exemplet konverterar en tidsstämpel i angivna tidszon och format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konvertera en tidsstämpel från källtidszonen tidszon mål.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | String | Namn för källtidszonen. Mer information finns i [tidszon-ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*destinationTimeZone*> | Ja | String | Namnet på måltidszon. Mer information finns i [tidszon-ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Tidsstämpeln konverteras till måltidszon |
||||

*Exempel 1*

Det här exemplet konverterar källtidszonen tidszon mål:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Och returnerar resultatet: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

Det här exemplet konverterar en tidszon till angivna tidszon och format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konvertera en tidsstämpel från källtidszonen till Coordinated för Universal Time (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | String | Namn för källtidszonen. Mer information finns i [tidszon-ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Tidsstämpel som konverteras till UTC |
||||

*Exempel 1*

Det här exemplet konverterar en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Och returnerar resultatet: `"2018-01-01T08:00:00.0000000Z"`

*Exempel 2*

Det här exemplet konverterar en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Returnera en matris från flera inmatningar.
Matriser med en inkommande, se [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt1*>, <*object2*>,... | Ja | Sådana finns, men inte blandat | Minst två objekt att skapa matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*Objekt1*>, <*object2*>,...] | Matris | Matrisen som skapats från alla inkommande objekt |
||||

*Exempel*

Det här exemplet skapar en matris från dessa indata:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Och returnerar resultatet: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Returnera en data uniform resource identifier (URI) efter en sträng.

```
dataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Data-URI för Indatasträngen |
||||

*Exempel*

Det här exemplet skapar ett data-URI för strängen ”hello”:

```
dataUri('hello')
```

Och returnerar resultatet: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Returnera den binära versionen för en data uniform resource identifier (URI).
Använd den här funktionen snarare än [decodeDataUri()](#decodeDataUri).
Även om båda funktionerna fungerar på samma sätt `decodeDataUri()` är att föredra.

```
dataUriToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Data-URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Den binära versionen för data-URI |
||||

*Exempel*

Det här exemplet skapar en binära versionen för den här data-URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Returnera sträng för en data uniform resource identifier (URI).

```
dataUriToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Data-URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | Strängversion för data-URI |
||||

*Exempel*

Det här exemplet skapar en sträng för den här data-URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dag i månaden

Returnera dagen i månaden från en tidsstämpel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i månaden*> | Integer | Dagen i månaden från den angivna tidsstämpeln |
||||

*Exempel*

Det här exemplet returnerar numret för dagen i månaden från den här tidsstämpeln:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Och returnerar resultatet: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Returnera dagen i veckan från en tidsstämpel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i vecka*> | Integer | Dag i veckan från den angivna tidsstämpeln där är söndag 0, måndag är 1 och så vidare |
||||

*Exempel*

Det här exemplet returnerar numret för dagen i veckan från den här tidsstämpeln:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Och returnerar resultatet: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Returnera dagen på året från en tidsstämpel.

```
dayOfYear('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Dag på året från den angivna tidsstämpeln |
||||

*Exempel*

Det här exemplet returnerar antalet dagen på året från den här tidsstämpeln:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Och returnerar resultatet: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Returnera sträng för en base64-kodad sträng effektivt avkodning base64-sträng.
Överväg att använda [base64ToString()](#base64ToString) snarare än `decodeBase64()`.
Även om båda funktionerna fungerar på samma sätt `base64ToString()` är att föredra.

```
decodeBase64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Base64-kodade strängen att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Strängversion för en base64-kodad sträng |
||||

*Exempel*

Det här exemplet skapar en sträng för en base64-kodad sträng:

```
decodeBase64('aGVsbG8=')
```

Och returnerar resultatet: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Returnera den binära versionen för en data uniform resource identifier (URI).
Överväg att använda [dataUriToBinary()](#dataUriToBinary), snarare än `decodeDataUri()`.
Även om båda funktionerna fungerar på samma sätt `dataUriToBinary()` är att föredra.

```
decodeDataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Data-URI-strängen att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Den binära versionen för en URI-sträng |
||||

*Exempel*

Det här exemplet returnerar den binära versionen för den här data-URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Returnerar en sträng som ersätter escape-tecken med avkodade versioner.

```
decodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Strängen med escape-tecken att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Den uppdaterade strängen med avkodade escape-tecken |
||||

*Exempel*

Det här exemplet ersätter escape-tecken i den här strängen med avkodade versioner:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Returnera heltal resultatet från divisionen av två tal.
Om du vill få resten resultat, se [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*täljaren*> | Ja | Heltal eller flyttal | Talet att dela med de *divisorn* |
| <*divisor*> | Ja | Heltal eller flyttal | Talet som dividerar den *täljaren*, men kan inte vara 0 |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | Heltal resultatet från divisionen av den första siffran av den andra siffran |
||||

*Exempel*

Båda exemplen dela upp den första siffran av den andra siffran:

```
div(10, 5)
div(11, 5)
```

Och returnera resultatet: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en uniform resource identifier (URI)-kodad version för en sträng genom att ersätta URL-osäkra tecknen med escape-tecken.
Överväg att använda [uriComponent()](#uriComponent), snarare än `encodeUriComponent()`.
Även om båda funktionerna fungerar på samma sätt `uriComponent()` är att föredra.

```
encodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Sträng som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

Det här exemplet skapar en URI-kodad version för den här strängen:

```
encodeUriComponent('https://contoso.com')
```

Och returnerar resultatet: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>tom

Kontrollera om en samling är tom.
Returnerar värdet true när samlingen är tom eller returnera värdet false när det är inte tom.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matris eller ett objekt | Samlingen som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när samlingen är tom. Returnera värdet false när det är inte tom. |
||||

*Exempel*

De här exemplen kontrollera om de angivna samlingarna är tomt:

```
empty('')
empty('abc')
```

Och returnerar följande resultat:

* Det första exemplet: Skickar en tom sträng så returneras `true`.
* Andra exempel: Skickar strängen ”abc”, så returnerar funktionen `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Kontrollera om en sträng som slutar med en viss delsträng.
Returnerar värdet true när delsträngen hittas, eller returnera false när hittades inte.
Den här funktionen är inte skiftlägeskänsligt.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Sträng att söka |
| <*searchText*> | Ja | String | Den sista delsträngen att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT  | Boolesk | Returnera värdet true när den sista delsträngen hittas. Returnera false när hittades inte. |
||||

*Exempel 1*

Det här exemplet kontrollerar om strängen ”hello world” slutar med ”world”-sträng:

```
endsWith('hello world', 'world')
```

Och returnerar resultatet: `true`

*Exempel 2*

Det här exemplet kontrollerar om strängen ”hello world” slutar med ”universe”-sträng:

```
endsWith('hello world', 'universe')
```

Och returnerar resultatet: `false`

<a name="equals"></a>

### <a name="equals"></a>lika med

Kontrollera om värden, uttryck eller objekt är likvärdiga.
Returnera sant när båda är likvärdiga eller returnerar false när de inte är likvärdiga.

```
equals('<object1>', '<object2>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt1*>, <*object2*> | Ja | Olika | I värden, uttryck eller objekt att jämföra |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera sant när båda är likvärdiga. Returnera värdet false när det är inte likvärdig. |
||||

*Exempel*

De här exemplen kontrollera om de angivna indata är likvärdiga.

```
equals(true, 1)
equals('abc', 'abcd')
```

Och returnerar följande resultat:

* Det första exemplet: Båda värdena är likvärdiga, så returnerar funktionen `true`.
* Andra exempel: Båda värdena inte är likvärdiga, returnerar funktionen `false`.

<a name="first"></a>

### <a name="first"></a>första

Returnera den första posten från en sträng eller en matris.

```
first('<collection>')
first([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen var du hittar det första objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Alla | Det första objektet i samlingen |
||||

*Exempel*

De här exemplen hittar det första objektet i de här samlingarna:

```
first('hello')
first(createArray(0, 1, 2))
```

Och returnera följande resultat:

* Det första exemplet: `"h"`
* Andra exempel: `0`

<a name="float"></a>

### <a name="float"></a>flyt

Konvertera en strängversion för ett Flyttalsnummer till en faktiska Flyttalsnummer.
Du kan använda den här funktionen endast när skicka anpassade parametrar till en app, till exempel en logikapp eller ett flöde.

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Den sträng som har ett giltigt Flyttalsnummer att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*float-value*> | Flyttal | Flyttalsnummer för den angivna strängen |
||||

*Exempel*

Det här exemplet skapar en strängversion för den här flyttal:

```
float('10.333')
```

Och returnerar resultatet: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Returnera en tidsstämpel i formatet som anges.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | Uppdaterade tidsstämpeln i angivet format |
||||

*Exempel*

Det här exemplet konverterar en tidsstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar resultatet: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Returnerar en matris med värden som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulärkodade* utdata.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Åtgärden vars utdata har nyckelvärdet som du vill |
| <*Nyckel*> | Ja | String | Namn för den nyckel vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en matris av nyckeln ”ämne” värdet i den angivna åtgärden formulärdata eller formulärkodade utdata:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Och returnerar ämnestexten i en matris, till exempel: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Returnera ett enstaka värde som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulärkodade* utdata.
Om funktionen hittar fler än en matchning, genereras ett fel.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Åtgärden vars utdata har nyckelvärdet som du vill |
| <*Nyckel*> | Ja | String | Namn för den nyckel vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Värdet i den angivna nyckeln  |
||||

*Exempel*

Det här exemplet skapar en sträng från nyckeln ”ämne” värde i den angivna åtgärden formulärdata eller formulärkodade utdata:

```
formDataValue('Send_an_email', 'Subject')
```

Och returnerar ämnestexten som en sträng, till exempel: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter att subtrahera |
| <*timeUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall*: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”månad”, ”år” |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Den aktuella tidsstämpeln plus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är ”2018-03-01T00:00:00.0000000Z”.
Det här exemplet lägger till fem dagar tidsstämpeln:

```
getFutureTime(5, 'Day')
```

Och returnerar resultatet: `"2018-03-06T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är ”2018-03-01T00:00:00.0000000Z”.
Det här exemplet lägger till fem dagar och konverterar resultatet till ”D”-format:

```
getFutureTime(5, 'Day', 'D')
```

Och returnerar resultatet: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter att subtrahera |
| <*timeUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall*: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”månad”, ”år” |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Den aktuella tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är ”2018-02-01T00:00:00.0000000Z”.
Det här exemplet subtraherar fem dagar från den tidsstämpeln:

```
getPastTime(5, 'Day')
```

Och returnerar resultatet: `"2018-01-27T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är ”2018-02-01T00:00:00.0000000Z”.
Det här exemplet subtraherar fem dagar och konverterar resultatet till ”D”-format:

```
getPastTime(5, 'Day', 'D')
```

Och returnerar resultatet: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>större

Kontrollera om det första värdet är större än det andra värdet.
Returnerar värdet true när det första värdet är mer eller returnerar false när mindre.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Heltal, flyttal och sträng | Det första värdet för att kontrollera om det är större än det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal och sträng, respektive | Värdet för jämförelse |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när det första värdet är större än det andra värdet. Returnera false när det första värdet är lika med eller mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollera om det första värdet är större än det andra värdet:

```
greater(10, 5)
greater('apple', 'banana')
```

Och returnera följande resultat:

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Kontrollera om det första värdet är större än eller lika med det andra värdet.
Returnerar värdet true när det första värdet är större eller lika med, eller returnera false när det första värdet är mindre.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Heltal, flyttal och sträng | Det första värdet för att kontrollera om det är större än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal och sträng, respektive | Värdet för jämförelse |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när det första värdet är större än eller lika med det andra värdet. Returnera värdet false när det första värdet är mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollera om det första värdet är större eller lika med än det andra värdet:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Och returnera följande resultat:

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Generera en globalt unik identifierare (GUID) som en sträng, till exempel ”c2ecc88d-88c8-4096-912c-d6f2e2b138ce”:

```
guid()
```

Du kan också ange ett annat format för GUID än standardformat, ”D”, vilket är 32 siffror avgränsade med bindestreck.

```
guid('<format>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Nej | String | En enda [formatera specificerare](https://msdn.microsoft.com/library/97af8hh4) för GUID som returnerades. Formatet är ”D” som standard, men du kan använda ”N”, ”D”, ”B”, ”P” eller ”X”. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Ett slumpmässigt genererat GUID |
||||

*Exempel*

Det här exemplet genererar samma GUID, men som 32 siffror, avgränsade med bindestreck och omgivna av parenteser:

```
guid('P')
```

Och returnerar resultatet: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Kontrollera om ett uttryck är SANT eller FALSKT.
Baserat på resultatet kan returnera ett angivet värde.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck*> | Ja | Boolesk | Uttryck för att kontrollera |
| <*valueIfTrue*> | Ja | Alla | Värdet som returneras om uttrycket är SANT |
| <*valueIfFalse*> | Ja | Alla | Värdet som returneras om uttrycket är FALSKT |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Alla | Det angivna värdet som returnerar baserat på om uttrycket är SANT eller FALSKT |
||||

*Exempel*

Det här exemplet returnerar `"yes"` eftersom det angivna uttrycket returnerar true.
I annat fall i exempel returneras `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Returnera startpositionen eller indexvärde efter en delsträng.
Den här funktionen är inte skiftlägeskänsligt och index som börjar med 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Den sträng som har delsträngen att hitta |
| <*searchText*> | Ja | String | Delsträngen som ska hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Positionen eller index startvärdet för den angivna delsträngen. <p>Om strängen hittas, returnera talet -1. |
||||

*Exempel*

Det här exemplet hittar index startvärde för ”world”-delsträngen i strängen ”hello world”:

```
indexOf('hello world', 'world')
```

Och returnerar resultatet: `6`

<a name="int"></a>

### <a name="int"></a>int

Returnera heltal för en sträng.

```
int('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Heltal-version för den angivna strängen |
||||

*Exempel*

Det här exemplet skapar en heltal version efter strängen ”10”:

```
int('10')
```

Och returnerar resultatet: `10`

<a name="item"></a>

### <a name="item"></a>Objekt

När den används inuti en upprepningsåtgärd över en matris, returnera det aktuella objektet i matrisen under åtgärdens aktuella iteration.
Du kan också få värdena från egenskaperna för det objektet.

```
item()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Alla | Det aktuella objektet i matrisen för åtgärdens aktuella iteration |
||||

*Exempel*

Det här exemplet hämtar den `body` elementet från det aktuella meddelandet för ”Send_an_email”-åtgärd inuti en för varje slinga aktuella iteration:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>objekt

Returnera det aktuella objektet från varje cykel i en för varje loop.
Använd den här funktionen i för-each-loop.

```
items('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Ja | String | Namnet för varje slinga |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*item*> | Alla | Objektet från den aktuella cykeln i den angivna för varje slingan |
||||

*Exempel*

Det här exemplet hämtar det aktuella objektet från den angivna för varje slingan:

```
items('myForEachLoopName')
```

<a name="json"></a>

### <a name="json"></a>JSON

Returnera TYPVÄRDE för JavaScript Object Notation (JSON) eller objekt för en sträng eller XML.

```
json('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Sträng eller XML | Den sträng eller XML som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Inbyggda JSON-typ eller objekt | Värde av ursprunglig typ. JSON eller -objekt för den angivna sträng eller XML. Om strängen är null returnerar funktionen ett tomt-objekt. |
||||

*Exempel 1*

Det här exemplet konverterar den här strängen till JSON-värde:

```
json('[1, 2, 3]')
```

Och returnerar resultatet: `[1, 2, 3]`

*Exempel 2*

Det här exemplet konverterar den här strängen till JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Och returnerar resultatet:

```
{
  "fullName": "Sophia Owen"
}
```

*Exempel 3*

Det här exemplet konverterar den här XML till JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Och returnerar resultatet:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>skärningspunkten

Returnerar en samling som har *endast* vanliga objekt mellan de angivna samlingarna.
Om du vill visas i resultatet, måste ett objekt visas i alla samlingar som skickas till den här funktionen.
Om ett eller flera objekt har samma namn, visas det sista objektet med det namnet i resultatet.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*samling2*>,... | Ja | Matris eller objekt, men inte båda | Samlingar som du vill från *endast* vanliga objekt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*common-items*> | Matris eller ett objekt, respektive | En samling som har endast objekt som är gemensamma mellan de angivna samlingarna |
||||

*Exempel*

Det här exemplet hittar vanliga objekt över dessa matriser:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Och returnerar en matris med *endast* dessa objekt: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Returnerar en sträng som har alla objekt från en matris och har varje tecken avgränsade med ett *avgränsare*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Matris som innehåller de element att ansluta till |
| <*delimiter*> | Ja | String | Avgränsaren mellan varje tecken i den resulterande strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*char1*><*avgränsare*><*char2*><*avgränsare*>... | String | Den resulterande strängen som skapats från alla objekt i den angivna matrisen |
||||

*Exempel*

Det här exemplet skapar en sträng från alla objekt i den här matrisen med det angivna tecknet som avgränsare:

```
join(createArray('a', 'b', 'c'), '.')
```

Och returnerar resultatet: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>senaste

Returnera det sista objektet från en samling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen var du hittar det sista objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Sträng eller matris, respektive | Det sista objektet i samlingen |
||||

*Exempel*

De här exemplen hitta det sista objektet i de här samlingarna:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Och returnerar följande resultat:

* Det första exemplet: `"d"`
* Andra exempel: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Returnera startpositionen eller indexvärde för den sista förekomsten av en delsträng.
Den här funktionen är inte skiftlägeskänsligt och index som börjar med 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Den sträng som har delsträngen att hitta |
| <*searchText*> | Ja | String | Delsträngen som ska hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | Positionen eller index startvärdet för den sista förekomsten av den angivna delsträngen. <p>Om strängen hittas, returnera talet -1. |
||||

*Exempel*

Det här exemplet hittar index startvärde för den sista förekomsten av ”world”-delsträngen i strängen ”hello world”:

```
lastIndexOf('hello world', 'world')
```

Och returnerar resultatet: `6`

<a name="length"></a>

### <a name="length"></a>Längd

Returnera antalet objekt i en samling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | I samlingen med de objekt som ska räknas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Antalet objekt i samlingen |
||||

*Exempel*

De här exemplen räkna antalet objekt i de här samlingarna:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Och returnera resultatet: `4`

<a name="less"></a>

### <a name="less"></a>mindre

Kontrollera om det första värdet är mindre än det andra värdet.
Returnerar värdet true när det första värdet är mindre eller returneras false när det första värdet är mer.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Heltal, flyttal och sträng | Det första värdet för att kontrollera om mindre än det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal och sträng, respektive | Jämförelse av-objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera sant när det första värdet är mindre än det andra värdet. Returnera false när det första värdet är lika med eller större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollera om det första värdet är mindre än det andra värdet.

```
less(5, 10)
less('banana', 'apple')
```

Och returnera följande resultat:

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Kontrollera om det första värdet är mindre än eller lika med det andra värdet.
Returnerar värdet true när det första värdet är mindre än eller lika med eller returneras false när det första värdet är mer.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Heltal, flyttal och sträng | Det första värdet att kontrollera om mindre än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal och sträng, respektive | Jämförelse av-objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT  | Boolesk | Returnera värdet true när det första värdet är mindre än eller lika med det andra värdet. Returnera false när det första värdet är större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollera om det första värdet är mindre eller lika med det andra värdet.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Och returnera följande resultat:

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Returnera ”Motringnings-URL” som anropar en utlösare eller åtgärder.
Den här funktionen fungerar bara med utlösare och åtgärder för den **HttpWebhook** och **ApiConnectionWebhook** connector typer, men inte den **manuell**,  **Upprepning**, **HTTP**, och **APIConnection** typer.

```
listCallbackUrl()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | Motringnings-URL för en utlösare eller åtgärd |
||||

*Exempel*

Det här exemplet visar ett exempel Motringnings-URL att den här funktionen kan returnera:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Returnera det högsta värdet från en lista eller en matris med tal som är inkluderande i båda ändar.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Number1*>, <*number2*>,... | Ja | Heltal, flyttal eller båda | Uppsättningen talen som du vill ha det högsta värdet |
| [<*number1*>, <*number2*>,...] | Ja | Matris - heltal, flyttal eller båda | Matris med tal som du vill det högsta värdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*max-value*> | Heltal eller flyttal | Det högsta värdet i den angivna matrisen eller taluppsättning |
||||

*Exempel*

De här exemplen hämta det högsta värdet från en uppsättning siffror och matrisen:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Och returnera resultatet: `3`

<a name="min"></a>

### <a name="min"></a>min.

Returnera det lägsta värdet från en uppsättning siffror eller en matris.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Number1*>, <*number2*>,... | Ja | Heltal, flyttal eller båda | Uppsättningen talen som du vill ha det lägsta värdet |
| [<*number1*>, <*number2*>,...] | Ja | Matris - heltal, flyttal eller båda | Matris med tal som du vill det lägsta värdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*min-value*> | Heltal eller flyttal | Det lägsta värdet i den angivna uppsättningen med siffror eller matrisen |
||||

*Exempel*

De här exemplen hämta det lägsta värdet i en uppsättning siffror och matrisen:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Och returnera resultatet: `1`

<a name="mod"></a>

### <a name="mod"></a>MOD

Returnera återstoden av att dividera två tal.
För att få heltalsresultat, se [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*täljaren*> | Ja | Heltal eller flyttal | Talet att dela med de *divisorn* |
| <*divisor*> | Ja | Heltal eller flyttal | Talet som dividerar den *täljaren*, men kan inte vara 0. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Heltal eller flyttal | Återstoden av att dividera det första talet av den andra siffran |
||||

*Exempel*

Det här exemplet dividerar den första siffran av den andra siffran:

```
mod(3, 2)
```

Och returnera resultatet: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Returnera produkten från multiplicera två tal.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Heltal eller flyttal | Talet att multiplicera med *multiplicand2* |
| <*multiplicand2*> | Ja | Heltal eller flyttal | Hur många som multipler *multiplicand1* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*product-result*> | Heltal eller flyttal | Produkten från multiplicera den första siffran av den andra siffran |
||||

*Exempel*

De här exemplen flera första talet med den andra siffran:

```
mul(1, 2)
mul(1.5, 2)
```

Och returnera följande resultat:

* Det första exemplet: `2`
* Andra exempel `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Returnera brödtexten för en viss del i utdata för en åtgärd som har flera delar.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Namnet på den åtgärd som har utdata med flera delar |
| <*index*> | Ja | Integer | Indexvärdet för den del som du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*body*> | String | Brödtexten för den angivna delen |
||||

<a name="not"></a>

### <a name="not"></a>inte

Kontrollera om ett uttryck är FALSKT.
Returnerar värdet true när uttrycket är FALSKT, eller returnera värdet false när det är sant.

```json
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck*> | Ja | Boolesk | Uttryck för att kontrollera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när uttrycket är FALSKT. Returnera false när uttrycket är sant. |
||||

*Exempel 1*

De här exemplen kontrollera om de angivna uttrycken är false:

```json
not(false)
not(true)
```

Och returnera följande resultat:

* Det första exemplet: Uttrycket är FALSKT, så returnerar funktionen `true`.
* Andra exempel: Uttrycket är sant, så returnerar funktionen `false`.

*Exempel 2*

De här exemplen kontrollera om de angivna uttrycken är false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Och returnera följande resultat:

* Det första exemplet: Uttrycket är FALSKT, så returnerar funktionen `true`.
* Andra exempel: Uttrycket är sant, så returnerar funktionen `false`.

<a name="or"></a>

### <a name="or"></a>eller

Kontrollera om minst en uttrycket är sant.
Returnerar värdet true när minst en uttrycket är SANT och returnerar false när alla är FALSKT.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck1*>, <*uttryck2*>,... | Ja | Boolesk | Uttryck för att kontrollera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT | Boolesk | Returnera värdet true när minst en uttrycket är sant. Returnera false om alla uttrycken är falska. |
||||

*Exempel 1*

De här exemplen kontrollera om minst en uttrycket är sant:

```json
or(true, false)
or(false, false)
```

Och returnera följande resultat:

* Det första exemplet: Minst en uttrycket är sant, så returnerar funktionen `true`.
* Andra exempel: Båda uttrycken är falska, så returnerar funktionen `false`.

*Exempel 2*

De här exemplen kontrollera om minst en uttrycket är sant:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Och returnera följande resultat:

* Det första exemplet: Minst en uttrycket är sant, så returnerar funktionen `true`.
* Andra exempel: Båda uttrycken är falska, så returnerar funktionen `false`.

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Returnera värdet för en parameter som beskrivs i din arbetsflödesdefinitionen.

```
parameters('<parameterName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | String | Namnet på parametern vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Alla | Värdet för den angivna parametern |
||||

*Exempel*

Anta att du har det här JSON-värdet:

```json
{
  "fullName": "Sophia Owen"
}
```

Det här exemplet hämtar värdet för den angivna parametern:

```
parameters('fullName')
```

Och returnerar resultatet: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Returnera ett slumpvist heltal från ett visst intervall är inkluderande endast i från slutet.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Integer | Ett heltal i intervallet |
| <*maxValue*> | Ja | Integer | Det heltal som följer det högsta heltalet i intervallet som funktionen kan returnera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Slumpvist heltal som returneras från det angivna intervallet |
||||

*Exempel*

Det här exemplet hämtar ett slumpvist heltal från det angivna intervallet, förutom det maximala värdet:

```
rand(1, 5)
```

Och returnerar ett av dessa siffror som ett resultat: `1`, `2`, `3`, eller `4`

<a name="range"></a>

### <a name="range"></a>Adressintervall

Returnera en heltalsmatris som startar från en angiven heltal.

```
range(<startIndex>, <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Ja | Integer | Ett heltalsvärde som startar matrisen är den första posten |
| <*Antal*> | Ja | Integer | Antalet heltal i matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*intervallet resultatet*>] | Matris | Matris med heltal med början från det angivna indexet |
||||

*Exempel*

Det här exemplet skapar en heltalsmatris som startar från det angivna indexet och har det angivna antalet heltal:

```
range(1, 4)
```

Och returnerar resultatet: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Ersätt

Ersätta en understräng med den angivna strängen och returnera resultatsträngen. Den här funktionen är skiftlägeskänslig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Den sträng som har delsträngen som ska ersättas |
| <*oldText*> | Ja | String | Delsträngen som ska ersätta |
| <*newText*> | Ja | String | Ersättningssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | Uppdaterade strängen när du har ersatt delsträngen <p>Om delsträngen inte hittas, returnera den ursprungliga strängen. |
||||

*Exempel*

Det här exemplet söker du efter ”gamla” delsträngen i ”old string” och ersätter ”gamla” med ”nytt”:

```
replace('the old string', 'old', 'new')
```

Och returnerar resultatet: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Ta bort en egenskap från ett-objekt och returnera det uppdaterade objektet.

```
removeProperty(<object>, '<property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Objekt | JSON-objekt som du vill ta bort en egenskap från |
| <*Egenskapen*> | Ja | String | Namnet på egenskapen att ta bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Uppdaterade JSON-objekt utan att den angivna egenskapen |
||||

*Exempel*

Det här exemplet tar bort den `"accountLocation"` egenskap från en `"customerProfile"` objekt som konverteras till JSON med den [JSON()](#json) fungera, och returnerar det uppdaterade objektet:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Ange värdet för egenskapen för ett objekt och returnera det uppdaterade objektet.
Du kan använda den här funktionen för att lägga till en ny egenskap eller [addProperty()](#addProperty) funktion.

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Objekt | JSON-objektet vars egenskap som du vill ange |
| <*Egenskapen*> | Ja | String | Namn för den befintliga eller nya egenskapen att ställa in |
| <*value*> | Ja | Alla | Värdet som angetts för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet vars egenskapsuppsättningen |
||||

*Exempel*

Det här exemplet anges den `"accountNumber"` egenskapen på en `"customerProfile"` objekt som konverteras till JSON med den [JSON()](#json) funktion.
Funktionen tilldelar ett värde av [guid()](#guid) fungera, och returnerar det uppdaterade JSON-objektet:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>hoppa över

Ta bort objekt från början av en samling och returnerar *alla andra* objekt.

```
skip([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Den samling som du vill ta bort element |
| <*Antal*> | Ja | Integer | Ett positivt heltal för antalet objekt att ta bort längst fram |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*uppdateras samling*>] | Matris | Uppdaterade samlingen när du tar bort de angivna objekt |
||||

*Exempel*

Det här exemplet tar bort en artikel, hur många 0, från början av den angivna matrisen:

```
skip(createArray(0, 1, 2, 3), 1)
```

Och returnerar den här matrisen med de återstående objekt: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, baserat på den angivna avgränsningstecken i den ursprungliga strängen.

```
split('<text>', '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Strängen som ska dela upp i delsträngar baserat på den angivna avgränsaren i den ursprungliga strängen |
| <*delimiter*> | Ja | String | Tecken i den ursprungliga strängen som ska användas som avgränsare |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*substring1*>, <*substring2*>,...] | Matris | En matris som innehåller delsträngar från den ursprungliga strängen, avgränsade med kommatecken |
||||

*Exempel*

Det här exemplet skapar en matris med delsträngar från den angivna strängen baserat på det angivna tecknet som avgränsare:

```
split('a_b_c', '_')
```

Och returnerar den här matrisen som ett resultat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Returnera början på dagen för en tidsstämpel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Den angivna tidsstämpeln men med början vid noll mark för dagen |
||||

*Exempel*

Det här exemplet hittar början på dagen för den här tidsstämpeln:

```
startOfDay('2018-03-15T13:30:30Z')
```

Och returnerar resultatet: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Returnera början på timmen för en tidsstämpel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Den angivna tidsstämpeln men med början vid noll minuter mark för timme |
||||

*Exempel*

Det här exemplet hittar timmens för den här tidsstämpeln:

```
startOfHour('2018-03-15T13:30:30Z')
```

Och returnerar resultatet: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Returnera början på månaden för en tidsstämpel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Den angivna tidsstämpeln men från och med den första dagen i månaden på noll mark |
||||

*Exempel*

Det här exemplet returnerar början på månaden för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar resultatet: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontrollera om en sträng som börjar med en viss delsträng.
Returnerar värdet true när delsträngen hittas, eller returnera false när hittades inte.
Den här funktionen är inte skiftlägeskänsligt.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Sträng att söka |
| <*searchText*> | Ja | String | Från strängen som ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| SANT eller FALSKT  | Boolesk | Returnera värdet true när från delsträngen hittas. Returnera false när hittades inte. |
||||

*Exempel 1*

Det här exemplet kontrollerar om strängen ”hello world” börjar med ”hello” delsträngen:

```
startsWith('hello world', 'hello')
```

Och returnerar resultatet: `true`

*Exempel 2*

Det här exemplet kontrollerar om strängen ”hello world” börjar med ”hälsningar” delsträngen:

```
startsWith('hello world', 'greetings')
```

Och returnerar resultatet: `false`

<a name="string"></a>

### <a name="string"></a>sträng

Returnera sträng för ett värde.

```
string(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Alla | Värdet att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*string-value*> | String | Strängversionen för det angivna värdet |
||||

*Exempel 1*

Det här exemplet skapar strängversionen för det här värdet:

```
string(10)
```

Och returnerar resultatet: `"10"`

*Exempel 2*

Det här exemplet skapar en sträng för det angivna JSON-objektet och använder ett omvänt snedstreck (\\) som escape-tecken för citattecken (”).

```
string( { "name": "Sophie Owen" } )
```

Och returnerar resultatet: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Returnera resultatet från att subtrahera den andra siffran från den första siffran.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras den *subtrahend* |
| <*subtrahend*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras från den *minuenden* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*result*> | Heltal eller flyttal | Resultatet från att subtrahera den andra siffran från den första siffran |
||||

*Exempel*

Det här exemplet subtraherar det andra talet från den första siffran:

```
sub(10.3, .3)
```

Och returnerar resultatet: `10`

<a name="substring"></a>

### <a name="substring"></a>delsträngen

Returnera tecken från en sträng, med början från den angivna positionen eller index.
Indexera värden start med siffran 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Sträng vars tecken som du vill |
| <*startIndex*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som position eller index startvärde |
| <*Längd*> | Ja | Integer | Ett positivt antal tecken som du vill ha i delsträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | En understräng med det angivna antalet tecken, med början vid position i Källsträngen angivna indexet |
||||

*Exempel*

Det här exemplet skapar en understräng fem tecken från den angivna strängen, med början från indexvärdet 6:

```
substring('hello world', 6, 5)
```

Och returnerar resultatet: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Ta bort ett antal tidsenheter från en tidsstämpel.
Se även [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Den sträng som innehåller tidsstämpeln |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter att subtrahera |
| <*timeUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall*: ”Andra”, ”minut”, ”Hour”, ”Day”, ”Week”, ”månad”, ”år” |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Tidsstämpel minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Och returnerar resultatet: `"2018-01-01T00:00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet i valfritt ”D”-format: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>ta

Returnera objekt från början av en samling.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen vars objekt som du vill |
| <*Antal*> | Ja | Integer | Ett positivt heltal för antalet objekt som du vill från början |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delmängd*> eller [<*delmängd*>] | Sträng eller matris, respektive | En sträng eller en matris som har det angivna antalet objekt som tagits från början av den ursprungliga samlingen |
||||

*Exempel*

De här exemplen hämta det angivna antalet objekt från början av de här samlingarna:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Och returnera följande resultat:

* Det första exemplet: `"abc"`
* Andra exempel: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>tick

Returnera den `ticks` egenskapsvärdet för en angiven tidsstämpel.
En *skalstreck* är ett intervall om 100 nanosekunder.

```
ticks('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Strängen för en tidsstämpel |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Antalet tick eftersom den angivna tidsstämpeln |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Returnera en sträng i gemener format. Om ett tecken i strängen inte har en gemen version, förblir tecknet oförändrat i den returnerade strängen.

```
toLower('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Strängen att returnera i gemener format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | Den ursprungliga strängen i gemener format |
||||

*Exempel*

Det här exemplet konverterar den här strängen till gemener:

```
toLower('Hello World')
```

Och returnerar resultatet: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Returnera en sträng i versaler format. Om ett tecken i strängen inte har en versal version, förblir tecknet oförändrat i den returnerade strängen.

```
toUpper('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Strängen att returnera i versaler format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | Den ursprungliga strängen i versaler format |
||||

*Exempel*

Det här exemplet konverterar den här strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar resultatet: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>trigger

Returnera en utlösare utdata vid körning, eller värden från andra JSON namn och värde par som du kan tilldela till ett uttryck.

* Den här funktionen returnerar inuti en utlösarens indata och utdata från föregående körning.

* Den här funktionen returnerar utdata från den aktuella körningen inuti en utlösarvillkor.

Funktionen refererar till objektet hela utlösare på som standard, men du kan du ange en egenskap vars värde som du vill.
Dessutom har snabbformat-versioner som är tillgängliga för den här funktionen kan du se [triggerOutputs()](#triggerOutputs) och [triggerBody()](#triggerBody).

```
trigger()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | Utdata från en utlösare vid körning |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Returnerar en utlösare `body` utdata vid körning.
Snabbformat för `trigger().outputs.body`.
Se [trigger()](#trigger).

```
triggerBody()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | Den `body` utdata från utlösaren |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Returnerar en matris med värden som matchar ett nyckelnamn i en utlösare *formulärdata* eller *formulärkodade* utdata.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | String | Namn för den nyckel vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en matris från ”feedUrl” nyckelvärdet i en RSS-utlösare formulärdata eller formulärkodade utdata:

```
triggerFormDataMultiValues('feedUrl')
```

Och returnerar den här matrisen som en exempelresultat: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Returnerar en sträng med ett enda värde som matchar ett nyckelnamn i en utlösare *formulärdata* eller *formulärkodade* utdata.
Om funktionen hittar fler än en matchning, genereras ett fel.

```
triggerFormDataValue('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | String | Namn för den nyckel vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Värdet i den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en sträng från ”feedUrl” nyckelvärdet i en RSS-utlösare formulärdata eller formulärkodade utdata:

```
triggerFormDataValue('feedUrl')
```

Och returnerar den här strängen som en exempelresultat: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Returnera brödtexten för en viss del i utdata för en utlösare som har flera delar.

```
triggerMultipartBody(<index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*index*> | Ja | Integer | Indexvärdet för den del som du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*body*> | String | Brödtexten för den angivna delen i en utlösarens multipart-utdata |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Returnera en utlösare utdata vid körning, eller värden från andra JSON-namn och värde-par.
Snabbformat för `trigger().outputs`.
Se [trigger()](#trigger).

```
triggerOutputs()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | Utdata från en utlösare vid körning  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

Ta bort inledande och avslutande blanksteg från en sträng och returnera den uppdaterade strängen.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Den sträng som har den inledande och avslutande blankstegen för att ta bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | En uppdaterad version för den ursprungliga strängen utan inledande eller avslutande blanksteg |
||||

*Exempel*

Det här exemplet tar bort inledande och avslutande blanksteg från strängen ”Hello World”:

```
trim(' Hello World  ')
```

Och returnerar resultatet: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Union

Returnerar en samling som har *alla* objekt från de angivna samlingarna.
Om du vill visas i resultatet, kan ett objekt visas i en samling som skickas till den här funktionen. Om ett eller flera objekt har samma namn, visas det sista objektet med det namnet i resultatet.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*samling2*>,...  | Ja | Matris eller objekt, men inte båda | Samlingar som du vill från *alla* objekt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matris eller ett objekt, respektive | En samling med alla objekt från de angivna samlingarna - inga dubbletter |
||||

*Exempel*

Det här exemplet hämtar *alla* objekt från de här samlingarna:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Och returnerar resultatet: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Returnera en uniform resource identifier (URI)-kodad version för en sträng genom att ersätta URL-osäkra tecknen med escape-tecken.
Använd den här funktionen snarare än [encodeUriComponent()](#encodeUriComponent).
Även om båda funktionerna fungerar på samma sätt `uriComponent()` är att föredra.

```
uriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Sträng som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

Det här exemplet skapar en URI-kodad version för den här strängen:

```
uriComponent('https://contoso.com')
```

Och returnerar resultatet: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Returnera den binära versionen för en komponent för uniform resource identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | URI-kodad sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | Den binära versionen för den URI-kodad strängen. Binärt innehåll är base64-kodad och representeras av `$content`. |
||||

*Exempel*

Det här exemplet skapar den binära versionen för den här URI-kodad sträng:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Returnera den strängversionen för en uniform resource identifier (URI)-kodad sträng, effektivt avkodning URI-kodad sträng.

```
uriComponentToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | URI-kodade strängen att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Den avkodade versionen för den URI-kodad strängen |
||||

*Exempel*

Det här exemplet skapar den avkodade strängversionen för den här URI-kodad sträng:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Returnera den `host` värde för en uniform resource identifier (URI).

```
uriHost('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `host` värdet du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*host-value*> | String | Den `host` värde för den angivna URI: N |
||||

*Exempel*

Det här exemplet hittar den `host` värdet för den här URI: N:

```
uriHost('https://www.localhost.com:8080')
```

Och returnerar resultatet: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Returnera den `path` värde för en uniform resource identifier (URI).

```
uriPath('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `path` värdet du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*path-value*> | String | Den `path` värde för angiven URI. Om `path` inte ha ett värde, returnerar tecknet ”/”. |
||||

*Exempel*

Det här exemplet hittar den `path` värdet för den här URI: N:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Returnera den `path` och `query` värden för en uniform resource identifier (URI).

```
uriPathAndQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `path` och `query` värden du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | Den `path` och `query` värden för den angivna URI: N. Om `path` inte ange ett värde, returnerar tecknet ”/”. |
||||

*Exempel*

Det här exemplet hittar den `path` och `query` värden för den här URI: N:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Returnera den `port` värde för en uniform resource identifier (URI).

```
uriPort('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `port` värdet du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*port-value*> | Integer | Den `port` värde för angiven URI. Om `port` inte ange ett värde, returneras standardporten för protokollet. |
||||

*Exempel*

Det här exemplet returnerar den `port` värdet för den här URI: N:

```
uriPort('http://www.localhost:8080')
```

Och returnerar resultatet: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Returnera den `query` värde för en uniform resource identifier (URI).

```
uriQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `query` värdet du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*query-value*> | String | Den `query` värde för den angivna URI: N |
||||

*Exempel*

Det här exemplet returnerar den `query` värdet för den här URI: N:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Returnera den `scheme` värde för en uniform resource identifier (URI).

```
uriScheme('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | URI: N vars `scheme` värdet du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | Den `scheme` värde för den angivna URI: N |
||||

*Exempel*

Det här exemplet returnerar den `scheme` värdet för den här URI: N:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Alternativt kan du ange ett annat format med den <*format*> parametern.


| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Nej | String | Antingen en [enskild formatspecifierare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | Aktuellt datum och tid |
||||

*Exempel 1*

Anta att dagens datum är den 15 April 2018 klockan 13:00:00.
Det här exemplet hämtar den aktuella tidsstämpeln:

```
utcNow()
```

Och returnerar resultatet: `"2018-04-15T13:00:00.0000000Z"`

*Exempel 2*

Anta att dagens datum är den 15 April 2018 klockan 13:00:00.
Det här exemplet hämtar den aktuella tidsstämpeln i valfritt ”D”-format:

```
utcNow('D')
```

Och returnerar resultatet: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variabler

Returnera värdet för en specifik variabel.

```
variables('<variableName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | String | Namnet på variabeln vars värde du vill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*variable-value*> | Alla | Värdet för den angivna variabeln |
||||

*Exempel*

Anta att det aktuella värdet för en variabel för ”numItems” är 20.
Det här exemplet hämtar heltalsvärdet för den här variabeln:

```
variables('numItems')
```

Och returnerar resultatet: `20`

<a name="workflow"></a>

### <a name="workflow"></a>arbetsflöde

Returnera all information om själva arbetsflödet under körningen.

```
workflow().<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Nej | String | Namnet på egenskapen arbetsflöde vars värde du vill <p>Ett arbetsflödesobjekt har de här egenskaperna: **namn**, **typ**, **id**, **plats**, och **kör**. Den **kör** egenskapsvärdet är också ett objekt som har de här egenskaperna: **namn**, **typ**, och **id**. |
|||||

*Exempel*

Det här exemplet returnerar namnet för ett arbetsflöde aktuella körningen:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Returnera XML-versionen för en sträng som innehåller ett JSON-objekt.

```
xml('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Strängen med JSON-objekt att konvertera <p>JSON-objekt måste ha endast en rot-egenskapen, vilket inte får vara en matris. <br>Använd omvänt snedstreck (\\) som escape-tecken för dubbelt citattecken (”). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-version*> | Objekt | Kodad XML-filen för den angivna strängen eller JSON-objekt |
||||

*Exempel 1*

Det här exemplet skapar XML-versionen för den här strängen, som innehåller ett JSON-objekt:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Och returnerar resultatet för den här XML:

```xml
<name>Sophia Owen</name>
```

*Exempel 2*

Anta att du har den här JSON-objekt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Det här exemplet skapar XML för en sträng som innehåller JSON-objektet:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Och returnerar resultatet för den här XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Kontrollera XML för noder eller värden som matchar ett XPath (XML Path Language)-uttryck och returnerar matchande noder eller värden. En XPath-uttryck, eller bara ”XPath”, hjälper dig att gå en XML-dokumentstruktur så att du kan välja noder eller beräkning värden i XML-innehåll.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Ja | Alla | XML-sträng att söka efter noder eller värden som matchar ett värde för XPath-uttryck |
| <*xpath*> | Ja | Alla | XPath-uttrycket som används för att hitta matchande värdena eller XML-noder |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | En XML-nod när endast en nod matchar det angivna XPath-uttrycket |
| <*value*> | Alla | Värdet från en XML-nod när endast ett värde matchar det angivna XPath-uttrycket |
| [<*xml-nod 1*>, <*xml-nod 2*>,...] </br>ELLER </br>[<*value1*>, <*value2*>, ...] | Matris | En matris med XML-noder eller värden som matchar det angivna XPath-uttrycket |
||||

*Exempel 1*

Det här exemplet hittar noder som matchar den `<name></name>` nod i de angivna argumenten och returnerar en matris med dessa värden för noden:

`xpath(xml(parameters('items')), '/produce/item/name')`

Här följer argument:

* ”Objekt”-sträng som innehåller den här XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  I exemplet används den [parameters()](#parameters) för att hämta XML-strängen från argumentet ”objekt”, men måste också att konvertera strängen till XML-format med hjälp av den [xml()](#xml) funktion.

* Den här XPath-uttryck som skickas som en sträng:

  `"/produce/item/name"`

Här är resultatet-matris med de noder som matchar `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exempel 2*

Följa i exempel 1, i det här exemplet hittar noder som matchar den `<count></count>` noden och lägger till dessa värden för noden med den `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Och returnerar resultatet: `30`

*Exempel 3*

I det här exemplet båda uttrycken hitta noder som matchar den `<location></location>` nod i de angivna argumenten, bland annat XML med ett namnområde. Uttryck som använder ett omvänt snedstreck (\\) som escape-tecken för dubbelt citattecken (”).

* *Uttrycket 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Uttrycket 2*

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Här följer argument:

* Den här XML, som innehåller XML-dokumentet namnområdet `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Antingen XPath-uttrycket här:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Här är noden resultat som matchar den `<location></location` nod:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exempel 4*

Efter exempel 3 i det här exemplet hittar värdet i den `<location></location>` nod:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Och returnerar resultatet: `"Paris"`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [Definitionsspråk för arbetsflödet](../logic-apps/logic-apps-workflow-definition-language.md)
