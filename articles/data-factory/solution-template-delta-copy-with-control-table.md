---
title: Deltakopiering från en databas med hjälp av en kontroll-tabell med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall du inkrementellt kopierar nya eller uppdaterade rader från en databas med Azure Data Factory.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312515"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Deltakopiering från en databas med en kontroll-tabell

Den här artikeln beskrivs en mall som är tillgängliga att stegvis läsa in nya eller uppdaterade rader från en databastabell till Azure med hjälp av en extern kontroll-tabell som lagrar en hög vattenstämpelvärdet.

Den här mallen kräver att schemat för källdatabasen innehåller en tidsstämpel kolumn eller öka för att identifiera nya eller uppdaterade rader.

>[!NOTE]
> Om du har en kolumn för tidsstämpel i din källdatabas för att identifiera nya eller uppdaterade rader men du inte vill skapa en extern tabell ska användas för deltakopiering kan du i stället använda den [verktyget kopieringsdata för Azure Data Factory](copy-data-tool.md) att hämta en pipeline. Verktyget använder en utlösare schemalagd tid som en variabel för att läsa nya rader från källdatabasen.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen kan du först hämtar det gamla värdet och jämför den med det aktuella värdet. Därefter kopieras bara ändringarna från källdatabasen, baserat på en jämförelse mellan de två vattenmärkesvärdena. Slutligen lagras det nya vattenstämpelvärdet inom hög till en extern tabell för deltadata som läser in nästa gång.

Mallen innehåller fyra aktiviteter:
- **Lookup** hämtar det gamla hög-värdet, som lagras i en extern tabell.
- En annan **Lookup** aktiviteten hämtar det aktuella hög-värdet från källdatabasen.
- **Kopiera** kopierar endast ändringar från källdatabasen till målarkiv. Den fråga som identifierar ändringar i källdatabasen liknar ”Välj * från Data_Source_Table där på TIMESTAMP_Column >” senaste hög-vattenstämpel ”och TIMESTAMP_Column < =” aktuella hög-vattenstämpel ”'.
- **SqlServerStoredProcedure** skriver det aktuella hög-värdet till en extern tabell för deltakopiering nästa gång.

Mallen definierar fem parametrar:
- *Data_Source_Table_Name* är tabellen i källdatabasen som du vill läsa in data från.
- *Data_Source_WaterMarkColumn* är namnet på kolumnen i källtabellen som används för att identifiera nya eller uppdaterade rader. Den här kolumnen är vanligtvis *datetime*, *INT*, eller liknande.
- *Data_Destination_Folder_Path* eller *Data_Destination_Table_Name* är den plats där data har kopierats till i din målarkiv.
- *Control_Table_Table_Name* är extern kontroll tabellen som lagrar du storleksgränsen för hög.
- *Control_Table_Column_Name* kolumnen i den externa kontroll-tabell som lagrar storleksgränsen för hög.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Utforska källan tabellen du vill läsa in och definiera hög-vattenstämpelkolumnen som kan användas för att identifiera nya eller uppdaterade rader. Vilken typ av den här kolumnen kan vara *datetime*, *INT*, eller liknande. Den här kolumnen värde ökar som nya rader har lagts till. Från följande exempel källtabellen (data_source_table), kan vi använda den *LastModifytime* kolumnen som hög-vattenstämpelkolumn.

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
    
2. Skapa en kontroll i SQL Server eller Azure SQL Database för att lagra värdet för hög vattenstämpel för läser in delta-data. I följande exempel visas namnet på tabellen kontroll är *watermarktable*. I den här tabellen *WatermarkValue* är den kolumn som innehåller storleksgränsen för hög och är av typen *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Skapa en lagrad procedur i samma SQL Server eller Azure SQL Database-instans som du använde för att skapa tabellen kontroll. Den lagrade proceduren används för att skriva det nya hög-värdet till tabellen externa kontroll för deltadata som läser in nästa gång.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Gå till den **deltakopiering från databasen** mall. Skapa en **New** anslutning till källdatabasen som du vill kopiera data från.

    ![Skapa en ny anslutning i källtabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Skapa en **New** anslutning till måldatalagret som du vill kopiera data.

    ![Skapa en ny anslutning till tabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Skapa en **New** anslutning till den externa kontroll tabell och lagrade procedur som du skapade i steg 2 och 3.

    ![Skapa en ny anslutning till arkivet för kontroll av tabell](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Välj **Använd den här mallen**.

     ![Använd den här mallen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Du kan se tillgängliga pipelinen som du ser i följande exempel:

     ![Granska pipelinen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Välj **lagrade proceduren**. För **namn på lagrad procedur**, Välj **[update_watermark]**. Välj **importera parameter**, och välj sedan **Lägg till dynamiskt innehåll**.  

     ![Ange aktivitet för lagrad procedur](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Skriv innehållet  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, och välj sedan **Slutför**.  

     ![Skriva innehållet i parametrarna för den lagrade proceduren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Välj **felsöka**, ange den **parametrar**, och välj sedan **Slutför**.

    ![Välj ** Debug **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultatet liknar följande exempel visas:

    ![Granska resultatet](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Du kan skapa nya rader i din källtabellen. Här är exempel SQL-språket för att skapa nya rader:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Om du vill köra pipelinen igen, Välj **felsöka**, ange den **parametrar**, och välj sedan **Slutför**.

    ![Välj ** Debug **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Du kan se att endast nya rader har kopierats till målet.

15. (Optional:) Om du har valt SQL Data Warehouse som datamålet, måste du också ange en anslutning till Azure Blob storage för mellanlagring, vilket krävs av SQL Data Warehouse Polybase. Kontrollera att behållaren har redan skapats i Blob storage.
    
    ![Konfigurera Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Masskopiering från en databas med hjälp av en kontroll-tabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
