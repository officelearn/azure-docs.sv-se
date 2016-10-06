<properties 
   pageTitle="Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal | Azure" 
   description="Lär dig att skapa ett Data Lake Analytics-konto med hjälp av Azure Portal samt skapa ett Data Lake Analytics-jobb med hjälp av U-SQL och skicka jobbet. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure Portal för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett jobb som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg. När ditt första jobb lyckas, kan du börja skriva mer komplexa datatransformationer med U-SQL.

##Krav

Innan du påbörjar de här självstudierna måste du ha:

- **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

##Skapa ett Data Lake Analytics-konto

Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb.

Varje Data Lake Analytics-konto har ett kontoberoende för [Azure Data Lake Store]().  Det här kontot kallas Data Lake Store-standardkonto.  Du kan skapa Data Lake Store-kontot i förväg eller när du skapar ditt Data Lake Analytics-konto. I de här självstudierna skapar du Data Lake Store-kontot med Data Lake Analytics-kontot.

**Om du vill skapa ett Data Lake Analytics-konto**

1. Logga in på den nya [klassiska Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt**, klicka på **Data + analys** och klicka sedan på **Data Lake Analytics**.
6. Ange eller välj följande:

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Namn**: Namnge Analytics-kontot.
    - **Data Lake Store**: Varje Data Lake Analytics-konto har ett beroende Data Lake Store-konto. Data Lake Analytics-kontot och det beroende Data Lake Store-kontot måste finnas i samma Azure-datacenter. Följ anvisningarna för att skapa ett nytt Data Lake Store-konto eller välj ett befintligt.
    - **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
    - **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny. Med Azure Resource Manager (ARM) kan du arbeta med resurserna i ditt program som en grupp. Mer information finns i [Översikt över Azure Resource Manager](resource-group-overview.md). 
    - **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot. 
7. Välj **Fäst på Startsidan**. Detta krävs för att följa de här självstudierna.
8. Klicka på **Skapa**. Det tar dig till Startsidan i portalen. En ny panel har lagts till på Startsidan med etiketten "Distribuera Azure Data Lake Analytics". Det tar en stund att skapa ett Data Lake Analytics-konto. När kontot skapas öppnar portalen kontot på ett nytt blad i portalen.

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


När Data Lake Analytics-kontot har skapats kan du lägga till ytterligare Data Lake Store-konton och Azure Storage-konton. Instruktioner finns i [Hantera datakällor för Data lake Analytics-kontot](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##Förbereda källdata

I självstudierna bearbetas vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring. 

Azure Portal innehåller ett användargränssnitt för att kopiera vissa exempeldatafiler till Data Lake-standardkontot, bland annat en sökloggfil.

**Om du vill kopiera exempeldatafiler**

1. Klicka på **Microsoft Azure** i det övre vänstra hörnet i Azure Portal.
2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Den har fästs här när kontot skapades.
Om kontot inte är fäst där, se [Öppna ett Data Lake Analytics-konto från portalen](data-lake-analytics-manage-use-portal.md#access-adla-account) för att öppna kontot.
3. Expandera fönstret **Essentials** och klicka sedan på **Utforska exempeljobb**. Ett annat blad som kallas **Exempeljobb** öppnas.
4. Klicka på **Kopiera exempeldata** och klicka sedan på **OK** för att bekräfta.
5. Klicka på **Meddelande** som är en klockformad ikon. Du kommer att se en logg som säger **Uppdatering av exempeldata slutfört**. Klicka utanför meddelandefönstret för att stänga det.
7. Klicka på **Data Explorer** högst upp på Data Lake Analytics-kontobladet. 

    ![Data Explorer-knappen för Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Två blad öppnas. Ett är **Data Explorer** och det andra är Data Lake Store-standardkontot.
8. I bladet Data Lake Store-standardkonto klickar du på **Exempel** för att expandera mappen och klickar sedan på **Data** för att expandera mappen. Du bör se följande filer och mappar:

    - AmbulanceData/
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    I de här självstudierna använder du SearchLog.tsv.

I praktiken kommer du antingen programmera dina program att skriva data till ett länkat lagringskonto eller ladda upp data. Information om att ladda upp filer finns i [Ladda upp data till Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) eller [Ladda upp data till Blob-lagring](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

##Skapa och skicka Data Lake Analytics-jobb

När du har förberett källdata kan du börja utveckla ett U-SQL-skript.  

**Skicka jobbet**

1. Välj Data Lake Analytics-kontobladet på portalen och klicka på **Nytt jobb**. 

    ![Knappen Nytt jobb i Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Om du inte ser bladet hittar du mer information i [Öppna ett Data Lake Analytics-konto från portalen](data-lake-analytics-manage-use-portal.md#access-adla-account).
4. Ange **Jobbnamn** och följande U-SQL-skript:

    ![Skapa Azure Data Lake Analytics U-SQL-jobb](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**. 
    
    Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.  I detta fall använder vi enkla, relativa sökvägar.  
    
    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Mer information om U-SQL finns i [Kom igång med U-SQL-språket i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).
     
5. Klicka på **Skicka jobbet** längst upp. Ett nytt jobbdetaljfönster öppnas. I namnlisten visas jobbets status.   
6. Vänta tills jobbstatusen har ändrats till **Lyckades**. När jobbet är slutfört, öppnar portalen jobbdetaljerna i ett nytt blad:

    ![Azure Data Lake Analytics-jobbdetaljer](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    Från föregående skärmbild kan du se att jobbet tog ungefär 1,5 minuter att slutföra från skickat till avslutat.
    
    Om jobbet misslyckades, se [Övervaka och felsöka Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md).

7. Längst ned på bladet **Jobbdetaljer** klickar du på jobbnamnet i **SearchLog-from-Data-Lake.csv**. Du kan förhandsgranska, hämta, byta namn på och ta bort utdatafilen.

    ![Egenskaper för Azure Data Lake Analytics-jobbets utdatafil](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. Klicka på **Förhandsgranska** för att se utdatafilen.

    ![Förhandsgranska Azure Data Lake Analytics-jobbets utdatafil](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##Se även

- Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
- Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).
- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)



<!--HONumber=Sep16_HO3-->


