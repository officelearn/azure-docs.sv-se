---
title: Modellera SQL-relationsdata för import och indexering
titleSuffix: Azure Cognitive Search
description: Lär dig hur du modellerar relationsdata, denormaliseras till en platt resultatuppsättning, för indexering och fulltextsökning i Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790082"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Så här modellerar du relations-SQL-data för import och indexering i Azure Cognitive Search

Azure Cognitive Search accepterar en platt raduppsättning som indata till [indexeringspipelinen](search-what-is-an-index.md). Om källdata kommer från kopplade tabeller i en SQL Server-relationsdatabas förklarar den här artikeln hur du skapar resultatuppsättningen och hur du modellerar en överordnad och underordnad relation i ett Azure Cognitive Search-index.

Som en illustration hänvisar vi till en hypotetisk hotelldatabas, baserad på [demodata.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Anta att databasen består av ett Hotels$ bord med 50 hotell, och ett rum $ bord med rum av olika typer, priser och bekvämligheter, för totalt 750 rum. Det finns en 1:1:1-relation mellan tabellerna. I vår metod kommer en vy att ge frågan som returnerar 50 rader, en rad per hotell, med tillhörande rumsdetalj inbäddad i varje rad.

   ![Tabeller och visa i hotelldatabasen](media/index-sql-relational-data/hotels-database-tables-view.png "Tabeller och visa i hotelldatabasen")


## <a name="the-problem-of-denormalized-data"></a>Problemet med denormaliserade data

En av utmaningarna med att arbeta med en-till-många-relationer är att standardfrågor som bygger på kopplade tabeller returnerar denormaliserade data, vilket inte fungerar bra i ett Azure Cognitive Search-scenario. Tänk på följande exempel som går med hotell och rum.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Resultaten från den här frågan returnerar alla hotellfält, följt av alla rumsfält, med preliminär hotellinformation som upprepas för varje rumsvärde.

   ![Denormaliserade data, redundanta hotelldata när rumsfält läggs till](media/index-sql-relational-data/denormalize-data-query.png "Denormaliserade data, redundanta hotelldata när rumsfält läggs till")


Även om den här frågan lyckas på ytan (som tillhandahåller alla data i en platt raduppsättning), misslyckas den med att leverera rätt dokumentstruktur för den förväntade sökupplevelsen. Under indexeringen skapar Azure Cognitive Search ett sökdokument för varje rad som används. Om dina sökdokument såg ut som ovanstående resultat, skulle du ha uppfattat dubbletter - sju separata dokument för Twin Dome hotellet ensam. En fråga om "hotell i Florida" skulle returnera sju resultat för bara Twin Dome hotel, driver andra relevanta hotell djupt in i sökresultaten.

För att få den förväntade upplevelsen av ett dokument per hotell, bör du ge en raduppsättning på rätt granularitet, men med fullständig information. Lyckligtvis kan du göra detta enkelt genom att anta teknikerna i den här artikeln.

## <a name="define-a-query-that-returns-embedded-json"></a>Definiera en fråga som returnerar inbäddadE JSON

För att ge den förväntade sökupplevelsen bör din datauppsättning bestå av en rad för varje sökdokument i Azure Cognitive Search. I vårt exempel vill vi ha en rad för varje hotell, men vi vill också att våra användare ska kunna söka på andra rumsrelaterade områden som de bryr sig om, till exempel nattliga, storlek och antal sängar, eller en utsikt över stranden, som alla är en del av ett rum detalj.

Lösningen är att fånga rumsdetaljerna som kapslade JSON och sedan infoga JSON-strukturen i ett fält i en vy, som visas i det andra steget. 

1. Anta att du har två sammanfogade bord, Hotels$ och Rooms$, som innehåller information om 50 hotell och 750 rum, och är förenade på HotelID-fältet. Individuellt innehåller dessa bord 50 hotell och 750 relaterade rum.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Skapa en vy som består av alla`SELECT * from dbo.Hotels$`fält i den överordnade tabellen ( ), med tillägg av ett nytt *rumsfält* som innehåller utdata för en kapslad fråga. A **FOR JSON** `SELECT * from dbo.Rooms$` AUTO-satsen på strukturerar utdata som JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Följande skärmbild visar den resulterande vyn med fältet *Rum* nvarchar längst ned. *Fältet Rum* finns bara i utsikten över HotelRooms.

   ![Utsikt över HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms-vy")

1. Kör `SELECT * FROM dbo.HotelRooms` för att hämta raduppsättningen. Den här frågan returnerar 50 rader, en per hotell, med tillhörande rumsinformation som en JSON-samling. 

   ![Rowset från HotelRooms visa](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset från HotelRooms visa")

Den här raduppsättningen är nu klar för import till Azure Cognitive Search.

> [!NOTE]
> Den här metoden förutsätter att inbäddade JSON ligger under de [maximala kolumnstorleksgränserna för SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Om dina data inte passar kan du prova en programmatisk metod, vilket illustreras i [Exempel: Modellera AdventureWorks Inventory-databasen för Azure Cognitive Search](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Använd en komplex samling för "många" sidan av en en-till-många-relation

På sidan Azure Cognitive Search skapar du ett indexschema som modellerar 1:11-relationen med kapslade JSON. Den resultatuppsättning som du skapade i föregående avsnitt motsvarar i allmänhet indexschemat nedan (vi klipper ut vissa fält för korthet).

Följande exempel liknar exemplet i [Så här modellerar du komplexa datatyper](search-howto-complex-data-types.md#creating-complex-fields). *Rummen* struktur, som har varit i fokus för denna artikel, är i fälten samling av ett index som heter *hotell*. I det här exemplet visas också en komplex typ för *Adress*, som skiljer sig från *Rum* genom att den består av en fast uppsättning objekt, i motsats till det många godtyckliga antalet objekt som tillåts i en samling.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Med tanke på den tidigare resultatuppsättningen och indexschemat ovan har du alla nödvändiga komponenter för en lyckad indexeringsåtgärd. Den förenklade datauppsättningen uppfyller indexeringskraven men bevarar detaljinformation. I Azure Cognitive Search-indexet hamnar sökresultaten enkelt i hotellbaserade entiteter, samtidigt som kontexten för enskilda rum och deras attribut bevaras.

## <a name="next-steps"></a>Nästa steg

Med hjälp av din egen datauppsättning kan du använda [guiden Importera data](search-import-data-portal.md) för att skapa och läsa in indexet. Guiden identifierar den inbäddade JSON-samlingen, till exempel den som finns i *Rum,* och härleder ett indexschema som innehåller en komplex typsamling. 

  ![Guiden Importera dataindex](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Guiden Importera dataindex")

Prova följande snabbstart för att lära dig de grundläggande stegen i guiden Importera data.

> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett sökindex med Azure-portalen](search-get-started-portal.md)