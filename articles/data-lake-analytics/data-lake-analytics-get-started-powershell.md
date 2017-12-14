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
ms.openlocfilehash: 832a859e70e382eb2eeb41560d1b880f7b87de53
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure PowerShell för att skapa Azure Data Lake Analytics-konton och sedan skicka och köra U-SQL-jobb. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudiekursen behöver du följande information:

* **Ett Azure Data Lake Analytics-konto**. Se [Kom igång med Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **En arbetsstation med Azure PowerShell**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Logga in på Azure

Den här kursen förutsätter att du redan är bekant med Azure PowerShell. Framför allt behöver du veta hur du loggar in på Azure. Läs [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) om du behöver hjälp.

Logga in med ett prenumerationsnamn:

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Du kan också använda ett prenumerations-id för att logga in i stället för prenumerationsnamnet:

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Om detta lyckas ser kommandots utdata ut som följande text:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Förbereda för självstudier

PowerShell-kodfragmenten i den här självstudien använder dessa variabler för att lagra informationen:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Hämta information om ett Data Lake Analytics-konto

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Skicka ett U-SQL-jobb

Skapa en PowerShell-variabel för att lagra U-SQL-skript.

```
$script = @"
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

"@
```

Skicka jobbet.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" –Script $script
```

Du kan också spara skriptet som en fil och skicka med följande kommando:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" –ScriptPath $filename
```


Hämta status för ett specifikt jobb. Fortsätta att använda denna cmdlet tills jobbet är klart.

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Istället för att anropa Get-AdlAnalyticsJob om och om igen tills ett jobb blir klart kan du använda cmdleten Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Hämta utdatafilen.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Se även
* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
