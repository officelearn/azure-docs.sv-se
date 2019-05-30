---
title: Villkorlig kognitiv sökning färdighet (Azure Search) | Microsoft Docs
description: Villkorlig färdighet gör det möjligt för filtrering, skapar standardinställningar och koppla värden.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791507"
---
#   <a name="conditional-skill"></a>Villkorlig färdighet

Den *villkorlig färdighet* kan Azure Search-scenarier som kräver en boolesk operation att fastställa vilka data som ska tilldelas utdata. Några vanliga scenarier filtrering, att tilldela ett standardvärde och sammanfogar data baserat på ett villkor.

Följande pseudocode visar villkorlig färdighet uppnår:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Kompetensen är inte bunden till en Azure Cognitive Services-API och du debiteras inte för att använda den. Du bör dock fortfarande [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md) åsidosätta alternativet ”kostnadsfri” resurs som begränsar du till ett litet antal enrichments per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Utvärderade fält

Kompetensen är speciell eftersom dess indata är utvärderade fält.

Följande objekt är giltiga värden för ett uttryck:

-   Anteckningens sökvägar (sökvägar i uttryck måste avgränsas med ”$(" and ")”)
 <br/>
    Exempel:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Litteraler (strängar, tal, true, false, null) <br/>
    Exempel:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Uttryck som använder jämförelseoperatorer (==,! =, > =, >, < =, <) <br/>
    Exempel:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Uttryck med booleska operatorer (& &, ||,!, ^) <br/>
    Exempel:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Uttryck som använder numeriska operatorer (+, -, \*, /, %) <br/>
    Exempel: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Eftersom villkorlig färdighet stöder utvärdering kan använda du den i mindre omvandlingsscenarier. Se exempelvis [färdighet definition 4](#transformation-example).

## <a name="skill-inputs"></a>Färdighet indata
Indata är skiftlägeskänsliga.

| Indata   | Beskrivning |
|-------------|-------------|
| condition   | Detta indata är en [utvärderas fältet](#evaluated-fields) som representerar villkoret du vill utvärdera. Det här villkoret ska utvärderas till ett booleskt värde (*SANT* eller *FALSKT*).   <br/>  Exempel: <br/> ”= true” <br/> ”= $(/document/language) ==” fr ”” <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Detta indata är en [utvärderas fältet](#evaluated-fields) som representerar värdet som returneras om villkoret utvärderas till *SANT*. Konstanter strängar som ska returneras inom enkla citattecken ('och'). <br/>Exempelvärden: <br/> "= 'contract'"<br/>”= $(/ dokument/contractType)” <br/> ”= $(/dokument/entiteter/\*)” <br/> |
| whenFalse   | Detta indata är en [utvärderas fältet](#evaluated-fields) som representerar värdet som returneras om villkoret utvärderas till *FALSKT*. <br/>Exempelvärden: <br/> "= 'contract'"<br/>”= $(/ dokument/contractType)” <br/> ”= $(/dokument/entiteter/\*)” <br/>

## <a name="skill-outputs"></a>Färdighet utdata
Det finns ett enda utflöde som kallas ”utdata”. Returnerar värdet *whenFalse* om villkoret är FALSKT eller *whenTrue* om villkoret är sant.

## <a name="examples"></a>Exempel

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Färdighet exempeldefinition 1: Filtrera dokument för att returnera endast franska dokument

Följande utdata returnerar en matris med meningar (”/ dokument/frenchSentences”) om språket i dokumentet är franska. Om språket inte är franska, värdet till *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Om ”/ dokument/frenchSentences” används som den *kontext* av en annan färdighet körs kompetensen bara om ”/ dokument/frenchSentences” inte är inställd på *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Färdighet exempeldefinition 2: Ange ett standardvärde för ett värde som inte finns

Följande utdata skapar en anteckning (”/ dokument/languageWithDefault”) som har angetts till språket i dokumentet eller ”es” om språket inte anges.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Färdighet exempeldefinition 3: Sammanfoga värden från två fält i en

I det här exemplet har några meningar en *frenchSentiment* egenskapen. När den *frenchSentiment* -egenskapen är null, ska vi använda den *englishSentiment* värde. Vi tilldela utdatan till en medlem som kallas *sentiment* (”/ dokumentera/sentiment / * / sentiment”).

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Omvandling exempel
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Färdighet exempeldefinition 4: Transformering av data på ett fält

I det här exemplet vi tar emot en *sentiment* som ligger mellan 0 och 1. Vi vill omvandla den till att vara mellan 1 och 1. Vi kan använda villkorlig kunskaper för att göra detta mindre.

I det här exemplet vi använder inte villkorlig aspekt av kompetensen eftersom villkoret alltid är *SANT*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Att tänka på
Vissa parametrar utvärderas så måste du vara särskilt noga med att följa dokumenterade. Uttryck måste börja med ett likhetstecken. En sökväg måste avgränsas med ”$(" and ")”. Se till att placera strängar inom enkla citattecken. Som hjälper uttrycksutvärderaren skilja mellan strängar och faktiska sökvägarna och operatörer. Kontrollera också att placera utrymme runt operatörer (till exempel en ”*” i en sökväg betyder det något annat än multiplicera).


## <a name="next-steps"></a>Nästa steg

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
