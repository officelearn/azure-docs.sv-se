---
title: Deltakopia från en databas med hjälp av en kontrolltabell
description: Lär dig hur du använder en lösningsmall för att stegvis kopiera nya eller uppdaterade rader endast från en databas med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414792"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Deltakopia från en databas med en kontrolltabell
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs en mall som är tillgänglig för att stegvis läsa in nya eller uppdaterade rader från en databastabell till Azure med hjälp av en extern kontrolltabell som lagrar ett värde med hög vattenstämpel.

Den här mallen kräver att schemat för källdatabasen innehåller en tidsstämpelkolumn eller en stegvis nyckel för att identifiera nya eller uppdaterade rader.

>[!NOTE]
> Om du har en tidsstämpelkolumn i källdatabasen för att identifiera nya eller uppdaterade rader men du inte vill skapa en extern kontrolltabell som ska användas för deltakopia, kan du i stället använda [azure data fabrikskopieringsdata](copy-data-tool.md) för att hämta en pipeline. Det verktyget använder en utlöst schemalagd tid som en variabel för att läsa nya rader från källdatabasen.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar först det gamla vattenstämpelvärdet och jämför det med det aktuella vattenstämpelvärdet. Därefter kopieras bara ändringarna från källdatabasen, baserat på en jämförelse mellan de två vattenstämpelvärdena. Slutligen lagras det nya högvattenstämpelvärdet i en extern kontrolltabell för deltadata som läses in nästa gång.

Mallen innehåller fyra aktiviteter:
- **Sökningen** hämtar det gamla högvattenstämpelvärdet, som lagras i en extern kontrolltabell.
- En annan **uppslagsaktivitet** hämtar det aktuella högvattenstämpelvärdet från källdatabasen.
- **Kopiera** bara ändringar från källdatabasen till målarkivet. Frågan som identifierar ändringarna i källdatabasen liknar 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" och TIMESTAMP_Column <= "aktuell högvattenstämpel".The query that identifies the changes in the source database is similar to 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark"".The query that identifies the changes in the source database is similar to 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark"'.
- **SqlServerStoredProcedure** skriver det aktuella högvattenstämpelvärdet till en extern kontrolltabell för deltakopia nästa gång.

Mallen definierar följande parametrar:
- *Data_Source_Table_Name* är tabellen i källdatabasen som du vill läsa in data från.
- *Data_Source_WaterMarkColumn* är namnet på kolumnen i källtabellen som används för att identifiera nya eller uppdaterade rader. Typen av den här kolumnen är vanligtvis *datetime*, *INT*eller liknande.
- *Data_Destination_Container* är rotsökvägen för den plats där data kopieras till i målarkivet.
- *Data_Destination_Directory* är katalogsökvägen under roten på den plats där data kopieras till i målarkivet.
- *Data_Destination_Table_Name* är den plats där data kopieras till i din målbutik (gäller när "Azure Synapse Analytics (tidigare SQL DW)" väljs som datamål).
- *Data_Destination_Folder_Path* är den plats där data kopieras till i målarkivet (gäller när "Filsystem" eller "Azure Data Lake Storage Gen1" väljs som datamål).
- *Control_Table_Table_Name* är den externa kontrolltabell som lagrar värdet med hög vattenstämpel.
- *Control_Table_Column_Name* är kolumnen i den externa kontrolltabellen som lagrar värdet med hög vattenstämpel.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Utforska källtabellen som du vill läsa in och definiera kolumnen hög vattenstämpel som kan användas för att identifiera nya eller uppdaterade rader. Typen av den här kolumnen kan vara *datetime,* *INT*eller liknande. Den här kolumnens värde ökar när nya rader läggs till. Från följande exempelkälltabell (data_source_table) kan vi använda kolumnen *LastModifytime* som högvattenstämpelkolumn.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Skapa en kontrolltabell i SQL Server eller Azure SQL Database för att lagra värdet med hög vattenstämpel för deltadatainläsning. I följande exempel är namnet på kontrolltabellen *vattenstämpelbar*. I den här tabellen är *WatermarkValue* den kolumn som lagrar värdet med hög vattenstämpel och dess typ är *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Skapa en lagrad procedur i samma SQL Server- eller Azure SQL Database-instans som du använde för att skapa kontrolltabellen. Den lagrade proceduren används för att skriva det nya högvattenstämpelvärdet till den externa kontrolltabellen för deltadata som läses in nästa gång.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Gå till **deltakopian från** databasmallen. Skapa en **ny** anslutning till den källdatabas som du vill datakopiera från.

    ![Skapa en ny anslutning till källtabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Skapa en **ny** anslutning till det måldatalager som du vill kopiera data till.

    ![Skapa en ny anslutning till måltabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Skapa en **ny** anslutning till den externa kontrolltabellen och den lagrade proceduren som du skapade i steg 2 och 3.

    ![Skapa en ny anslutning till kontrolltabelldatalagret](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Välj **Använd den här mallen**.
    
8. Du ser den tillgängliga pipelinen, som visas i följande exempel:
  
    ![Granska pipelinen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Välj **lagrad procedur**. För **Namnet lagrad procedur**väljer du **[dbo].[ update_watermark]**. Välj **importparameter**och välj sedan **Lägg till dynamiskt innehåll**.  

    ![Ange den lagrade proceduraktiviteten](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Skriv innehållet ** \@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** och välj sedan **Slutför**.  

    ![Skriv innehållet för parametrarna för den lagrade proceduren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Välj **Felsökning**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultat som liknar följande exempel visas:

    ![Granska resultatet](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Du kan skapa nya rader i källtabellen. Här är exempel på SQL-språk för att skapa nya rader:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Om du vill köra pipelinen igen väljer du **Felsöka,** anger **parametrarna**och väljer sedan **Slutför**.

    Du kommer att se att endast nya rader har kopierats till målet.

15. (Valfritt:) Om du väljer Azure Synapse Analytics (tidigare SQL DW) som datamål, måste du också tillhandahålla en anslutning till Azure Blob-lagring för mellanlagring, vilket krävs av SQL Data Warehouse Polybase. Mallen genererar en behållarsökväg åt dig. Kontrollera om behållaren har skapats i Blob-lagring efter pipelinekörningen.
    
    ![Konfigurera Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Masskopiering från en databas med hjälp av en kontrolltabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
