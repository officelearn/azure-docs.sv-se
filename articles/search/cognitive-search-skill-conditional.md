---
title: Villkorlig kognitiv skicklighet
titleSuffix: Azure Cognitive Search
description: Den villkorliga färdigheten i Azure Cognitive Search gör det möjligt att filtrera, skapa standardvärden och slå samman värden i en kompetensdefinition.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792050"
---
# <a name="conditional-cognitive-skill"></a>Villkorlig kognitiv skicklighet

Den **villkorliga** färdigheten aktiverar Azure Cognitive Search-scenarier som kräver en boolesk åtgärd för att bestämma vilka data som ska tilldelas en utdata. Dessa scenarier omfattar filtrering, tilldelning av ett standardvärde och sammanslagning av data baserat på ett villkor.

Följande pseudokod visar vad den villkorliga färdigheten åstadkommer:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Den här färdigheten är inte bunden till ett Azure Cognitive Services-API och du debiteras inte för att du använder den. Du bör dock fortfarande [bifoga en Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md) för att åsidosätta resursalternativet "Gratis" som begränsar dig till ett litet antal enrichments per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.util.conditionalskill


## <a name="evaluated-fields"></a>Utvärderade fält

Den här färdigheten är speciell eftersom dess indata utvärderas fält.

Följande objekt är giltiga värden för ett uttryck:

-   Anteckningsbanor (banor i uttryck måste avgränsas med "$(" och ")")
 <br/>
    Exempel:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Litteraler (strängar, siffror, sant, falskt, null) <br/>
    Exempel:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Uttryck som använder jämförelseoperatorer (==, !=, >=, >, <=, <) <br/>
    Exempel:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Uttryck som använder booleska operatorer (&&, ||, !, ^) <br/>
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

Eftersom den villkorliga färdigheten stöder utvärdering kan du använda den i scenarier med mindre omvandlingar. Se till exempel [färdighetsdefinition 4](#transformation-example).

## <a name="skill-inputs"></a>Indata för färdighet
Indata är skiftlägeskänsliga.

| Indata   | Beskrivning |
|-------------|-------------|
| Villkor   | Den här indata är ett [utvärderat fält](#evaluated-fields) som representerar villkoret att utvärdera. Detta villkor bör utvärderas till ett booleskt värde (*sant* eller *falskt*).   <br/>  Exempel: <br/> "= sant" <br/> "= $(/dokument/språk) =='fr'" <br/> "= $(/dokument/sidor/\*/språk) == $(/dokument/expectedLanguage)" <br/> |
| whenTrue    | Den här indata är ett [utvärderat fält](#evaluated-fields) som representerar värdet som ska returneras om villkoret utvärderas till *true*. Konstanter strängar bör returneras med enkla citattecken (' och '). <br/>Exempelvärden: <br/> "= "kontrakt""<br/>"= $(/dokument/kontraktType)" <br/> "= $(/dokument/enheter/\*)" <br/> |
| närFalse   | Den här indata är ett [utvärderat fält](#evaluated-fields) som representerar värdet att returnera om villkoret utvärderas till *false*. <br/>Exempelvärden: <br/> "= "kontrakt""<br/>"= $(/dokument/kontraktType)" <br/> "= $(/dokument/enheter/\*)" <br/>

## <a name="skill-outputs"></a>Utdata för färdighet
Det finns en enda utgång som helt enkelt kallas "output". Värdet *returneras närFalse returneras* om villkoret är falskt eller *närBeta* om villkoret är sant.

## <a name="examples"></a>Exempel

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Exempel på färdighetsdefinition 1: Filtrera dokument för att endast returnera franska dokument

Följande utdata returnerar en matris med meningar ("/dokument/frenchSentences") om språket i dokumentet är franskt. Om språket inte är franskt anges värdet till *null*.

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
Om "/document/frenchSentences" används som *kontext för* en annan färdighet körs den färdigheten endast om "/document/frenchSentences" inte är inställd på *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Exempel på färdighetsdefinition 2: Ange ett standardvärde för ett värde som inte finns

Följande utdata skapar en anteckning ("/dokument/languageWithDefault") som är inställd på dokumentets språk eller till "es" om språket inte är inställt.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Exempel på färdighetsdefinition 3: Sammanfoga värden från två fält till ett

I det här exemplet har vissa meningar en *frenchSentiment-egenskap.* När *fransmannens* egendom är null vill vi använda *värdet englishSentiment.* Vi tilldelar utdata till en medlem som kallas *sentiment* ("/document/sentiment/*/sentiment").

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

## <a name="transformation-example"></a>Exempel på omformning
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Exempel på färdighetsdefinition 4: Dataomvandling på ett enda fält

I det här exemplet får vi en *känsla* som är mellan 0 och 1. Vi vill omvandla det till mellan -1 och 1. Vi kan använda den villkorliga färdigheten för att göra denna mindre omvandling.

I det här exemplet använder vi inte den villkorliga aspekten av färdigheten eftersom villkoret alltid är *sant*.

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

## <a name="special-considerations"></a>Särskilda överväganden
Vissa parametrar utvärderas, så du måste vara särskilt noga med att följa det dokumenterade mönstret. Uttryck måste börja med ett likhetstecken. En sökväg måste avgränsas med "$(" och ")". Se till att placera strängar med enkla citattecken. Det hjälper utvärderaren att skilja mellan strängar och faktiska banor och operatorer. Se också till att placera tomt utrymme runt operatörer (till exempel betyder en "*" i en bana något annat än att multiplicera).


## <a name="next-steps"></a>Nästa steg

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
