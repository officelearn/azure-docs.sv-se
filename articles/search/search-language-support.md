---
title: Indexering av flera språk för sökfrågor som inte är engelska
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search stöder 56 språk och utnyttjar språkanalysatorer från Lucene och Natural Language Processing-teknik från Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793597"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Skapa ett index för flera språk i Azure Cognitive Search

Index kan innehålla fält som innehåller innehåll från flera språk, till exempel skapa enskilda fält för språkspecifika strängar. Bäst resultat under indexering och frågor tilldelar du en språkanalysator som tillhandahåller lämpliga språkregler. 

Azure Cognitive Search erbjuder ett stort urval av språkanalysatorer från både Lucene och Microsoft som kan tilldelas enskilda fält med egenskapen Analyzer. Du kan också ange en språkanalysator i portalen, enligt beskrivningen i den här artikeln.

## <a name="add-analyzers-to-fields"></a>Lägga till analysatorer i fält

En språkanalysator anges när ett fält skapas. Om du lägger till en analysator i en befintlig fältdefinition måste indexet skrivas över (och läsas in om) eller skapa ett nytt fält som är identiskt med originalet, men med en analysatortilldelning. Du kan sedan ta bort det oanvända fältet när det passar dig.

1. Logga in på [Azure-portalen](https://portal.azure.com) och hitta din söktjänst.
1. Klicka på **Lägg till index** i kommandofältet högst upp på tjänstinstrumentpanelen för att starta ett nytt index, eller öppna ett befintligt index för att ange en analysator på nya fält som du lägger till i ett befintligt index.
1. Starta en fältdefinition genom att ange ett namn.
1. Välj datatypen Edm.String. Endast strängfält är fulltextsökbara.
1. Ange **attributet Sökbart** för att aktivera egenskapen Analyzer. Ett fält måste vara textbaserat för att kunna använda en språkanalysator.
1. Välj en av de tillgängliga analysatorerna. 

![Tilldela språkanalysatorer under fältdefinition](media/search-language-support/select-analyzer.png "Tilldela språkanalysatorer under fältdefinition")

Som standard använder alla sökbara fält [standard Lucene-analysatorn](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som är språkoberoende. Information om hur du visar hela listan över analysatorer som stöds finns [i Lägga till språkanalysatorer i ett Azure Cognitive Search-index](index-add-language-analyzers.md).

I portalen är analysatorer avsedda att användas som de är. Om du behöver anpassning eller en specifik konfiguration av filter och tokenizers bör du [skapa en anpassad analysator](index-add-custom-analyzers.md) i kod. Portalen stöder inte att välja eller konfigurera anpassade analysatorer.

## <a name="query-language-specific-fields"></a>Frågespråksspecifika fält

När språkanalysatorn har valts för ett fält används den med varje indexerings- och sökbegäran för det fältet. När en fråga utfärdas mot flera fält med hjälp av olika analysatorer bearbetas frågan oberoende av de tilldelade analysatorerna för varje fält.

Om språket för agenten som utfärdar en fråga är känt kan en sökbegäran begränsas till ett visst fält med hjälp av sökfältsfrågeparametern. **searchFields** Följande fråga kommer endast att utfärdas mot beskrivningen på polska:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Du kan fråga ditt index från portalen med hjälp av [**Sökutforskaren**](search-explorer.md) för att klistra in en fråga som liknar den som visas ovan.

## <a name="boost-language-specific-fields"></a>Marknadsför språkspecifika fält

Ibland är språket för agenten som utfärdar en fråga inte känt, i vilket fall frågan kan utfärdas mot alla fält samtidigt. Om det behövs kan inställningar för resultat på ett visst språk definieras med hjälp av [poängprofiler](index-add-scoring-profiles.md). I exemplet nedan kommer matchningar som finns i beskrivningen på engelska att poängsättas högre i förhållande till matcher på polska och franska:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Nästa steg

Om du är .NET-utvecklare bör du tänka på att du kan konfigurera språkanalysatorer med hjälp av [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) och egenskapen [Analyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 