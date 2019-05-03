---
title: Villkorlig kognitiv sökning färdighet (Azure Search) | Microsoft Docs
description: Villkorlig kunskaper som gör att filtrering, skapar standardinställningar och koppla värden.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028432"
---
#   <a name="conditional-skill"></a>Villkorlig färdighet

Den **villkorlig färdighet** möjliggör en mängd olika scenarier som kräver en boolesk operation bestämma de data som ska tilldelas till utdata. Några vanliga scenarier: Filtrera, tilldela ett standardvärde och sammanfogar data baserat på ett villkor.

Följande pseudocode förklaras vad villkorlig färdighet uppnår:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Kompetensen är inte bunden till en Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md), men att åsidosätta den **kostnadsfri** resurs-alternativ som begränsar du till ett litet antal dagliga enrichments per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Utvärderade fält

Kompetensen är speciell eftersom dess indata är utvärderade fält.

Följande är giltiga värden för ett uttryck:

-   Anteckningens sökvägar (sökvägar i uttryck måste avgränsas med ”$(" and ")”) <br/>
    Exempel:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Litteraler (strängar, tal, true, false, null) <br/>
    Exempel:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Uttryck som använder en jämförelseoperator (==,! =, > =, >, < =, <) <br/>
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

-   Uttryck som använder en numerisk operator (+, -, \*, /, %) <br/>
    Exempel: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

På grund av den utvärdering som stöds, kan villkorlig färdighet användas för mindre omvandlingsscenarier. Se exempel [färdighet definition 4](#transformation-examples) ett exempel.

## <a name="skill-inputs"></a>Färdighet indata
Indata är skiftlägeskänsliga.

| Indata      | Beskrivning |
|-------------|-------------|
| villkor   | Detta indata är en [utvärderas fältet](#evaluated-fields) som representerar villkoret du vill utvärdera. Det här villkoret ska utvärderas till ett booleskt värde (SANT eller FALSKT).   <br/>  Exempel: <br/> ”= true” <br/> ”= $(/document/language) ==” fr ”” <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Detta indata är en [utvärderas fältet](#evaluated-fields). Värdet som returneras om villkoret utvärderas till true. Konstanter strängar som ska returneras i ' ' citattecken. <br/>Exempelvärden: <br/> "= 'contract'"<br/>”= $(/ dokument/contractType)” <br/> ”= $(/dokument/entiteter/\*)” <br/> |
| whenFalse   | Detta indata är en [utvärderas fältet](#evaluated-fields). Värdet som returneras om villkoret utvärderas till false.  <br/>Exempelvärden: <br/> "= 'contract'"<br/>”= $(/ dokument/contractType)” <br/> ”= $(/dokument/entiteter/\*)” <br/>

## <a name="skill-outputs"></a>Färdighet utdata
Det finns ett enda utflöde som kallas ”utdata”. Värdet för whenFalse om villkoret är FALSKT eller whenTrue returneras om villkoret är sant.

## <a name="examples"></a>Exempel

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Färdighet exempeldefinition 1: Filtrera dokument att returnera endast ”franska” dokument

Följande utdata returneras en matris med meningar (”/ dokument/frenchSentences”) om språket i dokumentet är franska. Om språket inte är franska värdet ska anges till null.

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
Om ”/ dokument/frenchSentences” används som den *kontext* av en annan färdighet kompetensen körs bara om ”/ dokument/frenchSentences” inte har angetts till null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Färdighet exempeldefinition 2: Ange ett standardvärde när det inte finns.

Följande utdata skapas en anteckning (”/ dokument/languageWithDefault”) som är inställd på något av språken för dokumentet eller ”es” om språket inte har angetts.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Färdighet exempeldefinition 3: Sammanfoga värden från två olika fält i ett fält

I det här exemplet har några meningar en *frenchSentiment* egenskapen. När den *frenchSentiment* -egenskapen är null, vi skulle vilja använda den *englishSentiment* värde. Vi tilldela utdatan till en medlem som kallas bara *sentiment* (”/ dokumentera/sentiment / * / sentiment”).

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

## <a name="transformation-examples"></a>Omvandling exempel
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Färdighet exempeldefinition 4: Utföra dataomvandlingar på ett fält

I det här exemplet vi får en känsla mellan 0 och 1 och vi vill omvandla det så att det är mellan 1 och 1. Det här är en liten matematiska omvandling att vi kan göra med hjälp av villkorlig färdigheter.

I det här exemplet använder vi aldrig villkorlig aspekt av färdigheten som villkoret alltid är sant. 

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
Observera att några av parametrarna utvärderas, så måste du vara särskilt noggrann följer mönstret dokumenterade. Uttryck måste börja med en är lika med inloggning ”=” och sökvägar måste avgränsas med ”$(" and ")”. Se till att placera din strängar i ”enkla” eftersom det kommer att hjälpa uttrycksutvärderaren skilja mellan strängar och faktiska sökvägarna och operatörer. Kontrollera också att placera ett tomt utrymme runt operatörer (till exempel en * i en sökväg har en annan betydelse än multiplikationsoperatorn).


## <a name="next-steps"></a>Nästa steg

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
