---
title: Search explorer-verktyget för att fråga efter data i Azure portal – Azure Search
description: Använd Azure portal-verktyg som Sökutforskaren för frågan index i Azure Search. Ange sökvillkor eller fullständiga söksträngar med avancerade syntax.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 85e574a56380384b10d0916385a8816fd26c2eeb
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244808"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Sökutforskaren för att fråga efter data i Azure Search 

Den här artikeln visar hur du frågar efter en befintlig Azure Search index med hjälp av **Sökutforskaren** i Azure-portalen. Du kan använda Sökutforskaren för att skicka enkla eller fullständiga Lucene frågesträngar till befintliga index i din tjänst. 

   ![Söka efter kommandot Sökutforskaren i portalen](./media/search-explorer/search-explorer-cmd2.png "söka efter kommandot Sökutforskaren i portalen")


Hjälp att komma igång, se [starta Sökutforskaren](#start-search-explorer).

## <a name="basic-search-strings"></a>Grundläggande söksträngar

I följande exempel förutsätts inbyggda realestate-exempelindex. Läs hur du skapar det här indexet, [Snabbstart: Importera, index och frågor i Azure-portalen](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Exempel 1 – tom sökning

För en första titt på ditt innehåll, kör du en tom sökning genom att klicka på **Search** med inga villkor som angetts. En tom sökning är användbart som en första fråga eftersom den returnerar hela dokument så att du kan granska dokumentet sammansättning. Det finns inga sökrangordningen på en tom sökning och returneras dokument i valfri ordning (`"@search.score": 1` för alla dokument). Som standard returneras 50 dokument i en sökbegäran.

Motsvarande syntaxen för en tom sökning är `*` eller `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Tom fråga exempel](./media/search-explorer/search-explorer-example-empty.png "Unqualified eller tom fråga exempel")

### <a name="example-2---free-text-search"></a>Exempel 2 – fritextsökningen

Formaliafri frågor, med eller utan operatörer, är användbara för att simulera en användardefinierad frågor som skickats från en anpassad app till Azure Search. Observera att om du anger sökord eller uttryck, kommer sökrangordningen betydelse. I följande exempel visas en fritextsökningen.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Du kan använda Ctrl-F för att söka i resultaten efter specifika termer i närheten.

   ![Exempel på sökfråga fritext](./media/search-explorer/search-explorer-example-freetext.png "fritext exempel på sökfråga")

### <a name="example-3---count-of-matching-documents"></a>Exempel 3 – antalet matchande dokument 

Lägg till **$count** att hämta antalet matchningar i ett index. Antalet är det totala antalet dokument i indexet på en tom sökning. På en kvalificerad sökning är det antal dokument som matchar frågan indata.

   ```Input1
   $count=true
   ```
   **Results**

   ![Antal dokument exempel](./media/search-explorer/search-explorer-example-count.png "antalet matchande dokument i indexet")

### <a name="example-4---restrict-fields-in-search-results"></a>Exempel 4 – begränsa fält i sökresultat

Lägg till **$select** att begränsa resultaten till de uttryckligen namngivna fält för lättare att läsa utdata i **Sökutforskaren**. Att hålla söksträngen och **$count = true**, prefix argument med **&**. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Gränsen fält exempel](./media/search-explorer/search-explorer-example-selectfield.png "begränsa fält i sökresultat")

### <a name="example-5---return-next-batch-of-results"></a>Exempel 5 - returnerade nästa batch med resultat

Azure Search returnerar de översta 50 träffar baserat på sökrangordningen. Hämta nästa uppsättning matchande dokument genom att lägga till **$top = 100 & $skip = 50** att öka resultatuppsättningen till 100 dokument (standardvärdet är 50, maximalt är 1 000), hoppar du över de första 50 dokument. Kom ihåg att du måste ange sökvillkor, till exempel en frågeterm eller ett uttryck som hämta rangordnas resultat. Observera att search poäng minska den djupare du når i sökresultaten.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Results**

   ![Batch-sökresultat](./media/search-explorer/search-explorer-example-topskip.png "returnerade nästa batch med sökresultat")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtrera uttryck (större än, mindre än, lika med)

Använd den **$filter** parameter när du vill ange exakta kriterier i stället för fritextsökningen. Det här exemplet söker efter sovrum som är större än 3: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Filteruttryck](./media/search-explorer/search-explorer-example-filter.png "filtrera efter villkor")

## <a name="order-by-expressions"></a>Order by-uttryck

Lägg till **$orderby** att sortera resultaten efter ett annat fält än sökpoängen. Ett exempel-uttryck som du kan använda för att testa det här är `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Výraz OrderBy](./media/search-explorer/search-explorer-example-ordery.png "ändra sorteringsordning")

Båda **$filter** och **$orderby** uttryck är OData-konstruktioner. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Så här startar du Sökutforskaren

1. I den [Azure-portalen](https://portal.azure.com), öppna söktjänstsidan från instrumentpanelen eller [hitta din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänstelistan.

2. I tjänstöversiktssidan klickar du på **Sökutforskaren**.

   ![Söka efter kommandot Sökutforskaren i portalen](./media/search-explorer/search-explorer-cmd2.png "söka efter kommandot Sökutforskaren i portalen")

3. Välj indexet till frågan.

   ![Välj indexet som frågan](./media/search-explorer/search-explorer-changeindex-se2.png "Välj indexet")

4. Du kan också ange API-version. Som standard den aktuella allmänt tillgängliga API-versionen är markerad, men du kan välja en förhandsversion eller äldre API om syntaxen som du vill använda versionsspecifika.

5. När indexet och API-versionen är markerat, ange sökvillkor eller fullständiga frågeuttryck i sökfältet och klicka på **Search** att köra.

   ![Ange sökorden och klicka på Sök](./media/search-explorer/search-explorer-query-string-example.png "RETUR sökning villkor och klicka på Sök")

Tips för att söka i **Sökutforskaren**:

+ Resultaten returneras som utförlig JSON-dokument så att du kan visa dokumentet konstruktionen och innehåll, i helhet. Du kan använda frågeuttryck som visas i exemplen är att begränsa vilka fält som returneras.

+ Dokument består av alla fält som markerats som **hämtningsbara** i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-us-sample* i den **index** listan på översiktssidan.

+ Formaliafri frågor, liknar vad du kan ange i en extern webbläsare är användbara för att testa en slutanvändarens upplevelse. Exempel: Anta att exempelindexet inbyggda realestate och du kan skriva ”Seattle lägenheter lake washington” och kan du använda Ctrl-F för att hitta villkoren i sökresultaten. 

+ Fråga och filtrera uttryck måste vara uppvisat i en syntax som stöds av Azure Search. Standardvärdet är en [enkel syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), men du kan också använda [fullständig Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) för mer kraftfulla frågor. [Filtrera uttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) är en OData-syntax.


## <a name="next-steps"></a>Nästa steg

Följande resurser innehåller ytterligare information om och exempel på frågesyntax.

 + [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene-exempelfrågor](search-query-lucene-examples.md) 
 + [Syntax för OData-filteruttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 