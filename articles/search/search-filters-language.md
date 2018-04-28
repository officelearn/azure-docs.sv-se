---
title: Språk filter i Azure Search | Microsoft Docs
description: Filtervillkor av användaridentitet för säkerhet, språk, geografiska plats eller numeriska värden att minska sökresultat på frågorna i Azure Search värdbaserade moln search-tjänsten på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 6d7fa7ab6db1fe9f8e2d1530c2917f4716a38079
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Så här filtrerar du efter språk i Azure Search 

Ett krav i ett flerspråkig sökprogram är möjligheten att söka över och hämta resultaten på användarens egna språk. I Azure Search är ett sätt att uppfylla kraven för språk för en flerspråkig app att skapa en serie fält som lagrar strängar i ett visst språk och sedan begränsa fulltextsökning till enbart de fält när databasfrågan.

Frågeparametrar på begäran används för både omfång search-åtgärd och trim resultatet av alla fält som inte innehåller innehåll som är kompatibel med personer som du vill leverera.

| Parametrar | Syfte |
|-----------|--------------|
| **searchFields** | Gränser fulltextsökning i listan över namngivna fält. |
| **$select** | Tar bort svar om du vill inkludera endast de fält som du anger. Som standard returneras alla strängfält fält. Den **$select** parametern kan du välja vilka som ska returneras. |

Genomförandet av den här metoden kräver integriteten för fält. Azure Search inte översätta strängar eller utföra identifiera språk. Det är du se till att fält innehåller strängar som du förväntar dig.

## <a name="define-fields-for-content-in-different-languages"></a>Definiera fält för innehåll på olika språk

På Azure Search mål frågor ett index. Utvecklare som vill ge språkspecifika strängar i en enda sökinställningar vanligtvis definiera dedikerade fält för att lagra värdena: ett fält för engelska strängar, en för franska och så vidare. 

I vårt exempel, inklusive den [fastigheter exempel](search-get-started-portal.md) visas nedan, du kanske har sett fältdefinitioner som liknar följande skärmbild. Observera hur det här exemplet visar språket analyzer tilldelningar för fälten i indexet. Fält som innehåller strängar bättre i fulltextsökning med en analyzer som utformats för att hantera språkliga reglerna för mål-språk.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Kodexempel visar fältdefinitioner med språk analyzers finns [definiera ett index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) och [definiera ett index (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Skapa och läsa in ett index

Ett mellanliggande (och kanske uppenbara) steg är att du behöver [bygga och Fyll i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) innan du utforma en fråga. Vi har nämnt det här steget för fullständighetens skull. Ett sätt att avgöra om indexet är tillgänglig är genom att kontrollera listan över index den [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Begränsa frågan och trim resultat

Parametrar i frågan används för att begränsa sökningen till specifika fält och trim resultatet av alla fält som inte är användbara för ditt scenario. Få ett mål av Begränsningsaspekten sökning till fält som innehåller franska strängar, som du vill använda **searchFields** att rikta frågan till fält som innehåller strängar på det språket. 

En sökning returnerar alla fält som är markerade som hämtas som standard. Därför kanske du vill undanta fält som inte överensstämmer med det språkspecifika sökinställningar som du vill ge. I synnerhet om du har begränsad sökning till ett fält med franska strängar, vill du förmodligen utesluta resultat fält med engelska strängar. Med hjälp av den **$select** fråga parametern ger dig kontroll över vilka fält som returneras till det anropande programmet.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Även om det finns no $filter argument på frågan, är den här användningsfall starkt knuten till filter begrepp, så vi finns det ett scenario med filtrering.

## <a name="see-also"></a>Se också

+ [Filter i Azure Search](search-filters.md)
+ [Språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Hur full textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Sök dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

