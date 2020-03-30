---
title: Uttryck och funktioner i Azure Data Factory
description: Den här artikeln innehåller information om uttryck och funktioner som du kan använda för att skapa datacenter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9ef4b569fd8413d2825374c963fb272dd450cf0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533128"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Uttryck och funktioner i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuell version](control-flow-expression-language-functions.md)

Den här artikeln innehåller information om uttryck och funktioner som stöds av Azure Data Factory. 

## <a name="expressions"></a>Uttryck

JSON-värden i definitionen kan vara litterala eller uttryck som utvärderas vid körning. Ett exempel:  
  
```json
"name": "value"
```

 eller  
  
```json
"name": "@pipeline().parameters.password"
```

Uttryck kan visas var som helst i ett JSON-strängvärde och alltid resultera i ett annat JSON-värde. Om ett JSON-värde är ett uttryck extraheras uttryckets brödtext\@genom att at-sign ( ). Om en litteral sträng behövs \@som börjar med måste \@ \@den flytas med hjälp av . Följande exempel visar hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|"parametrar"|Tecknen "parametrar" returneras.|  
|"parametrar[1]"|Tecknen "parametrar[1]" returneras.|  
|"\@\@"|En 1 teckensträng som\@innehåller ' ' returneras.|  
|" \@"|En 2 teckensträng som \@innehåller ' ' returneras.|  
  
 Uttryck kan också visas inuti strängar med hjälp av en funktion som `@{ ... }`kallas *stränginterpolering* där uttryck är insvept i . Exempel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Med stränginter interpolation är resultatet alltid en sträng. Säg att `myNumber` jag `42` `myString` har `foo`definierat som och som:  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Returnerar `foo` som en sträng.|  
|"\@{pipeline().parameters.myString}"| Returnerar `foo` som en sträng.|  
|"\@pipeline().parameters.myNumber"| Returnerar `42` som ett *tal*.|  
|"\@{pipeline().parameters.myNumber}"| Returnerar `42` som en *sträng*.|  
|"Svaret är: @{pipeline().parameters.myNumber}"| Returnerar `Answer is: 42`strängen .|  
|"\@concat('Svar är: ', sträng(pipeline().parameters.myNumber))"| Returnerar strängen`Answer is: 42`|  
|"Svaret är: \@ \@{pipeline().parameters.myNumber}"| Returnerar `Answer is: @{pipeline().parameters.myNumber}`strängen .|  
  
## <a name="examples"></a>Exempel

### <a name="a-dataset-with-a-parameter"></a>En datauppsättning med en parameter
I följande exempel tar BlobDataset en parameter med namnet **path**. Dess värde används för att ange ett värde för `dataset().path` **egenskapen folderPath** med hjälp av uttrycket: . 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>En pipeline med en parameter
I följande exempel tar pipelinen **indataPath** och **outputPath-parametrar.** **Sökvägen** för den parameteriserade blob-datauppsättningen anges med hjälp av värden för dessa parametrar. Syntaxen som används `pipeline().parameters.parametername`här är: . 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Självstudier
Den här [självstudien](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) går igenom hur du skickar parametrar mellan en pipeline och aktivitet samt mellan aktiviteterna.

  
## <a name="functions"></a>Funktioner

Du kan anropa funktioner i uttryck. Följande avsnitt innehåller information om de funktioner som kan användas i ett uttryck.  

## <a name="string-functions"></a>Strängfunktioner  

Om du vill arbeta med strängar kan du använda dessa strängfunktioner och även vissa [samlingsfunktioner](#collection-functions).
Strängfunktioner fungerar bara på strängar.

| Strängfunktion | Aktivitet |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | Kombinera två eller flera strängar och returnera den kombinerade strängen. |
| [slutarMed](control-flow-expression-language-functions.md#endswith) | Kontrollera om en sträng slutar med den angivna delsträngen. |
| [Guid](control-flow-expression-language-functions.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Returnera startpositionen för en delsträng. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Returnera startpositionen för den sista förekomsten av en delsträng. |
| [Ersätta](control-flow-expression-language-functions.md#replace) | Ersätt en delsträng med den angivna strängen och returnera den uppdaterade strängen. |
| [split](control-flow-expression-language-functions.md#split) | Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, från en större sträng baserat på ett angivet avgränsare i den ursprungliga strängen. |
| [börjarMed](control-flow-expression-language-functions.md#startswith) | Kontrollera om en sträng börjar med en viss delsträng. |
| [Delsträng](control-flow-expression-language-functions.md#substring) | Returnera tecken från en sträng, med början från den angivna positionen. |
| [toLower (TillLågare)](control-flow-expression-language-functions.md#toLower) | Returnera en sträng i gemener format. |
| [tillUpper](control-flow-expression-language-functions.md#toUpper) | Returnera en sträng i versaler. |
| [Trimma](control-flow-expression-language-functions.md#trim) | Ta bort inledande och avslutande blanktecken från en sträng och returnera den uppdaterade strängen. |

## <a name="collection-functions"></a>Insamlingsfunktioner

Om du vill arbeta med samlingar, i allmänhet matriser, strängar och ibland ordlistor, kan du använda dessa samlingsfunktioner.

| Funktionen Samling | Aktivitet |
| ------------------- | ---- |
| [Innehåller](control-flow-expression-language-functions.md#contains) | Kontrollera om en samling har ett visst objekt. |
| [Tom](control-flow-expression-language-functions.md#empty) | Kontrollera om en samling är tom. |
| [Första](control-flow-expression-language-functions.md#first) | Returnera det första objektet från en samling. |
| [Korsningen](control-flow-expression-language-functions.md#intersection) | Returnera en samling som *bara* har de vanliga objekten i de angivna samlingarna. |
| [join](control-flow-expression-language-functions.md#join) | Returnera en sträng som har *alla* objekt från en matris, avgränsade med det angivna tecknet. |
| [Senaste](control-flow-expression-language-functions.md#last) | Returnera det sista objektet från en samling. |
| [Längd](control-flow-expression-language-functions.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Hoppa över](control-flow-expression-language-functions.md#skip) | Ta bort objekt från framsidan av en samling och returnera *alla andra* objekt. |
| [Ta](control-flow-expression-language-functions.md#take) | Returnera objekt från framsidan av en samling. |
| [Unionen](control-flow-expression-language-functions.md#union) | Returnera en samling som har *alla* objekt från de angivna samlingarna. | 

## <a name="logical-functions"></a>Logiska funktioner  

Dessa funktioner är användbara inuti villkor, kan de användas för att utvärdera alla typer av logik.  
  
| Logisk jämförelsefunktion | Aktivitet |
| --------------------------- | ---- |
| [och](control-flow-expression-language-functions.md#and) | Kontrollera om alla uttryck är sanna. |
| [lika med](control-flow-expression-language-functions.md#equals) | Kontrollera om båda värdena är likvärdiga. |
| [större än](control-flow-expression-language-functions.md#greater) | Kontrollera om det första värdet är större än det andra värdet. |
| [större än eller lika med](control-flow-expression-language-functions.md#greaterOrEquals) | Kontrollera om det första värdet är större än eller lika med det andra värdet. |
| [Om](control-flow-expression-language-functions.md#if) | Kontrollera om ett uttryck är sant eller falskt. Baserat på resultatet returnerar du ett angivet värde. |
| [mindre än](control-flow-expression-language-functions.md#less) | Kontrollera om det första värdet är mindre än det andra värdet. |
| [mindre än eller lika med](control-flow-expression-language-functions.md#lessOrEquals) | Kontrollera om det första värdet är mindre än eller lika med det andra värdet. |
| [Inte](control-flow-expression-language-functions.md#not) | Kontrollera om ett uttryck är falskt. |
| [Eller](control-flow-expression-language-functions.md#or) | Kontrollera om minst ett uttryck är sant. |
  
## <a name="conversion-functions"></a>Konverteringsfunktioner  

 Dessa funktioner används för att konvertera mellan var och en av de inbyggda typerna på språket:  
-   sträng
-   heltal
-   float
-   boolean
-   Matriser
-   Ordböcker

| Konverteringsfunktion | Aktivitet |
| ------------------- | ---- |
| [Array](control-flow-expression-language-functions.md#array) | Returnera en matris från en enda angiven indata. För flera indata finns i [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Returnera den base64-kodade versionen för en sträng. |
| [bas64TillBinär](control-flow-expression-language-functions.md#base64ToBinary) | Returnera den binära versionen för en base64-kodad sträng. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Returnera strängversionen för en base64-kodad sträng. |
| [Binära](control-flow-expression-language-functions.md#binary) | Returnera den binära versionen för ett indatavärde. |
| [Bool](control-flow-expression-language-functions.md#bool) | Returnera den booleska versionen för ett indatavärde. |
| [smälter samman](control-flow-expression-language-functions.md#coalesce) | Returnera det första icke-null-värdet från en eller flera parametrar. |
| [skapaArray](control-flow-expression-language-functions.md#createArray) | Returnera en matris från flera indata. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Returnera data-URI för ett indatavärde. |
| [dataUriToBinär](control-flow-expression-language-functions.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Returnera strängversionen för en data-URI. |
| [avkodaBase64](control-flow-expression-language-functions.md#decodeBase64) | Returnera strängversionen för en base64-kodad sträng. |
| [avkodaDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Returnera den binära versionen för en data-URI. |
| [avkodaUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Returnera en sträng som ersätter escape-tecken med avkodade versioner. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecken med escape-tecken. |
| [float](control-flow-expression-language-functions.md#float) | Returnera ett flyttalsnummer för ett indatavärde. |
| [int](control-flow-expression-language-functions.md#int) | Returnera heltalsversionen för en sträng. |
| [Json](control-flow-expression-language-functions.md#json) | Returnera typvärdet eller objektet för JavaScript-objekt (JSON) för en sträng eller XML. |
| [sträng](control-flow-expression-language-functions.md#string) | Returnera strängversionen för ett indatavärde. |
| [uriKompent](control-flow-expression-language-functions.md#uriComponent) | Returnera den URI-kodade versionen för ett indatavärde genom att ersätta URL-osäkra tecken med escape-tecken. |
| [uriKompentTillbinären](control-flow-expression-language-functions.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriKompent TillString](control-flow-expression-language-functions.md#uriComponentToString) | Returnera strängversionen för en URI-kodad sträng. |
| [Xml](control-flow-expression-language-functions.md#xml) | Returnera XML-versionen för en sträng. |
| [Xpath](control-flow-expression-language-functions.md#xpath) | Sök efter XML efter noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera de matchande noderna eller värdena. |

## <a name="math-functions"></a>Matematikfunktioner  
 Dessa funktioner kan användas för båda typerna av tal: **heltal** och **flöten**.  

| Matematiska funktionen | Aktivitet |
| ------------- | ---- |
| [Add](control-flow-expression-language-functions.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](control-flow-expression-language-functions.md#div) | Returnera resultatet från att dela två tal. |
| [Max](control-flow-expression-language-functions.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [Min](control-flow-expression-language-functions.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [Mod](control-flow-expression-language-functions.md#mod) | Returnera resten från att dela två siffror. |
| [mul (mul)](control-flow-expression-language-functions.md#mul) | Returnera produkten från att multiplicera två tal. |
| [Rand](control-flow-expression-language-functions.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [Utbud](control-flow-expression-language-functions.md#range) | Returnera en heltalsmatris som startar från ett angivet heltal. |
| [Sub](control-flow-expression-language-functions.md#sub) | Returnera resultatet från att subtrahera det andra talet från det första talet. |
  
## <a name="date-functions"></a>Datumfunktioner  

| Funktionen Datum eller tid | Aktivitet |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Lägg till ett antal dagar i en tidsstämpel. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Lägg till ett antal timmar i en tidsstämpel. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Lägg till ett antal minuter i en tidsstämpel. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Lägg till ett antal sekunder i en tidsstämpel. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Lägg till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [konverteraFrånUtc](control-flow-expression-language-functions.md#convertFromUtc) | Konvertera en tidsstämpel från Universell tidskoordinerad (UTC) till måltidszonen. |
| [konverteraTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Konvertera en tidsstämpel från källtidszonen till måltidszonen. |
| [konverteraToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konvertera en tidsstämpel från källtidszonen till Universell tidskoordinerad (UTC). |
| [dayOfMonth dagOfMonth dagOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Returnera dagen i månaden komponenten från en tidsstämpel. |
| [dagVecka](control-flow-expression-language-functions.md#dayOfWeek) | Returnera dag i veckan komponenten från en tidsstämpel. |
| [dagiår](control-flow-expression-language-functions.md#dayOfYear) | Returnera dagen för året komponenten från en tidsstämpel. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Returnera datumet från en tidsstämpel. |
| [fåFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna. Se även [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [fåPastTime](control-flow-expression-language-functions.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtraheraFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Returnera början av dagen för en tidsstämpel. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Returnera början av timmen för en tidsstämpel. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Returnera början av månaden för en tidsstämpel. |
| [subtraheraFrånTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtrahera ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [Fästingar](control-flow-expression-language-functions.md#ticks) | Returnera `ticks` egenskapsvärdet för en angiven tidsstämpel. |
| [utcNow (olikartade)](control-flow-expression-language-functions.md#utcNow) | Returnera den aktuella tidsstämpeln som en sträng. |

## <a name="function-reference"></a>Funktionsreferens

I det här avsnittet visas alla tillgängliga funktioner i alfabetisk ordning.

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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Dagar*> | Ja | Integer | Det positiva eller negativa antalet dagar som ska läggas till |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln plus det angivna antalet dagar  |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Timmar*> | Ja | Integer | Det positiva eller negativa antalet timmar som ska läggas till |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln plus det angivna antalet timmar  |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Minuter*> | Ja | Integer | Det positiva eller negativa antalet minuter som ska läggas till |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln plus det angivna antalet minuter |
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

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Lägg till ett antal sekunder i en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Sekunder*> | Ja | Integer | Det positiva eller negativa antalet sekunder som ska läggas till |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln plus det angivna antalet sekunder  |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*tidUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln plus det angivna antalet tidsenheter  |
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
| <*uttryck1*>, <*uttryck2*>, ... | Ja | Boolean | De uttryck som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| sant eller falskt | Boolean | Returnera sant när alla uttryck är sanna. Returnera falskt när minst ett uttryck är falskt. |
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
| <*Värde*> | Ja | String | Strängen för att skapa en matris |
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
| <*Värde*> | Ja | String | Ingångssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*base64-sträng*> | String | Den base64-kodade versionen för indatasträngen |
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
| <*Värde*> | Ja | String | Den base64-kodade strängen för att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-bas64-sträng*> | String | Den binära versionen för den base64-kodade strängen |
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
| <*Värde*> | Ja | String | Den base64-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-base64-sträng*> | String | Strängversionen för en base64-kodad sträng |
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
| <*Värde*> | Ja | String | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binärt-för-in-värde*> | String | Den binära versionen för den angivna strängen |
||||

*Exempel*

I det här exemplet konverteras "hello"-strängen till en binär sträng:

```
binary('hello')
```

Och returnerar detta resultat:

`"0110100001100101011011000110110001101111"`

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
| sant eller falskt | Boolean | Den booleska versionen för det angivna värdet |
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
| <*text1*>, <*text2*>, ... | Ja | String | Minst två strängar för att kombinera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | Strängen som skapats från de kombinerade indatasträngarna |
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
| sant eller falskt | Boolean | Returnera sant när objektet hittas. Returnera falskt när det inte hittas. |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*destinationTimeZone*> | Ja | String | Namnet på måltidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | String | Tidsstämpeln konverteras till måltidszonen |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*källaTimeZone*> | Ja | String | Namnet på källtidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*destinationTimeZone*> | Ja | String | Namnet på måltidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | String | Tidsstämpeln konverteras till måltidszonen |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*källaTimeZone*> | Ja | String | Namnet på källtidszonen. För tidszonsnamn finns i [Microsofts tidszonsindexvärden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tidszonsnamnet. |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | String | Tidsstämpeln konverterad till UTC |
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
| <*Värde*> | Ja | String | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Data-URI för indatasträngen |
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
| <*Värde*> | Ja | String | De data URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-data-uri*> | String | Den binära versionen för data URI |
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
| <*Värde*> | Ja | String | De data URI att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sträng-för-data-uri*> | String | Strängversionen för data-URI |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
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
| <*Värde*> | Ja | String | Den base64-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-base64-sträng*> | String | Strängversionen för en base64-kodad sträng |
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
| <*Värde*> | Ja | String | Den data-URI-sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-data-uri*> | String | Den binära versionen för en data-URI-sträng |
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
| <*Värde*> | Ja | String | Strängen med escape-tecknen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-uri*> | String | Den uppdaterade strängen med de avkodade escape-tecknen |
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
| <*Värde*> | Ja | String | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-uri*> | String | Den URI-kodade strängen med escape-tecken |
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
| sant eller falskt | Boolean | Returnera sant när samlingen är tom. Returnera falskt när du inte är tom. |
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
| <*Text*> | Ja | String | Strängen för att kontrollera |
| <*söktext*> | Ja | String | Den avslutande delsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolean | Returnera sant när den avslutande delsträngen hittas. Returnera falskt när det inte hittas. |
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
| sant eller falskt | Boolean | Returnera sant när båda är likvärdiga. Returnera falskt när den inte är likvärdig. |
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

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | String | Strängen som har ett giltigt flyttalsnummer för att konvertera |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterad tidsstämpel*> | String | Den uppdaterade tidsstämpeln i angivet format |
||||

*Exempel*

I det här exemplet konverteras en tidsstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar detta resultat:`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>fåFutureTime

Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*tidUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Den aktuella tidsstämpeln plus det angivna antalet tidsenheter |
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
| <*tidUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Den aktuella tidsstämpeln minus det angivna antalet tidsenheter |
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
| sant eller falskt | Boolean | Returnera sant när det första värdet är större än det andra värdet. Returnera falskt när det första värdet är lika med eller mindre än det andra värdet. |
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
| sant eller falskt | Boolean | Returnera sant när det första värdet är större än eller lika med det andra värdet. Returnera falskt när det första värdet är mindre än det andra värdet. |
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
| <*Format*> | Inga | String | En enda [formatespecificerare](https://msdn.microsoft.com/library/97af8hh4) för det returnerade GUID.A single format specifier for the returned GUID. Som standard är formatet "D", men du kan använda "N", "D", "B", "P" eller "X". |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*GUID-värde*> | String | Ett slumpmässigt genererat GUID |
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
| <*Uttryck*> | Ja | Boolean | Uttrycket som ska kontrolleras |
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
| <*Text*> | Ja | String | Strängen som har delsträngen för att hitta |
| <*söktext*> | Ja | String | Delsträngen för att hitta |
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
| <*Värde*> | Ja | String | Strängen som ska konverteras |
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
| <*Avgränsare*> | Ja | String | Avgränsaren som visas mellan varje tecken i den resulterande strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*char1*><*avgränsare*><*char2*><*avgränsare*>... | String | Den resulterande strängen som skapats från alla objekt i den angivna matrisen |
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
| <*Text*> | Ja | String | Strängen som har delsträngen för att hitta |
| <*söktext*> | Ja | String | Delsträngen för att hitta |
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
| sant eller falskt | Boolean | Returnera sant när det första värdet är mindre än det andra värdet. Returnera falskt när det första värdet är lika med eller större än det andra värdet. |
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
| sant eller falskt  | Boolean | Returnera sant när det första värdet är mindre än eller lika med det andra värdet. Returnera falskt när det första värdet är större än det andra värdet. |
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

<a name="not"></a>

### <a name="not"></a>inte

Kontrollera om ett uttryck är falskt.
Returnera sant när uttrycket är falskt eller returnera falskt när det är sant.

```json
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck*> | Ja | Boolean | Uttrycket som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolean | Returnera sant när uttrycket är falskt. Returnera falskt när uttrycket är sant. |
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
| <*uttryck1*>, <*uttryck2*>, ... | Ja | Boolean | De uttryck som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolean | Returnera sant när minst ett uttryck är sant. Returnera falskt när alla uttryck är falska. |
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
| <*Text*> | Ja | String | Strängen som har delsträngen för att ersätta |
| <*gammalText*> | Ja | String | Delsträngen för att ersätta |
| <*nytext*> | Ja | String | Ersättningssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad text*> | String | Den uppdaterade strängen efter att ha ersatt delsträngen <p>Om delsträngen inte hittas returnerar du den ursprungliga strängen. |
||||

*Exempel*

I det här exemplet hittas den "gamla" delsträngen i "den gamla strängen" och den "gamla" ersätts med "ny":

```
replace('the old string', 'old', 'new')
```

Och returnerar detta resultat:`"the new string"`

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
| <*Text*> | Ja | String | Strängen som ska separeras i delsträngar baserat på den angivna avgränsaren i den ursprungliga strängen |
| <*Avgränsare*> | Ja | String | Tecknet i den ursprungliga strängen som ska användas som avgränsare |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Den angivna tidsstämpeln men börjar vid nolltimmarsmarkeringen för dagen |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Den angivna tidsstämpeln men börjar vid nollminutmärket för timmen |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Den angivna tidsstämpeln men med början den första dagen i månaden vid nolltimmarsstrecket |
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
| <*Text*> | Ja | String | Strängen för att kontrollera |
| <*söktext*> | Ja | String | Startsträngen för att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolean | Returnera sant när startundersträngningen hittas. Returnera falskt när det inte hittas. |
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
| <*strängvärde*> | String | Strängversionen för det angivna värdet |
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
| <*Text*> | Ja | String | Strängen vars tecken du vill använda |
| <*startIndex (startIndex)*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som startposition eller indexvärde |
| <*Längd*> | Ja | Integer | Ett positivt antal tecken som du vill ha i delsträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delsträngsresultat*> | String | En delsträng med angivet antal tecken, med början vid den angivna indexpositionen i källsträngen |
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
| <*Tidsstämpel*> | Ja | String | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska subtraheras |
| <*tidUnit*> | Ja | String | Den tidsenhet som ska användas med *intervall:*"Sekund", "Minut", "Timme", "Dag", "Vecka", "Månad", "År" |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | String | Tidsstämpeln minus det angivna antalet tidsenheter |
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
| <*Tidsstämpel*> | Ja | String | Strängen för en tidsstämpel |
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
| <*Text*> | Ja | String | Strängen som ska returneras i gemener |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*gemener-text*> | String | Den ursprungliga strängen i gemener |
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
| <*Text*> | Ja | String | Strängen som ska returneras i versaler |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*versaler-text*> | String | Den ursprungliga strängen i versaler |
||||

*Exempel*

I det här exemplet konverteras strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar detta resultat:`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Trimma

Ta bort inledande och avslutande blanktecken från en sträng och returnera den uppdaterade strängen.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | String | Strängen som har det inledande och avslutande blanksteget för att ta bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | En uppdaterad version för den ursprungliga strängen utan inledande eller avslutande blanktecken |
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
| <*Värde*> | Ja | String | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-uri*> | String | Den URI-kodade strängen med escape-tecken |
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
| <*Värde*> | Ja | String | Den URI-kodade strängen för att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binär-för-kodad-uri*> | String | Den binära versionen för den URI-kodade strängen. Det binära innehållet är base64-kodat `$content`och representerat av . |
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
| <*Värde*> | Ja | String | Den URI-kodade strängen för att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-uri*> | String | Den avkodade versionen för den URI-kodade strängen |
||||

*Exempel*

I det här exemplet skapas den avkodade strängversionen för den här URI-kodade strängen:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Och returnerar detta resultat:`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow (olikartade)

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Du kan också ange ett annat format med parametern <*format*>.

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Inga | String | Antingen en [enda formatespecificerare](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat formatmönster](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), som överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*aktuell tidsstämpel*> | String | Aktuellt datum och aktuell tid |
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

<a name="xml"></a>

### <a name="xml"></a>xml

Returnera XML-versionen för en sträng som innehåller ett JSON-objekt.

```
xml('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | String | Strängen med JSON-objektet för att konvertera <p>JSON-objektet får bara ha en rotegenskap, vilket inte kan vara en matris. <br>Använd omvänt snedstreck\\( ) som ett escape-tecken för det dubbla citattecknet ("). |
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

I det här exemplet efter exempel 1 `<count></count>` hittar det här exemplet noder som matchar noden och lägger till dessa nodvärden med `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Och returnerar detta resultat:`30`

*Exempel 2*

I det här exemplet hittar båda uttrycken `<location></location>` noder som matchar noden, i de angivna argumenten, som innehåller XML med ett namnområde. Uttrycken använder omvänt snedstreck\\( ) som ett escape-tecken för det dubbla citattecknet (").

* *Uttryck 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Uttryck 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Här är argumenten:

* Den här XML-koden, som `xmlns="http://contoso.com"`innehåller namnområdet för XML-dokument, :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Antingen XPath-uttryck här:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Här är den resultatnod `<location></location>` som matchar noden:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exempel 3*

I det här exemplet efter exempel 3 hittar du värdet i `<location></location>` noden:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Och returnerar detta resultat:`"Paris"`

## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [Systemvariabler](control-flow-system-variables.md).
