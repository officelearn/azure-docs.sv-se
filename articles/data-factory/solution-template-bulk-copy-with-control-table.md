---
title: Masskopiera från en databas med hjälp av en kontroll-tabell med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall för att kopiera stora mängder data från en databas med en extern tabell för att lagra en partition över källtabellerna med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635453"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Masskopiering från en databas med en kontroll-tabell

Om du vill kopiera data från ett informationslager i Oracle-Server, Netezza, Teradata eller SQL Server till Azure SQL Data Warehouse, som du behöver läsa in stora mängder data från flera tabeller. Vanligtvis har data som ska partitioneras i varje tabell så att du kan läsa in rader med flera trådar parallellt från en enskild tabell. Den här artikeln beskrivs en mall som ska användas i dessa scenarier.

 >! Observera att om du vill kopiera data från ett litet antal tabeller med relativt små datavolym till SQL Data Warehouse, är det mer effektivt att använda den [verktyget kopieringsdata för Azure Data Factory](copy-data-tool.md). Den mall som beskrivs i den här artikeln är högre än vad du behöver för scenariot.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar en lista över databasen källpartitionerna att kopiera från en extern tabell. Sedan itererar över varje partition i källdatabasen och kopierar data till målet.

Mallen innehåller tre aktiviteter:
- **Lookup** hämtar listan över databaspartitioner för att från en extern tabell.
- **ForEach** hämtar listan över partition från Lookup-aktiviteten och upprepas varje partition till kopieringsaktiviteten.
- **Kopiera** kopierar varje partition från databasen källagringen till målarkiv.

Mallen definierar fem parametrar:
- *Control_Table_Name* är din externa kontroll-tabell som lagrar Partitionslista för källdatabasen.
- *Control_Table_Schema_PartitionID* är namnet på kolumnnamnet i din externa kontroll-tabell som lagrar varje partition-ID. Se till att partitions-ID är unikt för varje partition i källdatabasen.
- *Control_Table_Schema_SourceTableName* är extern kontroll tabellen som lagrar varje tabellnamn från källdatabasen.
- *Control_Table_Schema_FilterQuery* är namnet på kolumnen i din externa kontroll-tabellen som lagrar filterfråga för att hämta data från varje partition i källdatabasen. Till exempel om du har partitionerade data efter år den fråga som lagras i varje rad kan se ut ”Välj * från datasource där LastModifytime > = '' 2015-01-01 00:00:00” och LastModifytime < = '' 2015-12-31 23:59:59.999'' '.
- *Data_Destination_Folder_Path* är den sökväg där data har kopierats till ditt målarkiv. Den här parametern visas endast om det mål som du väljer är filbaserad lagring. Om du väljer SQL Data Warehouse som målarkiv måste den här parametern krävs inte. Men tabellnamn och schema i SQL Data Warehouse måste vara samma som de i källdatabasen.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Skapa en kontroll i SQL Server eller Azure SQL Database för att lagra källistan för databasen partition för masskopiering. I följande exempel finns fem partitioner i källdatabasen. Tre partitioner avser den *datasource_table*, och två är avsedda för den *project_table*. Kolumnen *LastModifytime* används för att partitionera data i tabellen *datasource_table* från källdatabasen. Den fråga som används för att läsa den första partitionen är ”Välj * från datasource_table där LastModifytime > = '' 2015-01-01 00:00:00” och LastModifytime < = '' 2015-12-31 23:59:59.999'' '. Du kan använda en liknande fråga för att läsa data från andra partitioner.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Gå till den **masskopiering från databasen** mall. Skapa en **New** anslutning till tabellen externa kontroll som du skapade i steg 1.

    ![Skapa en ny anslutning till tabellen kontroll](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Skapa en **New** anslutning till källdatabasen som du kopierar data från.

     ![Skapa en ny anslutning till källdatabasen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Skapa en **New** anslutning till sidan måldatalager lagra att du kopierar data till.

    ![Skapa en ny anslutning till målarkiv](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Du kan se pipelinen, som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Välj **felsöka**, ange den **parametrar**, och välj sedan **Slutför**.

    ![Klicka på ** Debug **](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Du ser resultaten som liknar följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Valfritt) Om du valde SQL Data Warehouse som datamålet, måste du ange en anslutning till Azure Blob storage för mellanlagring som krävs av SQL Data Warehouse Polybase. Kontrollera att behållaren i Blob storage har redan skapats.
    
    ![Polybase-inställning](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
