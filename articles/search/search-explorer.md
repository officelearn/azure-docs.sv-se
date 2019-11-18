---
title: Verktyget search Explorer-fråga i Azure Portal
titleSuffix: Azure Cognitive Search
description: Sök Utforskaren är inbyggd i Azure Portal som är användbar för att utforska innehåll och verifiera frågor i Azure Kognitiv sökning. Ange strängar för Sök termer eller fraser, eller fullständigt kvalificerade Sök uttryck med avancerad syntax.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ff2986c4e90cb997df250d647bdfbd068d70e51f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112090"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Använd Sök Utforskaren i Azure Portal för att köra frågor mot dokument i Azure Kognitiv sökning 

Den här artikeln visar hur du frågar ett befintligt Azure Kognitiv sökning-index med hjälp av **Sök Utforskaren** i Azure Portal. Du kan starta Sök Utforskaren från kommando fältet för att skicka enkla eller fullständiga Lucene-frågeuttryck till ett befintligt index i tjänsten. 

   ![Kommandot Sök Utforskaren i portalen](./media/search-explorer/search-explorer-cmd2.png "Kommandot Sök Utforskaren i portalen")

## <a name="basic-search-strings"></a>Grundläggande Sök strängar

Följande exempel förutsätter det inbyggda exempel indexet för fastighets fastighets. Du kan skapa det här indexet med hjälp av guiden Importera data i portalen och välja **exempel** som data källa.

### <a name="example-1---empty-search"></a>Exempel 1 – tom sökning

För en första titt på ditt innehåll, kör en tom sökning genom att klicka på **Sök** utan att ange några villkor. En tom sökning är användbar som första fråga eftersom den returnerar hela dokument så att du kan granska dokument kompositionen. I en tom sökning finns det ingen Sök rang och dokument returneras i valfri ordning (`"@search.score": 1` för alla dokument). Som standard returneras 50-dokument i en Sök förfrågan.

Motsvarande syntax för en tom sökning är `*` eller `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Exempel på tom fråga](./media/search-explorer/search-explorer-example-empty.png "Icke-kvalificerat eller tomt fråge exempel")

### <a name="example-2---free-text-search"></a>Exempel 2 – kostnads fri texts ökning

Kostnads fria former, med eller utan operatörer, är användbara för att simulera användardefinierade frågor som skickas från en anpassad app till Azure Kognitiv sökning. Observera att när du anger villkor eller uttryck för frågor kommer Sök rankningen att bli i spel. I följande exempel visas en texts ökning i fritext.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Du kan använda Ctrl-F för att söka i resultat för specifika ränte villkor.

   ![Exempel på fritext fråga](./media/search-explorer/search-explorer-example-freetext.png "Exempel på fritext fråga")

### <a name="example-3---count-of-matching-documents"></a>Exempel 3 – antal matchande dokument 

Lägg till **$Count** för att hämta antalet matchningar som finns i ett index. I en tom sökning är Count det totala antalet dokument i indexet. I en kvalificerad sökning är det antalet dokument som matchar frågans inmatade frågor.

   ```Input1
   $count=true
   ```
   **Results**

   ![Exempel på antal dokument](./media/search-explorer/search-explorer-example-count.png "Antal matchande dokument i index")

### <a name="example-4---restrict-fields-in-search-results"></a>Exempel 4 – begränsa fält i Sök Resultat

Lägg till **$Select** för att begränsa resultaten till de uttryckligen namngivna fälten för mer läsbara utdata i **Sök Utforskaren**. Om du vill behålla Sök strängen och **$Count = True**, prefix argument med **&** . 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Exempel på gräns fält](./media/search-explorer/search-explorer-example-selectfield.png "Begränsa fält i Sök Resultat")

### <a name="example-5---return-next-batch-of-results"></a>Exempel 5 – returnera nästa resultat grupp

Azure Kognitiv sökning returnerar de översta 50-matchningarna baserat på Sök rankningen. För att hämta nästa uppsättning matchande dokument, Lägg till **$Top = 100, & $Skip = 50** för att öka resultat uppsättningen till 100 dokument (Standardvärdet är 50, maximum är 1000) och hoppa över de första 50-dokumenten. Kom ihåg att du måste ange Sök villkor, till exempel en frågeterm eller ett uttryck för att få rankade resultat. Observera att Sök Resultat minskar djupare som du når i Sök resultaten.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Sök Resultat för batch](./media/search-explorer/search-explorer-example-topskip.png "Returnera nästa batch med Sök Resultat")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filter uttryck (större än, mindre än, lika med)

Använd parametern **$filter** när du vill ange exakta villkor i stället för texts ökning utan text. Det här exemplet söker efter sovrum som är större än 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Filter uttryck](./media/search-explorer/search-explorer-example-filter.png "Filtrera efter kriterier")

## <a name="order-by-expressions"></a>Ordning efter uttryck

Lägg till **$OrderBy** för att sortera resultat efter ett annat fält förutom Sök resultat. Ett exempel uttryck som du kan använda för att testa det här är:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![OrderBy-uttryck](./media/search-explorer/search-explorer-example-ordery.png "Ändra sorterings ordning")

Både **$filter** -och **$OrderBy** -uttryck är OData-konstruktioner. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Starta Sök Utforskaren

1. I [Azure Portal](https://portal.azure.com)öppnar du sidan Sök tjänst från instrument panelen eller [söker efter tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänst listan.

2. Klicka på **search Explorer**på sidan tjänst översikt.

   ![Kommandot Sök Utforskaren i portalen](./media/search-explorer/search-explorer-cmd2.png "Kommandot Sök Utforskaren i portalen")

3. Välj index att fråga.

   ![Välj index att fråga](./media/search-explorer/search-explorer-changeindex-se2.png "Välj index")

4. Du kan också ange API-version. Som standard är den aktuella allmänt tillgängliga API-versionen markerad, men du kan välja en för hands version eller en äldre API om den syntax som du vill använda är versions bestämd.

5. När du har valt index-och API-version anger du Sök villkor eller fullständigt kvalificerade frågeuttryck i Sök fältet och klickar på **Sök** för att köra.

   ![Ange Sök villkor och klicka på Sök](./media/search-explorer/search-explorer-query-string-example.png "Ange Sök villkor och klicka på Sök")

Tips för att söka i **Sök Utforskaren**:

+ Resultat returneras som utförliga JSON-dokument så att du kan se dokument konstruktion och innehåll, i sin helhet. Du kan använda frågeuttryck, som visas i exemplen, för att begränsa vilka fält som returneras.

+ Dokument består av alla fält som har marker ATS som **hämtnings** bara i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-US-Sample* i listan **index** på sidan Sök översikt.

+ Kostnads fria frågor, som liknar vad du kan ange i en kommersiell webbläsare, är användbara för att testa en slut användar upplevelse. Om du till exempel antar det inbyggda realestate exempel indexet, kan du ange "Seattle lägenheter Lake Washington" och sedan använda Ctrl-F för att hitta termer inom Sök resultaten. 

+ Fråga-och filter uttryck måste ledas i en syntax som stöds av Azure Kognitiv sökning. Standardvärdet är en [enkel syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), men du kan också använda [fullständig Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) för mer kraftfulla frågor. [Filter uttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) är en OData-syntax.


## <a name="next-steps"></a>Nästa steg

Följande resurser innehåller ytterligare information om och exempel på frågesyntax.

 + [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene-exempelfrågor](search-query-lucene-examples.md) 
 + [Syntax för OData-filteruttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
