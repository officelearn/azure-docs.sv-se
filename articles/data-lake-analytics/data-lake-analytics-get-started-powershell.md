---
title: "Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell | Microsoft Docs"
description: "Skapa ett Data Lake Analytics-konto med hjälp av Azure PowerShell, hur du skapar ett Data Lake Analytics-jobb med hjälp av U-SQL och hur du skickar jobbet. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: sv-se
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure PowerShell för att skapa Azure Data Lake Analytics-konton och sedan skicka och köra U-SQL-jobb. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen behöver du följande information:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **En arbetsstation med Azure PowerShell**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Förbereda för självstudier
Om du vill skapa ett Data Lake Analytics-konto måste du först definiera:

* **Azure-resursgrupp**: Ett Data Lake Analytics-konto måste skapas i en Azure-resursgrupp.
* **Data Lake Analytics-kontonamn**: Data Lake Analytics-kontonamnet får bara innehålla gemener och siffror.
* **Plats**: Ett av de Azure-datacenter som stöder Data Lake Analytics.
* **Data Lake Store-standardkonto**: varje Data Lake Analytics-konto har ett Data Lake Store-standardkonto. Dessa konton måste vara på samma plats.

PowerShell-kodfragmenten i den här självstudien använder dessa variabler för att lagra informationen

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Om du inte redan har en resursgrupp skapar du en. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Alla Data Lake Analytics-konton kräver ett Data Lake Store-standardkonto som används för att lagra loggar. Du kan skapa ett nytt konto eller använda ett befintligt konto. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

När en resursgrupp och ett Data Lake Store-konto är tillgängligt skapar du ett Data Lake Analytics-konto.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Hämta information om ett Data Lake Analytics-konto

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Skicka ett U-SQL-jobb

Skapa en textfil med följande U-SQL-skript.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Skicka jobbet.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Övervaka U-SQL-jobb

Räkna upp alla jobb för kontot. Resultatet innehåller de jobb som körs för närvarande och de jobb som nyss blev klara.

```
Get-AdlJob -Account $adla
```

Hämta status för ett specifikt jobb.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Istället för att anropa Get-AdlAnalyticsJob om och om igen tills ett jobb blir klart kan du använda cmdleten Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

När jobbet är klart kontrollerar du om utdatafilen finns genom att lista filerna i en mapp.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Kontrollera om finns en fil.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Ladda upp och ned filer

Ladda ned utdata från U-SQL-skriptet.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Ladda upp en fil som ska användas som en utdata till ett U-SQL-skript.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Se även
* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).

