---
title: "Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics | Microsoft Docs"
description: "Lär dig att analysera webbplatsloggar med hjälp av Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 52d19297ae5c34f9daf5e42250a53a78e0168192
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics
Lär dig att analysera webbplatsloggar med hjälp av Data Lake Analytics, särskilt för att ta reda på vilka referenter råkade ut för fel när de försöker att gå till webbplatsen.

## <a name="prerequisites"></a>Krav
* **Visual Studio 2015 eller Visual Studio 2013**.
* **[Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs)**.

    När Data Lake-verktyg för Visual Studio är installerat visas en **Datasjö** objektet i den **verktyg** -menyn i Visual Studio:

    ![U-SQL Visual Studio-menyn](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Grundläggande kunskaper om Data Lake Analytics och Data Lake-verktyg för Visual Studio**. Om du vill komma igång, se:

  * [Utveckla U-SQL-skript med Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Ett Data Lake Analytics-konto.**  Se [skapa ett Azure Data Lake Analytics-konto](data-lake-analytics-get-started-portal.md).
* **Installera exempeldata.** I Azure-portalen öppnar du Data Lake Analytics-kontot och klickar på **exempelskript** klicka sedan på den vänstra menyn **kopiera exempeldata**. 

## <a name="connect-to-azure"></a>Anslut till Azure
Innan du kan skapa och testa alla U-SQL-skript, måste du först ansluta till Azure.

**Så här ansluter du till Data Lake Analytics**

1. Öppna Visual Studio.
2. Klicka på **Data Lake > Alternativ och inställningar för**.
3. Klicka på **logga In**, eller **ändra användare** om någon har loggat in och följ instruktionerna.
4. Klicka på **OK** att stänga dialogrutan Alternativ och inställningar.

**Att bläddra i Data Lake Analytics-konton**

1. Visual Studio, öppna **Server Explorer** genom att klicka på **CTRL + ALT + S**.
2. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **Data Lake Analytics**. En lista över dina Data Lake Analytics-konton visas om det finns några. Du kan inte skapa Data Lake Analytics-konton från studio. För att skapa ett konto, se [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md) eller [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Utveckla U-SQL-program
Ett U-SQL-program är oftast ett U-SQL-skript. Mer information om U-SQL finns [Kom igång med U-SQL](data-lake-analytics-u-sql-get-started.md).

Du kan lägga till ytterligare användardefinierade operatorer till programmet.  Mer information finns i [utveckla U-SQL-operatörer för Data Lake Analytics-jobb för användardefinierade](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Skapa och skicka ett Data Lake Analytics-jobb**

1. Klickar du på den **Arkiv > Nytt > projekt**.
2. Välj typ av U-SQL-projekt.

    ![nytt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Klicka på **OK**. Visual studio skapar en lösning med en Script.usql-fil.
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

    Information om U-SQL finns [Kom igång med Data Lake Analytics U-SQL-språket](data-lake-analytics-u-sql-get-started.md).    
5. Lägg till ett nytt U-SQL-skript i projektet och ange följande:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Växla tillbaka till det första U-SQL-skriptet och bredvid den **skicka** knappen, ange ditt Analytics-konto.
7. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skapa skript**. Kontrollera resultatet i utdatarutan.
8. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skicka skript**.
9. Kontrollera den **Analytics-konto** är där du vill köra jobbet och klicka sedan på **skicka**. Resultat för skicka och jobblänk är tillgängliga i resultatfönstret för Data Lake-verktyg för Visual Studio när överföringen är klar.
10. Vänta tills jobbet har slutförts.  Om jobbet inte saknar troligen källfilen.  Se avsnittet förutsättningar i den här kursen. Ytterligare felsökningsinformation finns i [övervaka och felsöka Azure Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    När jobbet är slutfört, bör du se följande skärm:

    ![datasjöanalys analysera webbloggar webbplatsloggar](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Upprepa steg 7 – 10 för **Script1.usql**.

**Visa jobbutdata**

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**, högerklicka på standardkontot för lagring av Data Lake och klicka sedan på **Explorer**.
2. Dubbelklicka på **exempel** att öppna mappen och dubbelklickar på **utdata**.
3. Dubbelklicka på **UnsuccessfulResponsees.log**.
4. Du kan också dubbelklicka på filen i diagramvyn för jobbet för att kunna gå direkt till utdata.

## <a name="see-also"></a>Se även
Om du vill komma igång med Data Lake Analytics med hjälp av olika verktyg, se:

* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av NET SDK](data-lake-analytics-get-started-net-sdk.md)
