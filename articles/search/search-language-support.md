---
title: Sökfrågor för flera språk indexering för icke-engelska – Azure Search
description: Azure Search har stöd för 56 språk att utnyttja språkanalysverktyg från Lucene och naturlig Språkbearbetning teknik från Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 3f88965e0d48a9ed3e9a91b4df42a79b83d2a79b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313334"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Skapa ett index för dokument på flera språk i Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Utnyttja kraften i språkanalysverktyg är lika enkelt som att inställningen för en egenskap på ett sökbart fält i indexdefinitionen. Nu kan du göra det här steget i portalen.

Nedan visas skärmdumpar av Azure Portal-blad för Azure Search som tillåter användare att definiera ett indexschema. Användarna kan skapa alla fält och ange egenskapen analyzer för var och en av dem från det här bladet.

> [!IMPORTANT]
> Du kan endast ange ett språkanalysverktyg under fältdefinition, som i när du skapar ett nytt index från grunden upp eller när du lägger till ett nytt fält till ett befintligt index. Kontrollera att du anger fullständigt alla attribut, inklusive analyzer, när du skapar fältet. Du kan inte redigera attribut eller ändra typen av analyzer när du sparar dina ändringar.
>
>

## <a name="define-a-new-field-definition"></a>Definiera en ny fältdefinition
1. Logga in på den [Azure-portalen](https://portal.azure.com) och öppna bladet för tjänsten för search-tjänsten.
2. Klicka på **Lägg till index** i kommandofältet överst på instrumentpanelen för tjänsten att starta ett nytt index, eller öppna ett befintligt index om du vill ange en analyzer på nya fält som du lägger till ett befintligt index.
3. Bladet fält visas med alternativ för att definiera schemat för indexet, inklusive fliken Analyzer används för att välja ett språkanalysverktyg.
4. Starta en fältdefinition i fält genom att ange ett namn, välja datatypen och attribut markerar fältet som fulltext sökbara, hämtningsbara i sökresultaten kan användas i aspekten navigeringsstrukturer, sorterbara och så vidare.
5. Innan du fortsätter till nästa fält, öppna den **Analyzer** fliken.

![][1]
*För att välja en analyzer, klickar du på fliken Analyzer på bladet fält*

## <a name="choose-an-analyzer"></a>Välj en analyzer
1. Bläddra till fältet som du definierar.
2. Om du inte har markerat fältet sökbart, klickar du på kryssrutan nu för att markera den som **sökbar**.
3. Klicka på området Analyzer för att visa listan över tillgängliga analysverktyg.
4. Välj det analysverktyg som du vill använda.

![][2]
*Välj en av analysverktyg som stöds för varje fält*

Som standard alla sökbara fält använder den [Standard Lucene analyzer](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som är språkoberoende. Om du vill visa en fullständig lista över stöds analysverktyg, se [språkstöd i Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

När språkanalysverktyg har valts för ett fält, används det med varje begäran för indexering och sökning för det fältet. När en fråga utfärdas mot flera fält med hjälp av olika analysverktyg, bearbetas frågan oberoende av rätt analysverktyg för varje fält.

Många webbprogram och mobilappar fungerar användare över hela världen med hjälp av olika språk. Det är möjligt att definiera ett index för ett scenario som detta genom att skapa ett fält för varje språk som stöds.

![][3]
*Definition av index med en beskrivningsfält för varje språk som stöds*

Om språket för agenten skickar en fråga kallas en sökbegäran kan vara begränsad till ett visst fält med hjälp av den **searchFields** frågeparameter. Följande fråga utfärdas endast mot beskrivningen i polska:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

Du kan fråga ditt index i portalen med hjälp av **Sökutforskaren** att klistra in i en fråga som liknar den som visas ovan. Sökutforskaren är tillgänglig från kommandofältet på bladet för tjänsten. Se [fråga ditt Azure Search-index i portalen](search-explorer.md) mer information.

Ibland är språket i agenten skickar en fråga inte känd, då frågan kan utfärdas mot alla fält samtidigt. Om det behövs, inställningar för resulterar i ett visst språk kan definieras med hjälp av [poängprofiler](https://msdn.microsoft.com/library/azure/dn798928.aspx). I exemplet nedan får matchningar hittades i beskrivningen på engelska resultatet högre i förhållande till matchar i polska och franska:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

Om du är en .NET-utvecklare, Observera att du kan konfigurera språkanalysverktyg med hjälp av den [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). Den senaste versionen innehåller stöd för Microsoft språkanalysverktyg samt.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
