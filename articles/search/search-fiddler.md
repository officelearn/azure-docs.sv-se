---
title: "Hur du använder och testar REST API:erna för Azure Search med hjälp av Fiddler | Microsoft Docs"
description: "Använd Fiddler för att verifiera Azure Search-tillgänglighet och testa REST-API:erna utan kod."
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: 790e5779-c6a3-4a07-9d1e-d6739e6b87d2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c38b73fa69bee34ce2434c6274cb017c99ef3c35
ms.contentlocale: sv-se
ms.lasthandoff: 12/07/2016

---

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utvärdera och testa REST-API:erna för Azure Search med hjälp av Fiddler
> [!div class="op_single_selector"]
>
> * [Översikt](search-query-overview.md)
> * [Sökutforskaren](search-explorer.md)
> * [Fiddler](search-fiddler.md)
> * [NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Den här artikeln beskriver hur du använder Fiddler, som är tillgängligt som en [kostnadsfri nedladdning från Telerik](http://www.telerik.com/fiddler), för att skicka HTTP-förfrågningar till och visa svar med hjälp av REST-API:et för Azure Search, utan att skriva någon kod. Azure Search är en fullständigt hanterad, värd- och molnbaserad söktjänst i Microsoft Azure, som enkelt kan programmeras via .NET och REST-API:er. Azure Search-tjänstens REST API:er finns dokumenterade på [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

I följande steg ska du skapa ett index, ladda upp dokument, fråga indexet och fråga systemet efter tjänstinformation.

För att slutföra dessa steg behöver du en Azure Search-tjänst och `api-key`. Anvisningar för hur du kommer igång finns i [Skapa en Azure Search-tjänst på portalen](search-create-service-portal.md).

## <a name="create-an-index"></a>Skapa ett index
1. Starta Fiddler. Inaktivera **Capture Traffic**på **File**-menyn för att dölja HTTP-aktivitet som inte rör den aktuella aktiviteten.
2. På fliken **Composer** formulerar du en begäran som ser ut som i följande skärmbild.

      ![][1]
3. Välj **PUT**.
4. Ange en URL som anger tjänstens URL, attribut för begäran och API-versionen. Några saker att tänka på:

   * Använd HTTPS som prefix.
   * Attributet för begäran är ”/indexes/hotels”. Detta beordrar Search att skapa ett index med namnet ”hotels”.
   * API-versionen anges i gemener, i följande format: ”?api-version=2016-09-01”. API-versioner är viktiga eftersom Azure Search distribuerar uppdateringar regelbundet. I sällsynta fall kan en tjänstuppdatering implementera en viktig ändring i API:et. Av den anledningen kräver Azure Search en API-version i varje begäran så att du har full kontroll över vilken version som används.

     En fullständig URL ser ut som i följande exempel.

             https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01
5. Ange huvudet för begäran och ersätt värden och API-nyckeln med värdena för din tjänst.

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
6. I Begärandetext klistrar du in fälten som bildar indexdefinitionen.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }
7. Klicka på **Execute**.

Efter några sekunder bör du se ett 201 HTTP-svar i sessionslistan som anger att indexet har skapats.

Om HTTP 504 returneras kontrollerar du att HTTPS används i URL:en. Om HTTP 400 eller 404 returneras kontrollerar du att alla fält har kopierats och klistrats in korrekt i begärandetexten. HTTP 403 tyder vanligen på problem med API-nyckeln (antingen en ogiltig nyckel eller ett syntaxproblem när API-nyckeln angavs).

## <a name="load-documents"></a>Läsa in dokument
På fliken **Composer** ser din begäran om att publicera dokument ut så här: Begäran innehåller sökdata för fyra hotell.

   ![][2]

1. Välj **POST**.
2. Ange en URL som börjar med HTTPS, följt av tjänstens URL, följt av ”/indexes/<'indexname'>/docs/index?api-version=2016-09-01”. En fullständig URL ser ut som i följande exempel.

         https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
3. Huvudet i begäran ska vara samma som tidigare. Kom ihåg att du ersatte värden och API-nyckeln med värden för din tjänst.

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. Begärandetexten innehåller fyra dokument som ska läggas till i hotellindexet.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }
5. Klicka på **Execute**.

Efter några sekunder bör du se ett 200 HTTP-svar i sessionslistan. Detta anger att dokumenten har skapats. Om ett 207-svar returneras misslyckades uppladdningen av minst ett dokument. Om ett 404-svar returneras beror det på ett syntaxfel i sidhuvudet för begäran eller i begärandetexten.

## <a name="query-the-index"></a>Skicka frågor mot indexet
Nu när ett index och dokument har lästs in kan du skicka frågor mot dem.  **GET**-kommandot på fliken **Composer**skickar frågor mot din tjänst och ser ut som i följande skärmbild.

   ![][3]

1. Välj **GET**.
2. Ange en URL som börjar med HTTPS, följt av tjänstens URL, följt av ”/indexes/<'indexname'>/docs?”, följt av frågeparametrar. Som ett exempel kan du använda följande URL och ersätta exempelvärdnamnet med ett som är giltigt för din tjänst.

         https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2016-09-01

   Den här frågan söker efter termen ”motel” och hämtar aspektkategorier för betygsättning.
3. Huvudet i begäran ska vara samma som tidigare. Kom ihåg att du ersatte värden och API-nyckeln med värden för din tjänst.

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444

Svarskoden bör vara 200 och svarsutdata bör se ut som i följande skärmbild.

   ![][4]

Följande exempelfråga kommer från avsnittet om [sökindexåtgärder (Azure Search-API)](http://msdn.microsoft.com/library/dn798927.aspx) på MSDN. Många av exempelfrågorna i det här avsnittet innehåller blanksteg, som inte är tillåtna i Fiddler. Ersätt varje blanksteg med ett plustecken (+) innan du klistrar in frågesträngen innan du försöker köra frågan i Fiddler.

**Innan blankstegen har ersatts:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**När blankstegen har ersatts med +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-the-system"></a>Fråga systemet
Du kan också skicka frågor i systemet för att visa antalet dokument och lagringsanvändningen. På fliken **Composer** ser din begäran ut ungefär som nedan, och svaret returnerar antalet dokument och mängden förbrukat utrymme.

 ![][5]

1. Välj **GET**.
2. Ange en URL som innehåller tjänstens URL, följt av ”/indexes/hotels/stats?api-version=2016-09-01”:

         https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01
3. Ange huvudet för begäran och ersätt värden och API-nyckeln med värdena för din tjänst.

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. Lämna begärandetexten tom.
5. Klicka på **Execute**. Du bör se en 200 HTTP-statuskod i sessionslistan. Välj posten som publicerats för ditt kommando.
6. Klicka på fliken **Inspectors**, klicka på fliken **Headers** och välj sedan JSON-formatet. Du bör se antalet dokument och lagringsstorleken (i kB).

## <a name="next-steps"></a>Nästa steg
Avsnittet [Hantera din Search-tjänst i Azure](search-manage.md) innehåller information om hur du hanterar och använder Azure Search utan att skriva någon kod.

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png

