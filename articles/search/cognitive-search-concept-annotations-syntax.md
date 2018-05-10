---
title: Referera till en anteckning i indata och utdata i en kognitiva Sök pipeline i Azure Search | Microsoft Docs
description: Förklarar anteckningen syntaxen och hur du refererar till en anteckning i indata och utdata för en kunskaper i en kognitiva Sök pipeline i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2e838e9c94d5b19565bea3d02890fe6164bb37d0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Hur du refererar till anteckningar i en kognitiva Sök kunskaper

I den här artikeln får du lära dig hur att referera till anteckningar i kunskaper definitioner med exempel för att illustrera olika scenarier. Så här flödar innehåll i ett dokument via en uppsättning egenskaper, hämtar den utökat med anteckningar. Anteckningar kan användas som indata för ytterligare underordnade berikande eller utdata fält i ett index. 
 
Exemplen i den här artikeln baseras på den *innehåll* fältet genereras automatiskt av [Azure Blob indexerare](search-howto-indexing-azure-blob-storage.md) som en del av dokumentet sprickbildning fasen. När du refererar till dokument från en blobbbehållare, Använd ett format som `"/document/content"`, där den *innehåll* fältet är en del av den *dokument*. 

## <a name="background-concepts"></a>Bakgrund-begrepp

Innan du granska syntaxen kan vi ångra några viktiga begrepp för att bättre förstå exemplen senare i den här artikeln.

| Period | Beskrivning |
|------|-------------|
| Utökade dokumentet | En utökade dokumentet är en intern struktur skapas och används av pipelinen för att rymma alla anteckningar som är relaterade till ett dokument. Tänk dig ett utökade dokument som ett träd med anteckningar. I allmänhet blir en anteckning som skapas från en tidigare anteckningen dess underordnade.<p/>Utökade dokument finns endast under körning av kunskaper. När innehållet har mappats till sökindexet, behövs utökade dokumentet inte längre. Även om du inte interagera med utökade dokument direkt, är det praktiskt att ha en psykisk modell dokument när du skapar en kunskaper. |
| Berikande kontext | Kontexten där berikande sker, enligt vilken elementet utökat. Kontexten berikande är som standard på den `"/document"` nivå är begränsade till enskilda dokument. När en kunskap körs, utdata för kompetensen blir [egenskaper för kontexten definierade](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exempel 1: Enkel anteckningsreferens

Anta att du har ett antal filer som innehåller referenser till namn på personer som du vill extrahera använder namngiven enhet i Azure Blob storage. I kunskaper definitionen nedan `"/document/content"` är textrepresentation av hela dokumentet och ”personer” är en extrahering av fullständiga namn för enheter som identifierats som personer.

Eftersom standardkontexten `"/document"`, listan med personer som kan nu refereras som `"/document/people"`. I den här specifika fall `"/document/people"` är en anteckning som nu kan mappas till ett fält i ett index, eller användas i en annan kunskaper i samma kunskaper.

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

Det här exemplet bygger på den föregående som visar hur du anropa ett berikande steg flera gånger under samma dokument. Anta att föregående exempel genereras en strängmatris med 10 personnamn från ett enskilt dokument. En rimlig nästa steg kan vara en andra berikande som extraherar efternamn från ett fullständigt namn. Eftersom det finns 10 namn, bör det här steget ska anropas 10 gånger i detta dokument, en gång för varje person. 

Ange sedan kontexten som för att anropa rätt antal upprepningar `"/document/people/*"`, där en asterisk (`"*"`) representerar alla noder i det utökade dokumentet som underordnade `"/document/people"`. Även om kompetensen definieras bara en gång i matrisen kunskaper, kallas det för varje medlem i dokumentet tills alla medlemmar behandlas.

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

När anteckningar är matriser eller samlingar med strängar, kanske du vill rikta specifika medlemmar i stället för matrisen som helhet. I exemplet ovan skapar en anteckning som kallas `"last"` under varje nod som representeras av kontexten. Om du vill att referera till den här serien i anteckningar, du kan använda syntaxen `"/document/people/*/last"`. Om du vill referera till en viss anteckning kunde du använda ett explicit index: `"/document/people/1/last`”för att referera till efternamnet på den första personen som identifierats i dokumentet. Observera att matriser är ”1 indexerade” i den här syntaxen.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exempel 3: Hänvisar till medlemmar i en matris

Ibland behöver gruppen alla anteckningar för en viss typ att skicka dem till en viss kunskap. Överväg att hypotetiska anpassade kunskaper som identifierar de vanligaste efternamn från alla namnen extraherats i exempel 2. Ange om du vill ange bara efternamn med anpassade kompetensen kontext som `"/document"` och indata som `"/document/people/*/lastname"`.

Observera att Kardinaliteten för `"/document/people/*/lastname"` är större än dokumentet. Det kan finnas 10 efternamn noder när det finns endast en Dokumentnod för det här dokumentet. I så fall systemet skapar automatiskt en matris med `"/document/people/*/lastname"` som innehåller alla element i dokumentet.

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
+ [Hur du integrerar en anpassad kunskap i en berikande pipeline](cognitive-search-custom-skill-interface.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Skapa kunskaper (REST)](ref-create-skillset.md)
+ [Utökade mappning till ett index](cognitive-search-output-field-mapping.md)
