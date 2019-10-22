---
title: Uttryck och funktioner i Azure Data Factory | Microsoft Docs
description: Den här artikeln innehåller information om uttryck och funktioner som du kan använda för att skapa Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9fc8dd1e22e16ef5342b405b602f8baaf8ce7edf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692848"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Uttryck och funktioner i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuell version](control-flow-expression-language-functions.md)

Den här artikeln innehåller information om uttryck och funktioner som stöds av Azure Data Factory. 

## <a name="introduction"></a>Introduktion
JSON-värden i definitionen kan vara literala eller uttryck som utvärderas vid körning. Exempel:  
  
```json
"name": "value"
```

 eller  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Uttryck  
Uttryck kan finnas var som helst i ett JSON-sträng värde och resulterar alltid i ett annat JSON-värde. Om ett JSON-värde är ett uttryck extraheras bröd texten i uttrycket genom att ta bort at-sign (\@). Om en tecken sträng krävs som börjar med \@ måste den föregås av \@ \@. I följande exempel visas hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|komponentparametrar|Tecknens parametrar returneras.|  
|"parametrar [1]"|Tecknens parametrar [1] returneras.|  
|"\@ \@"|En 1 tecken sträng som innehåller \@ returneras.|  
|"\@"|En 2-tecken sträng som innehåller \@ returneras.|  
  
 Uttryck kan också visas i strängar med hjälp av en funktion som kallas *String-interpolation* där uttryck omsluts i `@{ ... }`. Exempel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Med hjälp av String-interpolation är resultatet alltid en sträng. Anta att jag har definierat `myNumber` som `42` och `myString` som `foo`:  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|"\@pipeline (). Parameters. prestring"| Returnerar `foo` som en sträng.|  
|"\@ {pipeliner (). Parameters. unstring}"| Returnerar `foo` som en sträng.|  
|"\@pipeline (). Parameters. Number"| Returnerar `42` som ett *tal*.|  
|"\@ {pipeliner (). Parameters. Number}"| Returnerar `42` som en *sträng*.|  
|"Svar är: @ {pipeline (). Parameters. Number}"| Returnerar strängen `Answer is: 42`.|  
|"\@concat (" svar är: ", sträng (pipeline (). Parameters. Number))"| Returnerar strängen `Answer is: 42`|  
|"Svar är: \@ \@ {pipeliner (). Parameters. Number}"| Returnerar strängen `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Exempel

#### <a name="a-dataset-with-a-parameter"></a>En data uppsättning med en parameter
I följande exempel tar BlobDataset en parameter med namnet **Path**. Värdet används för att ange ett värde för egenskapen **folderPath** med hjälp av uttrycket: `dataset().path`. 

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
I följande exempel använder pipelinen **inputPath** -och **outputPath** -parametrar. **Sökvägen** för den parameterstyrda BLOB-datauppsättningen anges med hjälp av värden för dessa parametrar. Syntaxen som används här är: `pipeline().parameters.parametername`. 

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
#### <a name="tutorial"></a>Självstudiekurs
Den här [självstudien](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vägleder dig genom hur du skickar parametrar mellan en pipeline och aktivitet samt mellan aktiviteterna.

  
## <a name="functions"></a>Functions  
 Du kan anropa Functions i uttryck. I följande avsnitt finns information om de funktioner som kan användas i ett uttryck.  

## <a name="string-functions"></a>Strängfunktioner  
 Följande funktioner gäller endast för strängar. Du kan också använda ett antal samlings funktioner på strängar.  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|concat|Kombinerar valfritt antal strängar. Om Parameter1 exempelvis är `foo,` följande uttryck returnera `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn**: sträng *n*<br /><br /> **Beskrivning**: krävs. De strängar som ska kombineras till en enda sträng.|  
|under sträng|Returnerar en delmängd av tecken från en sträng. Till exempel följande uttryck:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> avkastning<br /><br /> `foo`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som under strängen ska hämtas från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: Start index<br /><br /> **Beskrivning**: krävs. Index för var under strängen börjar i parameter 1. Start index är noll-baserat. <br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: längd<br /><br /> **Beskrivning**: krävs. Längden på under strängen.|  
|bytt|Ersätter en sträng med en specifik sträng. Till exempel uttrycket:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> avkastning<br /><br /> `the new string`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs.  Om parameter 2 finns i parameter 1 är strängen som genomsöks efter parameter 2 och uppdaterad med parametern 3.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: gammal sträng<br /><br /> **Beskrivning**: krävs. Strängen som ska ersättas med parameter 3 när en matchning hittas i parameter 1<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: ny sträng<br /><br /> **Beskrivning**: krävs. Strängen som används för att ersätta strängen i parameter 2 när en matchning hittas i parameter 1.|  
|LED| Genererar en globalt unik sträng (aka. GUID). Följande utdata kan till exempel skapas `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. En enkel formats specifikation som anger [hur värdet för denna GUID ska formateras](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Format parametern kan vara "N", "D", "B", "P" eller "X". Om formatet inte anges används "D".|  
|toLower|Konverterar en sträng till gemener. Följande returnerar till exempel `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som ska konverteras till mindre Skift läge. Om ett tecken i strängen inte har en motsvarande gemen, inkluderas det i den returnerade strängen.|  
|toUpper|Konverterar en sträng till versaler. Till exempel returnerar följande uttryck `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som ska konverteras till versal hölje. Om ett tecken i strängen inte har en motsvarande versal, inkluderas det i den returnerade strängen.|  
|indexof|Hitta indexet för ett värde i en sträng som inte är Skift läges känsligt. Indexet är noll-baserat. Till exempel returnerar följande uttryck `7`: `indexof('hello, world.', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Värdet att söka i indexet för.|  
|lastindexof|Hitta det sista indexet för ett värde i en sträng som inte är Skift läges känsligt. Indexet är noll-baserat. Till exempel returnerar följande uttryck `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Värdet att söka i indexet för.|  
|StartsWith|Kontrollerar om strängen börjar med ett värde som inte är Skift läges okänslig. Till exempel returnerar följande uttryck `true`: `startswith('hello, world', 'hello')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Värdet som strängen kan börja med.|  
|EndsWith|Kontrollerar om strängen slutar med ett värde som inte är Skift läges okänslig. Till exempel returnerar följande uttryck `true`: `endswith('hello, world', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Värdet som strängen kan sluta med.|  
|split|Delar upp strängen med en avgränsare. Till exempel returnerar följande uttryck `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Den sträng som delas.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Avgränsaren.|  
  
  
## <a name="collection-functions"></a>Samlings funktioner  
 Dessa funktioner fungerar över samlingar som matriser, strängar och ibland ord listor.  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|ingår|Returnerar true om ord listan innehåller en nyckel, listan innehåller värde eller sträng innehåller under sträng. Till exempel returnerar följande uttryck `true:``contains('abacaba','aca')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: inom samling<br /><br /> **Beskrivning**: krävs. Samlingen att söka i.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: Sök efter objekt<br /><br /> **Beskrivning**: krävs. Objektet att söka efter i **samlingen**.|  
|krävande|Returnerar antalet element i en matris eller sträng. Till exempel returnerar följande uttryck `3`: `length('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Samlingen för att hämta längden på.|  
|saknas|Returnerar true om objektet, matrisen eller strängen är tom. Följande uttryck returnerar till exempel `true`:<br /><br /> `empty('')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Samlingen för att kontrol lera om den är tom.|  
|överlappning|Returnerar en enskild matris eller ett objekt med gemensamma element mellan matriserna eller objekten som skickats till den. Den här funktionen returnerar till exempel `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametrarna för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av dessa). Om det finns två objekt med samma namn visas det sista objektet med det namnet i det sista objektet.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn**: samling *n*<br /><br /> **Beskrivning**: krävs. De samlingar som ska utvärderas. Ett objekt måste finnas i alla samlingar som skickas in för att visas i resultatet.|  
|Union|Returnerar en enskild matris eller ett objekt med alla element i matrisen eller objektet som skickas till den. Den här funktionen returnerar till exempel `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametrarna för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av dessa). Om det finns två objekt med samma namn i slut resultatet visas det sista objektet med det namnet i det sista objektet.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn**: samling *n*<br /><br /> **Beskrivning**: krävs. De samlingar som ska utvärderas. Ett objekt som visas i någon av samlingarna visas i resultatet.|  
|förstagångskörningen|Returnerar det första elementet i matrisen eller strängen som har skickats. Den här funktionen returnerar till exempel `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Den samling som det första objektet ska tas från.|  
|pågå|Returnerar det sista elementet i matrisen eller strängen som har skickats. Den här funktionen returnerar till exempel `3`:<br /><br /> `last('0123')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Den samling som det sista objektet ska tas från.|  
|Gå|Returnerar de första **Count** -elementen från matrisen eller strängen som skickades, till exempel den här funktionen returnerar `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Den samling som ska ta det första **antalet** objekt från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: antal<br /><br /> **Beskrivning**: krävs. Antalet objekt som ska tas från **samlingen**. Måste vara ett positivt heltal.|  
|Ignorera|Returnerar elementen i matrisen som börjar vid index **antal**, till exempel den här funktionen returnerar `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling<br /><br /> **Beskrivning**: krävs. Samlingen som hoppar över det första **antalet** objekt från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: antal<br /><br /> **Beskrivning**: krävs. Antalet objekt som ska tas bort från början av **samlingen**. Måste vara ett positivt heltal.|  
  
## <a name="logical-functions"></a>Logiska funktioner  
 Dessa funktioner är användbara i villkor, de kan användas för att utvärdera vilken typ av logik som helst.  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|är lika med|Returnerar true om två värden är lika. Om Parameter1 till exempel är foo, returnerar följande uttryck `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: objekt 1<br /><br /> **Beskrivning**: krävs. Objektet som ska jämföras med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: objekt 2<br /><br /> **Beskrivning**: krävs. Objektet som ska jämföras med **objekt 1**.|  
|minskad|Returnerar true om det första argumentet är mindre än det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar följande uttryck `true`: `less(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: objekt 1<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är mindre än **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: objekt 2<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är större än **objekt 1**.|  
|lessOrEquals|Returnerar true om det första argumentet är mindre än eller lika med det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar följande uttryck `true`: `lessOrEquals(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: objekt 1<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är mindre än eller lika med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: objekt 2<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är större än eller lika med **objekt 1**.|  
|störst|Returnerar true om det första argumentet är större än det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar följande uttryck `false`: `greater(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: objekt 1<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är större än **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: objekt 2<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är mindre än **objekt 1**.|  
|Större|Returnerar true om det första argumentet är större än eller lika med det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar följande uttryck `false`: `greaterOrEquals(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: objekt 1<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är större än eller lika med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: objekt 2<br /><br /> **Beskrivning**: krävs. Objektet för att kontrol lera om det är mindre än eller lika med **objekt 1**.|  
|och|Returnerar true om båda parametrarna är sanna. Båda argumenten måste vara booleska värden. Följande returnerar `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: boolesk 1<br /><br /> **Beskrivning**: krävs. Det första argumentet som måste vara `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: boolesk 2<br /><br /> **Beskrivning**: krävs. Det andra argumentet måste vara `true`.|  
|eller|Returnerar true om någon av parametrarna är true. Båda argumenten måste vara booleska värden. Följande returnerar `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: boolesk 1<br /><br /> **Beskrivning**: krävs. Det första argumentet som kan vara `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: boolesk 2<br /><br /> **Beskrivning**: krävs. Det andra argumentet kan vara `true`.|  
|Ogiltigt|Returnerar true om parametern är `false`. Båda argumenten måste vara booleska värden. Följande returnerar `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: boolesk<br /><br /> **Beskrivning**: returnerar true om parametern är `false`. Båda argumenten måste vara booleska värden. Följande returnerar `true`: `not(contains('200 Success','Fail'))`|  
|eventuella|Returnerar ett angivet värde baserat på om det angivna uttrycket resulterar i `true` eller `false`.  Följande returnerar till exempel `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: uttryck<br /><br /> **Beskrivning**: krävs. Ett booleskt värde som avgör vilket värde som returneras av uttrycket.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sant<br /><br /> **Beskrivning**: krävs. Värdet som ska returneras om uttrycket är `true`.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: falskt<br /><br /> **Beskrivning**: krävs. Värdet som ska returneras om uttrycket är `false`.|  
  
## <a name="conversion-functions"></a>Konverterings funktioner  
 Dessa funktioner används för att konvertera mellan var och en av de ursprungliga typerna på språket:  
  
-   sträng  
  
-   heltal  
  
-   flyt  
  
-   boolesk  
  
-   lagringsmatriser  
  
-   lista  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|int|Konvertera parametern till ett heltal. Följande uttryck returnerar till exempel 100 som ett tal i stället för en sträng: `int('100')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till ett heltal.|  
|sträng|Konvertera parametern till en sträng. Till exempel returnerar följande uttryck `'10'`: `string(10)` du kan även konvertera ett objekt till en sträng, till exempel om parametern **foo** är ett objekt med en egenskap `bar : baz`, returnerar följande `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till en sträng.|  
|utgör|Konvertera parametern till ett JSON-värde. Det är motsatsen till sträng (). Till exempel returnerar följande uttryck `[1,2,3]` som en matris, i stället för en sträng:<br /><br /> `json('[1,2,3]')`<br /><br /> På samma sätt kan du konvertera en sträng till ett objekt. @No__t_0 returnerar till exempel:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som konverteras till ett internt typ värde.<br /><br /> JSON-funktionen stöder även XML-indata. Till exempel parameter värde för:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> konverteras till följande JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|flyt|Omvandla parameter argumentet till ett flyttal-nummer. Till exempel returnerar följande uttryck `10.333`: `float('10.333')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till ett flytt ALS nummer.|  
|bool|Konvertera parametern till ett booleskt värde. Till exempel returnerar följande uttryck `false`: `bool(0)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till ett booleskt värde.|  
|Coalesce|Returnerar det första icke-null-objektet i de angivna argumenten. Obs: en tom sträng är inte null. Om t. ex. parametrarna 1 och 2 inte har definierats returneras `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn**: objekt*n*<br /><br /> **Beskrivning**: krävs. De objekt som ska sökas efter `null`.|  
|Base64|Returnerar den base64-representation av Indatasträngen. Till exempel returnerar följande uttryck `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng 1<br /><br /> **Beskrivning**: krävs. Strängen som ska kodas i base64-representation.|  
|base64ToBinary|Returnerar en binär representation av en Base64-kodad sträng. Till exempel returnerar följande uttryck en binär representation av en sträng: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Den base64-kodade strängen.|  
|base64ToString|Returnerar en sträng representation av en based64-kodad sträng. Till exempel returnerar följande uttryck en sträng: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Den base64-kodade strängen.|  
|binär|Returnerar en binär representation av ett värde.  Följande uttryck returnerar till exempel en binär representation av en sträng: `binary(‘some string’).`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till Binary.|  
|dataUriToBinary|Returnerar en binär representation av en data-URI. Till exempel returnerar följande uttryck en binär representation av en sträng: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Data-URI: n som ska konverteras till binär representation.|  
|dataUriToString|Returnerar en sträng representation av en data-URI. Till exempel returnerar följande uttryck en sträng: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br />**Beskrivning**: krävs. Data-URI: n som ska konverteras till sträng representation.|  
|dataUri|Returnerar en data-URI för ett värde. Följande uttryck returnerar till exempel data: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn**: värde<br /><br />**Beskrivning**: krävs. Värdet som ska konverteras till data-URI.|  
|decodeBase64|Returnerar en sträng representation av en based64-sträng. Till exempel returnerar följande uttryck `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: returnerar en sträng representation av en based64-sträng.|  
|encodeUriComponent|URL – undantar den sträng som skickades. Till exempel returnerar följande uttryck `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen som ska undantas URL-osäkra tecken från.|  
|decodeUriComponent|FN-URL – avstår den sträng som skickades. Till exempel returnerar följande uttryck `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. Strängen att avkoda URL-osäkra tecken från.|  
|decodeDataUri|Returnerar en binär representation av en indata-URI-sträng. Följande uttryck returnerar till exempel den binära representationen av `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: krävs. DataURI för att avkoda till en binär representation.|  
|uriComponent|Returnerar en URI-kodad representation av ett värde. Till exempel returnerar följande uttryck `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parameter information: antal: 1, namn: sträng, beskrivning: krävs. Strängen som ska vara URI-kodad.|  
|uriComponentToBinary|Returnerar en binär representation av en URI-kodad sträng. Följande uttryck returnerar till exempel en binär representation av `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br />**Beskrivning**: krävs. URI-kodad sträng.|  
|uriComponentToString|Returnerar en sträng representation av en URI-kodad sträng. Till exempel returnerar följande uttryck `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn**: sträng<br /><br />**Beskrivning**: krävs. URI-kodad sträng.|  
|xml|Returnera en XML-representation av värdet. Till exempel returnerar följande uttryck ett XML-innehåll som representeras av `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. XML-funktionen stöder även indata från JSON-objekt. Parametern `{ "abc": "xyz" }` konverteras till exempel till ett XML-innehåll `\<abc>xyz\</abc>`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn**: värde<br /><br />**Beskrivning**: krävs. Värdet som ska konverteras till XML.|  
|XPath|Returnera en matris med XML-noder som matchar XPath-uttrycket för ett värde som XPath-uttrycket utvärderar till.<br /><br />  **Exempel 1**<br /><br /> Antag att värdet för parametern P1 är en sträng representation av följande XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. den här koden: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Returnerar<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> tanke<br /><br /> 2. den här koden: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Returnerar<br /><br /> `13`<br /><br /> <br /><br /> **Exempel 2**<br /><br /> Följande XML-innehåll har angetts:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. den här koden: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> eller<br /><br /> 2. den här koden: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Avkastning<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> och<br /><br /> 3. den här koden: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Avkastning<br /><br /> ``bar``<br /><br /> **Parameter nummer**: 1<br /><br />**Namn**: XML<br /><br />**Beskrivning**: krävs. XML-filen som XPath-uttrycket ska utvärderas på.<br /><br /> **Parameter nummer**: 2<br /><br />**Namn**: XPath<br /><br />**Beskrivning**: krävs. Det XPath-uttryck som ska utvärderas.|  
|matris|Konvertera parametern till en matris.  Till exempel returnerar följande uttryck `["abc"]`: `array('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: värde<br /><br /> **Beskrivning**: krävs. Värdet som konverteras till en matris.|
|createArray|Skapar en matris från parametrarna.  Till exempel returnerar följande uttryck `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Parameter nummer**: 1... m<br /><br /> **Namn**: any n<br /><br /> **Beskrivning**: krävs. Värdena som ska kombineras i en matris.|

## <a name="math-functions"></a>Matematiska funktioner  
 Dessa funktioner kan användas för antingen typer av tal: **heltal** och **flyttal**.  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|add|Returnerar resultatet av additionen av de två talen. Den här funktionen returnerar till exempel `20.333`: `add(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: Summand 1<br /><br /> **Beskrivning**: krävs. Talet som ska läggas till i **Summand 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: Summand 2<br /><br /> **Beskrivning**: krävs. Talet som ska läggas till i **Summand 1**.|  
|Build|Returnerar resultatet av subtraktionen av de två talen. Den här funktionen returnerar till exempel: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: MINUEND<br /><br /> **Beskrivning**: krävs. Numret som **SUBTRAHEND** tas bort från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: SUBTRAHEND<br /><br /> **Beskrivning**: krävs. Det tal som ska tas bort från **MINUEND**.|  
|mul|Returnerar resultatet av multiplikationen av de två talen. Följande returnerar till exempel `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: Multiplicand 1<br /><br /> **Beskrivning**: krävs. Talet som **Multiplicand 2** ska multipliceras med.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: Multiplicand 2<br /><br /> **Beskrivning**: krävs. Talet som **Multiplicand 1** ska multipliceras med.|  
|tagg|Returnerar resultatet av divisionen av de två talen. Följande returnerar till exempel `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: utdelning<br /><br /> **Beskrivning**: krävs. Talet som ska divideras med **divisorn**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: divisor<br /><br /> **Beskrivning**: krävs. Numret att dividera **utdelningen** med.|  
|rest|Returnerar resultatet av resten efter divisionen av de två talen (modulo). Följande uttryck returnerar till exempel `2`:<br /><br /> `mod(10,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: utdelning<br /><br /> **Beskrivning**: krävs. Talet som ska divideras med **divisorn**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: divisor<br /><br /> **Beskrivning**: krävs. Numret att dividera **utdelningen** med. Efter divisionen tas resten.|  
|min.|Det finns två olika mönster för att anropa den här funktionen: `min([0,1,2])` här tar det för min matris. Det här uttrycket returnerar `0`. Den här funktionen kan också ta en kommaavgränsad lista med värden: `min(0,1,2)` den här funktionen returnerar även 0. Obs! alla värden måste vara siffror, så om parametern är en matris behöver den bara innehålla siffror.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling eller värde<br /><br /> **Beskrivning**: krävs. Det kan antingen vara en matris med värden för att hitta det lägsta värdet eller det första värdet i en mängd.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Namn**: värde *n*<br /><br /> **Beskrivning**: valfritt. Om den första parametern är ett värde kan du skicka ytterligare värden och minst alla skickade värden returneras.|  
|bekräftat|Det finns två olika mönster för att anropa den här funktionen: `max([0,1,2])`<br /><br /> Detta Max tar en matris. Det här uttrycket returnerar `2`. Den här funktionen kan också ta en kommaavgränsad lista med värden: `max(0,1,2)` den här funktionen returnerar 2. Obs! alla värden måste vara siffror, så om parametern är en matris behöver den bara innehålla siffror.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: samling eller värde<br /><br /> **Beskrivning**: krävs. Det kan antingen vara en matris med värden för att hitta det högsta värdet eller det första värdet i en mängd.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Namn**: värde *n*<br /><br /> **Beskrivning**: valfritt. Om den första parametern är ett värde kan du skicka ytterligare värden och det högsta värdet för alla skickade värden returneras.|  
|intervall| Genererar en matris med heltal från ett visst tal och du definierar längden på den returnerade matrisen. Den här funktionen returnerar till exempel `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: Start index<br /><br /> **Beskrivning**: krävs. Det är det första heltalet i matrisen.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: antal<br /><br /> **Beskrivning**: krävs. Antal heltal i matrisen.|  
|slump| Genererar ett slumpmässigt heltal inom det angivna intervallet (inklusive båda ändar. Detta kan till exempel returnera `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: minimum<br /><br /> **Beskrivning**: krävs. Det lägsta heltal som kunde returneras.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: högsta<br /><br /> **Beskrivning**: krävs. Det högsta heltalet som kunde returneras.|  
  
## <a name="date-functions"></a>Datum funktioner  
  
|Funktions namn|Beskrivning|  
|-------------------|-----------------|  
|utcnow|Returnerar den aktuella tidstämpeln som en sträng. Till exempel `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addseconds|Lägger till ett heltal i sekunder till en sträng tidstämpel som skickats in. Antalet sekunder kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: tidsstämpel<br /><br /> **Beskrivning**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: sekunder<br /><br /> **Beskrivning**: krävs. Det antal sekunder som ska läggas till. Kan vara negativt för att subtrahera sekunder.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addminutes|Lägger till ett heltal i minuter till en sträng tidstämpel som skickats in. Antalet minuter kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: tidsstämpel<br /><br /> **Beskrivning**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: minuter<br /><br /> **Beskrivning**: krävs. Det antal minuter som ska läggas till. Kan vara negativt för att subtrahera minuter.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addhours|Lägger till ett heltal i timmar till en sträng tidstämpel som skickats in. Antalet timmar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: tidsstämpel<br /><br /> **Beskrivning**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: timmar<br /><br /> **Beskrivning**: krävs. Antalet timmar som ska läggas till. Kan vara negativt för att ta bort timmar.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|adddays|Lägger till ett heltals antal dagar till en sträng-tidsstämpel som skickats in. Antalet dagar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: tidsstämpel<br /><br /> **Beskrivning**: krävs. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: dagar<br /><br /> **Beskrivning**: krävs. Det antal dagar som ska läggas till. Kan vara negativt för att subtrahera dagar.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|formatDateTime|Returnerar en sträng i datum format. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />Om du vill formatera ett datum i formatet ÅÅÅÅ/MM/DD använder du formatDateTime (utcnow (), ÅÅÅÅ/MM/DD).</br>Om du vill lägga till ett namn i datumet använder @concat (' foo-', '/', formatDateTime (utcnow (), ' ÅÅÅÅ/MM/DD ')).<br><br> **Parameter nummer**: 1<br /><br /> **Namn**: datum<br /><br /> **Beskrivning**: krävs. En sträng som innehåller datumet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn**: format<br /><br /> **Beskrivning**: valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o"). |  

## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [Systemvariabler](control-flow-system-variables.md).
