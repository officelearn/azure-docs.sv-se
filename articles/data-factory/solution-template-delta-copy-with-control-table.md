---
title: Deltakopiering från databasen med kontroll tabell med Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967495"
---
# <a name="delta-copy-from-database-with-control-table"></a>Deltakopiering från databasen med kontroll tabell

När du vill läsa in stegvis ändringar (nya eller uppdaterade rader) från en tabell i en databas till Azure med en extern kontroll-tabell som lagrar du storleksgränsen för hög.  Den finns mallen har utformats för detta. 

Den här mallen kräver att schemat för källdatabasen måste som innehåller tidsstämpel-kolumnen eller öka för att identifiera nya eller uppdaterade rader.

Om du har kolumn för tidsstämpel i din källdatabas för att identifiera nya eller uppdaterade rader, men inte vill skapa en extern tabell om du vill aktivera deltakopiering, kan du använda verktyget kopieringsdata för att få en pipeline som använder en utlösare schemalagd tid som en variabel för att läsa de nya raderna från källdatabasen.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar alltid det gamla värdet först och jämför den med det aktuella värdet. Efter det kan bara kopieras ändringarna från källdatabasen baserat på en jämförelse mellan 2 värden för högvattenmärket.  När du är klar lagras det nya vattenstämpelvärdet inom hög till en extern tabell för deltadata som läser in nästa gång.

Mallen innehåller fyra aktiviteter:
-   En **Lookup** aktivitet för att hämta det gamla hög-värdet lagras i en extern tabell.
-   En **Lookup** aktivitet för att hämta det aktuella hög-värdet från källdatabasen.
-   En **kopia** aktivitet för att kopiera ändringarna endast från källdatabasen till målarkiv. Frågan används för att identifiera ändringar från källdatabasen i kopieringsaktiviteten är lika som ”Välj * från Data_Source_Table där på TIMESTAMP_Column >” senaste hög-vattenstämpel ”och TIMESTAMP_Column < =” aktuella hög-vattenstämpel ”'.
-   En **SqlServerStoredProcedure** aktivitet för att skriva det aktuella hög-värdet till en extern tabell för deltakopiering nästa gång.

Mallen definierar fem parametrar:
-   Parametern *Data_Source_Table_Name* är tabellnamnet från källdatabasen där du vill läsa in data från.
-   Parametern *Data_Source_WaterMarkColumn* är kolumnnamn i källtabellen som kan användas för att identifiera nya eller uppdaterade rader. Vilken typ av den här kolumnen kan normalt sett vara datetime eller INT osv.
-   Parametern *Data_Destination_Folder_Path* eller *Data_Destination_Table_Name* är den plats där data har kopierats till ditt målarkiv.
-   Parametern *Control_Table_Table_Name* är namnet på externa kontroll tabell för att lagra storleksgränsen för hög.
-   Parametern *Control_Table_Column_Name* är kolumnnamn i tabellen externa kontroll för att lagra storleksgränsen för hög.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Utforska källtabellen som du vill läsa in och definiera hög-vattenstämpelkolumnen som kan användas för att dela upp de nya eller uppdaterade raderna. Vilken typ av den här kolumnen kan normalt sett vara datetime eller INT etc. och dess data att hålla ökar när nya rader som läggs till.  Från exemplet källtabellen (tabellnamnet: data_source_table) nedan, kan vi använda kolumnen *LastModifytime* som hög-vattenstämpelkolumn.

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
    
2. Skapa en kontroll i en SQL-server eller SQL Azure att lagra värdet för hög vattenstämpel för läser in delta-data. Från exemplet nedan ser du namnet på kontrollen tabell är *watermarktable*. I denna kolumnnamn för att lagra storleksgränsen för hög är *WatermarkValue* och är av typen *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Skapa en lagrad procedur i samma SQLServer eller SQL Azure används för att skapa tabellen för kontroll. Den lagrade proceduren används för att skriva det nya hög-värdet till tabellen externa kontroll för deltadata som läser in nästa gång.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Gå till mallen **deltakopiering från databasen**, och skapa en **ny anslutning** till källdatabasen där data ska kopieras.

    ![Skapa en ny anslutning i källtabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Skapa en **ny anslutning** till din måldatalager där att kopiera data till.

    ![Skapa en ny anslutning till tabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Skapa en **ny anslutning** till externa kontroll tabell och lagrade proceduren.  Det ansluter till databasen där du hade skapat tabellen kontroll och lagrad procedur i steg #2 och #3.

    ![Skapa en ny anslutning till arkivet för kontroll av tabell](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Klicka på **Använd den här mallen**.

     ![Använd den här mallen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Du kan se pipelinen i panelen som visas i följande exempel:

     ![Granska pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Klicka på lagringsprocedur-aktivitet och välj **namn på lagrad procedur**, klickar du på **importparameter** och klicka på **Lägg till dynamiskt innehåll**.  

     ![Ange lagringsprocedur-aktivitet](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Skriv innehållet **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** och klicka på **Slutför**.  

     ![Skriv innehållet för parametern för lagringsprocedur](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Klicka på **felsöka**, de indataparametrar och klickar på **Slutför**.

    ![Klicka på felsökning](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Du ser resultatet i panelen som visas i följande exempel:

    ![Granska resultatet](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Du kan skapa nya rader i din källtabellen.  Exempel-sql för att skapa nya rader kan vara på följande:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Kör pipelinen igen genom att klicka på **felsöka**, de indataparametrar och klickar på **Slutför**.

    ![Klicka på felsökning](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Du ser bara nya rader har kopierats till målet.

15. (Valfritt) Om du väljer SQL Data Warehouse som datamålet, måste du också ange anslutning av en Azure blob storage som en mellanlagringsmiljö, vilket krävs av SQL Data Warehouse Polybase.  Kontrollera att behållaren i blob storage har redan skapat.  
    
    ![Konfigurera Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)