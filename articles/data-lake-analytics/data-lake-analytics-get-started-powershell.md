---
title: "Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell | Microsoft Docs"
description: "Skapa ett Data Lake Analytics-konto med hjälp av Azure PowerShell, hur du skapar ett Data Lake Analytics-jobb med hjälp av U-SQL och hur du skickar jobbet. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2bc3e4573ff4f202c3c8492e8110dc35c7e8ff2a
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig att skapa Azure Data Lake Analytics-konton med hjälp av Azure PowerShell, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett jobb som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg.

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **En arbetsstation med Azure PowerShell**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb. Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande:

* **Azure-resursgrupp**: Ett Data Lake Analytics-konto måste skapas i en Azure-resursgrupp. Med [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i programmet i en enda, samordnad åtgärd.  

    Om du vill räkna upp resursgrupper i din prenumeration:

        Get-AzureRmResourceGroup

    Om du vill skapa en ny resursgrupp:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Data Lake Analytics-kontonamn**
* **Plats**: Ett av de Azure-datacenter som stöder Data Lake Analytics.
* **Data Lake-standardkonto**: Varje Data Lake Analytics-konto har ett Data Lake-standardkonto.

    Om du vill skapa ett nytt Data Lake-konto:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

  > [!NOTE]
  > Data Lake-kontonamnet får bara innehålla gemener och siffror.
  >
  >

**Skapa ett Data Lake Analytics-konto**

1. Öppna PowerShell ISE från din Windows-arbetsstation.
2. Kör följande skript:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"

        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location

        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location

        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName

        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

## <a name="upload-data-to-data-lake"></a>Ladda upp data till Data Lake
I självstudierna bearbetas vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring.

Ett exempel på en sökloggfil har kopierats till en offentlig Azure Blob-behållare. Använd följande PowerShell-skript för att ladda ner filen till din arbetsstation och sedan ladda upp filen till Data Lake Store-standardkontot för Data Lake Analytics-kontot.

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location    
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $$blobs = Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Följande PowerShell-skript visar hur du hämtar Data Lake Store-standardnamnet för ett Data Lake Analytics-konto:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeStoreAccount
    echo $dataLakeStoreName

> [!NOTE]
> Azure Portal tillhandahåller ett användargränssnitt för att kopiera exempeldatafilerna till Data Lake Store-standardkontot. Instruktioner finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md#prepare-source-data).
>
>

Data Lake Analytics kan också använda Azure Blob-lagring.  Information om att ladda upp data till Azure Blob-lagring finns i [Använda Azure PowerShell med Azure Storage](../storage/storage-powershell-guide-full.md).

## <a name="submit-data-lake-analytics-jobs"></a>Skicka Data Lake Analytics-jobb
Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

**Skapa ett Data Lake Analytics-jobbskript**

* Skapa en textfil med följande U-SQL-skript och spara filen på din arbetsstation:

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

    Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.

    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Åtkomstbehörighet till Azure Blob-behållare med offentliga blobbar eller offentliga behållare stöds inte.    
  >
  >

**Skicka jobbet**

1. Öppna PowerShell ISE från din Windows-arbetsstation.
2. Kör följande skript:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"

        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    U-SQL-skriptfilen lagras i skriptet på c:\tutorials\data-lake-analytics\copyFile.usql. Uppdatera sökvägen till filen på samma sätt.

När jobbet har slutförts kan du använda följande cmdletar för att visa och ladda ned filen:

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Se även
* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
* Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

