---
title: Referens för indata och utdata i cognitive Sök pipelines – Azure Search
description: Förklarar anteckning syntaxen och hur du refererar till en anteckning i indata och utdata för en kompetens i en pipeline för kognitiv sökning i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 57fed710d7d58199fb3cb70640d1f2d3f316f180
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315840"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Hur du refererar till kommentarer i en kognitiv sökning-kompetens

I den här artikeln får lära du att referera till anteckningar i färdighet definitioner, med exempel för att illustrera olika scenarier. När innehållet i ett dokument förs vidare via en uppsättning egenskaper, hämtar den berikats med anteckningar. Anteckningar kan användas som indata för ytterligare underordnade berikande eller utdata fält i ett index. 
 
Exemplen i den här artikeln baseras på den *innehåll* fältet genereras automatiskt av [Azure Blob-indexerare](search-howto-indexing-azure-blob-storage.md) som en del av de document cracking-fasen. När du refererar till dokument från en blobbehållare, använda ett format som `"/document/content"`, där den *innehåll* fält är en del av den *dokumentet*. 

## <a name="background-concepts"></a>BITS-begrepp

Innan du granska syntaxen kan vi gå tillbaka till några viktiga begrepp för att bättre förstå exemplen senare i den här artikeln.

| Period | Beskrivning |
|------|-------------|
| Avancerad och dokument | En avancerad och dokumentet är en intern struktur skapas och används av pipelinen för att lagra alla kommentarer som är relaterade till ett dokument. Tänk på en avancerad och dokumentet som ett träd med anteckningar. I allmänhet blir en anteckning som skapats från en tidigare anteckningen dess underordnade.<p/>Avancerad och dokument finns bara för hela kompetens körning. När innehållet har mappats till search-index, behövs avancerad och dokumentet inte längre. Även om du inte interagerar med avancerad och dokument direkt, är det praktiskt att ha en mentala modell dokument när du skapar en kompetens. |
| Berikande kontext | Kontexten där berikande äger rum, enligt vilken elementet berikats. Berikande kontexten är som standard på den `"/document"` nivå, begränsad till enskilda dokument. När en färdighet körs utdata på kompetensen blir [egenskaperna för kontexten definierade](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exempel 1: Anteckningsreferens för enkel

Anta att du har en mängd olika filer som innehåller referenser till namn på personer som du vill extrahera med igenkänning av namngivna entiteter i Azure Blob storage. I färdighet definitionen nedan, `"/document/content"` textrepresentation av hela dokumentet och ”personer” är en extrahering av fullständiga namnen för enheter som identifierats som personer.

Eftersom standardkontexten `"/document"`, lista över personer kan nu referera till som `"/document/people"`. I den här specifika fall `"/document/people"` är en anteckning, vilket kan nu vara mappade till ett fält i ett index eller används i en annan färdighet i samma kompetens.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

Det här exemplet bygger på den föregående som, där du kan se hur du anropar ett berikande steg flera gånger under samma dokument. Anta att det tidigare exemplet genereras en matris med strängar med 10 personnamn från ett enskilt dokument. En rimlig nästa steg kan vara en andra funktioner som extraherar efternamn från ett fullständigt namn. Eftersom det finns 10 namn kan du det här steget ska anropas 10 gånger i detta dokument, en gång för varje person. 

Om du vill anropa rätt antal upprepningar anger kontexten som `"/document/people/*"`, där asterisken (`"*"`) representerar alla noder i avancerad och dokumentet som underordnade `"/document/people"`. Även om kompetensen definieras bara en gång i matrisen kunskaper, kallas det för varje medlem i dokumentet tills alla medlemmar behandlas.

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

När anteckningar är matriser eller samlingar med strängar, kanske du vill rikta särskilda medlemmar i stället för matrisen som helhet. Exemplet ovan skapar en anteckning som kallas `"last"` under varje nod som representeras av kontexten. Om du vill referera till den här serien med kommentarer kan du använda syntaxen `"/document/people/*/last"`. Om du vill referera till en viss anteckning kan du använda ett explicit index: `"/document/people/1/last`”att referera till efternamnet på den första personen som identifierats i dokumentet. Observera att matriser i den här syntaxen är ”0 indexerade”.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exempel 3: För referensmedlemmar i en matris

Ibland behöver att gruppera alla anteckningar av en viss typ att skicka dem till en viss kunskap. Överväg en hypotetisk anpassade färdighet som identifierar de vanligaste efternamnet från alla efternamn som extraheras i exempel 2. För att ge bara efternamn med anpassade kompetensen, ange kontext som `"/document"` och indata som `"/document/people/*/lastname"`.

Observera att kardinalitet `"/document/people/*/lastname"` är större än den för dokumentet. Det kan finnas 10 lastname noder när den har endast en Dokumentnod för det här dokumentet. I så fall kan systemet automatiskt skapar en matris med `"/document/people/*/lastname"` som innehåller alla element i dokumentet.

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



## <a name="see-also"></a>Se också
+ [Hur du integrerar en anpassad kunskap till en berikande pipeline](cognitive-search-custom-skill-interface.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning till ett index](cognitive-search-output-field-mapping.md)
