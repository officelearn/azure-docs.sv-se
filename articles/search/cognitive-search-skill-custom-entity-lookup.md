---
title: Kognitiv sökfärdighet för anpassad entitetssökning
titleSuffix: Azure Cognitive Search
description: Extrahera olika anpassade entiteter från text i en cognitive search-pipeline för Azure Cognitive Search. Den här färdigheten är för närvarande i offentlig förhandsversion.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369785"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Anpassad entitetsuppslag kognitiv färdighet (förhandsgranskning)

> [!IMPORTANT] 
> Den här färdigheten är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för portal eller .NET SDK.

Den **anpassade entitetsuppslagsfärdigheten** söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketterar den alla dokument med alla matchande entiteter. Färdigheten stöder också en grad av luddig matchning som kan tillämpas för att hitta matcher som är liknande men inte riktigt exakt.  

Den här färdigheten är inte bunden till ett Cognitive Services API och kan användas kostnadsfritt under förhandsgranskningsperioden. Du bör dock fortfarande [koppla en Cognitive Services-resurs](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)för att åsidosätta den dagliga anrikningsgränsen. Den dagliga gränsen gäller för fri åtkomst till Cognitive Services när den nås via Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Databegränsningar
+ Den maximala indatapoststorleken som stöds är 256 MB. Om du behöver dela upp dina data innan du skickar dem till den anpassade entitetsuppslagsfärdigheten kan du överväga att använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).
+ Den maximala entitetsdefinitionstabellen som stöds är 10 MB om den tillhandahålls med parametern *EntitiesDefitionUri.* 
+ Om entiteterna definieras infogade, med parametern *infogadeentitiesDefinition,* är den maximala storleken som stöds 10 kB.

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| enheterDefinitionUri    | Sökväg till en JSON- eller CSV-fil som innehåller all måltext som ska matchas mot. Den här entitetsdefinitionen läss i början av en indexeringskörning. Eventuella uppdateringar av den här filen i mitten av körningen kommer inte att realiseras förrän efterföljande körningar. Denna konfiguration måste vara tillgänglig via HTTPS. Se [Custom Entity Definition](#custom-entity-definition-format) Format" nedan för förväntat CSV- eller JSON-schema.|
|inlineEntitiesDefiniera | Infogade JSON-entitetsdefinitioner. Den här parametern ersätter parametern entitiesDefinitionUri om det finns. Högst 10 kB konfiguration får anges i linje. Se [Anpassad entitetsdefinition](#custom-entity-definition-format) nedan för förväntat JSON-schema. |
|standardSpråkkod |    (Valfritt) Språkkod för indatatexten som används för att tokenisera och avgränsa indatatext. Följande språk stöds: `da, de, en, es, fi, fr, it, ko, pt`. Standard är engelska`en`( ). Om du skickar ett språkkodslandkodformat används bara språkkodsdelen i formatet.  |


## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| text          | Texten att analysera.          |
| languageCode    | Valfri. Standardvärdet är `"en"`.  |


## <a name="skill-outputs"></a>Utdata för färdighet


| Utdatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| Enheter | En matris med objekt som innehåller information om de matchningar som hittades och relaterade metadata. Var och en av de identifierade enheterna kan innehålla följande fält:  <ul> <li> *namn*: Den översta enheten som identifierats. Entiteten representerar formuläret "normaliserad". </li> <li> *id*: En unik identifierare för entiteten enligt användarens definitionsformat som definierats.</li> <li> *beskrivning*: Entitetsbeskrivning enligt användarens definitionsdefinierande definitionsdefinierande definitionsbesing i "Custom Entity Definition Format". </li> <li> *typ:* Entitetstyp enligt användarens definitionsdefinierande definitionsformat definierar.</li> <li> *undertyp:* Entitetsundertyp enligt användarens definitionsdefinierande definitionsformat definierar.</li>  <li> *matchar*: Samling som beskriver var och en av matchningarna för den entiteten i källtexten. Varje match kommer att ha följande medlemmar: </li> <ul> <li> *text*: Den råa texten matchar från källdokumentet. </li> <li> *offset*: Platsen där matchningen hittades i texten. </li> <li> *längd*: Längden på den matchade texten. </li> <li> *matchDistance*: Antalet tecken som skiljer sig från det ursprungliga entitetsnamnet eller aliaset.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Anpassat entitetsdefinitionsformat

Det finns tre olika sätt att tillhandahålla listan över anpassade entiteter till skillen för anpassad entitetsökning. Du kan ange listan i en . CSV-fil, en . JSON-fil eller som en infogad definition som en del av färdighetsdefinitionen.  

Om definitionsfilen är en . CSV eller . JSON-filen måste filens sökväg tillhandahållas som en del av parametern *entitiesDefitionUri.* I det här fallet hämtas filen en gång i början av varje indexeringskörning. Filen måste vara tillgänglig så länge indexeraren är avsedd att köras. Dessutom måste filen kodas UTF-8.

Om definitionen anges i linje bör den anges så infogat som innehållet i *skillparametern InlineEntitiesDefinition.* 

### <a name="csv-format"></a>CSV-format

Du kan ange definitionen av de anpassade entiteterna att leta efter i en CSV-fil (Comma-Separated Value) genom att ange sökvägen till filen och ange den i skill-parametern *DefitionUri.* Sökvägen ska vara på en https-plats. Definitionsfilen kan vara upp till 10 MB i storlek.

CSV-formatet är enkelt. Varje rad representerar en unik entitet, som visas nedan:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

I det här fallet finns det tre entiteter som kan returneras som entiteter hittades (Bill Gates, Satya Nadella, Microsoft), men de kommer att identifieras om något av villkoren på raden (alias) matchas på texten. Om strängen "William H. Gates" till exempel finns i ett dokument returneras en matchning för entiteten "Bill Gates".

### <a name="json-format"></a>JSON-format

Du kan också ange definitionen av anpassade entiteter som ska sökas i en JSON-fil. JSON-formatet ger dig lite mer flexibilitet eftersom det gör att du kan definiera matchande regler per term. Du kan till exempel ange det luddiga matchande avståndet (Damerau-Levenshtein-avståndet) för varje term eller om matchningen ska vara skiftlägeskänslig eller inte. 

 Precis som med CSV-filer måste du ange sökvägen till JSON-filen och ange den i skill-parametern *DefitionUri.* Sökvägen ska vara på en https-plats. Definitionsfilen kan vara upp till 10 MB i storlek.

Den mest grundläggande JSON-definitionen av anpassad entitetslista kan vara en lista över entiteter som ska matchas:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Ett mer komplext exempel på en JSON-definition kan eventuellt ange id, beskrivning, typ och undertyp för varje entitet - liksom andra *alias*. Om en aliasterm matchas returneras även entiteten:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Tabellerna nedan beskriver mer information de olika konfigurationsparametrar som du kan ställa in när du definierar entiteterna så att de matchar:

|  Fältnamn  |        Beskrivning  |
|--------------|----------------------|
| namn | Den översta entitetens beskrivning. Matchningar i färdighetsutdata kommer att grupperas efter det här namnet, och det bör representera den "normaliserade" formen av texten som hittas.  |
| description  | (Valfritt) Det här fältet kan användas som en genomströmning för anpassade metadata om den matchade texten/texterna. Värdet för det här fältet visas med varje matchning av dess entitet i färdighetsutdata. |
| typ | (Valfritt) Det här fältet kan användas som en genomströmning för anpassade metadata om den matchade texten/texterna. Värdet för det här fältet visas med varje matchning av dess entitet i färdighetsutdata. |
| Subtyp | (Valfritt) Det här fältet kan användas som en genomströmning för anpassade metadata om den matchade texten/texterna. Värdet för det här fältet visas med varje matchning av dess entitet i färdighetsutdata. |
| id | (Valfritt) Det här fältet kan användas som en genomströmning för anpassade metadata om den matchade texten/texterna. Värdet för det här fältet visas med varje matchning av dess entitet i färdighetsutdata. |
| caseSensitive | (Valfritt) Standardvärdet till false. Booleskt värde som anger om jämförelser med entitetsnamnet ska vara känsliga för teckenhölje. Exempelfall okänsliga matchningar av "Microsoft" kan vara: microsoft, microSoft, MICROSOFT |
| fuzzyReitDistance | (Valfritt) Standard till 0. Maximalt värde på 5. Betecknar det acceptabla antalet olika tecken som fortfarande skulle utgöra en matchning med entitetsnamnet. Minsta möjliga fuzziness för en viss match returneras.  Till exempel, om redigeringsavståndet är inställt på 3, "Windows 10" skulle fortfarande matcha "Windows", "Windows10" och "windows 7". <br/> När fallkänslighet är inställd på false, räknas inte fallskillnader mot fuzziness tolerans, men annars gör. |
| defaultCaseSensitive | (Valfritt) Ändrar standardvärdet för skiftlägeskänslighet för den här entiteten. Den används för att ändra standardvärdet för alla alias skiftlägeskänsliga värden. |
| standardFuzzyReitDistance | (Valfritt) Ändrar standardvärdet för fuzzy edit distance för den här entiteten. Den kan användas för att ändra standardvärdet för alla alias fuzzyEditDistance värden. |
| Alias | (Valfritt) En matris med komplexa objekt som kan användas för att ange alternativa stavningar eller synonymer till rotentitetsnamnet. |

| Egenskaper för alias | Beskrivning |
|------------------|-------------|
| text  | Den alternativa stavningen eller representationen av ett namn på en målentitet.  |
| caseSensitive | (Valfritt) Fungerar på samma sätt som rotentiteten "caseSensitive" parameter ovan, men gäller endast detta alias. |
| fuzzyReitDistance | (Valfritt) Fungerar på samma sätt som rotentiteten "fuzzyEditDistance" parameter ovan, men gäller endast detta ett alias. |


### <a name="inline-format"></a>Infogat format

I vissa fall kan det vara bekvämare att ange en lista över anpassade entiteter för att matcha infogade direkt i färdighetsdefinitionen. I så fall kan du använda ett liknande JSON-format som det som beskrivs ovan, men det är inlined i färdighetsdefinitionen.
Endast konfigurationer som är mindre än 10 kB i storlek (serialiserad storlek) kan definieras infogade. 

##    <a name="sample-definition"></a>Exempeldefinition

En provfärdighetsdefinition med ett infogat format visas nedan:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Om du bestämmer dig för att ange en pekare till entitetsdefinitionsfilen visas en exempelfärdighetsdefinition med formatet endeturi nedan:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Exempelinmatning

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Exempel på utdata

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Fel och varningar

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Varning: Nått maximal kapacitet för matchningar och hoppar över alla ytterligare dubblettmatchningar.

Den här varningen kommer att släppas ut om antalet matchningar som upptäckts är större än det högsta tillåtna antalet. I det här fallet kommer vi att sluta inkludera dubblettmatchningar. Om detta är oacceptabelt för dig, vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) så att vi kan hjälpa dig med ditt individuella användningsfall.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Entitetsigenkänningsfärdighet (för att söka efter välkända entiteter)](cognitive-search-skill-entity-recognition.md)
