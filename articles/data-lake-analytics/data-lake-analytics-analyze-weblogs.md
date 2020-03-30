---
title: Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics
description: Lär dig hur du analyserar webbplatsloggar med Azure Data Lake Analytics för att köra U-SQL-funktioner och frågor.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672885"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics
Lär dig hur du analyserar webbplatsloggar med Data Lake Analytics, särskilt när du vet vilka referenter som stötte på fel när de försökte besöka webbplatsen.

## <a name="prerequisites"></a>Krav
* **Visual Studio 2015 eller Visual Studio 2013**.
* **[Data Lake-verktyg för Visual Studio](https://aka.ms/adltoolsvs)**.

    När DataSjöverktyg för Visual Studio har installerats visas ett **DataSjöobjekt** på **Verktyg-menyn** i Visual Studio:

    ![U-SQL Visual Studio-menyn](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Grundläggande kunskaper i Data Lake Analytics och Data Lake Tools för Visual Studio**. För att komma igång, se:

  * [Utveckla U-SQL-skript med hjälp av DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Ett Data Lake Analytics-konto.**  Se [Skapa ett Azure Data Lake Analytics-konto](data-lake-analytics-get-started-portal.md).
* **Installera exempeldata.** Öppna ditt DataSjöanalyskonto i Azure Portal och klicka på **Exempelskript** på den vänstra menyn och klicka sedan på **Kopiera exempeldata**. 

## <a name="connect-to-azure"></a>Anslut till Azure
Innan du kan skapa och testa u-SQL-skript måste du först ansluta till Azure.

**Så här ansluter du till Data Lake Analytics**

1. Öppna Visual Studio.
2. Klicka på **Alternativ och inställningar för Datasjö**> .
3. Klicka på **Logga in**eller **Ändra användare** om någon har loggat in och följ instruktionerna.
4. Stäng dialogrutan Alternativ och inställningar genom att klicka på **OK.**

**Så här bläddrar du bland dina DataSjöanalyskonton**

1. Öppna **Server Explorer** från Visual Studio genom att trycka på **CTRL+ALT+S**.
2. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **Data Lake Analytics**. En lista över dina Data Lake Analytics-konton visas om det finns några. Du kan inte skapa Data Lake Analytics-konton från studion. Information om hur du skapar ett konto finns [i Komma igång med Azure Data Lake Analytics med Azure Portal](data-lake-analytics-get-started-portal.md) eller Komma igång med Azure Data Lake Analytics med Azure [PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Utveckla U-SQL-program
Ett U-SQL-program är oftast ett U-SQL-skript. Mer information om U-SQL finns i [Komma igång med U-SQL](data-lake-analytics-u-sql-get-started.md).

Du kan lägga till ytterligare användardefinierade operatorer i programmet.  Mer information finns i [Utveckla U-SQL-användardefinierade operatorer för Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Skapa och skicka ett Data Lake Analytics-jobb**

1. Klicka på **> nytt > Project**.
2. Välj typen U-SQL-projekt.

    ![nytt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Klicka på **OK**. Visual Studio skapar en lösning med en Script.usql-fil.
4. Ange följande skript i filen Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Information om hur du förstår U-SQL finns [i Komma igång med U-SQL-språket DataSjö Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Lägg till ett nytt U-SQL-skript i projektet och ange följande:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Växla tillbaka till det första U-SQL-skriptet och bredvid **skicka-knappen** anger du ditt Analytics-konto.
7. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skapa skript**. Kontrollera resultaten i utdatafönstret.
8. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skicka skript**.
9. Kontrollera **att Analytics-kontot** är det där du vill köra jobbet och klicka sedan på **Skicka**. Resultat för skicka och jobblänk är tillgängliga i resultatfönstret för Data Lake-verktyg för Visual Studio när överföringen är klar.
10. Vänta tills jobbet har slutförts.  Om jobbet misslyckades saknas troligen källfilen.  Se avsnittet Förutsättning i den här självstudien. Ytterligare felsökningsinformation finns i [Övervaka och felsöka Azure Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    När jobbet är klart visas följande skärm:

    ![datasjöanalys analyserar webbloggar webbplatsloggar](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Nu upprepa steg 7- 10 för **Script1.usql**.

**Visa jobbutdata**

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**, högerklicka på standardkontot för lagring av Data Lake och klicka sedan på **Explorer**.
2. Dubbelklicka på **Exempel** för att öppna mappen och dubbelklicka sedan på **Utdata**.
3. Dubbelklicka på **Misslyckat Svar.log**.
4. Du kan också dubbelklicka på utdatafilen i diagrammet på jobbet för att navigera direkt till utdata.

## <a name="see-also"></a>Se även
Om du vill komma igång med Data Lake Analytics med hjälp av olika verktyg, se:

* [Komma igång med DataSjöanalys med Azure Portal](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av NET SDK](data-lake-analytics-get-started-net-sdk.md)
