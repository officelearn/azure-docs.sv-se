---
title: Referensindata och resultat i färdigheter
titleSuffix: Azure Cognitive Search
description: Förklarar anteckningssyntaxen och hur du refererar till en anteckning i indata och utdata för en kompetens i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113866"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Referera till anteckningar i en Azure Cognitive Search-kompetens

I den här artikeln får du lära dig hur du refererar till anteckningar i färdighetsdefinitioner, med hjälp av exempel för att illustrera olika scenarier. När innehållet i ett dokument flödar genom en uppsättning kunskaper berikas det med anteckningar. Anteckningar kan användas som indata för ytterligare nedströmsanrikning eller mappas till ett utdatafält i ett index. 
 
Exempel i den här artikeln baseras på *innehållsfältet* som genereras automatiskt av [Azure Blob-indexerare](search-howto-indexing-azure-blob-storage.md) som en del av dokumentsprickningsfasen. När du refererar till dokument från en Blob-behållare använder du ett format som `"/document/content"`, där *innehållsfältet* ingår i *dokumentet*. 

## <a name="background-concepts"></a>Bakgrundskoncept

Innan du granskar syntaxen ska vi gå tillbaka till några viktiga begrepp för att bättre förstå exemplen senare i den här artikeln.

| Period | Beskrivning |
|------|-------------|
| Berikat dokument | Ett berikat dokument är en intern struktur som skapats och används av pipelinen för att lagra alla anteckningar som är relaterade till ett dokument. Tänk på ett berikat dokument som ett anteckningsträd. I allmänhet blir en anteckning som skapats från en tidigare anteckning dess underordnade.<p/>Berikade dokument finns bara under varaktigheten av körning av kunskaper. När innehållet har mappats till sökindexet behövs inte längre det berikade dokumentet. Även om du inte interagerar med berikade dokument direkt, är det bra att ha en mental modell av dokumenten när du skapar en kompetens. |
| Kontext för anrikning | Det sammanhang i vilket anrikningen äger rum, i vilket element berikas. Som standard är anrikningskontexten på den `"/document"` nivå som är begränsad till enskilda dokument. När en färdighet körs blir utdata för den färdigheten [egenskaper för den definierade kontexten](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exempel 1: Enkel anteckningsreferens

Anta att du har en mängd olika filer som innehåller referenser till personers namn som du vill extrahera med hjälp av entitetsigenkänning. I färdighetsdefinitionen nedan, `"/document/content"` är den textmässiga representationen av hela dokumentet, och "människor" är en utvinning av fullständiga namn för enheter som identifierats som personer.

Eftersom standardkontexten är `"/document"`kan listan över personer `"/document/people"`nu refereras som . I det `"/document/people"` här specifika fallet är en anteckning, som nu kan mappas till ett fält i ett index, eller användas i en annan färdighet i samma kompetens.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Exempel 2: Referera till en matris i ett dokument

Det här exemplet bygger på det föregående och visar hur du anropar ett anrikningssteg flera gånger över samma dokument. Anta att föregående exempel genererade en matris med strängar med 10 personnamn från ett enda dokument. Ett rimligt nästa steg kan vara en andra anrikning som extraherar efternamnet från ett fullständigt namn. Eftersom det finns 10 namn vill du att det här steget ska anropas 10 gånger i det här dokumentet, en gång för varje person. 

Om du vill anropa rätt antal iterationer `"/document/people/*"`anger du kontexten som , där asterisken (`"*"` `"/document/people"`) representerar alla noder i det berikade dokumentet som underordnade till . Även om den här färdigheten bara definieras en gång i kompetensmatrisen, kallas den för varje medlem i dokumentet tills alla medlemmar bearbetas.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

När anteckningar är matriser eller samlingar av strängar kanske du vill rikta in dig på specifika medlemmar i stället för matrisen som helhet. Exemplet ovan genererar en anteckning `"last"` som anropas under varje nod som representeras av kontexten. Om du vill referera till den här annoteringarfamiljen kan du använda syntaxen `"/document/people/*/last"`. Om du vill referera till en viss anteckning kan du `"/document/people/1/last`använda ett explicit index: " för att referera till efternamnet på den första personen som identifieras i dokumentet. Observera att i den här syntaxen är "0 indexerade".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exempel 3: Referensmedlemmar i en matris

Ibland måste du gruppera alla anteckningar av en viss typ för att skicka dem till en viss färdighet. Tänk dig en hypotetisk anpassad färdighet som identifierar det vanligaste efternamnet från alla efternamn som extraheras i exempel 2. Om du bara vill ange efternamn till den `"/document"` anpassade färdigheten anger du kontexten som och indata som `"/document/people/*/lastname"`.

Observera att kardinaliteten `"/document/people/*/lastname"` är större än dokumentets. Det kan finnas 10 efternamnsnoder medan det bara finns en dokumentnod för det här dokumentet. I så fall skapar systemet automatiskt en `"/document/people/*/lastname"` matris med alla element i dokumentet.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Se även
+ [Så här integrerar du en anpassad färdighet i en anrikningspipeline](cognitive-search-custom-skill-interface.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du utökade fält till ett index](cognitive-search-output-field-mapping.md)
