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
ms.openlocfilehash: c500c4b2d35a449a9f9c0f693a02c008ea6e3c5e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141697"
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
Uttryck kan finnas var som helst i ett JSON-sträng värde och resulterar alltid i ett annat JSON-värde. Om ett JSON-värde är ett uttryck extraheras bröd texten i uttrycket genom att ta bort at-tecknet (\@). Om en tecken sträng krävs som börjar med \@måste den föregås \@ \@av. I följande exempel visas hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|komponentparametrar|Tecknens parametrar returneras.|  
|"parametrar [1]"|Tecknens parametrar [1] returneras.|  
|"\@\@"|En 1-tecken sträng som innehåller\@returnerar.|  
|" \@"|En 2-tecken sträng som innehåller \@returnerar.|  
  
 Uttryck kan också visas i strängar med hjälp av en funktion som kallas *String* -interpolation där `@{ ... }`uttryck är omslutna. Exempel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Med hjälp av String-interpolation är resultatet alltid en sträng. Anta att jag har `myNumber` definierat `42` as `myString` och `foo`som:  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Returnerar `foo` som en sträng.|  
|"\@{pipeline().parameters.myString}"| Returnerar `foo` som en sträng.|  
|"\@pipeline().parameters.myNumber"| Returnerar `42` som ett *tal*.|  
|"\@{pipeline().parameters.myNumber}"| Returnerar `42` som en *sträng*.|  
|"Svar är: @ {pipeline (). Parameters. Number}"| Returnerar strängen `Answer is: 42`.|  
|"\@concat (" svar är:, String (pipeline (). Parameters. Number)) "| Returnerar strängen`Answer is: 42`|  
|"Svar är: \@ \@{pipeline (). Parameters. Number}"| Returnerar strängen `Answer is: @{pipeline().parameters.myNumber}`.|  
  
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
I följande exempel använder pipelinen **inputPath** -och **outputPath** -parametrar. **Sökvägen** för den parameterstyrda BLOB-datauppsättningen anges med hjälp av värden för dessa parametrar. Syntaxen som används här är `pipeline().parameters.parametername`:. 

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
#### <a name="tutorial"></a>Självstudie
Den [](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) här självstudien vägleder dig genom hur du skickar parametrar mellan en pipeline och aktivitet samt mellan aktiviteterna.

  
## <a name="functions"></a>Funktioner  
 Du kan anropa Functions i uttryck. I följande avsnitt finns information om de funktioner som kan användas i ett uttryck.  

## <a name="string-functions"></a>Strängfunktioner  
 Följande funktioner gäller endast för strängar. Du kan också använda ett antal samlings funktioner på strängar.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|concat|Kombinerar valfritt antal strängar. Om till exempel Parameter1 är `foo,` följande uttryck returnerar: `somevalue-foo-somevalue``concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn på**: Sträng *n*<br /><br /> **Beskrivning**: Obligatoriskt. De strängar som ska kombineras till en enda sträng.|  
|substring|Returnerar en delmängd av tecken från en sträng. Till exempel följande uttryck:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> avkastning<br /><br /> `foo`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som under strängen ska hämtas från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Start index<br /><br /> **Beskrivning**: Obligatoriskt. Index för var under strängen börjar i parameter 1. Start index är noll-baserat. <br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Längd<br /><br /> **Beskrivning**: Obligatoriskt. Längden på under strängen.|  
|bytt|Ersätter en sträng med en specifik sträng. Till exempel uttrycket:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> avkastning<br /><br /> `the new string`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn**: sträng<br /><br /> **Beskrivning**: Obligatoriskt.  Om parameter 2 finns i parameter 1 är strängen som genomsöks efter parameter 2 och uppdaterad med parametern 3.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Gammal sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som ska ersättas med parameter 3 när en matchning hittas i parameter 1<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Ny sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som används för att ersätta strängen i parameter 2 när en matchning hittas i parameter 1.|  
|LED| Genererar en globalt unik sträng (aka. GUID). Följande utdata kan till exempel genereras `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. En enkel formats specifikation som anger [hur värdet för denna GUID ska formateras](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Format parametern kan vara "N", "D", "B", "P" eller "X". Om formatet inte anges används "D".|  
|toLower|Konverterar en sträng till gemener. Till exempel returnerar `two by two is four`följande:`toLower('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som ska konverteras till mindre Skift läge. Om ett tecken i strängen inte har en motsvarande gemen, inkluderas det i den returnerade strängen.|  
|toUpper|Konverterar en sträng till versaler. Till exempel returnerar `TWO BY TWO IS FOUR`följande uttryck:`toUpper('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som ska konverteras till versal hölje. Om ett tecken i strängen inte har en motsvarande versal, inkluderas det i den returnerade strängen.|  
|indexof|Hitta indexet för ett värde i en sträng som inte är Skift läges känsligt. Indexet är noll-baserat. Till exempel returnerar `7`följande uttryck:`indexof('hello, world.', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Värdet att söka i indexet för.|  
|lastindexof|Hitta det sista indexet för ett värde i en sträng som inte är Skift läges känsligt. Indexet är noll-baserat. Till exempel returnerar `3`följande uttryck:`lastindexof('foofoo', 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Värdet att söka i indexet för.|  
|StartsWith|Kontrollerar om strängen börjar med ett värde som inte är Skift läges okänslig. Till exempel returnerar `true`följande uttryck:`startswith('hello, world', 'hello')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som strängen kan börja med.|  
|EndsWith|Kontrollerar om strängen slutar med ett värde som inte är Skift läges okänslig. Till exempel returnerar `true`följande uttryck:`endswith('hello, world', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som kan innehålla värdet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som strängen kan sluta med.|  
|split|Delar upp strängen med en avgränsare. Till exempel returnerar `["a", "b", "c"]`följande uttryck:`split('a;b;c',';')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Den sträng som delas.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Avgränsaren.|  
  
  
## <a name="collection-functions"></a>Samlings funktioner  
 Dessa funktioner fungerar över samlingar som matriser, strängar och ibland ord listor.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|contains|Returnerar true om ord listan innehåller en nyckel, listan innehåller värde eller sträng innehåller under sträng. Till exempel returnerar följande uttryck`true:``contains('abacaba','aca')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Inom samlingen<br /><br /> **Beskrivning**: Obligatoriskt. Samlingen att söka i.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sök efter objekt<br /><br /> **Beskrivning**: Obligatoriskt. Objektet att söka efter i **samlingen**.|  
|length|Returnerar antalet element i en matris eller sträng. Till exempel returnerar `3`följande uttryck:`length('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Samlingen för att hämta längden på.|  
|saknas|Returnerar true om objektet, matrisen eller strängen är tom. Till exempel returnerar `true`följande uttryck:<br /><br /> `empty('')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Samlingen för att kontrol lera om den är tom.|  
|överlappning|Returnerar en enskild matris eller ett objekt med gemensamma element mellan matriserna eller objekten som skickats till den. Den här funktionen returnerar `[1, 2]`till exempel:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametrarna för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av dessa). Om det finns två objekt med samma namn visas det sista objektet med det namnet i det sista objektet.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn på**: Samling *n*<br /><br /> **Beskrivning**: Obligatoriskt. De samlingar som ska utvärderas. Ett objekt måste finnas i alla samlingar som skickas in för att visas i resultatet.|  
|Union|Returnerar en enskild matris eller ett objekt med alla element i matrisen eller objektet som skickas till den. Den här funktionen returnerar till exempel`[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametrarna för funktionen kan antingen vara en uppsättning objekt eller en uppsättning matriser (inte en blandning av dessa). Om det finns två objekt med samma namn i slut resultatet visas det sista objektet med det namnet i det sista objektet.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn på**: Samling *n*<br /><br /> **Beskrivning**: Obligatoriskt. De samlingar som ska utvärderas. Ett objekt som visas i någon av samlingarna visas i resultatet.|  
|förstagångskörningen|Returnerar det första elementet i matrisen eller strängen som har skickats. Den här funktionen returnerar `0`till exempel:<br /><br /> `first([0,2,3])`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Den samling som det första objektet ska tas från.|  
|pågå|Returnerar det sista elementet i matrisen eller strängen som har skickats. Den här funktionen returnerar `3`till exempel:<br /><br /> `last('0123')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Den samling som det sista objektet ska tas från.|  
|take|Returnerar de första **Count** -elementen från matrisen eller strängen som skickades, till exempel `[1, 2]`den här funktionen returnerar:`take([1, 2, 3, 4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Den samling som ska ta det första **antalet** objekt från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Count<br /><br /> **Beskrivning**: Obligatoriskt. Antalet objekt som ska tas från **samlingen**. Måste vara ett positivt heltal.|  
|hoppa över|Returnerar elementen i matrisen som börjar vid index **antal**, till exempel den här `[3, 4]`funktionen returnerar:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Collection<br /><br /> **Beskrivning**: Obligatoriskt. Samlingen som hoppar över det första **antalet** objekt från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Count<br /><br /> **Beskrivning**: Obligatoriskt. Antalet objekt som ska tas bort från början av **samlingen**. Måste vara ett positivt heltal.|  
  
## <a name="logical-functions"></a>Logiska funktioner  
 Dessa funktioner är användbara i villkor, de kan användas för att utvärdera vilken typ av logik som helst.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|är lika med|Returnerar true om två värden är lika. Om Parameter1 till exempel är foo, returnerar `true`följande uttryck:`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Objekt 1<br /><br /> **Beskrivning**: Obligatoriskt. Objektet som ska jämföras med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Objekt 2<br /><br /> **Beskrivning**: Obligatoriskt. Objektet som ska jämföras med **objekt 1**.|  
|mindre|Returnerar true om det första argumentet är mindre än det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar `true`följande uttryck:`less(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Objekt 1<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är mindre än **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Objekt 2<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är större än **objekt 1**.|  
|lessOrEquals|Returnerar true om det första argumentet är mindre än eller lika med det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar `true`följande uttryck:`lessOrEquals(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Objekt 1<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är mindre än eller lika med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Objekt 2<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är större än eller lika med **objekt 1**.|  
|greater|Returnerar true om det första argumentet är större än det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar `false`följande uttryck:`greater(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Objekt 1<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är större än **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Objekt 2<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är mindre än **objekt 1**.|  
|Större|Returnerar true om det första argumentet är större än eller lika med det andra. Observera att värden endast kan vara av typen Integer, float eller String. Till exempel returnerar `false`följande uttryck:`greaterOrEquals(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Objekt 1<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är större än eller lika med **objekt 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Objekt 2<br /><br /> **Beskrivning**: Obligatoriskt. Objektet för att kontrol lera om det är mindre än eller lika med **objekt 1**.|  
|och|Returnerar true om båda parametrarna är sanna. Båda argumenten måste vara booleska värden. Följande returnerar `false`:`and(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Boolesk 1<br /><br /> **Beskrivning**: Obligatoriskt. Det första argumentet som måste vara `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Boolesk 2<br /><br /> **Beskrivning**: Obligatoriskt. Det andra argumentet måste vara `true`.|  
|eller|Returnerar true om någon av parametrarna är true. Båda argumenten måste vara booleska värden. Följande returnerar `true`:`or(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Boolesk 1<br /><br /> **Beskrivning**: Obligatoriskt. Det första argumentet som kan vara `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Boolesk 2<br /><br /> **Beskrivning**: Obligatoriskt. Det andra argumentet kan vara `true`.|  
|inte|Returnerar true om parametern är `false`. Båda argumenten måste vara booleska värden. Följande returnerar `true`:`not(contains('200 Success','Fail'))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Boolesk<br /><br /> **Beskrivning**: Returnerar true om parametern är `false`. Båda argumenten måste vara booleska värden. Följande returnerar `true`:`not(contains('200 Success','Fail'))`|  
|eventuella|Returnerar ett angivet värde baserat på om uttrycket tillhandahöll resultatet i `true` eller. `false`  Till exempel returnerar `"yes"`följande:`if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Uttryck<br /><br /> **Beskrivning**: Obligatoriskt. Ett booleskt värde som avgör vilket värde som returneras av uttrycket.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sant<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som ska returneras om uttrycket är `true`.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: False<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som ska returneras om uttrycket är `false`.|  
  
## <a name="conversion-functions"></a>Konverteringsfunktioner  
 Dessa funktioner används för att konvertera mellan var och en av de ursprungliga typerna på språket:  
  
-   sträng  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   lagringsmatriser  
  
-   lista  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|int|Konvertera parametern till ett heltal. Följande uttryck returnerar till exempel 100 som ett tal i stället för en sträng:`int('100')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till ett heltal.|  
|sträng|Konvertera parametern till en sträng. Till exempel returnerar `'10'`följande uttryck:  `string(10)`Du kan också konvertera ett objekt till en sträng, till exempel om parametern **foo** är ett objekt med en egenskap `bar : baz`, returnerar följande `{"bar" : "baz"}``string(pipeline().parameters.foo)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till en sträng.|  
|json|Konvertera parametern till ett JSON-värde. Det är motsatsen till sträng (). Till exempel returnerar `[1,2,3]` följande uttryck som en matris i stället för en sträng:<br /><br /> `json('[1,2,3]')`<br /><br /> På samma sätt kan du konvertera en sträng till ett objekt. `json('{"bar" : "baz"}')` Returnerar till exempel:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som konverteras till ett internt typ värde.<br /><br /> JSON-funktionen stöder även XML-indata. Till exempel parameter värde för:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> konverteras till följande JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Omvandla parameter argumentet till ett flyttal-nummer. Till exempel returnerar `10.333`följande uttryck:`float('10.333')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till ett flytt ALS nummer.|  
|bool|Konvertera parametern till ett booleskt värde. Till exempel returnerar `false`följande uttryck:`bool(0)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till ett booleskt värde.|  
|coalesce|Returnerar det första icke-null-objektet i de angivna argumenten. Obs: en tom sträng är inte null. Om t. ex. parametrarna 1 och 2 inte har definierats returneras `fallback`följande:`coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Namn på**: Objekt*n*<br /><br /> **Beskrivning**: Obligatoriskt. De objekt som ska sökas efter `null`.|  
|Base64|Returnerar den base64-representation av Indatasträngen. Till exempel returnerar `c29tZSBzdHJpbmc=`följande uttryck:`base64('some string')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng 1<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som ska kodas i base64-representation.|  
|base64ToBinary|Returnerar en binär representation av en Base64-kodad sträng. Till exempel returnerar följande uttryck en binär representation av en sträng: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Den base64-kodade strängen.|  
|base64ToString|Returnerar en sträng representation av en based64-kodad sträng. Till exempel returnerar följande uttryck en sträng: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Den base64-kodade strängen.|  
|Binary|Returnerar en binär representation av ett värde.  Följande uttryck returnerar till exempel en binär representation av en sträng:`binary(‘some string’).`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till Binary.|  
|dataUriToBinary|Returnerar en binär representation av en data-URI. Till exempel returnerar följande uttryck en binär representation av en sträng:`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Data-URI: n som ska konverteras till binär representation.|  
|dataUriToString|Returnerar en sträng representation av en data-URI. Till exempel returnerar följande uttryck en sträng:`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br />**Beskrivning**: Obligatoriskt. Data-URI: n som ska konverteras till sträng representation.|  
|dataUri|Returnerar en data-URI för ett värde. Följande uttryck returnerar till exempel data:`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn på**: Value<br /><br />**Beskrivning**: Obligatoriskt. Värdet som ska konverteras till data-URI.|  
|decodeBase64|Returnerar en sträng representation av en based64-sträng. Till exempel returnerar `some string`följande uttryck:`decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Returnerar en sträng representation av en based64-sträng.|  
|encodeUriComponent|URL – undantar den sträng som skickades. Till exempel returnerar `You+Are%3ACool%2FAwesome`följande uttryck:`encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen som ska undantas URL-osäkra tecken från.|  
|decodeUriComponent|FN-URL – avstår den sträng som skickades. Till exempel returnerar `You Are:Cool/Awesome`följande uttryck:`encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. Strängen att avkoda URL-osäkra tecken från.|  
|decodeDataUri|Returnerar en binär representation av en indata-URI-sträng. Följande uttryck returnerar till exempel den binära representation av `some string`:`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br /> **Beskrivning**: Obligatoriskt. DataURI för att avkoda till en binär representation.|  
|uriComponent|Returnerar en URI-kodad representation av ett värde. Till exempel returnerar följande uttryck`You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parameter information: Många 1, namn: Sträng, beskrivning: Obligatoriskt. Strängen som ska vara URI-kodad.|  
|uriComponentToBinary|Returnerar en binär representation av en URI-kodad sträng. Följande uttryck returnerar till exempel en binär representation av `You Are:Cool/Awesome`:`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Sträng<br /><br />**Beskrivning**: Obligatoriskt. URI-kodad sträng.|  
|uriComponentToString|Returnerar en sträng representation av en URI-kodad sträng. Till exempel returnerar `You Are:Cool/Awesome`följande uttryck:`uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn på**: Sträng<br /><br />**Beskrivning**: Obligatoriskt. URI-kodad sträng.|  
|xml|Returnera en XML-representation av värdet. Till exempel returnerar följande uttryck ett XML-innehåll som representeras `xml('\<name>Alan\</name>')`av `'\<name>Alan\</name>'`:. XML-funktionen stöder även indata från JSON-objekt. Till exempel konverteras parametern `{ "abc": "xyz" }` till ett XML-innehåll`\<abc>xyz\</abc>`<br /><br /> **Parameter nummer**: 1<br /><br />**Namn på**: Value<br /><br />**Beskrivning**: Obligatoriskt. Värdet som ska konverteras till XML.|  
|XPath|Returnera en matris med XML-noder som matchar XPath-uttrycket för ett värde som XPath-uttrycket utvärderar till.<br /><br />  **Exempel 1**<br /><br /> Antag att värdet för parametern P1 är en sträng representation av följande XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Den här koden:`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Returnerar<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> tanke<br /><br /> 2. Den här koden:`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Returnerar<br /><br /> `13`<br /><br /> <br /><br /> **Exempel 2**<br /><br /> Följande XML-innehåll har angetts:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Den här koden:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> eller<br /><br /> 2. Den här koden:`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> avkastning<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> och<br /><br /> 3. Den här koden:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> avkastning<br /><br /> ``bar``<br /><br /> **Parameter nummer**: 1<br /><br />**Namn på**: Xml<br /><br />**Beskrivning**: Obligatoriskt. XML-filen som XPath-uttrycket ska utvärderas på.<br /><br /> **Parameter nummer**: 2<br /><br />**Namn på**: XPath<br /><br />**Beskrivning**: Obligatoriskt. Det XPath-uttryck som ska utvärderas.|  
|array|Konvertera parametern till en matris.  Till exempel returnerar `["abc"]`följande uttryck:`array('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Value<br /><br /> **Beskrivning**: Obligatoriskt. Värdet som konverteras till en matris.|
|createArray|Skapar en matris från parametrarna.  Till exempel returnerar `["a", "c"]`följande uttryck:`createArray('a', 'c')`<br /><br /> **Parameter nummer**: 1... m<br /><br /> **Namn på**: Valfritt n<br /><br /> **Beskrivning**: Obligatoriskt. Värdena som ska kombineras i en matris.|

## <a name="math-functions"></a>Matematikfunktioner  
 Dessa funktioner kan användas för antingen typer av tal: **heltal** och flyttal.  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|lägg till|Returnerar resultatet av additionen av de två talen. Den här funktionen returnerar `20.333`till exempel:`add(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Summand 1<br /><br /> **Beskrivning**: Obligatoriskt. Talet som ska läggas till i **Summand 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Summand 2<br /><br /> **Beskrivning**: Obligatoriskt. Talet som ska läggas till i **Summand 1**.|  
|Build|Returnerar resultatet av subtraktionen av de två talen. Den här funktionen returnerar till exempel: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: MINUEND<br /><br /> **Beskrivning**: Obligatoriskt. Numret som **SUBTRAHEND** tas bort från.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: SUBTRAHEND<br /><br /> **Beskrivning**: Obligatoriskt. Det tal som ska tas bort från **MINUEND**.|  
|mul|Returnerar resultatet av multiplikationen av de två talen. Till exempel returnerar `103.33`följande:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Multiplicand 1<br /><br /> **Beskrivning**: Obligatoriskt. Talet som **Multiplicand 2** ska multipliceras med.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Multiplicand 2<br /><br /> **Beskrivning**: Obligatoriskt. Talet som **Multiplicand 1** ska multipliceras med.|  
|div|Returnerar resultatet av divisionen av de två talen. Till exempel returnerar `1.0333`följande:<br /><br /> `div(10.333,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Preferens<br /><br /> **Beskrivning**: Obligatoriskt. Talet som ska divideras med **divisorn**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Divisor<br /><br /> **Beskrivning**: Obligatoriskt. Numret att dividera utdelningen med.|  
|rest|Returnerar resultatet av resten efter divisionen av de två talen (modulo). Till exempel returnerar `2`följande uttryck:<br /><br /> `mod(10,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Preferens<br /><br /> **Beskrivning**: Obligatoriskt. Talet som ska divideras med **divisorn**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Divisor<br /><br /> **Beskrivning**: Obligatoriskt. Numret att dividera utdelningen med. Efter divisionen tas resten.|  
|min|Det finns två olika mönster för att anropa den här funktionen: `min([0,1,2])`Den här min tar en matris. Det här uttrycket `0`returnerar. Den här funktionen kan också ta en kommaavgränsad lista med värden:  `min(0,1,2)`Den här funktionen returnerar också 0. Obs! alla värden måste vara siffror, så om parametern är en matris behöver den bara innehålla siffror.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Samling eller värde<br /><br /> **Beskrivning**: Obligatoriskt. Det kan antingen vara en matris med värden för att hitta det lägsta värdet eller det första värdet i en mängd.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Namn på**: Värde *n*<br /><br /> **Beskrivning**: Valfritt. Om den första parametern är ett värde kan du skicka ytterligare värden och minst alla skickade värden returneras.|  
|max|Det finns två olika mönster för att anropa den här funktionen:`max([0,1,2])`<br /><br /> Detta Max tar en matris. Det här uttrycket `2`returnerar. Den här funktionen kan också ta en kommaavgränsad lista med värden:  `max(0,1,2)`Den här funktionen returnerar 2. Obs! alla värden måste vara siffror, så om parametern är en matris behöver den bara innehålla siffror.<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Samling eller värde<br /><br /> **Beskrivning**: Obligatoriskt. Det kan antingen vara en matris med värden för att hitta det högsta värdet eller det första värdet i en mängd.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Namn på**: Värde *n*<br /><br /> **Beskrivning**: Valfritt. Om den första parametern är ett värde kan du skicka ytterligare värden och det högsta värdet för alla skickade värden returneras.|  
|intervall| Genererar en matris med heltal från ett visst tal och du definierar längden på den returnerade matrisen. Den här funktionen returnerar `[3,4,5,6]`till exempel:<br /><br /> `range(3,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Start index<br /><br /> **Beskrivning**: Obligatoriskt. Det är det första heltalet i matrisen.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Count<br /><br /> **Beskrivning**: Obligatoriskt. Antal heltal i matrisen.|  
|slump| Genererar ett slumpmässigt heltal inom det angivna intervallet (inklusive båda ändar. Detta kan till exempel returnera `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Minimum<br /><br /> **Beskrivning**: Obligatoriskt. Det lägsta heltal som kunde returneras.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Maximal<br /><br /> **Beskrivning**: Obligatoriskt. Det högsta heltalet som kunde returneras.|  
  
## <a name="date-functions"></a>Datum funktioner  
  
|Funktionsnamn|Beskrivning|  
|-------------------|-----------------|  
|utcnow|Returnerar den aktuella tidstämpeln som en sträng. Exempel `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addseconds|Lägger till ett heltal i sekunder till en sträng tidstämpel som skickats in. Antalet sekunder kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Exempel `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Timestamp<br /><br /> **Beskrivning**: Obligatoriskt. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Sekunder<br /><br /> **Beskrivning**: Obligatoriskt. Det antal sekunder som ska läggas till. Kan vara negativt för att subtrahera sekunder.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addminutes|Lägger till ett heltal i minuter till en sträng tidstämpel som skickats in. Antalet minuter kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Till exempel `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Timestamp<br /><br /> **Beskrivning**: Obligatoriskt. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Minuter<br /><br /> **Beskrivning**: Obligatoriskt. Det antal minuter som ska läggas till. Kan vara negativt för att subtrahera minuter.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|addhours|Lägger till ett heltal i timmar till en sträng tidstämpel som skickats in. Antalet timmar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Exempel `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Timestamp<br /><br /> **Beskrivning**: Obligatoriskt. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Timmar<br /><br /> **Beskrivning**: Obligatoriskt. Antalet timmar som ska läggas till. Kan vara negativt för att ta bort timmar.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|adddays|Lägger till ett heltals antal dagar till en sträng-tidsstämpel som skickats in. Antalet dagar kan vara positivt eller negativt. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Exempel `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Timestamp<br /><br /> **Beskrivning**: Obligatoriskt. En sträng som innehåller tiden.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Dagar<br /><br /> **Beskrivning**: Obligatoriskt. Det antal dagar som ska läggas till. Kan vara negativt för att subtrahera dagar.<br /><br /> **Parameter nummer**: 3<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  
|formatDateTime|Returnerar en sträng i datum format. Resultatet är en sträng i ISO 8601-format ("o") som standard, om inte en format specificerare anges. Exempel `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Namn på**: Date<br /><br /> **Beskrivning**: Obligatoriskt. En sträng som innehåller datumet.<br /><br /> **Parameter nummer**: 2<br /><br /> **Namn på**: Format<br /><br /> **Beskrivning**: Valfritt. Antingen ett [enda format](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) eller ett [anpassat format-mönster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) som visar hur du formaterar värdet för den här tidsstämpeln. Om formatet inte anges används ISO 8601-formatet ("o").|  

## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [Systemvariabler](control-flow-system-variables.md).
