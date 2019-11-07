---
title: Mass kopiering från en databas med hjälp av en kontroll tabell med Azure Data Factory
description: Lär dig hur du använder en lösnings mall för att kopiera Mass data från en databas med hjälp av en extern kontroll tabell för att lagra en partitions lista med käll tabeller med hjälp av Azure Data Factory.
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
ms.openlocfilehash: b651721e9b833c02e4789c79ff5ad0b49ce31343
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684273"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Mass kopiering från en databas med en kontroll tabell

Om du vill kopiera data från ett informations lager i Oracle server, Netezza, Teradata eller SQL Server till Azure SQL Data Warehouse måste du läsa in stora mängder data från flera tabeller. Normalt måste data partitioneras i varje tabell så att du kan läsa in rader med flera trådar parallellt från en enda tabell. I den här artikeln beskrivs en mall som du kan använda i dessa scenarier.

 >! Obs! Om du vill kopiera data från ett litet antal tabeller med relativt små data volymer till SQL Data Warehouse, är det mer effektivt att använda [verktyget Azure Data Factory kopiera data](copy-data-tool.md). Mallen som beskrivs i den här artikeln är mer än du behöver för det scenariot.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen hämtar en lista över käll databas partitioner som ska kopieras från en extern kontroll tabell. Sedan upprepas det över varje partition i käll databasen och data kopieras till målet.

Mallen innehåller tre aktiviteter:
- **Lookup** hämtar listan över att se till att diskpartitioner från en extern kontroll tabell.
- Med den här **listan får du** en lista över partitionerna från söknings aktiviteten och upprepar varje partition till kopierings aktiviteten.
- **Kopiera** kopierar varje partition från käll databasens lager till mål arkivet.

Mallen definierar fem parametrar:
- *Control_Table_Name* är din externa kontroll tabell som lagrar partitionerings listan för käll databasen.
- *Control_Table_Schema_PartitionID* är namnet på kolumn namnet i den externa kontroll tabell som lagrar varje PARTITIONS-ID. Kontrol lera att partitions-ID: t är unikt för varje partition i käll databasen.
- *Control_Table_Schema_SourceTableName* är din externa kontroll tabell som lagrar varje tabell namn från käll databasen.
- *Control_Table_Schema_FilterQuery* är namnet på kolumnen i den externa kontroll tabell som lagrar filter frågan för att hämta data från varje partition i käll databasen. Om du till exempel har partitionerat data per år kan frågan som lagras på varje rad likna "Select * from DataSource WHERE LastModifytime > =" "2015-01-01 00:00:00" "och LastModifytime < =" "2015-12-31 23:59:59.999" ".
- *Data_Destination_Folder_Path* är sökvägen till den plats där data kopieras till mål lagret. Den här parametern visas bara om det mål du väljer är filbaserad lagring. Om du väljer SQL Data Warehouse som mål Arkiv, krävs inte den här parametern. Men tabell namnen och schemat i SQL Data Warehouse måste vara samma som i käll databasen.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Skapa en kontroll tabell i SQL Server eller Azure SQL Database för att lagra käll databasens partition lista för Mass kopiering. I följande exempel finns det fem partitioner i käll databasen. Tre partitioner är för *datasource_table*och två är för *project_table*. Kolumnen *LastModifytime* används för att partitionera data i Table *datasource_table* från käll databasen. Frågan som används för att läsa den första partitionen är Select * from datasource_table där LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' och LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' '. Du kan använda en liknande fråga för att läsa data från andra partitioner.

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

2. Gå till **samlings kopian från databas** mal len. Skapa en **ny** anslutning till den externa kontroll tabellen som du skapade i steg 1.

    ![Skapa en ny anslutning till kontroll tabellen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Skapa en **ny** anslutning till käll databasen som du kopierar data från.

     ![Skapa en ny anslutning till käll databasen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Skapa en **ny** anslutning till mål data lagret som du kopierar data till.

    ![Skapa en ny anslutning till mål arkivet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Du ser pipelinen, som du ser i följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Välj **Felsök**, ange **parametrarna**och välj sedan **Slutför**.

    ![Klicka på * * Felsök * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Du ser resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Valfritt Om du valde SQL Data Warehouse som data mål måste du ange en anslutning till Azure Blob Storage för mellanlagring, vilket krävs av SQL Data Warehouse PolyBase. Kontrol lera att behållaren i Blob Storage redan har skapats.
    
    ![PolyBase-inställning](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
