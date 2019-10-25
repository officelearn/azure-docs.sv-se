---
title: Språk filter för flerspråkigt innehåll i ett Sök index
titleSuffix: Azure Cognitive Search
description: Filtrera villkor för att stödja sökning på flera språk, köra frågor mot språkspecifika fält.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2762ce42f0d3f5829682e0910c452746a65ef2f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792877"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Så här filtrerar du efter språk i Azure Kognitiv sökning 

Ett nyckel krav i ett program för flerspråkig sökning är möjligheten att söka över och hämta resultat i användarens eget språk. I Azure Kognitiv sökning är det ett sätt att uppfylla språk kraven för en flerspråkig app att skapa en serie fält som är dedikerade för att lagra strängar på ett specifikt språk och sedan begränsa full texts ökningen till bara dessa fält vid tidpunkten i frågan.

Frågeparametrar på begäran används för att både hitta Sök åtgärden och sedan trimma resultaten för fält som inte tillhandahåller innehåll som är kompatibelt med den Sök funktion som du vill leverera.

| Parametrar | Syfte |
|-----------|--------------|
| **searchFields** | Begränsar fullständig texts ökning till listan över namngivna fält. |
| **$select** | Trimmar svaret så att det endast innehåller de fält som du anger. Som standard returneras alla fält som kan hämtas. Med parametern **$Select** kan du välja vilka som ska returneras. |

Det här är en lyckad genom gång av den här metoden för att skydda fält innehåll. Azure Kognitiv sökning översätter inte strängar eller utför språk identifiering. Det är upp till dig att se till att fälten innehåller de strängar som du förväntar dig.

## <a name="define-fields-for-content-in-different-languages"></a>Definiera fält för innehåll på olika språk

I Azure Kognitiv sökning riktar sig frågor mot ett enda index. Utvecklare som vill tillhandahålla språkspecifika strängar i en enda Sök funktion definierar vanligt vis dedikerade fält för att lagra värdena: ett fält för engelska strängar, ett för franska och så vidare. 

I våra exempel, inklusive exemplet på fastighets [mäklare](search-get-started-portal.md) som visas nedan, kan du ha sett fält definitioner som liknar följande skärm bild. Observera hur det här exemplet visar språk analys tilldelningarna för fälten i det här indexet. Fält som innehåller strängar fungerar bättre i full texts ökning när de kombineras med en analys som är utformad för att hantera språkspråkets språk regler.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Kod exempel som visar fält definitioner med språk analys verktyg finns i [definiera ett index (.net)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) och [definiera ett index (rest)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Bygga och läsa in ett index

Ett mellanliggande (och eventuellt uppenbart) steg är att du måste [bygga och fylla i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet) innan du skapar en fråga. Vi nämner detta steg för att slutföra. Ett sätt att avgöra om indexet är tillgängligt är genom att kontrol lera listan index i [portalen](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Begränsa frågan och trimma resultatet

Parametrar i frågan används för att begränsa sökningen till vissa fält och sedan trimma resultaten för fält som inte är användbara för ditt scenario. Baserat på ett mål för att begränsa sökningen till fält som innehåller franska strängar, använder du **searchFields** för att rikta frågan på fält som innehåller strängar på det språket. 

Som standard returnerar en sökning alla fält som är markerade som hämtnings bara. Därför kanske du vill utesluta fält som inte överensstämmer med den språkspecifika Sök upplevelse som du vill tillhandahålla. Om du har begränsat sökningen till ett fält med franska strängar vill du förmodligen utesluta fält med engelska strängar från dina resultat. Med hjälp av parametern **$Select** fråga kan du styra vilka fält som returneras till det anropande programmet.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Även om det inte finns något $filter argument i frågan, är det här användnings fallet starkt kopplat till filter koncept, så vi presenterar det som ett filtrerings scenario.

## <a name="see-also"></a>Se också

+ [Filter i Azure Kognitiv sökning](search-filters.md)
+ [Språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Hur full texts ökning fungerar i Azure Kognitiv sökning](search-lucene-query-architecture.md)
+ [Sök dokument REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

