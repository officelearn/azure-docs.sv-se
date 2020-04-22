---
title: Referensguide för funktioner i uttryck
description: Referensguide till funktioner i uttryck för Azure Logic Apps och Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 48be73a6385c9690909cb70abe558a2def1ace88
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730517"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referensguide till att använda funktioner i uttryck för Azure Logic Apps och Power Automate

För arbetsflödesdefinitioner i [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Power [Automate](https://docs.microsoft.com/flow/getting-started)får vissa [uttryck](../logic-apps/logic-apps-workflow-definition-language.md#expressions) sina värden från körningsåtgärder som kanske ännu inte finns när arbetsflödet börjar köras. Om du vill referera till dessa värden eller bearbeta värdena i dessa uttryck kan du använda *funktioner* som tillhandahålls av [arbetsflödesdefinitionsspråket](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Den här referenssidan gäller både Azure Logic Apps och Power Automate, men visas i Azure Logic Apps-dokumentationen. Även om den här sidan specifikt refererar till logikappar fungerar dessa funktioner för både flöden och logikappar. Mer information om funktioner och uttryck i Power Automate finns [i Använda uttryck i villkor](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Du kan till exempel beräkna värden med hjälp av matematiska funktioner, till exempel [funktionen add()](../logic-apps/workflow-definition-language-functions-reference.md#add)när du vill att summan ska vara heltal eller flytar. Här är andra exempeluppgifter som du kan utföra med funktioner:

| Aktivitet | Syntax för funktion | Resultat |
| ---- | --------------- | ------ |
| Returnera en sträng i gemener format. | toLower("<*text*>") <p>Till exempel: toLower('Hello') | "Hej" |
| Returnera en globalt unik identifierare (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Om du vill hitta funktioner [baserat på deras allmänna syfte](#ordered-by-purpose)läser du följande tabeller. Mer information om varje funktion finns i den [alfabetiska listan](#alphabetical-list).

> [!NOTE]
> I syntaxen för parameterdefinitioner betyder ett frågetecken (?) som visas efter en parameter att parametern är valfri.
> Se till exempel [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funktioner i uttryck

Om du vill visa hur du använder en funktion i ett `customerName` uttryck visar det här `accountName` exemplet hur du kan hämta värdet från parametern och tilldela värdet till egenskapen med hjälp av funktionen [parameters()](#parameters) i ett uttryck:

```json
"accountName": "@parameters('customerName')"
```

Här är några andra allmänna sätt som du kan använda funktioner i uttryck:

| Aktivitet | Funktionssyntax i ett uttryck |
| ---- | -------------------------------- |
| Utför arbete med ett objekt genom att skicka artikeln till en funktion. | "\@<*functionName*>(<*>)"* |
| 1. Hämta *parameterName's*värde med `parameters()` hjälp av den kapslade funktionen. </br>2. Utför arbete med resultatet genom att överföra det värdet till *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>')" |
| 1. Få resultatet från den kapslade inre *funktionsfunktionenName*. </br>2. Skicka resultatet till den yttre *funktionsfunktionenName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. Få resultatet från *functionName*. </br>2. Med tanke på att resultatet är ett objekt med *egenskapgenskapName*hämtar du egenskapens värde. | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

`concat()` Funktionen kan till exempel ta två eller flera strängvärden som parametrar. Den här funktionen kombinerar dessa strängar i en sträng.
Du kan antingen passera i stränglitteraler, till exempel "Sophia" och "Owen" så att du får en kombinerad sträng, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Du kan också hämta strängvärden från parametrar. I det `parameters()` här exemplet `concat()` används `firstName` funktionen `lastName` i varje parameter och parametrarna och och. Du skickar sedan de resulterande strängarna till `concat()` funktionen så att du får en kombinerad sträng, till exempel "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Båda exemplen tilldelar resultatet `customerName` resultatet till egenskapen.

Här är de tillgängliga funktionerna som beställts efter deras allmänna ändamål, eller så kan du bläddra i funktionerna baserat på [alfabetisk ordning](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Strängfunktioner

Om du vill arbeta med strängar kan du använda dessa strängfunktioner och även vissa [samlingsfunktioner](#collection-functions).
Strängfunktioner fungerar bara på strängar.

| Strängfunktion | Aktivitet |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinera två eller flera strängar och returnera den kombinerade strängen. |
| [slutarMed](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Kontrollera om en sträng slutar med den angivna delsträngen. |
| [formatNummer](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Returnera ett tal som en sträng baserat på det angivna formatet |
| [Guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Returnera startpositionen för en delsträng. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Returnera startpositionen för den sista förekomsten av en delsträng. |
| [Ersätta](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersätt en delsträng med den angivna strängen och returnera den uppdaterade strängen. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, från en större sträng baserat på ett angivet avgränsare i den ursprungliga strängen. |
| [börjarMed](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontrollera om en sträng börjar med en viss delsträng. |
| [Delsträng](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Returnera tecken från en sträng, med början från den angivna positionen. |
| [toLower (TillLågare)](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Returnera en sträng i gemener format. |
| [tillUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Returnera en sträng i versaler. |
| [Trimma](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Ta bort inledande och avslutande blanktecken från en sträng och returnera den uppdaterade strängen. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Insamlingsfunktioner

Om du vill arbeta med samlingar, i allmänhet matriser, strängar och ibland ordlistor, kan du använda dessa samlingsfunktioner.

| Funktionen Samling | Aktivitet |
| ------------------- | ---- |
| [Innehåller](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontrollera om en samling har ett visst objekt. |
| [Tom](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Kontrollera om en samling är tom. |
| [Första](../logic-apps/workflow-definition-language-functions-reference.md#first) | Returnera det första objektet från en samling. |
| [Korsningen](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Returnera en samling som *bara* har de vanliga objekten i de angivna samlingarna. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | När du är inne i en upprepande åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [Ansluta sig till](../logic-apps/workflow-definition-language-functions-reference.md#join) | Returnera en sträng som har *alla* objekt från en matris, avgränsade med det angivna tecknet. |
| [Senaste](../logic-apps/workflow-definition-language-functions-reference.md#last) | Returnera det sista objektet från en samling. |
| [Längd](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Hoppa över](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Ta bort objekt från framsidan av en samling och returnera *alla andra* objekt. |
| [Ta](../logic-apps/workflow-definition-language-functions-reference.md#take) | Returnera objekt från framsidan av en samling. |
| [Unionen](../logic-apps/workflow-definition-language-functions-reference.md#union) | Returnera en samling som har *alla* objekt från de angivna samlingarna. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logiska jämförelsefunktioner

Om du vill arbeta med villkor, jämföra värden och uttrycksresultat eller utvärdera olika typer av logik kan du använda dessa logiska jämförelsefunktioner.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Logisk jämförelsefunktion | Aktivitet |
| --------------------------- | ---- |
| [och](../logic-apps/workflow-definition-language-functions-reference.md#and) | Kontrollera om alla uttryck är sanna. |
| [lika med](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Kontrollera om båda värdena är likvärdiga. |
| [större än](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Kontrollera om det första värdet är större än det andra värdet. |
| [större än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Kontrollera om det första värdet är större än eller lika med det andra värdet. |
| [Om](../logic-apps/workflow-definition-language-functions-reference.md#if) | Kontrollera om ett uttryck är sant eller falskt. Baserat på resultatet returnerar du ett angivet värde. |
| [mindre än](../logic-apps/workflow-definition-language-functions-reference.md#less) | Kontrollera om det första värdet är mindre än det andra värdet. |
| [mindre än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Kontrollera om det första värdet är mindre än eller lika med det andra värdet. |
| [Inte](../logic-apps/workflow-definition-language-functions-reference.md#not) | Kontrollera om ett uttryck är falskt. |
| [Eller](../logic-apps/workflow-definition-language-functions-reference.md#or) | Kontrollera om minst ett uttryck är sant. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverteringsfunktioner

Om du vill ändra värdets typ eller format kan du använda dessa konverteringsfunktioner.
Du kan till exempel ändra ett värde från ett booleskt till ett heltal.
Mer information om hur Logic Apps hanterar innehållstyper under konverteringen finns i [Hantera innehållstyper](../logic-apps/logic-apps-content-type.md).
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Konverteringsfunktion | Aktivitet |
| ------------------- | ---- |
| [Array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Returnera en matris från en enda angiven indata. För flera indata finns i [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Returnera den base64-kodade versionen för en sträng. |
| [bas64TillBinär](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Returnera den binära versionen för en base64-kodad sträng. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Returnera strängversionen för en base64-kodad sträng. |
| [Binära](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Returnera den binära versionen för ett indatavärde. |
| [Bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Returnera den booleska versionen för ett indatavärde. |
| [skapaArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Returnera en matris från flera indata. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Returnera data-URI för ett indatavärde. |
| [dataUriToBinär](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Returnera strängversionen för en data-URI. |
| [avkodaBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Returnera strängversionen för en base64-kodad sträng. |
| [avkodaDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Returnera den binära versionen för en data-URI. |
| [avkodaUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Returnera en sträng som ersätter escape-tecken med avkodade versioner. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecken med escape-tecken. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Returnera ett flyttalsnummer för ett indatavärde. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Returnera heltalsversionen för en sträng. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Returnera typvärdet eller objektet för JavaScript-objekt (JSON) för en sträng eller XML. |
| [Sträng](../logic-apps/workflow-definition-language-functions-reference.md#string) | Returnera strängversionen för ett indatavärde. |
| [uriKompent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Returnera den URI-kodade versionen för ett indatavärde genom att ersätta URL-osäkra tecken med escape-tecken. |
| [uriKompentTillbinären](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriKompent TillString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Returnera strängversionen för en URI-kodad sträng. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Returnera XML-versionen för en sträng. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematikfunktioner

Om du vill arbeta med heltal och flöten kan du använda dessa matematiska funktioner.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Matematiska funktionen | Aktivitet |
| ------------- | ---- |
| [Add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Returnera resultatet från att dela två tal. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Returnera resten från att dela två siffror. |
| [mul (mul)](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Returnera produkten från att multiplicera två tal. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [Utbud](../logic-apps/workflow-definition-language-functions-reference.md#range) | Returnera en heltalsmatris som startar från ett angivet heltal. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Returnera resultatet från att subtrahera det andra talet från det första talet. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- och tidsfunktioner

Om du vill arbeta med datum och tider kan du använda dessa datum- och tidsfunktioner.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funktionen Datum eller tid | Aktivitet |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Lägg till ett antal dagar i en tidsstämpel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Lägg till ett antal timmar i en tidsstämpel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Lägg till ett antal minuter i en tidsstämpel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Lägg till ett antal sekunder i en tidsstämpel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Lägg till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [konverteraFrånUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertera en tidsstämpel från Universell tidskoordinerad (UTC) till måltidszonen. |
| [konverteraTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konvertera en tidsstämpel från källtidszonen till måltidszonen. |
| [konverteraToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertera en tidsstämpel från källtidszonen till Universell tidskoordinerad (UTC). |
| [dayOfMonth dagOfMonth dagOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Returnera dagen i månaden komponenten från en tidsstämpel. |
| [dagVecka](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Returnera dag i veckan komponenten från en tidsstämpel. |
| [dagiår](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Returnera dagen för året komponenten från en tidsstämpel. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Returnera datumet från en tidsstämpel. |
| [fåFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna. Se även [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [fåPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtraheraFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Returnera början av dagen för en tidsstämpel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Returnera början av timmen för en tidsstämpel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Returnera början av månaden för en tidsstämpel. |
| [subtraheraFrånTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrahera ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Fästingar](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Returnera `ticks` egenskapsvärdet för en angiven tidsstämpel. |
| [utcNow (olikartade)](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Returnera den aktuella tidsstämpeln som en sträng. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Arbetsflödesfunktioner

Dessa arbetsflödesfunktioner kan hjälpa dig att:

* Få information om en arbetsflödesinstans vid körning.
* Arbeta med de indata som används för att instansiera logikappar eller flöden.
* Referera till utdata från utlösare och åtgärder.

Du kan till exempel referera till utdata från en åtgärd och använda dessa data i en senare åtgärd.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Arbetsflödesfunktion | Aktivitet |
| ----------------- | ---- |
| [Åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#action) | Returnera den aktuella åtgärdens utdata vid körning eller värden från andra JSON-namn- och värdepar. Se även [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [åtgärdBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Returnera en åtgärds `body` utdata vid körning. Se även [kroppen](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Returnera en åtgärds utdata vid körning. Se [utdata](../logic-apps/workflow-definition-language-functions-reference.md#outputs) och [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Returnera en åtgärds utdata vid körning eller värden från andra JSON-namn- och värdepar. Se även [åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Kroppen](#body) | Returnera en åtgärds `body` utdata vid körning. Se även [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formulärDataMultiVärderar](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Skapa en matris med de värden som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* åtgärdsutdata. |
| [formulärDataVärde](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Returnera ett enda värde som matchar ett nyckelnamn i en åtgärds *formulärdata* eller *formulärkodade utdata*. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | När du är inne i en upprepande åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#items) | När du är inne i en Förförslinga eller Till-slinga returnerar du det aktuella objektet från den angivna loopen.|
| [iterationIndex](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | När du är inne i en Till-slinga returnerar du indexvärdet för den aktuella iterationen. Du kan använda den här funktionen inuti kapslade tills-loopar. |
| [listaCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Returnera "motringnings-URL: en" som anropar en utlösare eller åtgärd. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Returnera brödtexten för en viss del i en åtgärds utdata som har flera delar. |
| [Utgångar](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Returnera en åtgärds utdata vid körning. |
| [Parametrar](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Returnera värdet för en parameter som beskrivs i arbetsflödesdefinitionen. |
| [Resultatet](../logic-apps/workflow-definition-language-functions-reference.md#result) | Returnera indata och utdata från alla åtgärder i den angivna `For_each` `Until`scoped-åtgärden, till exempel , och `Scope`. |
| [Utlösa](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Returnera en utlösarutdata vid körning eller från andra JSON-namn- och värdepar. Se även [triggerOutputs](#triggerOutputs) och [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody (triggerBody)](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Returnera en `body` utlösarutdata vid körning. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [utlösande DataVärde](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Returnera ett enda värde som matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösarutdata. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Returnera brödtexten för en viss del i en utlösares flerdelade utgång. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Skapa en matris vars värden matchar ett nyckelnamn i *formulärdata* eller *formulärkodade* utlösarutdata. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Returnera en utlösarutdata vid körning eller värden från andra JSON-namn- och värdepar. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variabler](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Returnera värdet för en angiven variabel. |
| [Arbetsflöde](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Returnera all information om själva arbetsflödet under körning. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-tolkningsfunktioner

Om du vill arbeta med enhetliga resursidentifierare (URI: er) och hämta olika egenskapsvärden för dessa URI-tolkningsfunktioner kan du använda dessa URI-tolkningsfunktioner.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| URI-tolkningsfunktion | Aktivitet |
| -------------------- | ---- |
| [UriHost (uriHost)](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Returnera `host` värdet för en enhetlig resursidentifierare (URI). |
| [uriPath (uriPath)](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Returnera `path` värdet för en enhetlig resursidentifierare (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Returnera `path` värdena och `query` värdena för en enhetlig resursidentifierare (URI). |
| [uriPort (på en)](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Returnera `port` värdet för en enhetlig resursidentifierare (URI). |
| [uriquery (uriQuery)](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Returnera `query` värdet för en enhetlig resursidentifierare (URI). |
| [uriKemi](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Returnera `scheme` värdet för en enhetlig resursidentifierare (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulationsfunktioner: JSON & XML

Om du vill arbeta med JSON-objekt och XML-noder kan du använda dessa manipuleringsfunktioner.
Den fullständiga referensen för varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulationsfunktion | Aktivitet |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Lägg till en egenskap och dess värde, eller namn-värde-par, i ett JSON-objekt och returnera det uppdaterade objektet. |
| [smälter samman](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Returnera det första icke-null-värdet från en eller flera parametrar. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Ta bort en egenskap från ett JSON-objekt och returnera det uppdaterade objektet. |
| [Setproperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ange värdet för egenskapen för ett JSON-objekt och returnera det uppdaterade objektet. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sök efter XML efter noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera de matchande noderna eller värdena. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alla funktioner - alfabetisk lista

I det här avsnittet visas alla tillgängliga funktioner i alfabetisk ordning.

<a name="action"></a>

### <a name="action"></a>åtgärd

Returnera den *aktuella* åtgärdens utdata vid körning, eller värden från andra JSON-namn- och värdepar, som du kan tilldela ett uttryck.
Som standard refererar den här funktionen till hela åtgärdsobjektet, men du kan också ange en egenskap vars värde du vill använda.
Se även [åtgärder()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Du kan `action()` bara använda funktionen på följande platser:

* Egenskapen `unsubscribe` för en webhook-åtgärd så att `subscribe` du kan komma åt resultatet från den ursprungliga begäran
* Egenskapen `trackedProperties` för en åtgärd
* Loopvillkoret `do-until` för en åtgärd

```
action()
action().outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Inga | Sträng | Namnet på egenskapen för åtgärdsobjektet vars värde du vill använda: **namn**, **startTime**, **endTime**, **indata**, **utdata**, **status**, **kod**, **trackingId**och **clientTrackingId**. I Azure-portalen kan du hitta dessa egenskaper genom att granska information om en viss körningshistorik. Mer information finns i [REST API - Arbetsflödeskörningsåtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd-utdata*> | Sträng | Utdata från den aktuella åtgärden eller egenskapen |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>åtgärdBody

Returnera en åtgärds `body` utdata vid körning.
Stenografi `actions('<actionName>').outputs.body`för .
Se [body()](#body) och [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sträng | Utdata `body` från den angivna åtgärden |
||||

*Exempel*

Det här `body` exemplet hämtar utdata från Twitter-åtgärden: `Get user`

```
actionBody('Get_user')
```

Och returnerar detta resultat:

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

Returnera en åtgärds utdata vid körning.  och är stenografi för `actions('<actionName>').outputs`. Se [åtgärder()](#actions). Funktionen `actionOutputs()` matchas `outputs()` till i Logic App Designer, så överväg `actionOutputs()`att använda [outputs()](#outputs)i stället för . Även om båda funktionerna `outputs()` fungerar på samma sätt, är att föredra.

```
actionOutputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*Produktionen*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar utdata från Twitter-åtgärden: `Get user`

```
actionOutputs('Get_user')
```

Och returnerar detta resultat:

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

### <a name="actions"></a>åtgärder

Returnera en åtgärds utdata vid körning, eller värden från andra JSON-namn- och värdepar, som du kan tilldela ett uttryck. Som standard refererar funktionen till hela åtgärdsobjektet, men du kan också ange en egenskap vars värde du vill använda.
Korttidsversioner finns i [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)och [body()](#body).
Den aktuella åtgärden finns i [Action()](#action).

> [!NOTE]
> Tidigare kunde du `actions()` använda funktionen `conditions` eller elementet när du anger att en åtgärd kördes baserat på utdata från en annan åtgärd. Om du vill deklarera explicita beroenden mellan åtgärder måste `runAfter` du nu använda egenskapen för den beroende åtgärden.
> Mer information om `runAfter` egenskapen finns i [Fånga och hantera fel med egenskapen runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på det åtgärdsobjekt vars utdata du vill använda  |
| <*Egenskapen*> | Inga | Sträng | Namnet på egenskapen för åtgärdsobjektet vars värde du vill använda: **namn**, **startTime**, **endTime**, **indata**, **utdata**, **status**, **kod**, **trackingId**och **clientTrackingId**. I Azure-portalen kan du hitta dessa egenskaper genom att granska information om en viss körningshistorik. Mer information finns i [REST API - Arbetsflödeskörningsåtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd-utdata*> | Sträng | Utdata från den angivna åtgärden eller egenskapen |
||||

*Exempel*

Det här `status` exemplet hämtar egenskapsvärdet från Twitter-åtgärden `Get user` vid körning:

```
actions('Get_user').outputs.body.status
```

Och returnerar detta resultat:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Returnera resultatet från att lägga till två tal.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*summand_1*> <*summand_2*> | Ja | Heltal, Float eller blandat | Siffrorna som ska läggas till |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*resultatsumma*> | Heltal eller Flyta | Resultatet av att lägga till angivna tal |
||||

*Exempel*

I det här exemplet läggs de angivna talen till:

```
add(1, 1.5)
```

Och returnerar detta resultat:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Lägg till ett antal dagar i en tidsstämpel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Dagar*> | Ja | Integer | Det positiva eller negativa antalet dagar som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet dagar  |
||||

*Exempel 1*

I det här exemplet läggs 10 dagar till den angivna tidsstämpeln:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Och returnerar detta resultat:`"2018-03-25T00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar detta resultat:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Lägg till ett antal timmar i en tidsstämpel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Timmar*> | Ja | Integer | Det positiva eller negativa antalet timmar som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet timmar  |
||||

*Exempel 1*

I det här exemplet läggs 10 timmar till den angivna tidsstämpeln:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Och returnerar detta resultat:`"2018-03-15T10:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar detta resultat:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Lägg till ett antal minuter i en tidsstämpel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Minuter*> | Ja | Integer | Det positiva eller negativa antalet minuter som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet minuter |
||||

*Exempel 1*

I det här exemplet läggs 10 minuter till den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Och returnerar detta resultat:`"2018-03-15T00:20:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem minuter från den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Och returnerar detta resultat:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Lägg till en egenskap och dess värde, eller namn-värde-par, i ett JSON-objekt och returnera det uppdaterade objektet. Om egenskapen redan finns vid körning misslyckas funktionen och genererar ett fel.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*Egenskapen*> | Ja | Sträng | Namnet på egenskapen som ska läggas till |
| <*Värde*> | Ja | Alla | Värdet för fastigheten |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat objekt*> | Objekt | Det uppdaterade JSON-objektet med den angivna egenskapen |
||||

Om du vill lägga till en underordnad egenskap i en befintlig egenskap använder du den här syntaxen:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*förälder-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen där du vill lägga till den underordnade egenskapen |
| <*underordnade egendom*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska läggas till |
| <*Värde*> | Ja | Alla | Det värde som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat objekt*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

I det `middleName` här exemplet läggs egenskapen till i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON().](#json) Objektet innehåller redan `firstName` `surName` egenskaperna och. Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exempel 2*

I det `middleName` här exemplet läggs `customerName` den underordnade egenskapen till i den befintliga egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Lägg till ett antal sekunder i en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Sekunder*> | Ja | Integer | Det positiva eller negativa antalet sekunder som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet sekunder  |
||||

*Exempel 1*

I det här exemplet läggs 10 sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Och returnerar detta resultat:`"2018-03-15T00:00:10.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Och returnerar detta resultat:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Lägg till ett antal tidsenheter i en tidsstämpel.
Se även [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*tidUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet tidsenheter  |
||||

*Exempel 1*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Och returnerar detta resultat:`"2018-01-02T00:00:00.0000000Z"`

*Exempel 2*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet med hjälp av det valfria "D"-formatet:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>och

Kontrollera om alla uttryck är sanna.
Returnera sant när alla uttryck är sanna eller returnera falskt när minst ett uttryck är falskt.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*uttryck2*>, ... | Ja | Boolesk | De uttryck som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| sant eller falskt | Boolesk | Returnera sant när alla uttryck är sanna. Returnera falskt när minst ett uttryck är falskt. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna booleska värdena är sanna:

```
and(true, true)
and(false, true)
and(false, false)
```

Och returnerar dessa resultat:

* Första exemplet: Båda uttrycken är `true`sanna, så returnerar .
* Andra exemplet: Ett uttryck är `false`falskt, så returnerar .
* Tredje exemplet: Båda uttrycken är `false`falska, så returnerar .

*Exempel 2*

De här exemplen kontrollerar om alla angivna uttryck är sanna:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Och returnerar dessa resultat:

* Första exemplet: Båda uttrycken är `true`sanna, så returnerar .
* Andra exemplet: Ett uttryck är `false`falskt, så returnerar .
* Tredje exemplet: Båda uttrycken är `false`falska, så returnerar .

<a name="array"></a>

### <a name="array"></a>matris

Returnera en matris från en enda angiven indata.
För flera indata finns i [createArray()](#createArray).

```
array('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen för att skapa en matris |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*värde*>] | Matris | En matris som innehåller den enda angivna indata |
||||

*Exempel*

I det här exemplet skapas en matris från "hello"-strängen:

```
array('hello')
```

Och returnerar detta resultat:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Returnera den base64-kodade versionen för en sträng.

```
base64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Ingångssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*base64-sträng*> | Sträng | Den base64-kodade versionen för indatasträngen |
||||

*Exempel*

I det här exemplet konverteras "hello"-strängen till en base64-kodad sträng:

```
base64('hello')
```

Och returnerar detta resultat:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>bas64TillBinär

Returnera den binära versionen för en base64-kodad sträng.

```
base64ToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den base64-kodade strängen för att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-bas64-sträng*> | Sträng | Den binära versionen för den base64-kodade strängen |
||||

*Exempel*

I det här exemplet konverteras den "aGVsbG8=" base64-kodade strängen till en binär sträng:

```
base64ToBinary('aGVsbG8=')
```

Och returnerar detta resultat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Returnera strängversionen för en base64-kodad sträng, vilket effektivt avkodar base64-strängen.
Använd den här funktionen i stället [för avkodaBase64()](#decodeBase64).
Även om båda funktionerna `base64ToString()` fungerar på samma sätt, är att föredra.

```
base64ToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den base64-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-base64-sträng*> | Sträng | Strängversionen för en base64-kodad sträng |
||||

*Exempel*

I det här exemplet konverteras den "aGVsbG8=" base64-kodade strängen till bara en sträng:

```
base64ToString('aGVsbG8=')
```

Och returnerar detta resultat:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Returnera den binära versionen för en sträng.

```
binary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binärt-för-in-värde*> | Sträng | Den binära versionen för den angivna strängen |
||||

*Exempel*

I det här exemplet konverteras "hello"-strängen till en binär sträng:

```
binary('hello')
```

Och returnerar detta resultat:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Returnera en åtgärds `body` utdata vid körning.
Stenografi `actions('<actionName>').outputs.body`för .
Se [actionBody()](#actionBody) och [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sträng | Utdata `body` från den angivna åtgärden |
||||

*Exempel*

Det här `body` exemplet hämtar `Get user` utdata från Twitter-åtgärden:

```
body('Get_user')
```

Och returnerar detta resultat:

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

Returnera den booleska versionen för ett värde.

```
bool(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Alla | Värdet som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Den booleska versionen för det angivna värdet |
||||

*Exempel*

I de här exemplen konverteras de angivna värdena till booleska värden:

```
bool(1)
bool(0)
```

Och returnerar dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>smälter samman

Returnera det första icke-null-värdet från en eller flera parametrar.
Tomma strängar, tomma matriser och tomma objekt är inte null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object_1*> object_2 <*object_2*>... | Ja | Alla, kan blanda typer | Ett eller flera objekt som ska sökas efter null |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*första-icke-null-objektet*> | Alla | Det första objektet eller värdet som inte är null. Om alla parametrar är null returnerar den här funktionen null. |
||||

*Exempel*

De här exemplen returnerar det första icke-null-värdet från de angivna värdena, eller null när alla värden är null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Och returnerar dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`"hello"`
* Tredje exemplet:`null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Kombinera två eller flera strängar och returnera den kombinerade strängen.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ja | Sträng | Minst två strängar för att kombinera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sträng | Strängen som skapats från de kombinerade indatasträngarna |
||||

*Exempel*

I det här exemplet kombineras strängarna "Hello" och "World":

```
concat('Hello', 'World')
```

Och returnerar detta resultat:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>innehåller

Kontrollera om en samling har ett visst objekt.
Returnera sant när objektet hittas eller returnera falskt när det inte hittas.
Den här funktionen är skiftlägeskänslig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Den här funktionen fungerar specifikt på dessa samlingstyper:

* En *sträng* för att hitta en *delsträng*
* En *matris* för att hitta ett *värde*
* En *ordlista* för att hitta en *nyckel*

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matris eller ordlista | Samlingen för att kontrollera |
| <*Värde*> | Ja | Sträng, matris respektive ordlista | Objektet att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när objektet hittas. Returnera falskt när det inte hittas. |
||||

*Exempel 1*

I det här exemplet kontrolleras strängen "hello world" för delsträngs "världen" och returnerar sant:

```
contains('hello world', 'world')
```

*Exempel 2*

I det här exemplet kontrolleras strängen "hello world" för det underordnade "universum" och returnerar falskt:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>konverteraFrånUtc

Konvertera en tidsstämpel från Universell tidskoordinerad (UTC) till måltidszonen.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på måltidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverteras till måltidszonen |
||||

*Exempel 1*

I det här exemplet konverteras en tidsstämpel till den angivna tidszonen:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Och returnerar detta resultat:`"2018-01-01T00:00:00.0000000"`

*Exempel 2*

I det här exemplet konverteras en tidsstämpel till den angivna tidszonen och formatet:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Och returnerar detta resultat:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>konverteraTimeZone

Konvertera en tidsstämpel från källtidszonen till måltidszonen.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*källaTimeZone*> | Ja | Sträng | Namnet på källtidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på måltidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverteras till måltidszonen |
||||

*Exempel 1*

I det här exemplet konverteras källtidszonen till måltidszonen:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Och returnerar detta resultat:`"2018-01-01T00:00:00.0000000"`

*Exempel 2*

I det här exemplet konverteras en tidszon till den angivna tidszonen och formatet:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Och returnerar detta resultat:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>konverteraToUtc

Konvertera en tidsstämpel från källtidszonen till Universell tidskoordinerad (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*källaTimeZone*> | Ja | Sträng | Namnet på källtidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverterad till UTC |
||||

*Exempel 1*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Och returnerar detta resultat:`"2018-01-01T08:00:00.0000000Z"`

*Exempel 2*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Och returnerar detta resultat:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>skapaArray

Returnera en matris från flera indata.
För enstaka indatamatriser finns i [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*objekt1*>, <*objekt2*>, ... | Ja | Någon, men inte blandad | Minst två objekt för att skapa matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*objekt1*>, <*objekt2*>, ...] | Matris | Matrisen som skapats från alla indataobjekt |
||||

*Exempel*

I det här exemplet skapas en matris från dessa indata:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Och returnerar detta resultat:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Returnera en datauniform resursidentifierare (URI) för en sträng.

```
dataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*data-uri*> | Sträng | Data-URI för indatasträngen |
||||

*Exempel*

I det här exemplet skapas en data-URI för strängen "hej":

```
dataUri('hello')
```

Och returnerar detta resultat:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinär

Returnera den binära versionen för en datauniform resursidentifierare (URI).
Använd den här funktionen i stället [för att avkodaDataUri()](#decodeDataUri).
Även om båda funktionerna `dataUriBinary()` fungerar på samma sätt, är att föredra.

```
dataUriToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | De data URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-data-uri*> | Sträng | Den binära versionen för data URI |
||||

*Exempel*

I det här exemplet skapas en binär version för den här data-URI:ar:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar detta resultat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Returnera strängversionen för en datauniformig resursidentifierare (URI).

```
dataUriToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | De data URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sträng-för-data-uri*> | Sträng | Strängversionen för data-URI |
||||

*Exempel*

I det här exemplet skapas en sträng för den här data-URI:et:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar detta resultat:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Returnera dagen i månaden från en tidsstämpel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i månaden*> | Integer | Dagen i månaden från den angivna tidsstämpeln |
||||

*Exempel*

I det här exemplet returneras numret för dagen i månaden från den här tidsstämpeln:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Och returnerar detta resultat:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Returnera veckodagen från en tidsstämpel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*veckodag*> | Integer | Veckodagen från den angivna tidsstämpeln där söndag är 0, måndag är 1 och så vidare |
||||

*Exempel*

I det här exemplet returneras numret för veckodagen från den här tidsstämpeln:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Och returnerar detta resultat:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Returnera dagen på året från en tidsstämpel.

```
dayOfYear('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag på året*> | Integer | Dagen på året från den angivna tidsstämpeln |
||||

*Exempel*

I det här exemplet returneras antalet på årets dag från den här tidsstämpeln:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Och returnerar detta resultat:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>avkodaBase64

Returnera strängversionen för en base64-kodad sträng, vilket effektivt avkodar base64-strängen.
Överväg att använda [base64ToString()](#base64ToString) i stället `decodeBase64()`för .
Även om båda funktionerna `base64ToString()` fungerar på samma sätt, är att föredra.

```
decodeBase64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den base64-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-base64-sträng*> | Sträng | Strängversionen för en base64-kodad sträng |
||||

*Exempel*

I det här exemplet skapas en sträng för en base64-kodad sträng:

```
decodeBase64('aGVsbG8=')
```

Och returnerar detta resultat:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>avkodaDataUri

Returnera den binära versionen för en datauniform resursidentifierare (URI).
Överväg att använda [dataUriToBinary()](#dataUriToBinary)i stället `decodeDataUri()`för .
Även om båda funktionerna `dataUriToBinary()` fungerar på samma sätt, är att föredra.

```
decodeDataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den data-URI-sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-data-uri*> | Sträng | Den binära versionen för en data-URI-sträng |
||||

*Exempel*

I det här exemplet returneras den binära versionen för den här data-URI:en:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar detta resultat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>avkodaUriComponent

Returnera en sträng som ersätter escape-tecken med avkodade versioner.

```
decodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen med escape-tecknen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-uri*> | Sträng | Den uppdaterade strängen med de avkodade escape-tecknen |
||||

*Exempel*

Det här exemplet ersätter escape-tecknen i den här strängen med avkodade versioner:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Och returnerar detta resultat:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Returnera heltalsresultatet från att dela två tal.
För att få resten resultat, se [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Utdelning*> | Ja | Heltal eller Flyta | Talet som ska divideras med *divisorn* |
| <*Divisor*> | Ja | Heltal eller Flyta | Det tal som delar *utdelningen*, men kan inte vara 0 |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kvot-resultat*> | Integer | Heltalsresultatet från att dividera det första talet med det andra talet |
||||

*Exempel*

Båda exemplen dividerar det första talet med det andra talet:

```
div(10, 5)
div(11, 5)
```

Och returnera detta resultat:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en uri-kodad version (Uniform Resource Identifier) för en sträng genom att ersätta URL-osäkra tecken med escape-tecken.
Överväg att använda [uriComponent()](#uriComponent)i stället `encodeUriComponent()`för .
Även om båda funktionerna `uriComponent()` fungerar på samma sätt, är att föredra.

```
encodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-uri*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
encodeUriComponent('https://contoso.com')
```

Och returnerar detta resultat:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>tomt

Kontrollera om en samling är tom.
Returnera sant när samlingen är tom eller returnera false när den inte är tom.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matris eller objekt | Samlingen för att kontrollera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när samlingen är tom. Returnera falskt när du inte är tom. |
||||

*Exempel*

De här exemplen kontrollerar om de angivna samlingarna är tomma:

```
empty('')
empty('abc')
```

Och returnerar dessa resultat:

* Första exemplet: Skickar en tom sträng, så funktionen returnerar `true`.
* Andra exemplet: Skickar strängen "abc", `false`så funktionen returnerar .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Kontrollera om en sträng slutar med en viss delsträng.
Returnera sant när delsträngen hittas eller returnera falskt när den inte hittas.
Den här funktionen är inte skiftlägeskänslig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen för att kontrollera |
| <*söktext*> | Ja | Sträng | Den avslutande delsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera sant när den avslutande delsträngen hittas. Returnera falskt när det inte hittas. |
||||

*Exempel 1*

I det här exemplet kontrolleras om strängen "hello world" slutar med strängen "världen":

```
endsWith('hello world', 'world')
```

Och returnerar detta resultat:`true`

*Exempel 2*

I det här exemplet kontrolleras om strängen "hello world" slutar med "universum"-strängen:

```
endsWith('hello world', 'universe')
```

Och returnerar detta resultat:`false`

<a name="equals"></a>

### <a name="equals"></a>lika med

Kontrollera om båda värdena, uttrycken eller objekten är likvärdiga.
Returnera sant när båda är likvärdiga eller returnera falskt när de inte är likvärdiga.

```
equals('<object1>', '<object2>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*objekt1*>, <*objekt2*> | Ja | Olika | De värden, uttryck eller objekt som ska jämföras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när båda är likvärdiga. Returnera falskt när den inte är likvärdig. |
||||

*Exempel*

De här exemplen kontrollerar om de angivna ingångarna är likvärdiga.

```
equals(true, 1)
equals('abc', 'abcd')
```

Och returnerar dessa resultat:

* Första exemplet: Båda värdena är `true`likvärdiga, så funktionen returnerar .
* Andra exemplet: Båda värdena är inte `false`likvärdiga, så funktionen returnerar .

<a name="first"></a>

### <a name="first"></a>Första

Returnera det första objektet från en sträng eller matris.

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
| <*första samlingen-objekt*> | Alla | Det första objektet i samlingen |
||||

*Exempel*

De här exemplen hittar det första objektet i dessa samlingar:

```
first('hello')
first(createArray(0, 1, 2))
```

Och returnera dessa resultat:

* Första exemplet:`"h"`
* Andra exemplet:`0`

<a name="float"></a>

### <a name="float"></a>float

Konvertera en strängversion för ett flyttalsnummer till ett verkligt flyttalsnummer.
Du kan bara använda den här funktionen när du skickar anpassade parametrar till en app, till exempel en logikapp eller ett flöde.

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som har ett giltigt flyttalsnummer för att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*float-värde*> | Float (Flyttal) | Flyttalsnumret för den angivna strängen |
||||

*Exempel*

I det här exemplet skapas en strängversion för det här flyttalsnumret:

```
float('10.333')
```

Och returnerar detta resultat:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Returnera en tidsstämpel i angivet format.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterad tidsstämpel*> | Sträng | Den uppdaterade tidsstämpeln i angivet format |
||||

*Exempel*

I det här exemplet konverteras en tidsstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar detta resultat:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formulärDataMultiVärderar

Returnera en matris med värden som matchar ett nyckelnamn i en åtgärds *formulärdata* eller *formulärkodade* utdata.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Åtgärden vars utdata har det nyckelvärde du vill ha |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris-med-nyckel-värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en matris från "Ämnes"-tangentens värde i den angivna åtgärdens formulärdata eller formulärkodade utdata:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Och returnerar ämnestexten i en matris, till exempel:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formulärDataVärde

Returnera ett enda värde som matchar ett nyckelnamn i en åtgärds *formulärdata* eller *formulärkodade* utdata.
Om funktionen hittar mer än en matchning genererar funktionen ett fel.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Åtgärden vars utdata har det nyckelvärde du vill ha |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckelvärde*> | Sträng | Värdet i den angivna nyckeln  |
||||

*Exempel*

I det här exemplet skapas en sträng från "Ämnes"-tangentens värde i den angivna åtgärdens formulärdata eller formulärkodade utdata:

```
formDataValue('Send_an_email', 'Subject')
```

Och returnerar ämnestexten som en sträng, till exempel:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNummer

Returnera ett tal som en sträng som baseras på det angivna formatet.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nummer*> | Ja | Heltal eller Dubbel | Det värde som du vill formatera. |
| <*Format*> | Ja | Sträng | En sammansatt formatsträng som anger det format som du vill använda. För de numeriska formatsträngarna som stöds finns i [Standardnumeriska formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings), som stöds av `number.ToString(<format>, <locale>)`. |
| <*Locale*> | Inga | Sträng | Det språk som ska `number.ToString(<format>, <locale>)`användas som stöds av . Om inget anges är `en-us`standardvärdet . |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterat tal*> | Sträng | Det angivna talet som en sträng i det format som du angav. Du kan casta det `int` `float`här returvärdet till en eller . |
||||

*Exempel 1*

Anta att du vill `1234567890`formatera talet . I det här exemplet formateras talet som strängen "1 234 567 890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*Exempel 2"

Anta att du vill `1234567890`formatera talet . I det här exemplet formateras talet till strängen "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*Exempel 3*

Anta att du vill `17.35`formatera talet . I det här exemplet formateras numret till strängen "$17.35".

```
formatNumber(17.36, 'C2')
```

*Exempel 4*

Anta att du vill `17.35`formatera talet . I det här exemplet formateras numret till strängen "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>fåFutureTime

Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*tidUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den aktuella tidsstämpeln plus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.00000000Z".
I det här exemplet läggs fem dagar till i den tidsstämpeln:

```
getFutureTime(5, 'Day')
```

Och returnerar detta resultat:`"2018-03-06T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.00000000Z".
Det här exemplet lägger till fem dagar och konverterar resultatet till "D"-format:

```
getFutureTime(5, 'Day', 'D')
```

Och returnerar detta resultat:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>fåPastTime

Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska subtraheras |
| <*tidUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den aktuella tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.00000000Z".
Det här exemplet subtraherar fem dagar från den tidsstämpeln:

```
getPastTime(5, 'Day')
```

Och returnerar detta resultat:`"2018-01-27T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.00000000Z".
Det här exemplet subtraherar fem dagar och konverterar resultatet till "D"-format:

```
getPastTime(5, 'Day', 'D')
```

Och returnerar detta resultat:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>större än

Kontrollera om det första värdet är större än det andra värdet.
Returnera sant när det första värdet är mer, eller returnera falskt när mindre.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, Flyt eller Sträng | Det första värdet för att kontrollera om det är större än det andra värdet |
| <*jämförTill*> | Ja | Heltal, Float eller String, respektive | Jämförelsevärdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när det första värdet är större än det andra värdet. Returnera falskt när det första värdet är lika med eller mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större än det andra värdet:

```
greater(10, 5)
greater('apple', 'banana')
```

Och returnera dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>större än eller lika med

Kontrollera om det första värdet är större än eller lika med det andra värdet.
Returnera sant när det första värdet är större eller lika, eller returnera falskt när det första värdet är mindre.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, Flyt eller Sträng | Det första värdet för att kontrollera om det är större än eller lika med det andra värdet |
| <*jämförTill*> | Ja | Heltal, Float eller String, respektive | Jämförelsevärdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när det första värdet är större än eller lika med det andra värdet. Returnera falskt när det första värdet är mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större eller lika med det andra värdet:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Och returnera dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`false`

<a name="guid"></a>

### <a name="guid"></a>Guid

Generera en globalt unik identifierare (GUID) som en sträng, till exempel "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Du kan också ange ett annat format för GUID annat än standardformatet "D", som är 32 siffror avgränsade med bindestreck.

```
guid('<format>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Inga | Sträng | En enda [formatespecificerare](https://msdn.microsoft.com/library/97af8hh4) för det returnerade GUID.A single format specifier for the returned GUID. Som standard är formatet "D", men du kan använda "N", "D", "B", "P" eller "X". |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*GUID-värde*> | Sträng | Ett slumpmässigt genererat GUID |
||||

*Exempel*

Det här exemplet genererar samma GUID, men som 32 siffror, avgränsade med bindestreck och omslutna inom parentes:

```
guid('P')
```

Och returnerar detta resultat:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>om

Kontrollera om ett uttryck är sant eller falskt.
Baserat på resultatet returnerar du ett angivet värde.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck*> | Ja | Boolesk | Uttrycket som ska kontrolleras |
| <*värdeIfTrue*> | Ja | Alla | Värdet som ska returneras när uttrycket är sant |
| <*värdeIfFalse*> | Ja | Alla | Värdet som ska returneras när uttrycket är falskt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*specificerat returvärde*> | Alla | Det angivna värdet som returnerar baserat på om uttrycket är sant eller falskt |
||||

*Exempel*

Det här `"yes"` exemplet returneras eftersom det angivna uttrycket returnerar sant.
Annars returneras `"no"`exemplet:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Returnera startpositionen eller indexvärdet för en delsträng.
Den här funktionen är inte skiftlägeskänslig och index börjar med talet 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen för att hitta |
| <*söktext*> | Ja | Sträng | Delsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*indexvärde*>| Integer | Startpositionen eller indexvärdet för den angivna delsträngen. <p>Om strängen inte hittas returnerar du numret -1. |
||||

*Exempel*

I det här exemplet hittas startindexvärdet för delsträngen "världen" i strängen "hello world":

```
indexOf('hello world', 'world')
```

Och returnerar detta resultat:`6`

<a name="int"></a>

### <a name="int"></a>int

Returnera heltalsversionen för en sträng.

```
int('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*heltalsresultat*> | Integer | Heltalsversionen för den angivna strängen |
||||

*Exempel*

I det här exemplet skapas en heltalsversion för strängen "10":

```
int('10')
```

Och returnerar detta resultat:`10`

<a name="item"></a>

### <a name="item"></a>objekt

När den används i en upprepande åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration.
Du kan också hämta värdena från objektets egenskaper.

```
item()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*aktuell-array-objekt*> | Alla | Det aktuella objektet i matrisen för åtgärdens aktuella iteration |
||||

*Exempel*

Det här `body` exemplet hämtar elementet från det aktuella meddelandet för åtgärden "Send_an_email" i en för varje loops aktuella iteration:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Objekt

Returnera den aktuella artikeln från varje cykel i en för varje slinga.
Använd den här funktionen inuti för varje slinga.

```
items('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*loopName (loopName)*> | Ja | Sträng | Namnet på för varje slinga |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Objekt*> | Alla | Artikeln från den aktuella cykeln i den angivna för varje slinga |
||||

*Exempel*

Det här exemplet hämtar det aktuella objektet från den angivna för varje slinga:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndex

Returnera indexvärdet för den aktuella iterationen inuti en Till-loop. Du kan använda den här funktionen inuti kapslade tills-loopar. 

```
iterationIndexes('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*loopName (loopName)*> | Ja | Sträng | Namnet på till-loopen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Index*> | Integer | Indexvärdet för den aktuella iterationen inuti den angivna tills-loopen | 
|||| 

*Exempel* 

Det här exemplet skapar en räknarvariabel och ökar variabeln med en under varje iteration i en Till-slinga tills räknarvärdet når fem. Exemplet skapar också en variabel som spårar det aktuella indexet för varje iteration. I till-loopen, under varje iteration, ökar exemplet räknaren och tilldelar sedan räknarvärdet till det aktuella indexvärdet och ökar sedan räknaren. Du kan när som helst bestämma det aktuella iterationsnumret genom att hämta det aktuella indexvärdet.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Returnera typvärdet eller objektet för JavaScript-objekt (JSON) för en sträng eller XML.

```
json('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng eller XML | Den sträng eller XML som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*JSON-resultat*> | JSON-inbyggt text eller objekt | Det inbyggda JSON-värdet eller objektet för den angivna strängen eller XML-koden. Om strängen är null returnerar funktionen ett tomt objekt. |
||||

*Exempel 1*

I det här exemplet konverteras strängen till JSON-värdet:

```
json('[1, 2, 3]')
```

Och returnerar detta resultat:`[1, 2, 3]`

*Exempel 2*

I det här exemplet konverteras strängen till JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Och returnerar detta resultat:

```
{
  "fullName": "Sophia Owen"
}
```

*Exempel 3*

I det här exemplet konverteras xml-koden till JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Och returnerar detta resultat:

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

### <a name="intersection"></a>Korsningen

Returnera en samling som *bara* har de vanliga objekten i de angivna samlingarna.
Om du vill visa resultatet måste ett objekt visas i alla samlingar som skickas till den här funktionen.
Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Ja | Matris eller objekt, men inte båda | Samlingarna där du *bara* vill ha de vanliga objekten |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*vanliga objekt*> | Matris respektive objekt | En samling som bara har de gemensamma objekten i de angivna samlingarna |
||||

*Exempel*

I det här exemplet hittas de vanliga objekten i dessa matriser:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Och returnerar en matris med *endast* följande objekt:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Returnera en sträng som har alla objekt från en matris och som har varje tecken avgränsat med en *avgränsare*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Matrisen som har de objekt som ska sammanfogas |
| <*Avgränsare*> | Ja | Sträng | Avgränsaren som visas mellan varje tecken i den resulterande strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*char1*><*avgränsare*><*char2*><*avgränsare*>... | Sträng | Den resulterande strängen som skapats från alla objekt i den angivna matrisen |
||||

*Exempel*

I det här exemplet skapas en sträng från alla objekt i den här matrisen med det angivna tecknet som avgränsare:

```
join(createArray('a', 'b', 'c'), '.')
```

Och returnerar detta resultat:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Senaste

Returnera det sista objektet från en samling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen var den sista artikeln ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sista samlingsobjektet*> | Sträng respektive matris | Det sista objektet i samlingen |
||||

*Exempel*

De här exemplen hittar det sista objektet i dessa samlingar:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Och returnerar dessa resultat:

* Första exemplet:`"d"`
* Andra exemplet:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Returnera startpositionen eller indexvärdet för den senaste förekomsten av en delsträng.
Den här funktionen är inte skiftlägeskänslig och index börjar med talet 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen för att hitta |
| <*söktext*> | Ja | Sträng | Delsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slutindexvärde*> | Integer | Startpositionen eller indexvärdet för den senaste förekomsten av den angivna delsträngen. <p>Om strängen inte hittas returnerar du numret -1. |
||||

*Exempel*

I det här exemplet hittas startindexvärdet för den senaste förekomsten av delsträngen "världen" i strängen "hello world":

```
lastIndexOf('hello world', 'world')
```

Och returnerar detta resultat:`6`

<a name="length"></a>

### <a name="length"></a>length

Returnera antalet artiklar i en samling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen med de objekt som ska räknas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*längd-eller-antal*> | Integer | Antalet artiklar i samlingen |
||||

*Exempel*

I de här exemplen räknas antalet objekt i dessa samlingar:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Och returnera detta resultat:`4`

<a name="less"></a>

### <a name="less"></a>mindre än

Kontrollera om det första värdet är mindre än det andra värdet.
Returnera sant när det första värdet är mindre eller returnera falskt när det första värdet är mer.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, Flyt eller Sträng | Det första värdet för att kontrollera om mindre än det andra värdet |
| <*jämförTill*> | Ja | Heltal, Float eller String, respektive | Jämförelseobjektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när det första värdet är mindre än det andra värdet. Returnera falskt när det första värdet är lika med eller större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre än det andra värdet.

```
less(5, 10)
less('banana', 'apple')
```

Och returnera dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>mindre än eller lika med

Kontrollera om det första värdet är mindre än eller lika med det andra värdet.
Returnera sant när det första värdet är mindre än eller lika, eller returnera falskt när det första värdet är mer.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, Flyt eller Sträng | Det första värdet för att kontrollera om det är mindre än eller lika med det andra värdet |
| <*jämförTill*> | Ja | Heltal, Float eller String, respektive | Jämförelseobjektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera sant när det första värdet är mindre än eller lika med det andra värdet. Returnera falskt när det första värdet är större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre eller lika med det andra värdet.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Och returnera dessa resultat:

* Första exemplet:`true`
* Andra exemplet:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listaCallbackUrl

Returnera "motringnings-URL: en" som anropar en utlösare eller åtgärd.
Den här funktionen fungerar bara med utlösare och åtgärder för kopplingstyperna **HttpWebhook** och **ApiConnectionWebhook,** men inte typerna **Manuell**, **Återkommande**, **HTTP**och **APIConnection.**

```
listCallbackUrl()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*url för motringning*> | Sträng | Motringnings-URL:en för en utlösare eller åtgärd |
||||

*Exempel*

I det här exemplet visas en exempeladrerings-URL som den här funktionen kan returnera:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Max

Returnera det högsta värdet från en lista eller matris med tal som är inkluderande i båda ändar.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*nummer1*>, <*nummer2*>, ... | Ja | Heltal, Float eller båda | Den uppsättning siffror som du vill ha det högsta värdet av |
| [<*nummer1*>, <*nummer2*>, ...] | Ja | Array - Heltal, Float eller båda | Den matris med tal som du vill ha det högsta värdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*maxvärde*> | Heltal eller Flyta | Det högsta värdet i den angivna matrisen eller uppsättningen med tal |
||||

*Exempel*

De här exemplen får det högsta värdet från nummeruppsättningen och matrisen:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Och returnera detta resultat:`3`

<a name="min"></a>

### <a name="min"></a>min

Returnera det lägsta värdet från en uppsättning tal eller en matris.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*nummer1*>, <*nummer2*>, ... | Ja | Heltal, Float eller båda | Den uppsättning tal som du vill ha det lägsta värdet |
| [<*nummer1*>, <*nummer2*>, ...] | Ja | Array - Heltal, Float eller båda | Den matris med tal som du vill ha det lägsta värdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*min-värde*> | Heltal eller Flyta | Det lägsta värdet i den angivna uppsättningen tal eller angiven matris |
||||

*Exempel*

De här exemplen får det lägsta värdet i uppsättningen med tal och matrisen:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Och returnera detta resultat:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Returnera resten från att dela två siffror.
Mer om du vill få heltalsresultatet finns i [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Utdelning*> | Ja | Heltal eller Flyta | Talet som ska divideras med *divisorn* |
| <*Divisor*> | Ja | Heltal eller Flyta | Det tal som delar *utdelningen*, men kan inte vara 0. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*modulo-resultat*> | Heltal eller Flyta | Resten från att dividera det första talet med det andra talet |
||||

*Exempel*

Det här exemplet dividerar det första talet med det andra talet:

```
mod(3, 2)
```

Och returnera detta resultat:`1`

<a name="mul"></a>

### <a name="mul"></a>mul (mul)

Returnera produkten från att multiplicera två tal.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Heltal eller Flyta | Talet som multipliceras *med multiplicand2* |
| <*multiplicand2*> | Ja | Heltal eller Flyta | Numret som *multiplicand1* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*produkt-resultat*> | Heltal eller Flyta | Produkten från att multiplicera det första talet med det andra talet |
||||

*Exempel*

Dessa exempel flera det första numret med det andra numret:

```
mul(1, 2)
mul(1.5, 2)
```

Och returnera dessa resultat:

* Första exemplet:`2`
* Andra exemplet`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Returnera brödtexten för en viss del i en åtgärds utdata som har flera delar.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på åtgärden som har utdata med flera delar |
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Kroppen*> | Sträng | Kroppen för den angivna delen |
||||

<a name="not"></a>

### <a name="not"></a>inte

Kontrollera om ett uttryck är falskt.
Returnera sant när uttrycket är falskt eller returnera falskt när det är sant.

```json
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck*> | Ja | Boolesk | Uttrycket som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när uttrycket är falskt. Returnera falskt när uttrycket är sant. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(false)
not(true)
```

Och returnera dessa resultat:

* Första exemplet: Uttrycket är falskt, `true`så funktionen returnerar .
* Andra exemplet: Uttrycket är sant, `false`så funktionen returnerar .

*Exempel 2*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Och returnera dessa resultat:

* Första exemplet: Uttrycket är falskt, `true`så funktionen returnerar .
* Andra exemplet: Uttrycket är sant, `false`så funktionen returnerar .

<a name="or"></a>

### <a name="or"></a>eller

Kontrollera om minst ett uttryck är sant.
Returnera sant när minst ett uttryck är sant eller returnera falskt när alla är falska.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*uttryck2*>, ... | Ja | Boolesk | De uttryck som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera sant när minst ett uttryck är sant. Returnera falskt när alla uttryck är falska. |
||||

*Exempel 1*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(true, false)
or(false, false)
```

Och returnera dessa resultat:

* Första exemplet: Minst ett uttryck är sant, så funktionen returnerar `true`.
* Andra exemplet: Båda uttrycken är falska, så funktionen returnerar `false`.

*Exempel 2*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Och returnera dessa resultat:

* Första exemplet: Minst ett uttryck är sant, så funktionen returnerar `true`.
* Andra exemplet: Båda uttrycken är falska, så funktionen returnerar `false`.

<a name="outputs"></a>

### <a name="outputs"></a>Utgångar

Returnera en åtgärds utdata vid körning. Använd den här `actionOutputs()`funktionen i stället `outputs()` för , som matchas i Logic App Designer. Även om båda funktionerna `outputs()` fungerar på samma sätt, är att föredra.

```
outputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName (actionName)*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*Produktionen*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar utdata från Twitter-åtgärden: `Get user`

```
outputs('Get_user')
```

Och returnerar detta resultat:

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

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Returnera värdet för en parameter som beskrivs i arbetsflödesdefinitionen.

```
parameters('<parameterName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*parameterName (parameterName)*> | Ja | Sträng | Namnet på den parameter vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*parameter-värde*> | Alla | Värdet för den angivna parametern |
||||

*Exempel*

Anta att du har detta JSON-värde:

```json
{
  "fullName": "Sophia Owen"
}
```

Det här exemplet hämtar värdet för den angivna parametern:

```
parameters('fullName')
```

Och returnerar detta resultat:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Returnera ett slumpmässigt heltal från ett angivet intervall, som endast är inkluderande i startslutet.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minVärde*> | Ja | Integer | Det lägsta heltalet i intervallet |
| <*Maxvalue*> | Ja | Integer | Heltalet som följer det högsta heltalet i det område som funktionen kan returnera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slumpmässigt resultat*> | Integer | Det slumpmässiga heltalet som returneras från det angivna intervallet |
||||

*Exempel*

Det här exemplet får ett slumpmässigt heltal från det angivna intervallet, exklusive det maximala värdet:

```
rand(1, 5)
```

Och returnerar ett av dessa `1` `2`tal `3`som följd: , , eller`4`

<a name="range"></a>

### <a name="range"></a>Utbud

Returnera en heltalsmatris som startar från ett angivet heltal.

```
range(<startIndex>, <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*startIndex (startIndex)*> | Ja | Integer | Ett heltalsvärde som startar matrisen som det första objektet |
| <*Räkna*> | Ja | Integer | Antalet heltal i matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*intervall-resultat*>] | Matris | Matrisen med heltal från det angivna indexet |
||||

*Exempel*

I det här exemplet skapas en heltalsmatris som startar från det angivna indexet och har det angivna antalet heltal:

```
range(1, 4)
```

Och returnerar detta resultat:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Ersätta

Ersätt en delsträng med den angivna strängen och returnera resultatsträngen. Den här funktionen är skiftlägeskänslig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen för att ersätta |
| <*gammalText*> | Ja | Sträng | Delsträngen för att ersätta |
| <*nytext*> | Ja | Sträng | Ersättningssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad text*> | Sträng | Den uppdaterade strängen efter att ha ersatt delsträngen <p>Om delsträngen inte hittas returnerar du den ursprungliga strängen. |
||||

*Exempel*

I det här exemplet hittas den "gamla" delsträngen i "den gamla strängen" och den "gamla" ersätts med "ny":

```
replace('the old string', 'old', 'new')
```

Och returnerar detta resultat:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Ta bort en egenskap från ett objekt och returnera det uppdaterade objektet. Om egenskapen som du försöker ta bort inte finns returnerar funktionen det ursprungliga objektet.

```
removeProperty(<object>, '<property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet där du vill ta bort en egenskap |
| <*Egenskapen*> | Ja | Sträng | Namnet på egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat objekt*> | Objekt | Det uppdaterade JSON-objektet utan den angivna egenskapen |
||||

Om du vill ta bort en underordnad egenskap från en befintlig egenskap använder du den här syntaxen:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet vars egenskap du vill ta bort |
| <*förälder-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen med den underordnade egenskap som du vill ta bort |
| <*underordnade egendom*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat objekt*> | Objekt | Det uppdaterade JSON-objektet vars underordnade egenskap som du tog bort |
||||

*Exempel 1*

I det här `middleName` exemplet tas egenskapen bort från ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON()](#json) och det uppdaterade objektet returneras:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exempel 2*

I det här `middleName` exemplet tas `customerName` den underordnade egenskapen bort från en överordnad egenskap i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON()](#json) och det uppdaterade objektet returneras:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>resultat

Returnera indata och utdata från alla åtgärder som finns i den `For_each`angivna `Until`scoped-åtgärden, till exempel en , eller `Scope` åtgärd. Den här funktionen är användbar för att returnera resultaten från en misslyckad åtgärd så att du kan diagnostisera och hantera undantag. Mer information finns i [Hämta kontext och resultat för fel](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Sträng | Namnet på den begränsade åtgärd som du vill returnera indata och utdata från alla inre åtgärder |
||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*matrisobjekt*> | Matrisobjekt | En matris som innehåller matriser med indata och utdata från varje åtgärd som visas i den angivna scoped-åtgärden |
||||

*Exempel*

I det här exemplet returneras indata och utdata från varje `For_each` iteration av `result()` en `Compose` HTTP-åtgärd inuti som finns i en loop med hjälp av funktionen i åtgärden:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Så här kan den returnerade matrisen `outputs` se ut där det yttre objektet innehåller indata `For_each` och utdata från varje iteration av åtgärderna i åtgärden.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>Setproperty

Ange värdet för JSON-objektets egenskap och returnera det uppdaterade objektet. Om egenskapen som du försöker ange inte finns läggs egenskapen till i objektet. Om du vill lägga till en ny egenskap använder du funktionen [addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | Det JSON-objekt vars egenskap du vill ange |
| <*Egenskapen*> | Ja | Sträng | Namnet på den befintliga eller nya egenskapen som ska anges |
| <*Värde*> | Ja | Alla | Det värde som ska anges för den angivna egenskapen |
|||||

Om du vill ange den underordnade egenskapen i ett underordnat objekt använder du ett kapslat `setProperty()` anrop i stället. Annars returnerar funktionen bara det underordnade objektet som utdata.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | Det JSON-objekt vars egenskap du vill ange |
| <*förälder-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen med den underordnade egenskap som du vill ange |
| <*underordnade egendom*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska anges |
| <*Värde*> | Ja | Alla | Det värde som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat objekt*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

I det `surName` här exemplet anges egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exempel 2*

I det `surName` här exemplet `customerName` anges den underordnade egenskapen för den överordnade egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>hoppa över

Ta bort objekt från framsidan av en samling och returnera *alla andra* objekt.

```
skip([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Den samling vars objekt du vill ta bort |
| <*Räkna*> | Ja | Integer | Ett positivt heltal för antalet objekt som ska tas bort framtill |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<> med *>]* | Matris | Den uppdaterade samlingen efter att de angivna objekten har tagit bort |
||||

*Exempel*

I det här exemplet tas ett objekt, talet 0, bort från framsidan av den angivna matrisen:

```
skip(createArray(0, 1, 2, 3), 1)
```

Och returnerar den här matrisen med de återstående objekten:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, baserat på det angivna avgränsaren i den ursprungliga strängen.

```
split('<text>', '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska separeras i delsträngar baserat på den angivna avgränsaren i den ursprungliga strängen |
| <*Avgränsare*> | Ja | Sträng | Tecknet i den ursprungliga strängen som ska användas som avgränsare |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*delsträng1*>,<*delsträng2*>,...] | Matris | En matris som innehåller delsträngar från den ursprungliga strängen, avgränsad med kommatecken |
||||

*Exempel*

I det här exemplet skapas en matris med delsträngar från den angivna strängen baserat på det angivna tecknet som avgränsare:

```
split('a_b_c', '_')
```

Och returnerar den här matrisen som resultat:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Returnera början av dagen för en tidsstämpel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den angivna tidsstämpeln men börjar vid nolltimmarsmarkeringen för dagen |
||||

*Exempel*

I det här exemplet hittas början av dagen för den här tidsstämpeln:

```
startOfDay('2018-03-15T13:30:30Z')
```

Och returnerar detta resultat:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Returnera början av timmen för en tidsstämpel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den angivna tidsstämpeln men börjar vid nollminutmärket för timmen |
||||

*Exempel*

I det här exemplet hittas början av timmen för den här tidsstämpeln:

```
startOfHour('2018-03-15T13:30:30Z')
```

Och returnerar detta resultat:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Returnera början av månaden för en tidsstämpel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den angivna tidsstämpeln men med början den första dagen i månaden vid nolltimmarsstrecket |
||||

*Exempel*

I det här exemplet returneras början av månaden för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar detta resultat:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontrollera om en sträng börjar med en viss delsträng.
Returnera sant när delsträngen hittas eller returnera falskt när den inte hittas.
Den här funktionen är inte skiftlägeskänslig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen för att kontrollera |
| <*söktext*> | Ja | Sträng | Startsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera sant när startundersträngningen hittas. Returnera falskt när det inte hittas. |
||||

*Exempel 1*

I det här exemplet kontrolleras om strängen "hello world" börjar med understrängen "hello":

```
startsWith('hello world', 'hello')
```

Och returnerar detta resultat:`true`

*Exempel 2*

I det här exemplet kontrolleras om strängen "hello world" börjar med delsträngen "hälsningar":

```
startsWith('hello world', 'greetings')
```

Och returnerar detta resultat:`false`

<a name="string"></a>

### <a name="string"></a>sträng

Returnera strängversionen för ett värde.

```
string(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Alla | Värdet som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*strängvärde*> | Sträng | Strängversionen för det angivna värdet |
||||

*Exempel 1*

I det här exemplet skapas strängversionen för det här numret:

```
string(10)
```

Och returnerar detta resultat:`"10"`

*Exempel 2*

I det här exemplet skapas en sträng för det angivna\\JSON-objektet och omvänt snedstreck ( ) som ett escape-tecken för dubbelterrakttecknet (").

```
string( { "name": "Sophie Owen" } )
```

Och returnerar detta resultat:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Returnera resultatet från att subtrahera det andra talet från det första talet.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minuend ()*> | Ja | Heltal eller Flyta | Det tal som *subtrahend* |
| <*subtrahend (subtrahend)*> | Ja | Heltal eller Flyta | Talet som ska subtraheras från *minuend* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Resultatet*> | Heltal eller Flyta | Resultatet av att subtrahera det andra talet från det första talet |
||||

*Exempel*

Det här exemplet subtraherar det andra talet från det första talet:

```
sub(10.3, .3)
```

Och returnerar detta resultat:`10`

<a name="substring"></a>

### <a name="substring"></a>Delsträng

Returnera tecken från en sträng, med början från den angivna positionen eller index.
Indexvärden börjar med talet 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen vars tecken du vill använda |
| <*startIndex (startIndex)*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som startposition eller indexvärde |
| <*Längd*> | Ja | Integer | Ett positivt antal tecken som du vill ha i delsträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delsträngsresultat*> | Sträng | En delsträng med angivet antal tecken, med början vid den angivna indexpositionen i källsträngen |
||||

*Exempel*

I det här exemplet skapas en delsträng med fem tecken från den angivna strängen, med början från indexvärdet 6:

```
substring('hello world', 6, 5)
```

Och returnerar detta resultat:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtraheraFrånTime

Subtrahera ett antal tidsenheter från en tidsstämpel.
Se även [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska subtraheras |
| <*tidUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Och returnerar detta resultat:`"2018-01-01T00:00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar detta resultat med hjälp av det valfria "D"-formatet:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>Ta

Returnera objekt från framsidan av en samling.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen vars objekt du vill ha |
| <*Räkna*> | Ja | Integer | Ett positivt heltal för det antal objekt som du vill ha framifrån |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delmängd*> eller [<*delmängd*>] | Sträng respektive matris | En sträng eller matris som har det angivna antalet objekt som tagits från framsidan av den ursprungliga samlingen |
||||

*Exempel*

De här exemplen får det angivna antalet objekt från framsidan av dessa samlingar:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Och returnera dessa resultat:

* Första exemplet:`"abc"`
* Andra exemplet:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Fästingar

Returnera `ticks` egenskapsvärdet för en angiven tidsstämpel.
En *fästing* är ett intervall på 100 nanosecond.

```
ticks('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen för en tidsstämpel |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*fästingnummer*> | Integer | Antalet fästingar sedan den angivna tidsstämpeln |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower (TillLågare)

Returnera en sträng i gemener format. Om ett tecken i strängen inte har en gemen version förblir det tecknet oförändrat i den returnerade strängen.

```
toLower('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska returneras i gemener |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*gemener-text*> | Sträng | Den ursprungliga strängen i gemener |
||||

*Exempel*

I det här exemplet konverteras strängen till gemener:

```
toLower('Hello World')
```

Och returnerar detta resultat:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>tillUpper

Returnera en sträng i versaler. Om ett tecken i strängen inte har en versaler förblir det tecknet oförändrat i den returnerade strängen.

```
toUpper('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska returneras i versaler |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*versaler-text*> | Sträng | Den ursprungliga strängen i versaler |
||||

*Exempel*

I det här exemplet konverteras strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar detta resultat:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Utlösa

Returnera en utlösares utdata vid körning, eller värden från andra JSON-namn- och värdepar, som du kan tilldela ett uttryck.

* Inuti en utlösares indata returnerar den här funktionen utdata från föregående körning.

* I en utlösarens villkor returnerar den här funktionen utdata från den aktuella körningen.

Som standard refererar funktionen till hela utlösarobjektet, men du kan också ange en egenskap vars värde du vill använda.
Dessutom har den här funktionen stenografiversioner tillgängliga, se [triggerOutputs()](#triggerOutputs) och [triggerBody()](#triggerBody).

```
trigger()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*utdata för utlösare*> | Sträng | Utdata från en utlösare vid körning |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody (triggerBody)

Returnera en `body` utlösarutdata vid körning.
Stenografi `trigger().outputs.body`för .
Se [utlösare()](#trigger).

```
triggerBody()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Sträng | Utdata `body` från utlösaren |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Returnera en matris med värden som matchar ett nyckelnamn i en utlösarens *formulärdata* eller *formulärkodade* utdata.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris-med-nyckel-värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en matris från nyckelvärdet "feedUrl" i en RSS-utlösares formulärdata eller formulärkodade utdata:

```
triggerFormDataMultiValues('feedUrl')
```

Och returnerar den här matrisen som ett exempel resultat:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>utlösande DataVärde

Returnera en sträng med ett enda värde som matchar ett nyckelnamn i en utlösarens *formulärdata* eller *formulärkodade* utdata.
Om funktionen hittar mer än en matchning genererar funktionen ett fel.

```
triggerFormDataValue('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckelvärde*> | Sträng | Värdet i den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en sträng från nyckelvärdet "feedUrl" i en RSS-utlösares formulärdata eller formulärkodade utdata:

```
triggerFormDataValue('feedUrl')
```

Och returnerar den här strängen som ett exempelresultat:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Returnera brödtexten för en viss del i en utlösarutgång som har flera delar.

```
triggerMultipartBody(<index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Kroppen*> | Sträng | Brödtexten för den angivna delen i en utlösarens flerdelade utgång |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Returnera en utlösarutdata vid körning eller värden från andra JSON-namn- och värdepar.
Stenografi `trigger().outputs`för .
Se [utlösare()](#trigger).

```
triggerOutputs()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*utdata för utlösare*> | Sträng | Utdata från en utlösare vid körning  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trimma

Ta bort inledande och avslutande blanktecken från en sträng och returnera den uppdaterade strängen.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har det inledande och avslutande blanksteget för att ta bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedText*> | Sträng | En uppdaterad version för den ursprungliga strängen utan inledande eller avslutande blanktecken |
||||

*Exempel*

I det här exemplet tas det inledande och avslutande blanksteget bort från strängen " Hello World ":

```
trim(' Hello World  ')
```

Och returnerar detta resultat:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unionen

Returnera en samling som har *alla* objekt från de angivna samlingarna.
Om du vill visas i resultatet kan ett objekt visas i valfri samling som skickas till den här funktionen. Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Ja | Matris eller objekt, men inte båda | Samlingarna där du vill *ha alla* objekt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdateradInsamling*> | Matris respektive objekt | En samling med alla objekt från de angivna samlingarna - inga dubbletter |
||||

*Exempel*

Det här exemplet hämtar *alla* objekt från dessa samlingar:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Och returnerar detta resultat:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriKompent

Returnera en uri-kodad version (Uniform Resource Identifier) för en sträng genom att ersätta URL-osäkra tecken med escape-tecken.
Använd den här funktionen i stället [för att kodaUriComponent()](#encodeUriComponent).
Även om båda funktionerna `uriComponent()` fungerar på samma sätt, är att föredra.

```
uriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-uri*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
uriComponent('https://contoso.com')
```

Och returnerar detta resultat:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriKompentTillbinären

Returnera den binära versionen för en URI-komponent (uniform resource identifier).

```
uriComponentToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den URI-kodade strängen för att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-kodad-uri*> | Sträng | Den binära versionen för den URI-kodade strängen. Det binära innehållet är base64-kodat `$content`och representerat av . |
||||

*Exempel*

I det här exemplet skapas den binära versionen för den här URI-kodade strängen:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Och returnerar detta resultat:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriKompent TillString

Returnera strängversionen för en URI-kodad sträng (Uniform Resource Identifier) och avkoda den URI-kodade strängen.

```
uriComponentToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den URI-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-uri*> | Sträng | Den avkodade versionen för den URI-kodade strängen |
||||

*Exempel*

I det här exemplet skapas den avkodade strängversionen för den här URI-kodade strängen:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Och returnerar detta resultat:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>UriHost (uriHost)

Returnera `host` värdet för en enhetlig resursidentifierare (URI).

```
uriHost('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `host` vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*värd-värde*> | Sträng | Värdet `host` för den angivna URI |
||||

*Exempel*

I det `host` här exemplet hittas värdet för den här URI:n:

```
uriHost('https://www.localhost.com:8080')
```

Och returnerar detta resultat:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath (uriPath)

Returnera `path` värdet för en enhetlig resursidentifierare (URI).

```
uriPath('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `path` vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*banvärde*> | Sträng | Värdet `path` för den angivna URI:n. Om `path` du inte har något värde returnerar du tecknet "/". |
||||

*Exempel*

I det `path` här exemplet hittas värdet för den här URI:n:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar detta resultat:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Returnera `path` värdena och `query` värdena för en enhetlig resursidentifierare (URI).

```
uriPathAndQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `path` `query` vars och värden du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sökvägsfråga-värde*> | Sträng | `path` Värdena `query` och för den angivna URI:n. Om `path` du inte anger något värde returnerar du tecknet "/". |
||||

*Exempel*

I det `path` här `query` exemplet hittas värdena och för den här URI:n:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar detta resultat:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort (på en)

Returnera `port` värdet för en enhetlig resursidentifierare (URI).

```
uriPort('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `port` vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*port-värde*> | Integer | Värdet `port` för den angivna URI:n. Om `port` du inte anger något värde returnerar du protokollets standardport. |
||||

*Exempel*

I det `port` här exemplet returneras värdet för den här URI:en:

```
uriPort('http://www.localhost:8080')
```

Och returnerar detta resultat:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriquery (uriQuery)

Returnera `query` värdet för en enhetlig resursidentifierare (URI).

```
uriQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `query` vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*frågevärde*> | Sträng | Värdet `query` för den angivna URI |
||||

*Exempel*

I det `query` här exemplet returneras värdet för den här URI:en:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar detta resultat:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriKemi

Returnera `scheme` värdet för en enhetlig resursidentifierare (URI).

```
uriScheme('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI `scheme` vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*systembaserat värde*> | Sträng | Värdet `scheme` för den angivna URI |
||||

*Exempel*

I det `scheme` här exemplet returneras värdet för den här URI:en:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar detta resultat:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow (olikartade)

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Du kan också ange ett annat format med parametern <*format*>.


| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Inga | Sträng | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*aktuell tidsstämpel*> | Sträng | Aktuellt datum och aktuell tid |
||||

*Exempel 1*

Antag att det är den 15 april 2018 klockan 13:00.00.
Det här exemplet hämtar den aktuella tidsstämpeln:

```
utcNow()
```

Och returnerar detta resultat:`"2018-04-15T13:00:00.0000000Z"`

*Exempel 2*

Antag att det är den 15 april 2018 klockan 13:00.00.
Det här exemplet hämtar den aktuella tidsstämpeln med formatet "D":

```
utcNow('D')
```

Och returnerar detta resultat:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variabler

Returnera värdet för en angiven variabel.

```
variables('<variableName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*variableName (variabelNamn)*> | Ja | Sträng | Namnet på den variabel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*variabelt värde*> | Alla | Värdet för den angivna variabeln |
||||

*Exempel*

Anta att det aktuella värdet för variabeln "numItems" är 20.
Det här exemplet hämtar heltalsvärdet för den här variabeln:

```
variables('numItems')
```

Och returnerar detta resultat:`20`

<a name="workflow"></a>

### <a name="workflow"></a>arbetsflöde

Returnera all information om själva arbetsflödet under körning.

```
workflow().<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Inga | Sträng | Namnet på den arbetsflödesegenskap vars värde du vill använda <p>Ett arbetsflödesobjekt har följande egenskaper: **namn,** **typ,** **id,** **plats**och **kör**. Egenskapsvärdet **för körning** är också ett objekt som har dessa egenskaper: **namn,** **typ**och **id**. |
|||||

*Exempel*

I det här exemplet returneras namnet för ett arbetsflödes aktuella körning:

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
| <*Värde*> | Ja | Sträng | Strängen med JSON-objektet för att konvertera <p>JSON-objektet får bara ha en rotegenskap, vilket inte kan vara en matris. <br>Använd omvänt snedstreck\\( ) som ett escape-tecken för det dubbla citattecknet ("). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-version*> | Objekt | Den kodade XML-koden för det angivna strängen eller JSON-objektet |
||||

*Exempel 1*

I det här exemplet skapas XML-versionen för den här strängen, som innehåller ett JSON-objekt:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Och returnerar det här resultatet XML:

```xml
<name>Sophia Owen</name>
```

*Exempel 2*

Anta att du har detta JSON-objekt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

I det här exemplet skapas XML för en sträng som innehåller det här JSON-objektet:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Och returnerar det här resultatet XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Sök efter XML efter noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera de matchande noderna eller värdena. Ett XPath-uttryck, eller bara "XPath", hjälper dig att navigera i en XML-dokumentstruktur så att du kan välja noder eller beräkningsvärden i XML-innehållet.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Ja | Alla | XML-strängen för att söka efter noder eller värden som matchar ett XPath-uttrycksvärde |
| <*Xpath*> | Ja | Alla | XPath-uttrycket som används för att hitta matchande XML-noder eller värden |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-nod*> | XML | En XML-nod när endast en nod matchar det angivna XPath-uttrycket |
| <*Värde*> | Alla | Värdet från en XML-nod när endast ett värde matchar det angivna XPath-uttrycket |
| [<*xml-nod1*> <*xml-nod2*>, ...] </br>ELLER </br>[<*värde1*> <*värde2*>,...] | Matris | En matris med XML-noder eller värden som matchar det angivna XPath-uttrycket |
||||

*Exempel 1*

I det här exemplet `<name></name>` hittas noder som matchar noden i de angivna argumenten och en matris returneras med dessa nodvärden:

`xpath(xml(parameters('items')), '/produce/item/name')`

Här är argumenten:

* Strängen "objekt", som innehåller den här XML:a

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  I exemplet används funktionen [parameters()](#parameters) för att hämta XML-strängen från argumentet "objekt", men måste också konvertera strängen till XML-format med hjälp av funktionen [xml().](#xml)

* Detta XPath-uttryck, som skickas som en sträng:

  `"/produce/item/name"`

Här är resultatmatrisen med `<name></name`de noder som matchar:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exempel 2*

I det här exemplet efter exempel 1 `<count></count>` hittar det här exemplet noder som matchar noden och lägger till dessa nodvärden med `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Och returnerar detta resultat:`30`

*Exempel 3*

I det här exemplet hittar båda uttrycken `<location></location>` noder som matchar noden, i de angivna argumenten, som innehåller XML med ett namnområde. 

> [!NOTE]
>
> Om du arbetar i kodvyn kan du dra ut det dubbla citattecknet\\(") med hjälp av omvänt snedstreck ( ). 
> Du måste till exempel använda escape-tecken när du serialiserar ett uttryck som en JSON-sträng. 
> Men om du arbetar i Logic App Designer eller uttrycksredigerare behöver du inte undgå det dubbla citattecknet eftersom omvänt snedstreck läggs till automatiskt i den underliggande definitionen, till exempel:
> 
> * Kodvy:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Uttrycksredigerare:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> Följande exempel gäller för uttryck som du anger i uttrycksredigeraren.

* *Uttryck 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Uttryck 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Här är argumenten:

* Den här XML-koden, som `xmlns="http://contoso.com"`innehåller namnområdet för XML-dokument, :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Antingen XPath-uttryck här:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Här är den resultatnod `<location></location>` som matchar noden:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exempel 4*

I det här exemplet efter exempel 3 hittar du värdet i `<location></location>` noden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Och returnerar detta resultat:`"Paris"`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [arbetsflödesdefinitionsspråket](../logic-apps/logic-apps-workflow-definition-language.md)
