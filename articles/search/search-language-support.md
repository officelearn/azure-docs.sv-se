---
title: "Azure Search flera språk | Microsoft Docs"
description: "Azure Search stöder 56 språk utnyttja språkanalys från Lucene och bearbetning av naturligt språk teknik från Microsoft."
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Skapa ett index för dokument på flera språk i Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Unleashing kraften i språkanalys är så enkel som en egenskap på ett sökbara fält i indexdefinitionen. Nu kan du göra det här steget i portalen.

Nedan visas skärmdumpar av Azure Portal-blad för Azure Search som ger användarna möjlighet att definiera ett indexeringsschema. Användare kan skapa alla fält och ange egenskapen analyzer för var och en av dem från det här bladet.

> [!IMPORTANT]
> Du kan bara ange en språk analyzer under fältdefinition, som i när du skapar ett nytt index från grunden in eller när du lägger till ett nytt fält till ett befintligt index. Kontrollera att du anger fullständigt alla attribut, inklusive analyzer, när du skapar fältet. Du kan inte redigera attribut eller ändra typen av analyzer när du sparar ändringarna.
>
>

## <a name="define-a-new-field-definition"></a>Definiera en ny fältdefinition
1. Logga in på den [Azure-portalen](https://portal.azure.com) och öppna bladet service för din söktjänst.
2. Klicka på **Lägg till index** i kommandofältet längst upp i tjänsten instrumentpanelen för att starta ett nytt index eller öppna ett befintligt index om du vill ange en analyzer på nya fält som du vill lägga till i ett befintligt index.
3. Bladet fält visas med alternativ för att definiera schemat för index, inklusive fliken Analyzer används för att välja ett språk analyzer.
4. Starta en fältdefinition av i fälten genom att ange ett namn, väljer datatypen och ange attribut för att markera fältet som fulltext sökbara, strängfält i sökresultat kan användas i aspekten navigeringsstrukturer kan sorteras och så vidare.
5. Innan du går vidare till nästa fält, öppna den **Analyzer** fliken.

![][1]
*Klicka på fliken Analyzer på bladet fält om du vill välja en analyzer*

## <a name="choose-an-analyzer"></a>Välj en analyzer
1. Bläddra till fältet som du definierar.
2. Om du inte har markerat fältet sökbart, klickar du på kryssrutan för att markera den som **sökbara**.
3. Klicka på området Analyzer om du vill visa listan över tillgängliga analyzers.
4. Välj analyzer som du vill använda.

![][2]
*Välj en av stöds analyzers för varje fält*

Som standard använder alla sökbara fält i [Standard Lucene analyzer](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som är oberoende av språk. Om du vill visa en fullständig lista över stöds analyzers [språkstöd i Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

När språket analyzer har valts för ett fält, ska det användas med varje fulltextindexering och begäran för det här fältet. När en fråga skickas ut mot flera fält med olika analyzers kommer frågan att behandlas oberoende av rätt analyzers för varje fält.

Många webb- och mobilprogram kan du hantera användare över hela världen med olika språk. Det är möjligt att definiera ett index för ett scenario som detta genom att skapa ett fält för varje språk som stöds.

![][3]
*Indexdefinitionen med en beskrivningsfält för varje språk som stöds*

Om språket för agenten skickar en fråga kallas en sökbegäran kan vara begränsad till ett visst fält som använder den **searchFields** Frågeparametern. Följande fråga utfärdas endast mot beskrivningen i polska:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Du kan fråga ditt index från portalen med hjälp av **Sök explorer** att klistra in i en fråga som liknar det som visas ovan. Sök explorer är tillgänglig från kommandofältet i bladet service. Se [fråga ditt Azure Search-index i portalen](search-explorer.md) mer information.

Ibland är språket för agenten skickar en fråga Okänd, i vilket fall frågan kan utfärdas mot alla fält samtidigt. Om det behövs, inställningar för resulterar i ett visst språk kan definieras med [bedömningen profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx). I exemplet nedan får matchningar i beskrivningen på engelska resultatet högre i förhållande till matchar i polska och franska:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Om du är en .NET-utvecklare, Observera att du kan konfigurera språkanalys med hjälp av den [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). Den senaste versionen innehåller stöd för Microsoft språkanalys samt.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
