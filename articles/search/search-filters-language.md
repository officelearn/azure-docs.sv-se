---
title: Filtrera efter språk i ett sökindex
titleSuffix: Azure Cognitive Search
description: Filtrera villkor för att stödja sökning på flera språk, omfånga frågekörning till språkspecifika fält.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112059"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Så här filtrerar du efter språk i Azure Cognitive Search 

Ett viktigt krav i ett flerspråkigt sökprogram är möjligheten att söka igenom och hämta resultat på användarens eget språk. I Azure Cognitive Search är ett sätt att uppfylla språkkraven för en flerspråkig app att skapa en serie fält som är dedikerade till att lagra strängar på ett visst språk och sedan begränsa fulltextsökning till just dessa fält vid frågetid.

Frågeparametrar för begäran används för att både begränsa sökåtgärden och trimma sedan resultaten av alla fält som inte tillhandahåller innehåll som är kompatibelt med den sökupplevelse som du vill leverera.

| Parametrar | Syfte |
|-----------|--------------|
| **sökFält** | Begränsar fulltextsökning till listan med namngivna fält. |
| **$select** | Trimmar svaret så att det bara finns de fält som du anger. Som standard returneras alla fält som kan hämtas. Med **parametern $select** kan du välja vilka som ska returneras. |

Framgången för denna teknik beror på integriteten i fältinnehållet. Azure Cognitive Search översätter inte strängar eller utför språkidentifiering. Det är upp till dig att se till att fälten innehåller de strängar du förväntar dig.

## <a name="define-fields-for-content-in-different-languages"></a>Definiera fält för innehåll på olika språk

I Azure Cognitive Search riktar sig frågor till ett enda index. Utvecklare som vill tillhandahålla språkspecifika strängar i en enda sökupplevelse definierar vanligtvis dedikerade fält för att lagra värdena: ett fält för engelska strängar, ett för franska och så vidare. 

I våra prover, inklusive [fastighetsprovet](search-get-started-portal.md) som visas nedan, kanske du har sett fältdefinitioner som liknar följande skärmdump. Lägg märke till hur det här exemplet visar språkanalystilldelningarna för fälten i det här indexet. Fält som innehåller strängar presterar bättre i fulltextsökning när de paras ihop med en analysator som är konstruerad för att hantera målspråkets språkliga regler.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Kodexempel som visar fältdefinitioner med språkanalysatorer finns i [Definiera ett index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) och [Definiera ett index (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Skapa och ladda ett index

Ett mellanliggande (och kanske självklart) steg är att du måste [skapa och fylla i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet) innan du formulerar en fråga. Vi nämner detta steg här för fullständighet. Ett sätt att avgöra om indexet är tillgängligt är att kontrollera indexlistan i [portalen](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Begränsa fråge- och trimresultaten

Parametrar i frågan används för att begränsa sökningen till specifika fält och sedan trimma resultaten av eventuella fält som inte är till hjälp för ditt scenario. Med tanke på målet att begränsa sökningen till fält som innehåller franska strängar använder du **sökfält** för att rikta frågan i fält som innehåller strängar på det språket. 

Som standard returnerar en sökning alla fält som är markerade som hämtningsbara. Därför kanske du vill utesluta fält som inte överensstämmer med den språkspecifika sökupplevelse som du vill tillhandahålla. Om du har begränsat sökningen till ett fält med franska strängar vill du förmodligen utesluta fält med engelska strängar från resultaten. Med hjälp av **parametern $select** fråga ger du kontroll över vilka fält som returneras till det anropande programmet.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Även om det inte finns något $filter argument i frågan, är det här användningsfallet starkt kopplat till filterbegrepp, så vi presenterar det som ett filtreringsscenario.

## <a name="see-also"></a>Se även

+ [Filter i Azure Cognitive Search](search-filters.md)
+ [Språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents)

