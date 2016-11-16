---
title: "Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal | Microsoft Docs"
description: "Lär dig att skapa ett Data Lake Analytics-konto med hjälp av Azure Portal samt skapa ett Data Lake Analytics-jobb med hjälp av U-SQL och skicka jobbet. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/06/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 3810aa816b1734e56443754e34b2d7024ba571db


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure Portal för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett jobb som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg. När ditt första jobb lyckas, kan du börja skriva mer komplexa datatransformationer med U-SQL.

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb.

Varje Data Lake Analytics-konto har ett kontoberoende för [Azure Data Lake Store]().  Det här kontot kallas Data Lake Store-standardkonto.  Du kan skapa Data Lake Store-kontot i förväg eller när du skapar ditt Data Lake Analytics-konto. I de här självstudierna skapar du Data Lake Store-kontot med Data Lake Analytics-kontot.

**Skapa ett Data Lake Analytics-konto**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Nytt**, klicka på **Information + analys** och klicka sedan på **Data Lake Analytics**.
3. Ange eller välj följande värden:

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

   * **Namn:**: Namnge Data Lake Analytics-kontot.
   * **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
   * **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny. Med Azure Resource Manager kan du arbeta med resurserna i ditt program som en grupp. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   * **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot.
   * **Data Lake Store**: Varje Data Lake Analytics-konto har ett beroende Data Lake Store-konto. Data Lake Analytics-kontot och det beroende Data Lake Store-kontot måste finnas i samma Azure-datacenter. Följ anvisningarna för att skapa ett nytt Data Lake Store-konto eller välj ett befintligt.
4. Klicka på **Skapa**. Det tar dig till hemskärmen i portalen. En ny panel har lagts till på Startsidan med etiketten "Distribuera Azure Data Lake Analytics". Det tar en stund att skapa ett Data Lake Analytics-konto. När kontot skapas öppnar portalen kontot på ett nytt blad.

När Data Lake Analytics-kontot har skapats kan du lägga till ytterligare Data Lake Store-konton och Azure Storage-konton. Instruktioner finns i [Hantera datakällor för Data lake Analytics-kontot](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

## <a name="prepare-source-data"></a>Förbereda källdata
I den här självstudien bearbetar du vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring.

Azure Portal innehåller ett användargränssnitt för att kopiera vissa exempeldatafiler till Data Lake Store-standardkontot, bland annat en sökloggfil.

**Kopiera exempeldatafiler**

1. Öppna [Azure-portalen](https://portal.azure.com) och öppna ditt Data Lake Analytics-konto.  Se [Hantera Data Lake Analytics-konton](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account) för att skapa ett och öppna kontot i portalen.
2. Expandera fönstret **Essentials** och klicka sedan på **Utforska exempelskript**. Ett annat blad som kallas **Exempelskript** öppnas.

    ![Azure Data Lake Analytics-portal, exempelskript](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-sample-scripts.png)
3. Klicka på **Exempeldata saknas** för att kopiera exempeldatafiler. När det är klart visar portalen **Exempeldata har uppdaterats**.
4. Klicka på **Data Explorer** högst upp på Data Lake Analytics-kontobladet.

    ![Data Explorer-knappen för Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Två blad öppnas. Ett är **Data Explorer** och det andra är Data Lake Store-standardkontot.
5. I bladet Data Lake Store-standardkonto klickar du på **Exempel** för att expandera mappen och klickar sedan på **Data** för att expandera mappen. Du bör se följande filer och mappar:

   * AmbulanceData/
   * AdsLog.tsv
   * SearchLog.tsv
   * version.txt
   * WebLog.log

     I de här självstudierna använder du SearchLog.tsv.

I praktiken kommer du antingen programmera dina program att skriva data till ett länkat lagringskonto eller ladda upp data. Information om att ladda upp filer finns i [Ladda upp data till Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) eller [Ladda upp data till Blob-lagring](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Skapa och skicka Data Lake Analytics-jobb
När du har förberett källdata kan du börja utveckla ett U-SQL-skript.  

**Gör så här för att skicka ett jobb**

1. Välj Data Lake Analytics-kontobladet på portalen och klicka på **Nytt jobb**.

    ![Knappen Nytt jobb i Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Om du inte ser bladet hittar du mer information i [Öppna ett Data Lake Analytics-konto från portalen](data-lake-analytics-manage-use-portal.md#access-adla-account).
2. Ange **Jobbnamn** och följande U-SQL-skript:

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

    ![Skapa Azure Data Lake Analytics U-SQL-jobb](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

    U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**.

    Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.  I detta fall använder vi enkla, relativa sökvägar.  

    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Mer information om U-SQL finns i [Kom igång med U-SQL-språket i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

1. Klicka på **Skicka jobbet** längst upp.   
2. Vänta tills jobbstatusen har ändrats till **Lyckades**. Du kan se att jobbet tog ungefär en minut att slutföra.

    Om jobbet misslyckades, se [Övervaka och felsöka Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
3. Längst ned på bladet klickar du på fliken **Utdata** i **SearchLog-from-Data-Lake.csv**. Du kan förhandsgranska, hämta, byta namn på och ta bort utdatafilen.

    ![Egenskaper för Azure Data Lake Analytics-jobbets utdatafil](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)

## <a name="see-also"></a>Se även
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
* Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).
* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)



<!--HONumber=Nov16_HO2-->


