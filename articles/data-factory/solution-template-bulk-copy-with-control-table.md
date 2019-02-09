---
title: Masskopiera från databasen med kontroll tabell med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en mall för att fullständigt masskopiera data från en databas som använder en extern tabell för att lagra Partitionslista för källtabellerna med Azure Data Factory.
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
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967561"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Masskopiering från databasen med kontroll tabell

Du måste läsa in stor mängd data från flera tabeller i datakällor när du vill kopiera data från ditt informationslager som Oracle-server, Netezza server, Teradata-servern eller SQL Server till Azure. Data måste partitioneras ytterligare i varje tabell så att du kan läsa in rader med flera trådar parallellt från enskild tabell i de flesta fall. Den finns mallen har utformats för detta. 

Om du vill kopiera data från ett litet antal tabeller med små Datastorleken är det mer effektivt att gå till ”data-verktyget kopiera” att ha en enda kopiera aktivitet eller foreach-aktiviteten + kopiera aktivitet i din pipeline. Den här mallen är högre än vad du behöver för det här enkla användningsfall.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar listan över partitioner för källdatabasen från en extern kontroll-tabell som ska kopieras över till målarkivet itererar över varje partition i källdatabasen och utför själva Datakopieringen.

Mallen innehåller tre aktiviteter:
-   En **Lookup** aktivitet för att hämta listan över partitioner för källdatabasen från en extern tabell.
-   En **ForEach** aktivitet för att hämta listan över partition från lookup-aktiviteten och iterera dem till kopieringsaktiviteten.
-   En **kopia** aktivitet att kopiera varje partition från databasarkiv för källa till mål.

Mallen definierar fem parametrar:
-   Parametern *Control_Table_Name* är tabellnamnet för externa kontroll-tabellen. Tabellen kontroll används för att lagra Partitionslista för källdatabasen.
-   Parametern *Control_Table_Schema_PartitionID* är kolumnnamn i din externa kontroll-tabell för att lagra varje Partition-ID. Kontrollera att partitions-ID är unikt för varje partition i källdatabasen.
-   Parametern *Control_Table_Schema_SourceTableName* är kolumnnamn i din externa kontroll-tabell för att lagra varje tabellnamn från källdatabasen.
-   Parametern *Control_Table_Schema_FilterQuery* är kolumnnamn i din externa kontroll-tabell för att lagra filterfråga för att hämta data från varje partition i källdatabasen. Till exempel om du har partitionerade data efter varje år frågan som lagras i varje rad kan vara lika som ”Välj * från datasource där LastModifytime > = '' 2015-01-01 00:00:00” och LastModifytime < = '' 2015-12-31 23:59:59.999'' '
-   Parametern *Data_Destination_Folder_Path* är sökvägen till mappen där data har kopierats till ditt målarkiv.  Den här parametern visas bara när du väljer målet är en filbaserad lagring.  Om du väljer SQL Data Warehouse som målarkiv, det finns ingen parameter som krävs för att vara inputted här. Men tabellnamn och schema i SQL data warehouse måste vara samma som de i källdatabasen.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Skapa en kontroll i en SQL-server eller SQL Azure att lagra Partitionslista för källdatabasen för masskopiering.  Från exemplet nedan ser du det finns fem partitioner i källdatabasen, där tre partitioner som för en tabell:*datasource_table* och två partitioner i en annan tabell:*project_table*. Kolumnen *LastModifytime* används för att partitionera data i tabellen *datasource_table* från källdatabasen. Frågan används för att läsa den första partitionen är ”Välj * från datasource_table där LastModifytime > = '' 2015-01-01 00:00:00” och LastModifytime < = '' 2015-12-31 23:59:59.999'' '.  Du kan också se liknande frågan för att läsa data från andra partitioner. 

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

2. Gå till mallen **masskopiering från databasen**, och skapa en **ny anslutning** till den externa kontroll-tabellen.  Den här anslutningen ansluter till databasen där du skapade tabellen kontroll i steg #1.

    ![Skapa en ny anslutning till tabellen kontroll](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Skapa en **ny anslutning** till källdatabasen där data ska kopieras.

     ![Skapa en ny anslutning till källdatabasen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Skapa en **ny anslutning** till din måldatalager där att kopiera data till.

    ![Skapa en ny anslutning till målarkiv](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Klicka på **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Du kan se pipelinen i panelen som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Klicka på **felsöka**, de indataparametrar och klicka sedan på **Slutför**

    ![Klicka på felsökning](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Du ser resultatet i panelen som visas i följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Valfritt) Om du väljer SQL Data Warehouse som datamålet, måste du också ange anslutning av en Azure blob storage som en mellanlagringsmiljö, vilket krävs av SQL Data Warehouse Polybase.  Kontrollera att behållaren i blob storage har redan skapat.  
    
    ![Polybase-inställning](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)