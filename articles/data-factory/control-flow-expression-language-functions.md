---
title: Uttryck och funktioner i Azure Data Factory | Microsoft Docs
description: Den här artikeln innehåller information om uttryck och funktioner som du kan använda för att skapa data factory-entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 5cdaba2a280221fa5fa9274ebfa6cafa18e7690c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055023"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Uttryck och funktioner i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuell version](control-flow-expression-language-functions.md)

Den här artikeln innehåller information om uttryck och funktioner som stöds av Azure Data Factory. 

## <a name="introduction"></a>Introduktion
JSON-värden i definitionen kan vara literal eller uttryck som utvärderas vid körning. Exempel:  
  
```json
"name": "value"
```

 (eller)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Uttryck  
Uttryck kan någonstans i ett JSON-strängvärde och alltid resultera i ett annat JSON-värde. Om ett JSON-värde är ett uttryck, brödtexten i uttrycket ska extraheras genom att ta bort @-tecknet (\@). Om en teckensträng behövs som börjar med \@, den måste undantas med hjälp av \@ \@. I följande exempel visas hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|”parametrar”|Tecknen ”parameters” returneras.|  
|”Parametrar [1]”|Tecknen ”parameters [1]” returneras.|  
|"\@\@"|En 1 tecken lång sträng som innehåller ”\@” returneras.|  
|" \@"|En 2 tecken lång sträng som innehåller ” \@” returneras.|  
  
 Uttryck kan även visas i strängar, med hjälp av en funktion som kallas *string interpolation* där uttryck är omslutna i `@{ ... }`. Exempel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Med interpolering är resultatet alltid en sträng. Anta att jag har definierat `myNumber` som `42` och `myString` som `foo`:  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|”\@pipeline ()-.parameters.myString”| Returnerar `foo` som en sträng.|  
|”\@{pipeline ()-.parameters.myString}”| Returnerar `foo` som en sträng.|  
|”\@pipeline ()-.parameters.myNumber”| Returnerar `42` som en *nummer*.|  
|”\@{pipeline ()-.parameters.myNumber}”| Returnerar `42` som en *sträng*.|  
|”Svaret är: @{pipeline ()-.parameters.myNumber}”| Returnerar strängen `Answer is: 42`.|  
|”\@concat (' svaret är: ', string(pipeline().parameters.myNumber))”| Returnerar strängen `Answer is: 42`|  
|”Svaret är: \@ \@{pipeline ()-.parameters.myNumber}”| Returnerar strängen `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Exempel

#### <a name="a-dataset-with-a-parameter"></a>En datauppsättning med en parameter
I följande exempel visas BlobDataset tar en parameter med namnet **sökvägen**. Dess värde används för att ange ett värde för den **folderPath** egenskapen med hjälp av följande uttryck: `@{dataset().path}`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>En pipeline med en parameter
I följande exempel pipelinen har **inputPath** och **outputPath** parametrar. Den **sökväg** för parametriserade bloben datauppsättning har angetts med hjälp av värdena för dessa parametrar. Syntax som används här är: `pipeline().parameters.parametername`. 

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
  
## <a name="functions"></a>Funktioner  
 Du kan anropa funktioner i uttryck. Följande avsnitt innehåller information om de funktioner som kan användas i ett uttryck.  

## <a name="string-functions"></a>Strängfunktioner  
 Följande funktioner gäller endast för strängar. Du kan också använda ett antal funktioner för samlingen strängar.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|concat|Kombinerar valfritt antal strängar tillsammans. Till exempel om parameter1 `foo,` följande uttryck returnerar `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameternummer**: 1... *n*<br /><br /> **Namnet**: sträng *n*<br /><br /> **Beskrivning av**: krävs. Strängar att kombinera till en sträng.|  
|delsträngen|Returnerar en delmängd av tecken från en sträng. Till exempel följande uttryck:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Returnerar:<br /><br /> `foo`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng från vilken delsträngen hämtas.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: startIndex<br /><br /> **Beskrivning av**: krävs. Index för vilket delsträngen börjar i parameter 1.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: längd<br /><br /> **Beskrivning av**: krävs. Delsträngens längd.|  
|Ersätt|Ersätter en sträng med an given sträng. Till exempel uttryck:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Returnerar:<br /><br /> `the new string`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs.  Om parameter 2 påträffas i parameter 1, den sträng som är sökning efter parameter 2 och uppdateras med parameter 3.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: Old string<br /><br /> **Beskrivning av**: krävs. Strängen som ska ersättas ME parameter 3 när en matchning påträffas i parameter 1<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: ny sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som används för att ersätta strängen i parameter 2 när en matchning påträffas i parameter 1.|  
|GUID| Genererar en globalt unik sträng (det vill säga GUID). Till exempel följande utdata genereras `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. En enskild formatspecifierare som indikerar [hur du formaterar värdet för detta Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Formatparametern kan vara ”N”, ”D”, ”B”, ”P” eller ”X”. Om formatet inte anges används ”D”.|  
|toLower|Konverterar en sträng till gemener. Till exempel följande returnerar `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Strängen som ska konverteras till lägre gemener och versaler. Om ett tecken i strängen inte har någon gemen motsvarighet, ingår den i den returnerade strängen har inte ändrats.|  
|toUpper|Konverterar en sträng till versaler. Följande uttryck returnerar exempelvis `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Strängen som ska konverteras till övre gemener och versaler. Om ett tecken i strängen inte har någon versal motsvarighet, ingår den i den returnerade strängen har inte ändrats.|  
|indexOf|Hitta index för ett värde i ett sträng-ärende insensitively. Följande uttryck returnerar exempelvis `7`: `indexof('hello, world.', 'world')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som kan innehålla värdet.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Värdet att söka i indexet.|  
|lastIndexOf|Hitta det sista indexet för ett värde i ett sträng-ärende insensitively. Följande uttryck returnerar exempelvis `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som kan innehålla värdet.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Värdet att söka i indexet.|  
|StartsWith|Kontrollerar om strängen börjar med ett värde skiftläge insensitively. Följande uttryck returnerar exempelvis `true`: `startswith('hello, world', 'hello')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som kan innehålla värdet.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Värdet för strängen får börja med.|  
|EndsWith|Kontrollerar om strängen slutar med ett värde skiftläge insensitively. Följande uttryck returnerar exempelvis `true`: `endswith('hello, world', 'world')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som kan innehålla värdet.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Värdet för strängen kan avslutas med.|  
|split|Delar upp strängen med hjälp av en avgränsare. Följande uttryck returnerar exempelvis `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Strängen som ska delas.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Avgränsare.|  
  
  
## <a name="collection-functions"></a>Samling funktioner  
 Dessa funktioner används över samlingar som matriser, strängar och ibland ordlistor.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|innehåller|Returnerar SANT om ordlista innehåller en nyckel, lista innehåller värdet eller strängen innehåller delsträngen. Följande uttryck returnerar exempelvis `true:``contains('abacaba','aca')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: I samling<br /><br /> **Beskrivning av**: krävs. Söka i samlingen.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: hitta objektet<br /><br /> **Beskrivning av**: krävs. Det objekt som ska sökas efter i **inom samlingen**.|  
|Längd|Returnerar antalet element i en matris eller sträng. Följande uttryck returnerar exempelvis `3`:  `length('abc')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen som ska hämta längden för.|  
|tom|Returnerar true om objektet, matrisen eller strängen är tom. Följande uttryck returnerar exempelvis `true`:<br /><br /> `empty('')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen som ska kontrollera om den är tom.|  
|skärningspunkten|Returnerar en enskild matris eller ett objekt med de vanliga element matriser eller objekt som överförs till den. Den här funktionen returnerar exempelvis `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Funktionens parametrar kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av båda). Om det finns två objekt med samma namn, visas det sista objektet med det namnet i det slutliga objektet.<br /><br /> **Parameternummer**: 1... *n*<br /><br /> **Namnet**: samlingen *n*<br /><br /> **Beskrivning av**: krävs. Samlingar som ska utvärderas. Ett objekt måste finnas i alla samlingar som skickas och tas med i resultatet.|  
|Union|Returnerar en enskild matris eller ett objekt med alla de element som finns i den matris eller det objekt som överförs till den. Den här funktionen returnerar exempelvis `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Funktionens parametrar kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av båda). Om det finns två objekt med samma namn i utdata, visas det sista objektet med det namnet i det slutliga objektet.<br /><br /> **Parameternummer**: 1... *n*<br /><br /> **Namnet**: samlingen *n*<br /><br /> **Beskrivning av**: krävs. Samlingar som ska utvärderas. Ett objekt som visas i någon av samlingarna visas i resultatet.|  
|första|Returnerar det första elementet i den matris eller sträng som skickats in. Den här funktionen returnerar exempelvis `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen som ska ta det första objektet från.|  
|senaste|Returnerar det sista elementet i den matris eller sträng som skickats in. Den här funktionen returnerar exempelvis `3`:<br /><br /> `last('0123')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen som ska ta det sista objektet från.|  
|ta|Returnerar först **antal** element från den matris eller sträng skickas, till exempel den här funktionen returnerar `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen som ska hämtas först **antal** objekt från.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: antal<br /><br /> **Beskrivning av**: krävs. Antal objekt som ska hämtas från den **samling**. Måste vara ett positivt heltal.|  
|Hoppa över|Returnerar element i matrisen med början vid index **antal**, till exempel den här funktionen returnerar `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling<br /><br /> **Beskrivning av**: krävs. Samlingen att hoppa över först **antal** objekt från.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: antal<br /><br /> **Beskrivning av**: krävs. Antalet objekt att ta bort från framför **samling**. Måste vara ett positivt heltal.|  
  
## <a name="logical-functions"></a>Logiska funktioner  
 Dessa funktioner är användbara i villkor, de kan användas för att utvärdera alla slags logik.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|lika med|Returnerar true om båda värdena är likvärdiga. Till exempel om parameter1 foo följande uttryck returnerar `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: objekt-1<br /><br /> **Beskrivning av**: krävs. Det objekt som ska jämföras med **objekt 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: objekt-2<br /><br /> **Beskrivning av**: krävs. Det objekt som ska jämföras med **objekt 1**.|  
|mindre|Returnerar SANT om det första argumentet är mindre än andra. Observera att värdena får bara vara av typen heltal, flyttal och sträng. Följande uttryck returnerar exempelvis `true`:  `less(10,100)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: objekt-1<br /><br /> **Beskrivning av**: krävs. Objektet som ska se om det är mindre än **objekt 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: objekt-2<br /><br /> **Beskrivning av**: krävs. Objektet som ska kontrollera om det är större än **objekt 1**.|  
|lessOrEquals|Returnerar true om det första argumentet är mindre än eller lika med andra. Observera att värdena får bara vara av typen heltal, flyttal och sträng. Följande uttryck returnerar exempelvis `true`:  `lessOrEquals(10,10)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: objekt-1<br /><br /> **Beskrivning av**: krävs. Objektet som ska kontrollera om det är mindre eller lika med **objekt 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: objekt-2<br /><br /> **Beskrivning av**: krävs. Objektet som ska se om det är större än eller lika med **objekt 1**.|  
|större|Returnerar true om det första argumentet är större än andra. Observera att värdena får bara vara av typen heltal, flyttal och sträng. Följande uttryck returnerar exempelvis `false`:  `greater(10,10)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: objekt-1<br /><br /> **Beskrivning av**: krävs. Objektet som ska kontrollera om det är större än **objekt 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: objekt-2<br /><br /> **Beskrivning av**: krävs. Objektet som ska se om det är mindre än **objekt 1**.|  
|greaterOrEquals|Returnerar true om det första argumentet är större än eller lika med andra. Observera att värdena får bara vara av typen heltal, flyttal och sträng. Följande uttryck returnerar exempelvis `false`:  `greaterOrEquals(10,100)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: objekt-1<br /><br /> **Beskrivning av**: krävs. Objektet som ska se om det är större än eller lika med **objekt 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: objekt-2<br /><br /> **Beskrivning av**: krävs. Objektet som ska kontrollera om det är mindre än eller lika med **objekt 1**.|  
|och|Returnerar true om båda parametrarna är uppfyllda. Båda argument måste vara booleska värden. Följande returnerar `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: booleskt 1<br /><br /> **Beskrivning av**: krävs. Det första argumentet måste vara `true`.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: booleskt 2<br /><br /> **Beskrivning av**: krävs. Det andra argumentet måste vara `true`.|  
|eller|Returnerar true om något av parametrarna är sanna. Båda argument måste vara booleska värden. Följande returnerar `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: booleskt 1<br /><br /> **Beskrivning av**: krävs. Det första argumentet som kan vara `true`.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: booleskt 2<br /><br /> **Beskrivning av**: krävs. Det andra argumentet kan vara `true`.|  
|inte|Returnerar SANT om parametern är `false`. Båda argument måste vara booleska värden. Följande returnerar `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: booleskt<br /><br /> **Beskrivning av**: returnerar true om parametern är `false`. Båda argument måste vara booleska värden. Följande returnerar `true`:  `not(contains('200 Success','Fail'))`|  
|Om|Returnerar ett angivet värde baserat på om resultaten uttryck i `true` eller `false`.  Till exempel följande returnerar `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: uttryck<br /><br /> **Beskrivning av**: krävs. Ett booleskt värde som bestämmer vilket värde returneras av uttrycket.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: True<br /><br /> **Beskrivning av**: krävs. Värdet som returneras om uttrycket är `true`.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: False<br /><br /> **Beskrivning av**: krävs. Värdet som returneras om uttrycket är `false`.|  
  
## <a name="conversion-functions"></a>Konverteringsfunktioner  
 Dessa funktioner används för att konvertera mellan var och en av de inbyggda typerna på språket:  
  
-   sträng  
  
-   heltal  
  
-   flyt  
  
-   boolesk  
  
-   matriser  
  
-   ordlistor  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|int|Konvertera parametern till ett heltal. Följande uttryck returnerar till exempel 100 som ett tal i stället för en sträng:  `int('100')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till ett heltal.|  
|sträng|Konvertera parametern till en sträng. Följande uttryck returnerar exempelvis `'10'`: `string(10)` du kan också konvertera ett objekt till en sträng, till exempel om den **foo** parametern är ett objekt med en egenskap `bar : baz`, och sedan följande skulle Gå tillbaka `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till en sträng.|  
|JSON|Konvertera parametern till ett värde för typ av JSON. Det är motsatsen till string(). Följande uttryck returnerar exempelvis `[1,2,3]` som en matris i stället för en sträng:<br /><br /> `json('[1,2,3]')`<br /><br /> På samma sätt kan du konvertera en sträng till ett objekt. Till exempel `json('{"bar" : "baz"}')` returnerar:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Den sträng som konverteras till ett värde av ursprunglig typ.<br /><br /> Json-funktionen stöder även xml-indata. Exempelvis parametervärdet för:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> omvandlas till följande json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|flyt|Konvertera Parameterargumentet till ett Flyttalsnummer. Följande uttryck returnerar exempelvis `10.333`:  `float('10.333')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till ett Flyttalsnummer.|  
|Bool|Konvertera parametern till ett booleskt värde. Följande uttryck returnerar exempelvis `false`:  `bool(0)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till ett booleskt värde.|  
|Slå samman|Returnerar det första icke-null-objektet i argument som skickas. Obs: en tom sträng är inte null. Till exempel om parametrar 1 och 2 inte har definierats, det här returnerar `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameternummer**: 1... *n*<br /><br /> **Namnet**: objektet*n*<br /><br /> **Beskrivning av**: krävs. Objekt att söka efter `null`.|  
|Base64|Returnerar en base64-representation av den inmatade strängen. Följande uttryck returnerar exempelvis `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: String 1<br /><br /> **Beskrivning av**: krävs. Strängen som ska kodas i base64-representation.|  
|base64ToBinary|Returnerar en binär representation av en base64-kodad sträng. Till exempel följande uttryck returnerar en binär representation av en sträng: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Base64-kodad sträng.|  
|base64ToString|Returnerar en strängrepresentation av en Base 64-kodad sträng. Till exempel följande uttryck returnerar en sträng: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Base64-kodad sträng.|  
|Binär|Returnerar en binär representation av ett värde.  Följande uttryck returnerar till exempel en binär representation av en sträng: `binary(‘some string’).`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till binärt.|  
|dataUriToBinary|Returnerar en binär representation av ett data-URI. Följande uttryck returnerar till exempel en binär representation av en sträng: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Data-URI att konvertera till binär representation.|  
|dataUriToString|Returnerar en strängrepresentation av en data-URI. Till exempel returnerar följande uttryck en sträng: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br />**Beskrivning av**: krävs. Data-URI att konvertera till strängrepresentation.|  
|dataUri|Returnerar en data-URI för ett värde. Följande uttryck returnerar till exempel data: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameternummer**: 1<br /><br />**Namn på**: värde<br /><br />**Beskrivning av**: krävs. Värdet att konvertera till data-URI.|  
|decodeBase64|Returnerar en strängrepresentation av en indata-Base 64-sträng. Följande uttryck returnerar exempelvis `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: returnerar en strängrepresentation av en indata-Base 64-sträng.|  
|encodeUriComponent|URL-visar den sträng som skickas. Följande uttryck returnerar exempelvis `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Strängen att undvika URL-osäkra tecknen från.|  
|decodeUriComponent|Ta bort-URL-visar den sträng som skickas. Följande uttryck returnerar exempelvis `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. Strängen att avkoda de URL-osäkra tecknen från.|  
|decodeDataUri|Returnerar en binär representation av en indata-URI-sträng. Till exempel följande uttryck returnerar en binär representation av `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br /> **Beskrivning av**: krävs. DataURI att avkoda till binär representation.|  
|uriComponent|Returnerar en URI-kodad representation av ett värde. Följande uttryck returnerar exempelvis `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parameterinformation: Nummer: 1, namn: sträng, beskrivning: krävs. Strängen som ska URI-kodas.|  
|uriComponentToBinary|Returnerar en binär representation av en URI-kodad sträng. Till exempel följande uttryck returnerar en binär representation av `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: sträng<br /><br />**Beskrivning av**: krävs. URI-kodad sträng.|  
|uriComponentToString|Returnerar en strängrepresentation av en URI-kodad sträng. Följande uttryck returnerar exempelvis `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer**: 1<br /><br />**Namn på**: sträng<br /><br />**Beskrivning av**: krävs. URI-kodad sträng.|  
|xml|Returnera en xml-representation av värdet. Följande uttryck returnerar exempelvis en XML-innehåll som representeras av `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Xml-funktionen stöder även JSON-objekt-indata. Till exempel parametern `{ "abc": "xyz" }` konverteras till en xml-innehåll `\<abc>xyz\</abc>`<br /><br /> **Parameternummer**: 1<br /><br />**Namn på**: värde<br /><br />**Beskrivning av**: krävs. Värdet som konverteras till XML.|  
|XPath|Returnera en matris med matchande xpath-uttrycket för ett värde som xpath-uttrycket utvärderas till xml-noder.<br /><br />  **Exempel 1**<br /><br /> Anta att värdet för parametern ”p1' är en sträng som innehåller följande XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Den här koden: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Returnerar<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> medan<br /><br /> 2. Den här koden: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Returnerar<br /><br /> `13`<br /><br /> <br /><br /> **Exempel 2**<br /><br /> Beroende på följande XML-innehåll:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Den här koden: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> eller<br /><br /> 2. Den här koden: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Returnerar<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> och<br /><br /> 3. Den här koden: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Returnerar<br /><br /> ``bar``<br /><br /> **Parameternummer**: 1<br /><br />**Namn på**: Xml<br /><br />**Beskrivning av**: krävs. XML-filen som du vill utvärdera XPath-uttrycket.<br /><br /> **Parameternummer**: 2<br /><br />**Namn på**: XPath<br /><br />**Beskrivning av**: krävs. XPath-uttrycket ska utvärderas.|  
|matris|Konvertera parametern till en matris.  Följande uttryck returnerar exempelvis `["abc"]`: `array('abc')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: värde<br /><br /> **Beskrivning av**: krävs. Det värde som konverteras till en matris.|
|createArray|Skapar en matris från parametrarna.  Följande uttryck returnerar exempelvis `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Parameternummer**: 1... n<br /><br /> **Namn på**: alla n<br /><br /> **Beskrivning av**: krävs. Värdena att kombinera till en matris.|

## <a name="math-functions"></a>Matematikfunktioner  
 Dessa funktioner kan användas för båda typer av nummer: **heltal** och **flyter**.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|lägg till|Returnerar resultatet av att lägga till de två talen. Den här funktionen returnerar exempelvis `20.333`:  `add(10,10.333)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: Summand 1<br /><br /> **Beskrivning av**: krävs. Nummer att lägga till **Summand 2**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: Summand 2<br /><br /> **Beskrivning av**: krävs. Nummer att lägga till **Summand 1**.|  
|Sub|Returnerar resultatet av subtraktion av de två talen. Den här funktionen returnerar till exempel: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: Minuenden<br /><br /> **Beskrivning av**: krävs. Hur många som **Subtrahend** tas bort från.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: Subtrahend<br /><br /> **Beskrivning av**: krävs. Talet att ta bort från den **Minuenden**.|  
|mul|Returnerar resultatet av multiplikationen av de två talen. Till exempel följande returnerar `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: Multiplikand 1<br /><br /> **Beskrivning av**: krävs. Talet att multiplicera **Multiplikand 2** med.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: Multiplikand 2<br /><br /> **Beskrivning av**: krävs. Talet att multiplicera **Multiplikand 1** med.|  
|div|Returnerar resultatet från divisionen av de två talen. Till exempel följande returnerar `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: täljaren<br /><br /> **Beskrivning av**: krävs. Talet att dela med de **divisorn**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: divisorn<br /><br /> **Beskrivning av**: krävs. Talet att dela den **täljaren** av.|  
|MOD|Returnerar resultatet av resten efter att divisionen av de två talen (modulo). Följande uttryck returnerar exempelvis `2`:<br /><br /> `mod(10,4)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: täljaren<br /><br /> **Beskrivning av**: krävs. Talet att dela med de **divisorn**.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: divisorn<br /><br /> **Beskrivning av**: krävs. Talet att dela den **täljaren** av. Efter divisionen tas resten.|  
|min.|Det finns två olika mönster för att anropa den här funktionen: `min([0,1,2])` här min tar en matris. Det här uttrycket returnerar `0`. Den här funktionen kan också ta en kommaavgränsad lista med värden: `min(0,1,2)` den här funktionen returnerar också 0. Observera att alla värden måste vara siffror, så om parametern är en matris har endast ha det siffror.<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling eller -värde<br /><br /> **Beskrivning av**: krävs. Det kan antingen vara en matris med värden för att hitta det minsta värdet eller det första värdet i en uppsättning.<br /><br /> **Parameternummer**: 2... *n*<br /><br /> **Namnet**: värdet *n*<br /><br /> **Beskrivning av**: valfritt. Om den första parametern är ett värde, du kan sedan skicka ytterligare värden och minimum för alla överförda värden returneras.|  
|max|Det finns två olika mönster för att anropa den här funktionen:  `max([0,1,2])`<br /><br /> Här tar max en matris. Det här uttrycket returnerar `2`. Den här funktionen kan också ta en kommaavgränsad lista med värden: `max(0,1,2)` den här funktionen returnerar 2. Observera att alla värden måste vara siffror, så om parametern är en matris har endast ha det siffror.<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: samling eller -värde<br /><br /> **Beskrivning av**: krävs. Det kan antingen vara en matris med värden för att hitta det största värdet eller det första värdet i en uppsättning.<br /><br /> **Parameternummer**: 2... *n*<br /><br /> **Namnet**: värdet *n*<br /><br /> **Beskrivning av**: valfritt. Om den första parametern är ett värde, du kan sedan skicka ytterligare värden och maxvärdet för alla överförda värden returneras.|  
|Adressintervall| Genererar en matris med heltal med början från ett visst tal, och du definierar längden på den returnerade matrisen. Den här funktionen returnerar exempelvis `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: startIndex<br /><br /> **Beskrivning av**: krävs. Det är det första heltalet i matrisen.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: antal<br /><br /> **Beskrivning av**: krävs. Antal heltal som är i matrisen.|  
|rand| Genererar ett slumpvist heltal inom det angivna intervallet (inkluderande båda ändarna. Detta kan till exempel returnera `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: minsta<br /><br /> **Beskrivning av**: krävs. Det lägsta heltalet som kan returneras.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: högsta<br /><br /> **Beskrivning av**: krävs. Det högsta heltal som kan returneras.|  
  
## <a name="date-functions"></a>Datumfunktioner  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|utcnow|Returnerar den aktuella tidsstämpeln som en sträng. Till exempel `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  
|lägg_till_sekunder|Lägger till ett heltal av sekunder till en strängtidsstämpel skickas in. Hur många sekunder kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”) som standard såvida inte en formatangivelse har angetts. Till exempel `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: tidsstämpel<br /><br /> **Beskrivning av**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: sekunder<br /><br /> **Beskrivning av**: krävs. Antal sekunder att lägga till. Kan vara negativt för att subtrahera sekunder.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  
|addminutes|Lägger till ett heltal av minuter till en strängtidsstämpel skickas in. Hur många minuter kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”) som standard såvida inte en formatangivelse har angetts. Till exempel `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: tidsstämpel<br /><br /> **Beskrivning av**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: minuter<br /><br /> **Beskrivning av**: krävs. Antalet minuter att lägga till. Kan vara negativt för att subtrahera minuter.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  
|addhours|Lägger till ett heltal av timmar till en strängtidsstämpel skickas in. Hur många timmar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”) som standard såvida inte en formatangivelse har angetts. Till exempel `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: tidsstämpel<br /><br /> **Beskrivning av**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: timmar<br /><br /> **Beskrivning av**: krävs. Antal timmar att lägga till. Kan vara negativt för att subtrahera timmar.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  
|adddays|Lägger till ett heltal av dagar till en strängtidsstämpel skickas in. Hur många dagar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format (”o”) som standard såvida inte en formatangivelse har angetts. Till exempel `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: tidsstämpel<br /><br /> **Beskrivning av**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: dagar<br /><br /> **Beskrivning av**: krävs. Antal dagar att lägga till. Kan vara negativt för att subtrahera dagar.<br /><br /> **Parameternummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  
|formatDateTime|Returnerar en sträng i datumformat. Resultatet är en sträng i ISO 8601-format (”o”) som standard såvida inte en formatangivelse har angetts. Till exempel `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Parameternummer**: 1<br /><br /> **Namn på**: datum<br /><br /> **Beskrivning av**: krävs. En sträng som innehåller datum.<br /><br /> **Parameternummer**: 2<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning av**: valfritt. Antingen en [enkel formatspecificerartecken](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller en [anpassat](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) värde som anger hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges så används ISO 8601-format (”o”).|  

## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [systemvariabler](control-flow-system-variables.md).
