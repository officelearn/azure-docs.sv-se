---
title: Arbetsflödet Definition Language funktioner - Azure Logic Apps | Microsoft Docs
description: Lär dig mer om de funktioner som du kan använda i logik app arbetsflödesdefinitioner
services: logic-apps
author: ecfan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 0155e35641a0407fe48c4da07400fa188152b0af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Arbetsflödet Definition Language funktioner referens för Azure Logic Apps

Den här artikeln beskrivs de funktioner som du kan använda när du skapar arbetsflöden med [Azure Logikappar](../logic-apps/logic-apps-overview.md). Mer information om logik app definitioner finns [språk i Arbetsflödesdefinitionen för Logikappar i Azure](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Syntaxen för parameterdefinitioner är ett frågetecken (?) som visas när en parameter innebär att parametern valfri. Se exempelvis [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>åtgärd

Returnera den *aktuella* åtgärd effekt vid körning, eller värden från andra JSON namn och värde-par, som du kan tilldela ett uttryck. Den här funktionen refererar till objekt för hela åtgärden som standard, men du kan också ange en egenskap vars värde du vill. Se även [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Du kan använda den `action()` funktionen endast på dessa platser: 

* Den `unsubscribe` -egenskapen för ett webhook-åtgärd så att du kan använda resultatet från ursprungligt `subscribe` begäran
* Den `trackedProperties` egenskapen för en åtgärd
* Den `do-until` loop-villkoret för en åtgärd

```
action()
action().outputs.body.<property> 
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Egenskapen*> | Nej | Sträng | Namn på åtgärd-objektets egenskap vars värde du vill: **namn**, **startTime**, **endTime**, **indata**,  **matar ut**, **status**, **kod**, **trackingId**, och **clientTrackingId**. Du kan hitta dessa egenskaper genom att granska information om en specifik kör historik i Azure-portalen. Mer information finns i [REST API - arbetsflödet kör åtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*utdata*> | Sträng | Utdata från den aktuella åtgärden eller egenskapen | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Returnera en åtgärd `body` utdata vid körning. Snabbformen för `actions('<actionName>').outputs.body`. Se [body()](#body) och [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Namn för åtgärden `body` utdata som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*text-utdata*> | Sträng | Den `body` utdata från den angivna åtgärden | 
|||| 

*Exempel*

Det här exemplet hämtar den `body` utdata från åtgärden Twitter `Get user`: 

```
actionBody('Get_user')
```

Och returnerar resultatet för den här:

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

## <a name="actionoutputs"></a>actionOutputs

Returnera utdata för en åtgärd vid körning. Snabbformen för `actions('<actionName>').outputs`. Se [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Namn för åtgärden utdata som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*Utdata*> | Sträng | Utdata från den angivna åtgärden | 
|||| 

*Exempel*

Det här exemplet hämtar utdata från åtgärden Twitter `Get user`: 

```
actionOutputs('Get_user')
```

Och returnerar resultatet för den här:

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

## <a name="actions"></a>åtgärder

Returnera utdata för en åtgärd i körningen eller värden från andra JSON namn och värde-par, som du kan tilldela ett uttryck. Som standard funktionen refererar till åtgärdsobjektet hela, men du kan också ange en egenskap vars värde som du vill. Shorthand versioner finns [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), och [body()](#body). Den aktuella åtgärden finns i [action()](#action).

> [!NOTE] 
> Tidigare kunde du använda den `actions()` funktion eller `conditions` elementet när du anger att en åtgärd kördes baserat på utdata från en annan åtgärd. Men för att deklarera uttryckligen beroenden mellan åtgärder, måste du nu använda den beroende åtgärden `runAfter` egenskapen. Mer information om den `runAfter` egenskapen finns [fånga och hantera fel med egenskapen runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Namnet för åtgärden objekt vars utdata som du vill  | 
| <*Egenskapen*> | Nej | Sträng | Namn på åtgärd-objektets egenskap vars värde du vill: **namn**, **startTime**, **endTime**, **indata**,  **matar ut**, **status**, **kod**, **trackingId**, och **clientTrackingId**. Du kan hitta dessa egenskaper genom att granska information om en specifik kör historik i Azure-portalen. Mer information finns i [REST API - arbetsflödet kör åtgärder](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*utdata*> | Sträng | Utdata från den angivna åtgärden eller egenskapen | 
|||| 

*Exempel*

Det här exemplet hämtar den `status` egenskapsvärde från Twitter-åtgärden `Get user` vid körning: 

```
actions('Get_user').outputs.body.status 
```

Och returnerar resultatet för den här: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>lägg till

Returnera resultatet från att lägga till två tal.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Ja | Heltal, flyttal, eller blandat | Talen att lägga till | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*resultatet summan*> | Heltal eller flyttal | Resultatet från att lägga till den angivna siffror | 
|||| 

*Exempel*

Det här exemplet lägger till angivna siffror:

```
add(1, 1.5)
```

Och returnerar resultatet för den här: `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>addDays

Lägga till ett antal dagar i en tidsstämpel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*dagar*> | Ja | Integer | Positiva eller negativa antalet dagar att lägga till | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln plus det angivna antalet dagar  | 
|||| 

*Exempel 1*

Det här exemplet lägger till 10 dagar till den angivna tidsstämpeln:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Och returnerar resultatet för den här: `"2018-03-25T00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar resultatet för den här: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

Lägga till flera timmar att en tidsstämpel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Timmar*> | Ja | Integer | Positivt eller negativt tal timmar att lägga till | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln plus angivet antal timmar  | 
|||| 

*Exempel 1*

Det här exemplet lägger till 10 timmar till den angivna tidsstämpeln:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Och returnerar resultatet för den här: `"2018-03-15T10:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar resultatet för den här: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

Lägga till ett antal minuter i en tidsstämpel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*minuter*> | Ja | Integer | Det positiva eller negativa antalet minuter att lägga till | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln plus det angivna antalet minuter | 
|||| 

*Exempel 1*

Det här exemplet lägger till 10 minuter till den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Och returnerar resultatet för den här: `"2018-03-15T00:20:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem minuter från den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Och returnerar resultatet för den här: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

Lägg till en egenskap och dess värde eller namn / värde-par till ett JSON-objekt och returnerar det uppdaterade objektet. Om objektet finns redan vid körning, genereras ett fel.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Objektet*> | Ja | Objekt | JSON-objekt där du vill lägga till en egenskap | 
| <*Egenskapen*> | Ja | Sträng | Namn för egenskapen att lägga till | 
| <*Värdet*> | Ja | Alla | Värdet för egenskapen |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera objekt*> | Objekt | Uppdaterade JSON-objekt med den angivna egenskapen | 
|||| 

*Exempel*

Det här exemplet lägger till den `accountNumber` egenskapen till den `customerProfile` -objekt som konverteras till JSON med den [JSON()](#json) funktion. Funktionen tilldelas ett värde som genereras av den [guid()](#guid) fungerar och returnerar det uppdaterade objektet:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>Lägg_till_sekunder

Lägga till ett antal sekunder i en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Sekunder*> | Ja | Integer | Positivt eller negativt tal sekunder att lägga till | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln plus det angivna antalet sekunder  | 
|||| 

*Exempel 1*

Det här exemplet lägger till 10 sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Och returnerar resultatet för den här: `"2018-03-15T00:00:10.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Och returnerar resultatet för den här: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

Lägga till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter att lägga till | 
| <*timeUnit*> | Ja | Sträng | Tidsenhet för användning med *intervall*: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad”, ”år” | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln plus det angivna antalet tidsenheter  | 
|||| 

*Exempel 1*

Det här exemplet lägger till en dag till den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

Och returnerar resultatet för den här: `"2018-01-02T00:00:00:0000000Z"`

*Exempel 2*

Det här exemplet lägger till en dag till den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet i valfritt format ”D”: `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>och

Kontrollera om alla uttryck utvärderas som true. Returnerar värdet true när alla uttryck utvärderas som true och returnerar false om minst ett uttryck är FALSKT.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Uttryck1*>, <*uttryck2*>,... | Ja | Boolesk | Uttryck för att kontrollera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| True eller false | Boolesk | Returnera värdet true när alla uttryck utvärderas som true. Returnera värdet false när minst ett uttryck är false. | 
|||| 

*Exempel 1*

De här exemplen kontrollera om de angivna booleska värdena är förutsättningar:

```
and(true, true)
and(false, true)
and(false, false)
```

Och returnerar resultaten:

* Det första exemplet: båda uttrycken är sant, så returnerar `true`. 
* Andra exempel: ett uttryck som har värdet false, så returnerar `false`.
* Tredje exemplet: båda uttrycken är falska, så returnerar `false`.

*Exempel 2*

De här exemplen kontrollera om de angivna uttrycken föreligger:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Och returnerar resultaten:

* Det första exemplet: båda uttrycken är sant, så returnerar `true`. 
* Andra exempel: ett uttryck som har värdet false, så returnerar `false`.
* Tredje exemplet: båda uttrycken är falska, så returnerar `false`.

<a name="array"></a>

## <a name="array"></a>matris

Returnera en matris från en enda angivna indata. Flera inmatningar finns [createArray()](#createArray). 

```
array('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen för att skapa en matris | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*värdet*>] | Matris | En matris som innehåller en angiven ingång | 
|||| 

*Exempel*

Det här exemplet skapar en matris av strängen ”hello”:

```
array('hello')
```

Och returnerar resultatet för den här: `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>Base64

Returnera den base64-kodade versionen för en sträng.

```
base64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Indatasträngen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Base64-sträng*> | Sträng | Base64-kodade versionen för den inmatade strängen | 
|||| 

*Exempel*

Det här exemplet konverterar strängen ”hello” till en base64-kodad sträng:

```
base64('hello')
```

Och returnerar resultatet för den här: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Returnera den binära versionen för en base64-kodad sträng.

```
base64ToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den base64-kodad strängen som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*binär för base64-sträng*> | Sträng | Den binära versionen för base64-kodad sträng | 
|||| 

*Exempel*

Det här exemplet konverterar den ”aGVsbG8 =” base64-kodad sträng till en binär sträng:

```
base64ToBinary('aGVsbG8=')
```

Och returnerar resultatet för den här: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Returnera strängversionen för en base64-kodad sträng effektivt avkoda base64-sträng. Använd den här funktionen i stället [decodeBase64()](#decodeBase64). Även om båda funktionerna fungerar på samma sätt `base64ToString()` föredras.

```
base64ToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den base64-kodad strängen att avkoda | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*avkoda-base64-sträng*> | Sträng | Strängversion för en base64-kodad sträng | 
|||| 

*Exempel*

Det här exemplet konverterar den ”aGVsbG8 =” base64-kodad sträng till bara en sträng:

```
base64ToString('aGVsbG8=')
```

Och returnerar resultatet för den här: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>Binär 

Returnera den binära versionen för en sträng.

```
binary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*binär för indata värde*> | Sträng | Den binära versionen för den angivna strängen | 
|||| 

*Exempel*

Det här exemplet konverterar strängen ”hello” till en binär sträng:

```
binary('hello')
```

Och returnerar resultatet för den här: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>brödtext

Returnera en åtgärd `body` utdata vid körning. Snabbformen för `actions('<actionName>').outputs.body`. Se [actionBody()](#actionBody) och [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Namn för åtgärden `body` utdata som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | -----| ----------- | 
| <*text-utdata*> | Sträng | Den `body` utdata från den angivna åtgärden | 
|||| 

*Exempel*

Det här exemplet hämtar den `body` utdata från den `Get user` Twitter-åtgärd: 

```
body('Get_user')
```

Och returnerar resultatet för den här: 

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

## <a name="bool"></a>bool

Returnera booleskt versionen för ett värde.

```
bool(<value>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Alla | Värdet som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Booleskt versionen för det angivna värdet | 
|||| 

*Exempel*

De här exemplen konvertera de angivna värdena till booleska värden: 

```
bool(1)
bool(0)
```

Och returnerar resultaten: 

* Det första exemplet: `true` 
* Andra exempel: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>Slå samman

Returnera det första icke-null-värdet från en eller flera parametrar. Tomma strängar, tomma matriser och tomt-objekt är inte null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>,... | Ja | Eventuella kan blanda typer | Ett eller flera objekt att söka efter null | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*första icke-null-objekt*> | Alla | Det första objektet eller -värde som inte är null. Om alla parametrar är null returnerar funktionen null. | 
|||| 

*Exempel*

De här exemplen returnera det första icke-null-värdet från angivna värden eller null när alla värden är null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Och returnerar resultaten: 

* Det första exemplet: `true` 
* Andra exempel: `"hello"`
* Tredje exempel: `null`

<a name="concat"></a>

## <a name="concat"></a>concat

Kombinera två eller flera strängar och returnera kombinerade strängen. 

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*1*>, <*text2*>,... | Ja | Sträng | Minst två strängar som kombinerar | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Sträng | Strängen som skapas från de kombinerade indatasträng | 
|||| 

*Exempel*

Det här exemplet kombinerar strängar ”Hej” och ”World”:

```
concat('Hello', 'World')
```

Och returnerar resultatet för den här: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>innehåller

Kontrollera om en samling har ett specifikt objekt. Returnerar värdet true när objektet hittas, eller returnera false när gick inte att hitta. Den här funktionen är skiftlägeskänslig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Den här funktionen fungerar särskilt på dessa samlingstyper: 

* En *sträng* att hitta en *delsträngen*
* En *matris* att hitta en *värde*
* En *ordlista* att hitta en *nyckel*

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng eller matrisen ordlista | Samlingen för att kontrollera | 
| <*Värdet*> | Ja | Sträng eller matrisen ordlista respektive | Objektet du vill söka efter | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera sant när artikeln har hittats. Returnera värdet false när gick inte att hitta. |
|||| 

*Exempel 1*

Det här exemplet kontrollerar strängen ”hello world” för delsträngen ”world” och returnerar SANT:

```
contains('hello world', 'world')
```

*Exempel 2*

Det här exemplet kontrollerar strängen ”hello world” för delsträngen ”universum” och returnerar false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

Konvertera en tidsstämpel från Universal Coordinated för tid (UTC) till Målets tidszon.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*destinationTimeZone*> | Ja | Sträng | Namnet för mål-tidszonen. Mer information finns i [tidszon ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Konvertera tidsstämpel*> | Sträng | Tidstämpeln konverteras till Målets tidszon | 
|||| 

*Exempel 1*

Det här exemplet konverterar en tidsstämpel till den angivna tidszonen: 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Och returnerar resultatet för den här: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

Det här exemplet konverterar en tidsstämpel till den angivna tidszonen och format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet för den här: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

Konvertera en tidsstämpel från käll-tidszonen till Målets tidszon.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*sourceTimeZone*> | Ja | Sträng | Namnet för käll-tidszonen. Mer information finns i [tidszon ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | Ja | Sträng | Namnet för mål-tidszonen. Mer information finns i [tidszon ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Konvertera tidsstämpel*> | Sträng | Tidstämpeln konverteras till Målets tidszon | 
|||| 

*Exempel 1*

Det här exemplet konverterar tidszon källan till målet tidszon: 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Och returnerar resultatet för den här: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

Det här exemplet konverterar en tidszon till den angivna tidszonen och format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet för den här: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

Konvertera en tidsstämpel från källtidszonen till universell tid UTC (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*sourceTimeZone*> | Ja | Sträng | Namnet för käll-tidszonen. Mer information finns i [tidszon ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Konvertera tidsstämpel*> | Sträng | Tidstämpeln konverteras till UTC | 
|||| 

*Exempel 1*

Det här exemplet konverterar en tidsstämpel till UTC: 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Och returnerar resultatet för den här: `"2018-01-01T08:00:00.0000000Z"`

*Exempel 2*

Det här exemplet konverterar en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Och returnerar resultatet för den här: `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

Returnera en matris från flera inmatningar. Matriser med en inkommande, se [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt1*>, <*object2*>,... | Ja | Alla, men inte blandat | Minst två artiklar för att skapa matrisen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*Objekt1*>, <*object2*>,...] | Matris | Matrisen som skapas från alla objekt som indata | 
|||| 

*Exempel*

Det här exemplet skapar en matris av dessa indata:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Och returnerar resultatet för den här: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

Returnera en data uniform resource identifier (URI) för en sträng. 

```
dataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*data-uri*> | Sträng | Data-URI för Indatasträngen | 
|||| 

*Exempel*

Det här exemplet skapas en data-URI för strängen ”hello”:

```
dataUri('hello') 
```

Och returnerar resultatet för den här: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Returnera den binära versionen för en data uniform resource identifier (URI). Använd den här funktionen i stället [decodeDataUri()](#decodeDataUri). Även om båda funktionerna fungerar på samma sätt `decodeDataUri()` föredras.

```
dataUriToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Data-URI att konvertera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*binär för data-uri*> | Sträng | Den binära versionen för data-URI | 
|||| 

*Exempel*

Det här exemplet skapar en binär version för den här data-URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet för den här: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

Returnera strängversionen för en data uniform resource identifier (URI).

```
dataUriToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Data-URI att konvertera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*strängen för data-uri*> | Sträng | Strängversion för data-URI | 
|||| 

*Exempel*

Det här exemplet skapar en sträng för den här data-URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet för den här: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>Dag i månaden

Returnera dagen i månaden från en tidsstämpel. 

```
dayOfMonth('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*dag i månaden*> | Integer | Dagen i månaden från den angivna tidsstämpeln | 
|||| 

*Exempel*

Det här exemplet returnerar för dagen i månaden från denna tidstämpel:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Och returnerar resultatet för den här: `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>DayOfWeek

Returnera dagen i veckan från en tidsstämpel.  

```
dayOfWeek('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*dag i veckan*> | Integer | Dag i veckan från den angivna tidsstämpel där är söndag 0, måndag är 1 och så vidare | 
|||| 

*Exempel*

Det här exemplet returnerar för dag i veckan från denna tidstämpel:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Och returnerar resultatet för den här: `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>DayOfYear

Returnera dagen på året från en tidsstämpel. 

```
dayOfYear('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*dag på året*> | Integer | Dag på året från den angivna tidsstämpeln | 
|||| 

*Exempel*

Det här exemplet returnerar antalet dagen på året från denna tidstämpel:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Och returnerar resultatet för den här: `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Returnera strängversionen för en base64-kodad sträng effektivt avkoda base64-sträng. Överväg att använda [base64ToString()](#base64ToString) snarare än `decodeBase64()`. Även om båda funktionerna fungerar på samma sätt `base64ToString()` föredras.

```
decodeBase64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den base64-kodad strängen att avkoda | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*avkoda-base64-sträng*> | Sträng | Strängversion för en base64-kodad sträng | 
|||| 

*Exempel*

Det här exemplet skapar en sträng för en base64-kodad sträng:

```
decodeBase64('aGVsbG8=')
```

Och returnerar resultatet för den här: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Returnera den binära versionen för en data uniform resource identifier (URI). Överväg att använda [dataUriToBinary()](#dataUriToBinary), i stället `decodeDataUri()`. Även om båda funktionerna fungerar på samma sätt `dataUriToBinary()` föredras.

```
decodeDataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Data-URI-strängen att avkoda | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*binär för data-uri*> | Sträng | Den binära versionen för en URI-strängen | 
|||| 

*Exempel*

Det här exemplet returnerar den binära versionen för dessa data URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar resultatet för den här: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeUriComponent

Returnerar en sträng som ersätter escape-tecken med avkodade versioner. 

```
decodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen med escape-tecken för att avkoda | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*avkoda-uri*> | Sträng | Uppdaterade strängen med avkodade escape-tecken | 
|||| 

*Exempel*

Det här exemplet ersätter escape-tecken i strängen med avkodade versioner:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet för den här: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

Returnera heltal resultatet från divisionen av två tal. Om du vill få resten resultat, se [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*täljaren*> | Ja | Heltal eller flyttal | Det tal som du delar med den *divisor* | 
| <*divisor*> | Ja | Heltal eller flyttal | Talet som delar den *täljaren*, men kan inte vara 0 | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*kvoten resultat*> | Integer | Heltal resultatet från att dela upp den första siffran av den andra siffran | 
|||| 

*Exempel*

Båda exempel delar upp den första siffran av den andra siffran:

```
div(10, 5)
div(11, 5)
```

Och returnera resultatet för den här: `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en uniform resource identifier (URI)-kodade version för en sträng genom att ersätta URL-osäkra tecken med escape-tecken. Överväg att använda [uriComponent()](#uriComponent), i stället `encodeUriComponent()`. Även om båda funktionerna fungerar på samma sätt `uriComponent()` föredras.

```
encodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*kodad-uri*> | Sträng | URI-kodad strängen med escape-tecken | 
|||| 

*Exempel*

Det här exemplet skapas en URI-kodad version för den här strängen:

```
encodeUriComponent('https://contoso.com')
```

Och returnerar resultatet för den här: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>tom

Kontrollera om en samling är tom. Returnerar värdet true när samlingen är tom, eller returnera värdet false när det är inte tom.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng, matris eller ett objekt | Samlingen för att kontrollera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera sant när samlingen är tom. Returnera värdet false när det är inte tom. | 
|||| 

*Exempel* 

De här exemplen kontrollera om de angivna samlingarna är tom:

```
empty('')
empty('abc')
```

Och returnerar resultaten: 

* Det första exemplet: skickar en tom sträng så returnerar funktionen `true`. 
* Andra exempel: Överför strängen ”abc”, så returnerar funktionen `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

Kontrollera om en sträng som slutar med en specifik delsträng. Returnerar värdet true när delsträngen hittas, eller returnera false när gick inte att hitta. Den här funktionen är inte skiftlägeskänsligt.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Sträng att söka | 
| <*searchText*> | Ja | Sträng | Den sista delsträngen att hitta | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false  | Boolesk | Returnera värdet true när den sista delsträngen hittas. Returnera värdet false när gick inte att hitta. | 
|||| 

*Exempel 1* 

Det här exemplet kontrollerar om strängen ”hello world” slutar med ”world”-sträng:

```
endsWith('hello world', 'world')
```

Och returnerar resultatet för den här: `true`

*Exempel 2*

Det här exemplet kontrollerar om strängen ”hello world” slutar med ”universum”-sträng:

```
endsWith('hello world', 'universe')
```

Och returnerar resultatet för den här: `false`

<a name="equals"></a>

## <a name="equals"></a>lika med

Kontrollera om värden, uttryck eller objekt är likvärdiga. Returnera sant när båda är likvärdiga eller returnerar false när de inte är likvärdiga.

```
equals('<object1>', '<object2>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt1*>, <*object2*> | Ja | Olika | Värden, uttryck eller objekt som ska jämföras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera sant när båda är likvärdiga. Returnera värdet false när det är inte likvärdig. | 
|||| 

*Exempel*

De här exemplen kontrollera om de angivna indata är likvärdiga. 

```
equals(true, 1)
equals('abc', 'abcd')
```

Och returnerar resultaten: 

* Det första exemplet: båda värdena är likvärdiga, så returnerar funktionen `true`.
* Andra exmaple: båda värdena inte är likvärdiga, returnerar funktionen `false`.

<a name="first"></a>

## <a name="first"></a>första

Returnera det första objektet från en sträng eller en matris.

```
first('<collection>')
first([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng eller matris | Samlingen var du hittar det första objektet |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*första samlingsobjektet*> | Alla | Det första objektet i samlingen | 
|||| 

*Exempel*

Det första objektet finns i dessa samlingar i de här exemplen:

```
first('hello')
first([0, 1, 2])
```

Och returnerar följande resultat: 

* Det första exemplet: `"h"`
* Andra exmaple: `0`

<a name="float"></a>

## <a name="float"></a>flyttal

Konvertera en strängversion för ett flyttal till en faktisk flyttal. Du kan använda den här funktionen när du ange anpassade parametrar till en app, till exempel en logikapp.

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den sträng som har ett giltigt flyttal konvertera |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*flyttal*> | flyttal | Flyttal för den angivna strängen | 
|||| 

*Exempel*

Det här exemplet skapar en strängversion för den här flyttal:

```
float('10.333')
```

Och returnerar resultatet för den här: `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>FormatDateTime

Returnera en tidsstämpel i angivet format.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*omformateras tidsstämpel*> | Sträng | Det angivna formatet uppdaterade tidsstämpeln | 
|||| 

*Exempel*

Det här exemplet konverterar en tidsstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar resultatet för den här: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Returnerar en matris med värden som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulär-kodade* utdata. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Åtgärden vars utdata har värdet för nyckeln som du vill använda | 
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln | 
|||| 

*Exempel* 

Det här exemplet skapar en matris av nyckeln ”ämne” värdet i den angivna åtgärden formulärdata eller formulär-kodade utdata:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

Och returnerar ämnestexten i en matris, till exempel: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Returnerar ett värde som matchar ett nyckelnamn i en åtgärd *formulärdata* eller *formulär-kodade* utdata. Om mer än en matchning hittas i funktionen genererar funktionen ett fel.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Åtgärden vars utdata har värdet för nyckeln som du vill använda | 
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde som du vill |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*nyckel / värde*> | Sträng | Värdet i den angivna nyckeln  | 
|||| 

*Exempel* 

Det här exemplet skapar en sträng från nyckeln ”ämne” värde i den angivna åtgärden formulärdata eller formulär-kodade utdata:  

```
formDataValue('Send_an_email', 'Subject')
```

Och returnerar ämnestexten som en sträng, till exempel: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Returnera aktuell tidsstämpel plus de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter subtrahera | 
| <*timeUnit*> | Ja | Sträng | Tidsenhet för användning med *intervall*: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad”, ”år” | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Aktuell tidsstämpel plus det angivna antalet tidsenheter | 
|||| 

*Exempel 1*

Anta att aktuell tidsstämpel är ”2018-03-01T00:00:00.0000000Z”. Det här exemplet lägger till fem dagar till tidsstämpeln:

```
getFutureTime(5, 'Day')
```

Och returnerar resultatet för den här: `"2018-03-06T00:00:00.0000000Z"`

*Exempel 2*

Anta att aktuell tidsstämpel är ”2018-03-01T00:00:00.0000000Z”. Det här exemplet lägger till fem dagar och konverterar resultatet till ”D”-format:

```
getFutureTime(5, 'Day', 'D')
```

Och returnerar resultatet för den här: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Returnera aktuell tidsstämpel minus angivna enheterna.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter subtrahera | 
| <*timeUnit*> | Ja | Sträng | Tidsenhet för användning med *intervall*: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad”, ”år” | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Aktuell tidsstämpel minus det angivna antalet tidsenheter | 
|||| 

*Exempel 1*

Anta att aktuell tidsstämpel är ”2018-02-01T00:00:00.0000000Z”. Det här exemplet subtraherar fem dagar från tidsstämpeln:

```
getPastTime(5, 'Day')
```

Och returnerar resultatet för den här: `"2018-01-27T00:00:00.0000000Z"`

*Exempel 2*

Anta att aktuell tidsstämpel är ”2018-02-01T00:00:00.0000000Z”. Det här exemplet subtraherar fem dagar och konverterar resultatet till ”D”-format:

```
getPastTime(5, 'Day', 'D')
```

Och returnerar resultatet för den här: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>större

Kontrollera om det första värdet är större än det andra värdet. Returnerar värdet true när det första värdet är mer eller returnera värdet false när mindre.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om det är större än det andra värdet | 
| <*compareTo*> | Ja | Heltal, flyttal eller sträng, respektive | Jämförelsevärde | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera värdet true när det första värdet är större än det andra värdet. Returnera värdet false när det första värdet är lika med eller mindre än det andra värdet. | 
|||| 

*Exempel*

De här exemplen kontrollera om det första värdet är större än det andra värdet:

```
greater(10, 5)
greater('apple', 'banana')
```

Och returnerar följande resultat: 

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Kontrollera om det första värdet är större än eller lika med det andra värdet.
Returnerar värdet true när det första värdet är större eller lika och returnerar false när det första värdet är mindre.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om större än eller lika med det andra värdet | 
| <*compareTo*> | Ja | Heltal, flyttal eller sträng, respektive | Jämförelsevärde | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera värdet true när det första värdet är större än eller lika med det andra värdet. Returnera värdet false när det första värdet är mindre än det andra värdet. | 
|||| 

*Exempel*

De här exemplen kontrollera om det första värdet är större eller lika med än det andra värdet:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Och returnerar följande resultat: 

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="guid"></a>

## <a name="guid"></a>GUID

Generera en globalt unik identifierare (GUID) som en sträng, till exempel ”c2ecc88d-88c8-4096-912c-d6f2e2b138ce”: 

```
guid()
```

Du kan också ange ett annat format för GUID än standardformatet ”D”, vilket är 32 siffror avgränsade med bindestreck.

```
guid('<format>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Format*> | Nej | Sträng | En enda [formatera specificerare](https://msdn.microsoft.com/library/97af8hh4) för returnerade GUID. Formatet är ”D” som standard, men du kan använda ”N”, ”D”, ”B”, ”P” eller ”X”. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*GUID-värde*> | Sträng | Ett slumpmässigt genererat GUID | 
|||| 

*Exempel* 

Det här exemplet genererar samma GUID, men som 32 siffror, avgränsade med bindestreck och omslutna av parenteser: 

```
guid('P')
```

Och returnerar resultatet för den här: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>Om

Kontrollera om ett uttryck är SANT eller FALSKT. Baserat på resultatet returnera ett angivet värde.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*uttryck*> | Ja | Boolesk | Uttryck för att kontrollera | 
| <*värde_om_sant*> | Ja | Alla | Värdet som returneras när uttrycket är SANT | 
| <*värdeomfalskt*> | Ja | Alla | Värdet som returneras när uttrycket är FALSKT | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*angivna returvärde*> | Alla | Det angivna värdet som returnerar baserat på om uttrycket är SANT eller FALSKT | 
|||| 

*Exempel* 

Det här exemplet returnerar `"yes"` eftersom det angivna uttrycket returnerar true. Annars exemplet returnerar `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Returnera startpositionen eller indexvärdet för en understräng. Den här funktionen är inte skiftlägeskänsliga och index som börjar med 0. 

```
indexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Den sträng som har delsträngen att hitta | 
| <*searchText*> | Ja | Sträng | Hitta delsträngen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*index-värde*>| Integer | Positionen eller index startvärdet för den angivna delsträngen. <p>Returnera numret -1 om strängen inte hittas. </br>Returnera numret 0 om strängen är tom. | 
|||| 

*Exempel* 

Det här exemplet hittar index startvärdet för delsträngen ”world” i strängen ”hello world”:

```
indexOf('hello world', 'world')
```

Och returnerar resultatet för den här: `6`

<a name="int"></a>

## <a name="int"></a>int

Returnera heltal versionen för en sträng.

```
int('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*heltal-resultat*> | Integer | Versionen för den angivna strängen heltal | 
|||| 

*Exempel* 

Det här exemplet skapas en heltal version efter strängen ”10”:

```
int('10')
```

Och returnerar resultatet för den här: `10`

<a name="item"></a>

## <a name="item"></a>Objektet

När den används i en upprepande åtgärd över en matris, returnera det aktuella objektet i matrisen under åtgärdens aktuella iteration. Du kan också få värdena från att objektets egenskaper. 

```
item()
```

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*aktuella matristyp*> | Alla | Det aktuella objektet i matrisen för den åtgärden aktuella iteration | 
|||| 

*Exempel* 

Det här exemplet hämtar den `body` element från det aktuella meddelandet för åtgärden ”Send_an_email” i en för varje slinga aktuella iteration:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>objekt

Returnera det aktuella objektet från varje cykel i en för varje slinga. Använd den här funktionen i för varje slinga.

```
items('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ja | Sträng | Namnet för varje slinga | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Objektet*> | Alla | Objekt från den aktuella cykeln i den angivna för varje slingan | 
|||| 

*Exempel* 

Det här exemplet hämtar det aktuella objektet från den angivna för varje slingan:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>JSON

Returnera JavaScript Object Notation (JSON) TYPVÄRDE eller objekt för en sträng eller XML.

```
json('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng eller XML | Strängen eller XML för att konvertera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*JSON-resultat*> | Inbyggd JSON-typ eller ett objekt | JSON-värde för en inbyggd typ eller objekt för den angivna strängen eller XML. Om strängen är null returnerar funktionen ett tomt-objekt. | 
|||| 

*Exempel 1* 

Det här exemplet konverterar den här strängen till JSON-värde:

```
json('[1, 2, 3]')
```

Och returnerar resultatet för den här: `[1, 2, 3]`

*Exempel 2*

Det här exemplet konverterar den här strängen till JSON: 

```
json('{"fullName": "Sophia Owen"}')
```

Och returnerar resultatet för den här:

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

Och returnerar resultatet för den här:

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

## <a name="intersection"></a>skärningspunkten

Returnera en samling som har *endast* vanliga objekt mellan de angivna samlingarna. Ett objekt måste visas i alla samlingar som överförts till den här funktionen ska visas i resultatet. Om ett eller flera objekt har samma namn, visas det sista objektet med det namnet i resultatet.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,... | Ja | Matrisen eller objekt, men inte båda | Samlingar som du vill från *endast* vanliga objekt | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*gemensamma objekt*> | Matris eller ett objekt, respektive | En samling som har gemensamma objekten i de angivna samlingarna | 
|||| 

*Exempel* 

Det här exemplet Sök efter vanliga objekt över dessa matriser:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

Och returnerar en matris med *endast* dessa objekt: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Returnerar en sträng som har alla objekt från en matris och tecknen avgränsade med ett *avgränsare*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Matris | Matrisen som har objekt att ansluta till |  
| <*Avgränsare*> | Ja | Sträng | Avgränsaren som visas mellan varje tecken i strängen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*char1*><*avgränsare*><*char2*><*avgränsare*>... | Sträng | Den resulterande strängen som skapas från alla objekt i den angivna matrisen |
|||| 

*Exempel* 

Det här exemplet skapar en sträng från alla objekt i denna matris med det angivna tecknet som avgränsare:

```
join([a, b, c], '.')
```

Och returnerar resultatet för den här: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>senaste

Returnera det sista objektet från en samling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng eller matris | Samlingen var du hittar det sista objektet | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*senaste samlingsobjektet*> | Sträng eller Matrisindextyp, respektive | Det sista objektet i samlingen | 
|||| 

*Exempel* 

Det sista objektet finns i dessa samlingar i de här exemplen:

```
last('abcd')
last([0, 1, 2, 3])
```

Och returnerar resultaten: 

* Det första exemplet: `"d"`
* Andra exempel: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Returnera slutvärde för positionen eller index för en understräng. Den här funktionen är inte skiftlägeskänsliga och index som börjar med 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Den sträng som har delsträngen att hitta | 
| <*searchText*> | Ja | Sträng | Hitta delsträngen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*avslutas indexvärde*> | Integer | Positionen eller index slutvärdet för den angivna delsträngen. <p>Returnera numret -1 om strängen inte hittas. </br>Returnera numret 0 om strängen är tom. | 
|||| 

*Exempel* 

Det här exemplet hittar sista indexvärdet för delsträngen ”world” i strängen ”hello world”:

```
lastIndexOf('hello world', 'world')
```

Och returnerar resultatet för den här: `10`

<a name="length"></a>

## <a name="length"></a>Längd

Returnera antalet objekt i en samling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng eller matris | Samlingen med de objekt som ska räknas | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*längd eller räknare*> | Integer | Antal objekt i samlingen | 
|||| 

*Exempel*

Dessa exempel räknar antalet objekt i dessa samlingar: 

```
length('abcd')
length([0, 1, 2, 3])
```

Och returnera resultatet för den här: `4`

<a name="less"></a>

## <a name="less"></a>mindre

Kontrollera om det första värdet är mindre än det andra värdet.
Returnerar värdet true när det första värdet är mindre eller returnerar värdet false när det första värdet är mer.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om mindre än det andra värdet | 
| <*compareTo*> | Ja | Heltal, flyttal eller sträng, respektive | Jämförelse-objekt | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnerar true när det första värdet är mindre än det andra värdet. Returnera värdet false när det första värdet är lika med eller större än det andra värdet. | 
|||| 

*Exempel*

De här exemplen kontrollera om det första värdet är mindre än det andra värdet.

```
less(5, 10)
less('banana', 'apple')
```

Och returnerar följande resultat: 

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Kontrollera om det första värdet är mindre än eller lika med det andra värdet.
Returnerar värdet true när det första värdet är mindre än eller lika med eller returnerar värdet false när det första värdet är mer.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Heltal, flyttal eller sträng | Det första värdet att kontrollera om mindre än eller lika med det andra värdet | 
| <*compareTo*> | Ja | Heltal, flyttal eller sträng, respektive | Jämförelse-objekt | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false  | Boolesk | Returnera värdet true när det första värdet är mindre än eller lika med det andra värdet. Returnera värdet false när det första värdet är större än det andra värdet. |  
|||| 

*Exempel*

De här exemplen kontrollera om det första värdet är mindre eller lika med det andra värdet.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Och returnerar följande resultat: 

* Det första exemplet: `true`
* Andra exempel: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Returnera ”återanrop URL: en” som anropar en utlösare eller åtgärd. Den här funktionen fungerar bara med utlösare och åtgärder för den **HttpWebhook** och **ApiConnectionWebhook** connector typer, men inte den **manuell**,  **Återkommande**, **HTTP**, och **APIConnection** typer. 

```
listCallbackUrl()
```

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*motringning-URL*> | Sträng | URL-Adressen för återanrop för en utlösare eller åtgärd |  
|||| 

*Exempel*

Det här exemplet illustrerar en motringning exempel-URL att den här funktionen kan returnera:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

Returnera det högsta värdet från en lista eller en matris med siffror som är inklusiva i båda ändar. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Number1*>, <*number2*>,... | Ja | Heltal, flyttal eller båda | Uppsättning tal som du vill från det högsta värdet | 
| [<*number1*>, <*number2*>,...] | Ja | Matris - heltal, flyttal eller båda | Matris med tal som du vill att det högsta värdet | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för maximalt antal*> | Heltal eller flyttal | Det högsta värdet i den angivna matrisen eller uppsättning tal | 
|||| 

*Exempel* 

De här exemplen hämta det högsta värdet från uppsättningen av siffror och matrisen:

```
max(1, 2, 3)
max([1, 2, 3])
```

Och returnera resultatet för den här: `3`

<a name="min"></a>

## <a name="min"></a>min.

Returnera det lägsta värdet från en uppsättning tal eller en matris.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Number1*>, <*number2*>,... | Ja | Heltal, flyttal eller båda | Uppsättning tal som du vill från det lägsta värdet | 
| [<*number1*>, <*number2*>,...] | Ja | Matris - heltal, flyttal eller båda | Matris med tal som du vill från det lägsta värdet | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för minsta*> | Heltal eller flyttal | Det lägsta värdet i den angivna uppsättningen med tal eller matrisen | 
|||| 

*Exempel* 

De här exemplen hämta det lägsta värdet i uppsättningen av siffror och matrisen:

```
min(1, 2, 3)
min([1, 2, 3])
```

Och returnera resultatet för den här: `1`

<a name="mod"></a>

## <a name="mod"></a>MOD

Returnera resten från att dela två tal. Heltal resultatet finns [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*täljaren*> | Ja | Heltal eller flyttal | Det tal som du delar med den *divisor* | 
| <*divisor*> | Ja | Heltal eller flyttal | Talet som delar den *täljaren*, men kan inte vara 0. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*modulo resultat*> | Heltal eller flyttal | Rest från att dela upp den första siffran av den andra siffran | 
|||| 

*Exempel* 

Det här exemplet dividerar den första siffran av den andra siffran:

```
mod(3, 2)
```

Och returnera resultatet för den här: `1`

<a name="mul"></a>

## <a name="mul"></a>mul

Returnera produkten från multiplicera två tal.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Ja | Heltal eller flyttal | Talet som multipliceras med *multiplicand2* | 
| <*multiplicand2*> | Ja | Heltal eller flyttal | Numret som multiplar *multiplicand1* | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*produkt-resultat*> | Heltal eller flyttal | Produkten från första siffran som det andra talet multipliceras | 
|||| 

*Exempel* 

De här exemplen flera den första siffran av den andra siffran:

```
mul(1, 2)
mul(1.5, 2)
```

Och returnerar följande resultat:

* Det första exemplet: `2`
* Andra exempel `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

Returnera innehållet för en viss del i utdata för en åtgärd som har flera delar.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Ja | Sträng | Namn för den åtgärd som har utdata med flera delar | 
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Brödtext*> | Sträng | Innehållet för den angivna delen | 
|||| 

<a name="not"></a>

## <a name="not"></a>inte

Kontrollera om ett uttryck är false. Returnerar värdet true när uttrycket är FALSKT eller returnerar false om värdet är true.

```
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*uttryck*> | Ja | Boolesk | Uttryck för att kontrollera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera sant när uttrycket är FALSKT. Returnera värdet false när uttrycket är uppfyllt. |  
|||| 

*Exempel 1*

De här exemplen kontrollera om de angivna uttrycken är FALSKT: 

```
not(false)
not(true)
```

Och returnerar följande resultat:

* Det första exemplet: uttrycket är FALSKT, så returnerar funktionen `true`.
* Andra exempel: uttrycket är uppfyllt, så returnerar funktionen `false`.

*Exempel 2*

De här exemplen kontrollera om de angivna uttrycken är FALSKT: 

```
not(equals(1, 2))
not(equals(1, 1))
```

Och returnerar följande resultat:

* Det första exemplet: uttrycket är FALSKT, så returnerar funktionen `true`.
* Andra exempel: uttrycket är uppfyllt, så returnerar funktionen `false`.

<a name="or"></a>

## <a name="or"></a>eller

Kontrollera om minst ett uttryck är sant. Returnerar värdet true när minst ett uttryck är true, eller returnera false när alla är false.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Uttryck1*>, <*uttryck2*>,... | Ja | Boolesk | Uttryck för att kontrollera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false | Boolesk | Returnera sant när minst ett uttryck utvärderas som true. Returnera false om alla uttryck är false. |  
|||| 

*Exempel 1*

De här exemplen kontrollerar om minst ett uttryck är sant:

```
or(true, false)
or(false, false)
```

Och returnerar följande resultat:

* Det första exemplet: minst ett uttryck är sant, så returnerar funktionen `true`.
* Andra exempel: båda uttrycken är falska, så returnerar funktionen `false`.

*Exempel 2*

De här exemplen kontrollerar om minst ett uttryck är sant:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Det första exemplet: minst ett uttryck är sant, så returnerar funktionen `true`.
* Andra exempel: båda uttrycken är falska, så returnerar funktionen `false`.

<a name="parameters"></a>

## <a name="parameters"></a>parameters

Returnera värdet för en parameter som beskrivs i din app logik-definition. 

```
parameters('<parameterName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*parameterName*> | Ja | Sträng | Namnet på parametern vars värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för parameterns*> | Alla | Värdet för den angivna parametern | 
|||| 

*Exempel* 

Anta att du har den här JSON-värde:

```json
{
  "fullName": "Sophia Owen"
}
```

Det här exemplet hämtar värdet för den angivna parametern:

```
parameters('fullName')
```

Och returnerar resultatet för den här: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>SLUMP

Returnera ett slumpmässigt heltal från ett visst intervall är inkluderande endast i början.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*MinValue*> | Ja | Integer | Ett heltal i intervallet | 
| <*MaxValue*> | Ja | Integer | Heltal som följer det största heltalet i intervallet som funktionen kan returnera | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*slumpmässiga resultat*> | Integer | Slumpmässigt heltal som returneras från det angivna intervallet |  
|||| 

*Exempel*

Det här exemplet får ett slumpmässigt heltal från det angivna intervallet, utom det maximala värdet: 

```
rand(1, 5)
```

Och returnerar ett av dessa siffror som ett resultat: `1`, `2`, `3`, eller `4` 

<a name="range"></a>

## <a name="range"></a>intervallet

Returnera en heltalsmatris som startar från en angiven heltal.

```
range(<startIndex>, <count>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*startIndex*> | Ja | Integer | Heltalsvärdet som startar matrisen som det första objektet | 
| <*Antal*> | Ja | Integer | Antal heltal i matrisen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*intervallet resultatet*>] | Matris | Matris med heltal från det angivna indexet |  
|||| 

*Exempel*

Det här exemplet skapas en heltalsmatris som startar från det angivna indexet och har det angivna antalet heltal:

```
range(1, 4)
```

Och returnerar resultatet för den här: `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>Ersätt

Ersätta en understräng med den angivna strängen och returnera resultatet-sträng. Den här funktionen är skiftlägeskänslig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Den sträng som har delsträngen som ska ersättas | 
| <*gammal_text*> | Ja | Sträng | Delsträngen som ska ersätta | 
| <*ny_text*> | Ja | Sträng | Ersättningssträngen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*uppdaterad text*> | Sträng | Uppdaterade strängen när du ersätter delsträngen <p>Om delsträngen inte hittas returneras den ursprungliga strängen. | 
|||| 

*Exempel* 

Det här exemplet söker efter ”gamla” delsträngen i ”gamla strängen” och ersätter ”gamla” med ”nytt”: 

```
replace('the old string', 'old', 'new')
```

Och returnerar resultatet för den här: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Ta bort en egenskap från ett objekt och returnerar det uppdaterade objektet.

```
removeProperty(<object>, '<property>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Objektet*> | Ja | Objekt | JSON-objekt som du vill ta bort en egenskap | 
| <*Egenskapen*> | Ja | Sträng | Namn för egenskapen att ta bort | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera objekt*> | Objekt | Uppdaterade JSON-objekt utan att den angivna egenskapen | 
|||| 

*Exempel*

Det här exemplet tar bort den `"accountLocation"` egenskap från en `"customerProfile"` -objekt som konverteras till JSON med den [JSON()](#json) fungerar och returnerar det uppdaterade objektet:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>setProperty

Ange värdet för egenskapen för ett objekt och returnerar det uppdaterade objektet. Du kan använda den här funktionen för att lägga till en ny egenskap eller [addProperty()](#addProperty) funktion.

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Objektet*> | Ja | Objekt | JSON-objekt som du vill ange egenskapen | 
| <*Egenskapen*> | Ja | Sträng | Namn för den befintliga eller nya egenskapen att ställa in | 
| <*Värdet*> | Ja | Alla | Värdet som angetts för den angivna egenskapen |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera objekt*> | Objekt | Uppdaterade JSON-objekt vars egenskapsuppsättning | 
|||| 

*Exempel*

Det här exemplet anger den `"accountNumber"` -egenskapen i en `"customerProfile"` -objekt som konverteras till JSON med den [JSON()](#json) funktion. Funktionen tilldelas ett värde som genereras av [guid()](#guid) fungerar och returnerar det uppdaterade JSON-objektet:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>Hoppa över

Ta bort objekt från längst fram i en mängd och returnerar *alla andra* objekt.

```
skip([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Matris | Samlingen som du vill ta bort element | 
| <*Antal*> | Ja | Integer | Ett positivt heltal för antal objekt att ta bort längst fram | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*uppdatera samlingen*>] | Matris | Den uppdaterade samlingen efter att ta bort de angivna objekten | 
|||| 

*Exempel*

Det här exemplet tar bort en artikel, hur många 0, från den angivna matrisen framför: 

```
skip([0, 1, 2, 3], 1)
```

Och returnerar denna matris med återstående objekt: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Returnerar en matris som har alla tecken från en sträng och tecknen avgränsade med ett *avgränsare*.

```
split('<text>', '<separator>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Den sträng som innehåller tecken att dela |  
| <*avgränsare*> | Ja | Sträng | Avgränsaren som visas mellan varje tecken i den resulterande matrisen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*char1*><*avgränsare*><*char2*><*avgränsare*>...] | Matris | Den resulterande matrisen som skapas från alla objekt i den angivna strängen |
|||| 

*Exempel* 

Det här exemplet skapar en matris av den angivna strängen, Avgränsa varje tecken med kommatecken som avgränsare:

```
split('abc', ',')
```

Och returnerar resultatet för den här: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Returnera början på dagen för en tidsstämpel. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Den angivna tidsstämpeln men början på noll timmars Markera för dagen | 
|||| 

*Exempel* 

Det här exemplet returnerar början på dagen för denna tidstämpel:

```
startOfDay('2018-03-15T13:30:30Z')
```

Och returnerar resultatet för den här: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Returnera början på timmen för en tidsstämpel. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Den angivna tidsstämpeln men början på noll minut märket för timme | 
|||| 

*Exempel* 

Det här exemplet hittar start för den här tidsstämpel:

```
startOfHour('2018-03-15T13:30:30Z')
```

Och returnerar resultatet för den här: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Returnera början på månaden för en tidsstämpel. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Den angivna tidsstämpeln men startar den första dagen i månaden på noll timmars markering | 
|||| 

*Exempel* 

Det här exemplet returnerar början på månaden för denna tidstämpel:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar resultatet för den här: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>StartsWith

Kontrollera om en sträng som börjar med en specifik delsträng. Returnerar värdet true när delsträngen hittas, eller returnera false när gick inte att hitta. Den här funktionen är inte skiftlägeskänsligt.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Sträng att söka | 
| <*searchText*> | Ja | Sträng | Första sträng att söka efter | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| True eller false  | Boolesk | Returnera värdet true när den första delsträngen hittas. Returnera värdet false när gick inte att hitta. | 
|||| 

*Exempel 1* 

Det här exemplet kontrollerar om strängen ”hello world” börjar med ”hello” delsträngen:

```
startsWith('hello world', 'hello')
```

Och returnerar resultatet för den här: `true`

*Exempel 2*

Det här exemplet kontrollerar om strängen ”hello world” börjar med ”helg” delsträngen:

```
startsWith('hello world', 'greetings')
```

Och returnerar resultatet för den här: `false`

<a name="string"></a>

## <a name="string"></a>sträng

Returnera strängversionen för ett värde.

```
string(<value>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Alla | Värdet som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värde*> | Sträng | Strängversionen för det angivna värdet | 
|||| 

*Exempel 1* 

Det här exemplet skapas den strängversionen för det här numret:

```
string(10)
```

Och returnerar resultatet för den här: `"10"`

*Exempel 2*

Det här exemplet skapar en sträng för det angivna JSON-objektet och använder ett omvänt snedstreck (\\) som escape-tecken för citattecken (”).

```
string( { "name": "Sophie Owen" } )
```

Och returnerar resultatet för den här: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>Sub

Returnera resultatet från att subtrahera det andra talet från det första talet.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras den *subtrahend* | 
| <*subtrahend*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras från den *minuend* | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Resultat*> | Heltal eller flyttal | Resultatet från att subtrahera det andra talet från den första siffran | 
|||| 

*Exempel* 

Det här exemplet subtraherar andra tal från den första:

```
sub(10.3, .3)
```

Och returnerar resultatet för den här: `10`

<a name="substring"></a>

## <a name="substring"></a>delsträngen

Returnera tecken från en sträng från angiven position eller index. Index värden starta med siffran 0. 

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Sträng med tecken som du vill | 
| <*startIndex*> | Ja | Integer | Ett positivt tal för startpositionen eller indexvärde | 
| <*Längd*> | Ja | Integer | Ett positivt antal tecken som du vill använda i delsträngen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*delsträngen resultat*> | Sträng | En understräng med det angivna antalet tecken med början vid position i Källsträngen angivna indexet | 
|||| 

*Exempel* 

Det här exemplet skapas en understräng fem tecken från den angivna strängen från indexvärde 6:

```
substring('hello world', 6, 5)
```

Och returnerar resultatet för den här: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Ta bort ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpel | 
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter subtrahera | 
| <*timeUnit*> | Ja | Sträng | Tidsenhet för användning med *intervall*: ”andra”, ”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad”, ”år” | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Uppdatera tidsstämpel*> | Sträng | Tidstämpeln minus det angivna antalet tidsenheter | 
|||| 

*Exempel 1*

Det här exemplet subtraherar en dag från den här tidsstämpel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

Och returnerar resultatet för den här: `"2018-01-01T00:00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar en dag från den här tidsstämpel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

Och returnerar resultatet i valfritt format ”D”: `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>ta

Returnera objekt från framför en samling. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Samlingen*> | Ja | Sträng eller matris | Mängden med objekt som du vill | 
| <*Antal*> | Ja | Integer | Ett positivt heltal för antal objekt som du vill framifrån | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*delmängd*> eller [<*delmängd*>] | Sträng eller Matrisindextyp, respektive | En sträng eller en matris som har det angivna antalet objekt som tagits från den ursprungliga samlingen framför | 
|||| 

*Exempel*

De här exemplen hämta angivet antal objekt från framför dessa samlingar:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

Och returnerar följande resultat:

* Det första exemplet: `"abc"`
* Andra exempel: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>skalstreck

Returnera den `ticks` egenskapsvärdet för en angiven tidsstämpel. En *skalstreck* är ett intervall om 100 nanosekunder.

```
ticks('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*tidsstämpel*> | Ja | Sträng | Strängen för en tidsstämpel | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*tick-nummer*> | Integer | Antal markeringar eftersom den angivna tidsstämpeln | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Returnera en sträng i gemener format. Om ett tecken i strängen inte har en gemen version, förblir tecknet oförändrad i den returnerade strängen.

```
toLower('<text>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Strängen som ska returnera i gemener format | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*gemena text*> | Sträng | Den ursprungliga strängen i gemener format | 
|||| 

*Exempel* 

Det här exemplet konverterar den här strängen till gemener: 

```
toLower('Hello World')
```

Och returnerar resultatet för den här: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Returnera en sträng i versaler format. Om ett tecken i strängen inte har en versaler version, förblir tecknet oförändrad i den returnerade strängen.

```
toUpper('<text>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Strängen som ska returnera i versaler format | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*versaler text*> | Sträng | Den ursprungliga strängen i versaler format | 
|||| 

*Exempel* 

Det här exemplet konverterar den här strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar resultatet för den här: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>Utlösare

Returnera en utlösare utdata i körningen eller värden från andra JSON namn och värde-par, som du kan tilldela ett uttryck. 

* Den här funktionen returnerar utdata från föregående körning i en utlösare indata. 

* Den här funktionen returnerar utdata från den aktuella körningen inuti en utlösarvillkor. 

Som standard funktionen refererar till hela utlösarobjekt, men du kan också ange en egenskap vars värde som du vill. Dessutom har shorthand-versioner som är tillgängliga för den här funktionen, se [triggerOutputs()](#triggerOutputs) och [triggerBody()](#triggerBody). 

```
trigger()
```

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*utlösaren-utdata*> | Sträng | Utdata från en utlösare vid körning | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Returnera en utlösare `body` utdata vid körning. Snabbformen för `trigger().outputs.body`. Se [trigger()](#trigger). 

```
triggerBody()
```

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*utlösaren-text-utdata*> | Sträng | Den `body` utdata från utlösaren | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Returnerar en matris med värden som matchar ett nyckelnamn i en utlösare *formulärdata* eller *formulär-kodade* utdata. 

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln | 
|||| 

*Exempel* 

Det här exemplet skapar en matris av ”feedUrl” nyckelvärdet i en RSS-utlösare formulärdata eller formulär-kodade utdata: 

```
triggerFormDataMultiValues('feedUrl')
```

Och returnerar denna matris som ett exempel resultat: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Returnerar en sträng med ett enda värde som matchar ett nyckelnamn i en utlösare *formulärdata* eller *formulär-kodade* utdata. Om mer än en matchning hittas i funktionen genererar funktionen ett fel.

```
triggerFormDataValue('<key>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde som du vill |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*nyckel / värde*> | Sträng | Värdet i den angivna nyckeln | 
|||| 

*Exempel* 

Det här exemplet skapar en sträng från ”feedUrl” nyckelvärdet i en RSS-utlösare formulärdata eller formulär-kodade utdata:

```
triggerFormDataValue('feedUrl')
```

Och returnerar den här strängen som ett exempel resultat: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

Returnera innehållet för en viss del i utdata för en utlösare som har flera delar. 

```
triggerMultipartBody(<index>)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill |
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Brödtext*> | Sträng | Innehållet för den angivna del i en utlösare multipart-utdata | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Returnera en utlösare utdata i körningen eller värden från andra JSON namn och värde-par. Snabbformen för `trigger().outputs`. Se [trigger()](#trigger). 

```
triggerOutputs()
```

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*utlösaren-utdata*> | Sträng | Utdata från en utlösare vid körning  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>Rensa

Ta bort inledande och avslutande blanksteg från en sträng och returnera uppdaterade sträng.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ja | Sträng | Den sträng som har den inledande och avslutande blanksteg ska tas bort | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | Sträng | En uppdaterad version för den ursprungliga strängen utan inledande eller efterföljande blanksteg | 
|||| 

*Exempel* 

Det här exemplet tar bort inledande och avslutande blanksteg från strängen ”Hello World”:  

```
trim(' Hello World  ')
```

Och returnerar resultatet för den här: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>Union

Returnera en samling som har *alla* artiklar från de angivna samlingarna. Ett objekt kan visas i en samling som överförts till den här funktionen ska visas i resultatet. Om ett eller flera objekt har samma namn, visas det sista objektet med det namnet i resultatet. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,...  | Ja | Matrisen eller objekt, men inte båda | Samlingar som du vill från *alla* objekt | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Matris eller ett objekt, respektive | En samling med alla objekt från de angivna samlingarna - inga dubbletter | 
|||| 

*Exempel* 

Det här exemplet hämtar *alla* artiklar från dessa samlingar: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

Och returnerar resultatet för den här: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Returnera en uniform resource identifier (URI)-kodade version för en sträng genom att ersätta URL-osäkra tecken med escape-tecken. Använd den här funktionen i stället [encodeUriComponent()](#encodeUriComponent). Även om båda funktionerna fungerar på samma sätt `uriComponent()` föredras.

```
uriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*kodad-uri*> | Sträng | URI-kodad strängen med escape-tecken | 
|||| 

*Exempel*

Det här exemplet skapas en URI-kodad version för den här strängen:

```
uriComponent('https://contoso.com')
```

Och returnerar resultatet för den här: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Returnera den binära versionen för en uniform resource identifier (URI)-komponent.

```
uriComponentToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den URI-kodad strängen som ska konverteras | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*binär-för-kodade-uri*> | Sträng | Den binära versionen för URI-kodad sträng. Binär innehållet är base64-kodade och representeras av `$content`. | 
|||| 

*Exempel*

Det här exemplet skapas den binära versionen för den här URI-kodad sträng: 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet för den här: 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

Returnera strängversionen för en uniform resource identifier (URI)-kodad sträng, effektivt avkodning av URI-kodad sträng.

```
uriComponentToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Den URI-kodad strängen att avkoda | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*avkoda-uri*> | Sträng | Den avkodade versionen för URI-kodad sträng | 
|||| 

*Exempel*

Det här exemplet skapas den avkodade strängversionen för den här URI-kodad sträng: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Och returnerar resultatet för den här: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Returnera den `host` värde för en uniform resource identifier (URI).

```
uriHost('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `host` värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för värden*> | Sträng | Den `host` värde för angiven URI | 
|||| 

*Exempel*

Det här exemplet hittar den `host` värde för den här URI: 

```
uriHost('https://www.localhost.com:8080')
```

Och returnerar resultatet för den här: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Returnera den `path` värde för en uniform resource identifier (URI). 

```
uriPath('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `path` värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för sökväg*> | Sträng | Den `path` värde för angiven URI. Om `path` inte ha ett värde, returnerar tecknet ”/”. | 
|||| 

*Exempel*

Det här exemplet hittar den `path` värde för den här URI: 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet för den här: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Returnera den `path` och `query` värden för en uniform resource identifier (URI).

```
uriPathAndQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `path` och `query` värden som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för sökväg fråga*> | Sträng | Den `path` och `query` värden för angiven URI. Om `path` inte ange ett värde, returnerar tecknet ”/”. | 
|||| 

*Exempel*

Det här exemplet hittar den `path` och `query` värden för den här URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet för den här: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Returnera den `port` värde för en uniform resource identifier (URI).

```
uriPort('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `port` värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*värdet för porten*> | Integer | Den `port` värde för angiven URI. Om `port` inte ange ett värde, returnerar standardporten för protokollet. | 
|||| 

*Exempel*

Det här exemplet returnerar den `port` värde för den här URI:

```
uriPort('http://www.localhost:8080')
```

Och returnerar resultatet för den här: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Returnera den `query` värde för en uniform resource identifier (URI).

```
uriQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `query` värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*läsa värde*> | Sträng | Den `query` värde för angiven URI | 
|||| 

*Exempel*

Det här exemplet returnerar den `query` värde för den här URI: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet för den här: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>UriScheme

Returnera den `scheme` värde för en uniform resource identifier (URI).

```
uriScheme('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*URI: N*> | Ja | Sträng | URI: N vars `scheme` värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*schema-värde*> | Sträng | Den `scheme` värde för angiven URI | 
|||| 

*Exempel*

Det här exemplet returnerar den `scheme` värde för den här URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar resultatet för den här: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Returnera aktuell tidsstämpel. 

```
utcNow('<format>')
```

Alternativt kan du ange ett annat format med den <*format*> parametern. 


| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Format*> | Nej | Sträng | Antingen en [enda Formatspecificeraren](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller en [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpel är [”o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (åååå-MM-ddT:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och information om tidszonen bevaras. | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*aktuell tidsstämpel*> | Sträng | Aktuellt datum och tid | 
|||| 

*Exempel 1*

Anta att idag är 15 April 2018 klockan 13:00:00. Det här exemplet hämtar aktuell tidsstämpel: 

```
utcNow()
```

Och returnerar resultatet för den här: `"2018-04-15T13:00:00.0000000Z"`

*Exempel 2*

Anta att idag är 15 April 2018 klockan 13:00:00. Det här exemplet hämtar aktuell tidsstämpel i valfritt format ”D”:

```
utcNow('D')
```

Och returnerar resultatet för den här: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>variabler

Returnera värdet för en specifik variabel. 

```
variables('<variableName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Variabelnamn*> | Ja | Sträng | Namnet på den variabel vars värde som du vill | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*variabeln värdet*> | Alla | Värdet för den angivna variabeln | 
|||| 

*Exempel*

Anta att det aktuella värdet för en ”numItems” variabel är 20. Det här exemplet hämtar heltalsvärdet för den här variabeln:

```
variables('numItems')
```

Och returnerar resultatet för den här: `20`

<a name="workflow"></a>

## <a name="workflow"></a>arbetsflöde

Returnera alla detaljer om själva arbetsflödet vid körning. 

```
workflow().<property>
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Egenskapen*> | Nej | Sträng | Namnet på egenskapen arbetsflöde vars värde som du vill <p>Ett arbetsflödesobjekt har följande egenskaper: **namn**, **typen**, **id**, **plats**, och **kör**. Den **kör** egenskapsvärdet är också ett objekt som har följande egenskaper: **namn**, **typen**, och **id**. | 
||||| 

*Exempel*

Det här exemplet returnerar namnet för ett arbetsflöde aktuella kör:

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Returnera XML-version för en sträng som innehåller ett JSON-objekt. 

```
xml('<value>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*Värdet*> | Ja | Sträng | Strängen med JSON-objekt för att konvertera <p>JSON-objekt måste ha en enda rot-egenskapen. <br>Använd omvänt snedstreck (\\) som escape-tecken för dubbla citattecken (”). | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*XML-version*> | Objekt | Kodad XML för den angivna strängen eller JSON-objekt | 
|||| 

*Exempel 1*

Det här exemplet skapar XML-version för den här strängen, som innehåller ett JSON-objekt: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

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

Det här exemplet skapar XML för en sträng som innehåller den här JSON-objekt:

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

Och returnerar resultatet för den här XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>XPath

Kontrollera XML-noder eller värden som matchar ett XPath (XML Path Language)-uttryck och returnerar matchande noder eller värden. En XPath-uttryck eller bara ”XPath”, kan du gå en struktur för XML-dokument så att du kan välja noder eller beräkning värden i XML-innehåll.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*XML*> | Ja | Alla | XML-sträng att söka efter noder eller värden som matchar ett värde för XPath-uttryck | 
| <*XPath*> | Ja | Alla | XPath-uttrycket som används för att hitta matchande XML-noder eller värden | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*XML-nod*> | XML | En XML-nod när endast en nod matchar det angivna XPath-uttrycket | 
| <*Värdet*> | Alla | Värdet från en XML-nod när endast ett värde som matchar det angivna XPath-uttrycket | 
| [<*xml-Nod1*>, <*xml-nod 2*>,...] </br>ELLER </br>[<*value1*>, <*value2*>,...] | Matris | En matris med XML-noder eller med värden som matchar det angivna XPath-uttrycket | 
|||| 

*Exempel 1*

Det här exemplet hittar noder som matchar den `<name></name>` nod i de angivna argumenten och returnerar en matris med dessa värden för noden: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Här följer argumenten:

* ”Objekt”-sträng som innehåller den här XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  I exemplet används den [parameters()](#parameters) för att hämta XML-strängen från argumentet ”objekt”, men måste också konvertera strängen XML-format med hjälp av den [xml()](#xml) funktion. 

* Den här XPath-uttryck som skickas som en sträng:

  `"/produce/item/name"`

Här är resultatet matrisen med noder som matchar `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exempel 2*

Efter exempel 1 hittar det här exemplet noder som matchar den `<count></count>` nod och lägger till dessa nodvärden med den `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Och returnerar resultatet för den här: `30`

*Exempel 3*

I det här exemplet båda uttrycken hitta noder som matchar den `<location></location>` nod i de angivna argumenten, bland annat XML med ett namnområde. Uttryck som använder ett omvänt snedstreck (\\) som escape-tecken för dubbla citattecken (”).

* *Uttrycket 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Uttrycket 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Här följer argumenten:

* Den här XML, som innehåller namnområdet för XML-dokument, `xmlns="http://contoso.com"`: 

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

Efter exempel 3 i det här exemplet returnerar värdet i den `<location></location>` nod: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Och returnerar resultatet för den här: `"Paris"`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md)
