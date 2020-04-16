---
title: Masskopiering från en databas med hjälp av kontrolltabell
description: Lär dig hur du använder en lösningsmall för att kopiera massdata från en databas med hjälp av en extern kontrolltabell för att lagra en partitionslista över källtabeller med hjälp av Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414834"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Masskopiering från en databas med en kontrolltabell
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du vill kopiera data från ett informationslager i Oracle Server, Netezza, Teradata eller SQL Server till Azure SQL Data Warehouse måste du läsa in enorma mängder data från flera tabeller. Vanligtvis måste data partitioneras i varje tabell så att du kan läsa in rader med flera trådar parallellt från en enda tabell. I den här artikeln beskrivs en mall som ska användas i dessa scenarier.

 >! Obs! Om du vill kopiera data från ett litet antal tabeller med relativt liten datavolym till SQL Data Warehouse är det mer effektivt att använda [azure data fabrikskopieringsdataverktyget](copy-data-tool.md). Mallen som beskrivs i den här artikeln är mer än du behöver för det scenariot.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar en lista över källdatabaspartitioner som ska kopieras från en extern kontrolltabell. Därefter itererar den över varje partition i källdatabasen och kopierar data till målet.

Mallen innehåller tre aktiviteter:
- **Sökningen** hämtar listan över säkra databaspartitioner från en extern kontrolltabell.
- **ForEach** hämtar partitionslistan från uppslagsaktiviteten och itererar varje partition till kopieringsaktiviteten.
- **Kopiera** kopior av varje partition från källdatabasarkivet till målarkivet.

Mallen definierar följande parametrar:
- *Control_Table_Name* är din externa kontrolltabell, som lagrar partitionslistan för källdatabasen.
- *Control_Table_Schema_PartitionID* är namnet på kolumnnamnet i den externa kontrolltabellen som lagrar varje partitions-ID. Kontrollera att partitions-ID:et är unikt för varje partition i källdatabasen.
- *Control_Table_Schema_SourceTableName* är din externa kontrolltabell som lagrar varje tabellnamn från källdatabasen.
- *Control_Table_Schema_FilterQuery* är namnet på kolumnen i den externa kontrolltabellen som lagrar filterfrågan för att hämta data från varje partition i källdatabasen. Om du till exempel har partitionerat data efter år frågan som lagras i varje rad kan likna 'välj * från datakälla där LastModifytime >= ''2015-01-01 00:00:00'' och LastModifytime <= ''2015-12-31 23:59:59.999''.
- *Data_Destination_Folder_Path* är sökvägen där data kopieras till målarkivet (gäller när målet du väljer är "Filsystem" eller "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* är sökvägen till rotmappen där data kopieras till i målarkivet. 
- *Data_Destination_Directory* är katalogsökvägen under roten där data kopieras till målarkivet. 

De tre sista parametrarna, som definierar sökvägen i målarkivet, visas bara om målet du väljer är filbaserad lagring. Om du väljer "Azure Synapse Analytics (tidigare SQL DW)" som målbutik krävs inte dessa parametrar. Men tabellnamnen och schemat i SQL Data Warehouse måste vara desamma som de i källdatabasen.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Skapa en kontrolltabell i SQL Server eller Azure SQL Database för att lagra källdatabaspartitionslistan för masskopiering. I följande exempel finns det fem partitioner i källdatabasen. Tre partitioner är för *datasource_table*och två är för *project_table*. Kolumnen *LastModifytime* används för att partitionera data i tabell *datasource_table* från källdatabasen. Frågan som används för att läsa den första partitionen är 'välj * från datasource_table där LastModifytime >= ''2015-01-01 00:00:00'' och LastModifytime <= ''2015-12-31 23:59.999''. Du kan använda en liknande fråga för att läsa data från andra partitioner.

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

2. Gå till **masskopiering från** databasmallen. Skapa en **ny** anslutning till den externa kontrolltabell som du skapade i steg 1.

    ![Skapa en ny anslutning till kontrolltabellen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Skapa en **ny** anslutning till källdatabasen som du kopierar data från.

    ![Skapa en ny anslutning till källdatabasen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Skapa en **ny** anslutning till måldatalagret som du kopierar data till.

    ![Skapa en ny anslutning till målarkivet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Välj **Använd den här mallen**.

6. Du ser pipelinen, som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Klicka på **Felsökning**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Du ser resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Valfritt) Om du väljer "Azure Synapse Analytics (tidigare SQL DW)" som datamål måste du ange en anslutning till Azure Blob-lagring för mellanlagring, i enlighet med SQL Data Warehouse Polybase. Mallen genererar automatiskt en behållarsökväg för Blob-lagring. Kontrollera om behållaren har skapats efter pipelinekörningen.
    
    ![Polybasinställning](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
