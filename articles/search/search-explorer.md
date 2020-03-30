---
title: Frågeverktyget för sökutforskaren i Azure-portalen
titleSuffix: Azure Cognitive Search
description: I den här snabbstarten för Azure-portalen använder du Sök utforskaren för att lära dig frågesyntax, testa frågeuttryck eller granska ett sökdokument. Sökutforskaren frågar index i Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369671"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Snabbstart: Använd Sökutforskaren för att köra frågor i portalen

**Sökutforskaren** är ett inbyggt frågeverktyg som används för att köra frågor mot ett sökindex i Azure Cognitive Search. Det här verktyget gör det enkelt att lära sig frågesyntax, testa en fråga eller ett filteruttryck eller bekräfta resultatet av en indexuppdatering genom att kontrollera att det finns nyare innehåll.

Den här snabbstarten använder **realestate-us-sample-index** för att demonstrera Sök explorer. Begäranden formuleras med hjälp av [REST API för sök](https://docs.microsoft.com/rest/api/searchservice/), med svar som returneras som JSON-dokument.

## <a name="prerequisites"></a>Krav

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

+ **realestate-us-sample-index** används för den här snabbstarten. Gå igenom [**guiden Importera data**](search-import-data-portal.md) för att generera indexet från den inbyggda exempeldatakällan.

## <a name="start-search-explorer"></a>Starta utforskaren För sök

1. Öppna söktjänstsidan från instrumentpanelen i [Azure-portalen](https://portal.azure.com)eller [hitta tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Öppna Sökutforskaren från kommandofältet:

   ![Kommandot Sök utforskare i portalen](./media/search-explorer/search-explorer-cmd2.png "Kommandot Sök utforskare i portalen")

    Eller använd den inbäddade fliken **Sökutforskare** på ett öppet index:

   ![Fliken Sökutforskare](./media/search-explorer/search-explorer-tab.png "Fliken Sökutforskare")

## <a name="unspecified-query"></a>Ospecificerad fråga

Om du vill titta på innehållet första gången kör du en tom sökning genom att klicka på **Sök** utan några termer. En tom sökning är användbar som en första fråga eftersom den returnerar hela dokument så att du kan granska dokumentsammansättningen. Vid en tom sökning finns det ingen sökrankning och`"@search.score": 1` dokument returneras i godtycklig ordning ( för alla dokument). Som standard returneras 50 dokument i en sökbegäran.

Motsvarande syntax för en `*` `search=*`tom sökning är eller .
   
   ```http
   search=*
   ```

   **Results**
   
   ![Exempel på tom fråga](./media/search-explorer/search-explorer-example-empty.png "Okvalificerat eller tomt frågeexempel")

## <a name="free-text-search"></a>Gratis text sökning

Frågor i fri form, med eller utan operatörer, är användbara för att simulera användardefinierade frågor som skickas från en anpassad app till Azure Cognitive Search. Endast de fält som tillskrivs som **Sökbara** i indexdefinitionen genomsöks efter matchningar. 

Observera att när du anger sökvillkor, till exempel frågetermer eller uttryck, spelar sökrankningen in. Följande exempel illustrerar en fritextsökning.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Du kan använda Ctrl-F för att söka i resultat för specifika villkor av intresse.

   ![Exempel på fråga om fritext](./media/search-explorer/search-explorer-example-freetext.png "Exempel på fråga om fritext")

## <a name="count-of-matching-documents"></a>Antal matchande dokument 

Lägg till **$count=true** för att få antalet matchningar som finns i ett index. Vid en tom sökning är antalet det totala antalet dokument i indexet. Vid en kvalificerad sökning är det antalet dokument som matchar frågeindata.

   ```http
   $count=true
   ```

   **Results**

   ![Exempel på antal dokument](./media/search-explorer/search-explorer-example-count.png "Antal matchande dokument i index")

## <a name="limit-fields-in-search-results"></a>Begränsa fält i sökresultaten

Lägg till $select om [**du**](search-query-odata-select.md) vill begränsa resultaten till de uttryckligen namngivna fälten för mer läsbara utdata i **Sökutforskaren**. Om du vill behålla söksträngen och **$count=true**prefixargument med **&**. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Exempel på begränsa fält](./media/search-explorer/search-explorer-example-selectfield.png "Begränsa fält i sökresultaten")

## <a name="return-next-batch-of-results"></a>Returnera nästa batch av resultat

Azure Cognitive Search returnerar de 50 bästa matchningarna baserat på sökrankningen. Om du vill hämta nästa uppsättning matchande dokument lägger du **till $top=100 &$skip=50** för att öka resultatuppsättningen till 100 dokument (standard är 50, max 1000), och hoppar över de första 50 dokumenten. Kom ihåg att du måste ange sökvillkor, till exempel en frågeterm eller ett uttryck, för att få rangordnade resultat. Observera att sökresultaten minskar ju djupare du når in i sökresultaten.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Sökresultat för batch](./media/search-explorer/search-explorer-example-topskip.png "Returnera nästa omgång sökresultat")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filteruttryck (större än, mindre än, lika med)

Använd [**parametern $filter**](search-query-odata-filter.md) när du vill ange exakta villkor i stället för fritextsökning. Fältet måste anges som **filterbart** i indexet. Det här exemplet söker efter sovrum som är större än 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Filteruttryck](./media/search-explorer/search-explorer-example-filter.png "Filtrera efter villkor")

## <a name="order-by-expressions"></a>Order-för-uttryck

Lägg till [**$orderby**](search-query-odata-orderby.md) om du vill sortera resultat efter ett annat fält förutom sökresultat. Fältet måste tilldelas som **sorterbart** i indexet. Ett exempeluttryck som du kan använda för att testa det här är:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![Orderby uttryck](./media/search-explorer/search-explorer-example-ordery.png "Ändra sorteringsordning")

Både **$filter** och **$orderby** uttryck är OData-konstruktioner. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Lärdomar

I den här snabbstarten använde du **Sökutforskaren** för att fråga ett index med REST API.

+ Resultaten returneras som utförliga JSON-dokument så att du kan visa dokumentkonstruktion och innehåll i sin helhet. Du kan använda frågeuttryck som visas i exemplen för att begränsa vilka fält som returneras.

+ Dokument består av alla fält som är markerade som **Hämtningsbara** i indexet. Om du vill visa indexattribut i portalen klickar du på exempel på *realestate-us* i **indexlistan** på sidan Med sököversikt.

+ Frågor i fri form, liknande vad du kan ange i en kommersiell webbläsare, är användbara för att testa en slutanvändarupplevelse. Om du till exempel antar det inbyggda exempelindexet för fastigheter kan du ange "Seattle apartments lake washington", och sedan kan du använda Ctrl-F för att hitta termer i sökresultaten. 

+ Fråge- och filteruttryck artikuleras i en syntax som stöds av Azure Cognitive Search. Standard är en [enkel syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), men du kan eventuellt använda [full Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) för mer kraftfulla frågor. [Filteruttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) är en OData-syntax.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om frågestrukturer och syntax använder du Postman eller ett motsvarande verktyg för att skapa frågeuttryck som utnyttjar fler delar av API:et. [Search REST API](https://docs.microsoft.com/rest/api/searchservice/) är särskilt användbart för inlärning och utforskning.

> [!div class="nextstepaction"]
> [Skapa en grundläggande fråga i Postman](search-query-simple-examples.md)