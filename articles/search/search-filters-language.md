---
title: Språkfilter för flerspråkig innehåll i ett sökindex – Azure Search
description: Filtervillkor för att stödja flera sökning kan styra Frågekörningen till språkspecifik fält.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: aae081232d3633d3f7d8094979764606bf99430d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311192"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Så här filtrerar du efter språk i Azure Search 

Ett centralt krav i ett program för flerspråkig sökning är möjligheten att söker efter och hämta resultatet på användarens språk. I Azure Search är ett sätt att uppfylla kraven för språket i en app för flera språk att skapa en serie med fält som lagrar strängar i ett visst språk och sedan begränsa fulltextsökning till bara dessa fält när en fråga körs.

Frågeparametrar på begäran används för att både omfånget search-åtgärd och sedan trimma resultaten av eventuella fält som inte har innehåll som är kompatibel med sökupplevelsen som du vill leverera.

| Parametrar | Syfte |
|-----------|--------------|
| **searchFields** | Begränsningar för fulltextsökning i listan över namngivna fält. |
| **$select** | Tar bort svaret för att inkludera endast de fält som du anger. Som standard returneras alla hämtningsbara fält. Den **$select** parametern kan du välja vilka som ska returneras. |

Kan användas i den här metoden kräver integriteten för fält. Azure Search inte översätta strängar och utför språkidentifiering. Det är upp till dig att se till att fält som innehåller strängar som du förväntar dig.

## <a name="define-fields-for-content-in-different-languages"></a>Definiera fält för innehåll på olika språk

Frågor rikta en enda index i Azure Search. Utvecklare som vill ge språkspecifika strängar i en enda sökfunktion vanligtvis definiera dedikerade fält för att lagra värdena: frågesträngar för ett fält för engelska, en för franska och så vidare. 

I vårt exempel, inklusive den [fastigheter exemplet](search-get-started-portal.md) visas nedan, du har kanske sett fältdefinitioner som liknar följande skärmbild. Observera hur det här exemplet visar språket analyzer tilldelningar för fält i indexet. Fält som innehåller strängar ge bättre prestanda med fulltextsökning tillsammans med en analyzer konstruerat för att hantera språkliga reglerna för språket som mål.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Kodexempel som visar fältdefinitioner med analysverktyg för språk finns i [definiera ett index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) och [definiera ett index (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Skapa och läsa in ett index

Ett mellanliggande (och kanske uppenbara) steg är att du behöver [skapa och Fyll i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) innan utformningen av en fråga. Vi nämna det här steget för fullständighetens skull. Ett sätt att avgöra om indexet är tillgänglig är genom att kontrollera listan den [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Begränsa frågan och trimma resultat

Parametrar i frågan används för att begränsa sökningen till specifika fält och sedan trimma resultaten av eventuella fält som inte är användbara för ditt scenario. Få ett mål för begränsade sökning till fält som innehåller franska strängar, använder du **searchFields** att rikta frågan till fält som innehåller strängar på det språket. 

Som standard returnerar en sökning alla fält som markerats som ett hämtningsbart. Därför kanske du vill utesluta fält som inte överensstämmer med språkspecifika sökupplevelsen som du vill ge. Mer specifikt om du har begränsad sökning till ett fält med franska strängar, vill du förmodligen utesluta fält med engelska strängar från dina resultat. Med hjälp av den **$select** fråga parametern ger dig kontroll över vilka fält som returneras till det anropande programmet.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Även om det finns no $filter argumentet i frågan, är det här användningsfallet starkt knuten till filtret begrepp, så vi presentera dem som filtrerande scenario.

## <a name="see-also"></a>Se också

+ [Filter i Azure Search](search-filters.md)
+ [Språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

