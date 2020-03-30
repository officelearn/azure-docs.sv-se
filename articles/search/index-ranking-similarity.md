---
title: Ranking likhet Algoritm
titleSuffix: Azure Cognitive Search
description: Hur man ställer in likhetsalgoritmen för att prova ny likhetsalgoritm för rangordning
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409978"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Rankningsalgoritm i Azure Cognitive Search

> [!IMPORTANT]
> Från och med 15 juli 2020 kommer nyskapade söktjänster att använda BM25-rankningsfunktionen, som i de flesta fall har visat sig tillhandahålla sökrankningar som bättre överensstämmer med användarnas förväntningar än den aktuella standardrankningen.  Utöver överlägsen rangordning möjliggör BM25 även konfigurationsalternativ för att justera resultat baserat på faktorer som dokumentstorlek.  
>
> Med den här ändringen kommer du sannolikt att se små förändringar i ordningen på sökresultaten.   För dem som vill testa effekten av denna förändring har vi gjort tillgängliga i API:et 2019-05-06- En möjlighet att aktivera BM25-poängsättning på nya index.  

I den här artikeln beskrivs hur du kan uppdatera en tjänst som skapats före den 15 juli 2020 för att använda den nya BM25-rankningsalgoritmen.

Azure Cognitive Search kommer att använda den officiella Lucene-implementeringen av Okapi BM25-algoritmen, *BM25Similarity*, som ersätter den tidigare använda *ClassicSimilarity-implementeringen.* Liksom den äldre ClassicSimilarity-algoritmen är BM25Similarity en TF-IDF-liknande hämtningsfunktion som använder termfrekvensen (TF) och den omvända dokumentfrekvensen (IDF) som variabler för att beräkna relevanspoäng för varje dokumentfrågepar, vilket sedan är används för rangordning. Medan konceptuellt liknar den äldre Classic Likhet algoritm, BM25 tar sin rot i probabilistic information hämtning för att förbättra den. BM25 erbjuder också avancerade anpassningsalternativ, till exempel att låta användaren bestämma hur relevanspoängen skalor med termen frekvens för matchade termer.

## <a name="how-to-test-bm25-today"></a>Så här testar du BM25 idag

När du skapar ett nytt index kan du ange en "likhetsegenskap". Du måste använda *2019-05-06-Preview-versionen,* som visas nedan.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

För tjänster som skapats före den 15 juli 2020: Om likheten utelämnas eller anges till null, kommer indexet att använda den gamla klassiska likhetsalgoritmen.

För tjänster som skapats efter den 15 juli 2020: Om likheten utelämnas eller anges till null, kommer indexet att använda den nya likhetsalgoritmen BM25.

Du kan också uttryckligen ange likhetsvärdet till ett av följande två värden: *"#Microsoft.Azure.Search.ClassicSimilarity"* eller *"#Microsoft.Azure.Search.BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>BM25 likhetsparametrar

BM25 likhet lägger till två användare anpassningsbara parametrar för att styra den beräknade relevanspoäng:

### <a name="k1"></a>k1 (på andra)

Parametern *k1* styr skalningsfunktionen mellan termfrekvensen för varje matchande termer till den slutliga relevanspoängen för ett dokumentfrågepar.

Värdet noll representerar en "binär modell", där bidraget från en enda matchande term är detsamma för alla matchande dokument, oavsett hur många gånger termen visas i texten, medan ett större k1-värde gör att poängen kan fortsätta att öka som mer samma term finns i dokumentet. Som standard använder Azure Cognitive Search värdet 1.2 för parametern k1. Att använda ett högre K1-värde kan vara viktigt i de fall där vi förväntar oss att flera termer ska ingå i en sökfråga. I dessa fall kanske vi vill gynna dokument som matchar många av de olika frågetermer som genomsöks över dokument som bara matchar en enda, flera gånger. Till exempel, när du frågar index för dokument som innehåller termerna "Apollo Spaceflight", kanske vi vill sänka poängen för en artikel om grekisk mytologi som innehåller termen "Apollo" ett par dussin gånger, utan omnämnanden av "Spaceflight", jämfört med en annan artikel som uttryckligen nämner både "Apollo" och "Spaceflight" en handfull gånger bara. 
 
### <a name="b"></a>b

Parametern *b* styr hur längden på ett dokument påverkar relevanspoängen.

Ett värde på 0,0 innebär att dokumentets längd inte kommer att påverka poängen, medan värdet 1,0 innebär att termens frekvens påverkar relevanspoängen kommer att normaliseras med dokumentets längd. Standardvärdet som används i Azure Cognitive Search for the b-parametern är 0,75. Att normalisera termfrekvensen med dokumentets längd är användbart i de fall där vi vill straffa längre dokument. I vissa fall är längre dokument (t.ex. en fullständig roman) mer benägna att innehålla många irrelevanta termer, jämfört med mycket kortare dokument.

### <a name="setting-k1-and-b-parameters"></a>Ställa in parametrar för K1 och b

Om du vill anpassa b- eller k1-värdena lägger du bara till dem som egenskaper i likhetsobjektet när du använder BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Likhetsalgoritmen kan bara ställas in vid skapande av index. Det innebär att likhetsalgoritmen som används inte kan ändras för befintliga index. Parametrarna *"b"* och *"k1"* kan ändras när du uppdaterar en befintlig indexdefinition som använder BM25. Om du ändrar dessa värden i ett befintligt index kopplas indexet från i minst några sekunder, vilket gör att indexeringen och frågebegärandena misslyckas. På grund av detta måste du ange parametern "allowIndexDowntime=true" i frågesträngen för din uppdateringsbegäran:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Se även  

 [AZURE Kognitiv sökning REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Lägga till poängprofiler i indexet](index-add-scoring-profiles.md)    
 [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
