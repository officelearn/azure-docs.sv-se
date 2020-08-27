---
title: Verktyget search Explorer-fråga i Azure Portal
titleSuffix: Azure Cognitive Search
description: I den här Azure Portal snabb starten använder du Sök Utforskaren för att lära dig frågesyntaxen, testa frågeuttryck eller granska ett Sök dokument. Sök Utforskaren frågar efter index i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/07/2020
ms.openlocfilehash: 8f004ff8118829ebf2d245a136cf22cdde23accc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929796"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Snabb start: Använd Sök Utforskaren för att köra frågor i portalen

**Sök Utforskaren** är ett inbyggt frågespråk som används för att köra frågor mot ett Sök index i Azure kognitiv sökning. Det här verktyget gör det enkelt att lära sig frågesyntaxen, testa ett fråge-eller filter uttryck eller bekräfta data uppdatering genom att kontrol lera om nytt innehåll finns i indexet.

I den här snabb starten används ett befintligt index för att demonstrera Sök Utforskaren. Begär Anden formuleras med hjälp av [sök REST API](/rest/api/searchservice/), med svar som returneras som JSON-dokument.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ *Realestate-US-Sample-indexet* används för den här snabb starten. Använd guiden [**Importera data**](search-import-data-portal.md) för att skapa indexet. I det första steget när du uppmanas till data källan väljer du **exempel** och väljer sedan data källan **realestate-US-Sample** . Godkänn alla standardinställningar i guiden för att skapa indexet.

## <a name="start-search-explorer"></a>Starta Sök Utforskaren

1. Öppna sidan Sök tjänst i [Azure Portal](https://portal.azure.com)på instrument panelen eller [Sök efter din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Öppna Sök Utforskaren från kommando fältet:

   ![Kommandot Sök Utforskaren i portalen](./media/search-explorer/search-explorer-cmd2.png "Kommandot Sök Utforskaren i portalen")

    Eller Använd den inbäddade **Sök Utforskaren** -fliken i ett öppet index:

   ![Fliken Sök Utforskaren](./media/search-explorer/search-explorer-tab.png "Fliken Sök Utforskaren")

## <a name="unspecified-query"></a>Ospecificerad fråga

För en första titt på innehåll kör du en tom sökning genom att klicka på **Sök** utan att ange några villkor. En tom sökning är användbar som första fråga eftersom den returnerar hela dokument så att du kan granska dokument kompositionen. I en tom sökning finns det ingen Sök rang och dokument returneras i valfri ordning ( `"@search.score": 1` för alla dokument). Som standard returneras 50-dokument i en Sök förfrågan.

Motsvarande syntax för en tom sökning är `*` eller `search=*` .
   
   ```http
   search=*
   ```

   **Resultat**
   
   ![Exempel på tom fråga](./media/search-explorer/search-explorer-example-empty.png "Icke-kvalificerat eller tomt fråge exempel")

## <a name="free-text-search"></a>Texts ökning utan text

Kostnads fria former, med eller utan operatörer, är användbara för att simulera användardefinierade frågor som skickas från en anpassad app till Azure Kognitiv sökning. Endast de fält som är attribut som **sökbara** i index definitionen genomsöks efter matchningar. 

Observera att när du anger Sök villkor, t. ex. sökord eller uttryck, kommer Sök rankningen att spelas upp. I följande exempel visas en texts ökning i fritext.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultat**

   Du kan använda Ctrl-F för att söka i resultat för specifika ränte villkor.

   ![Exempel på fritext fråga](./media/search-explorer/search-explorer-example-freetext.png "Exempel på fritext fråga")

## <a name="count-of-matching-documents"></a>Antal matchande dokument 

Lägg till **$Count = sant** om du vill hämta antalet matchningar som finns i ett index. I en tom sökning är Count det totala antalet dokument i indexet. I en kvalificerad sökning är det antalet dokument som matchar frågans inmatade frågor.

   ```http
   $count=true
   ```

   **Resultat**

   ![Exempel på antal dokument](./media/search-explorer/search-explorer-example-count.png "Antal matchande dokument i index")

## <a name="limit-fields-in-search-results"></a>Begränsa fält i Sök Resultat

Lägg till [**$Select**](search-query-odata-select.md) för att begränsa resultaten till de uttryckligen namngivna fälten för mer läsbara utdata i **Sök Utforskaren**. Om du vill behålla Sök strängen och **$Count = True**, prefix argument med **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultat**

   ![Exempel på gräns fält](./media/search-explorer/search-explorer-example-selectfield.png "Begränsa fält i Sök Resultat")

## <a name="return-next-batch-of-results"></a>Returnera nästa resultat grupp

Azure Kognitiv sökning returnerar de översta 50-matchningarna baserat på Sök rankningen. Om du vill hämta nästa uppsättning matchande dokument, lägger du till **$Top = 100, &$Skip = 50** för att öka resultat uppsättningen till 100-dokument (standard är 50, maximum är 1000) och hoppar över de första 50-dokumenten. Kom ihåg att du måste ange Sök villkor, till exempel en frågeterm eller ett uttryck för att få rankade resultat. Observera att Sök Resultat minskar djupare som du når i Sök resultaten.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultat**

   ![Sök Resultat för batch](./media/search-explorer/search-explorer-example-topskip.png "Returnera nästa batch med Sök Resultat")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filter uttryck (större än, mindre än, lika med)

Använd parametern [**$filter**](search-query-odata-filter.md) när du vill ange exakta villkor i stället för texts ökning utan text. Fältet måste ha attributet **Filterable** i indexet. Det här exemplet söker efter sovrum som är större än 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultat**

   ![Filter uttryck](./media/search-explorer/search-explorer-example-filter.png "Filtrera efter kriterier")

## <a name="order-by-expressions"></a>Ordning efter uttryck

Lägg till [**$OrderBy**](search-query-odata-orderby.md) för att sortera resultat efter ett annat fält förutom Sök resultat. Fältet måste ha attributet **sorterat** i indexet. Ett exempel uttryck som du kan använda för att testa det här är:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultat**

   ![OrderBy-uttryck](./media/search-explorer/search-explorer-example-ordery.png "Ändra sorteringsordning")

Både **$filter** -och **$OrderBy** -uttryck är OData-konstruktioner. Mer information finns i [OData-filtersyntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Lärdomar

I den här snabb starten använde du **Sök Utforskaren** för att fråga ett index med hjälp av REST API.

+ Resultat returneras som utförliga JSON-dokument så att du kan se dokument konstruktion och innehåll, i sin helhet. Du kan använda frågeuttryck, som visas i exemplen, för att begränsa vilka fält som returneras.

+ Dokument består av alla fält som har marker ATS som **hämtnings** bara i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-US-Sample* i listan **index** på sidan Sök översikt.

+ Kostnads fria frågor, som liknar vad du kan ange i en kommersiell webbläsare, är användbara för att testa en slut användar upplevelse. Om du till exempel antar det inbyggda realestate exempel indexet, kan du ange "Seattle lägenheter Lake Washington" och sedan använda Ctrl-F för att hitta termer inom Sök resultaten. 

+ Fråga-och filter uttryck visas i en syntax som stöds av Azure Kognitiv sökning. Standardvärdet är en [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search), men du kan också använda [fullständig Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) för mer kraftfulla frågor. [Filter uttryck](/rest/api/searchservice/odata-expression-syntax-for-azure-search) är en OData-syntax.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om frågeuttryck och syntax använder du Postman eller ett motsvarande verktyg för att skapa frågeuttryck som utnyttjar fler delar av API: et. [Sök REST API](/rest/api/searchservice/) är särskilt användbart för utbildning och utforskning.

> [!div class="nextstepaction"]
> [Skapa en grundläggande fråga i Postman](search-query-simple-examples.md)