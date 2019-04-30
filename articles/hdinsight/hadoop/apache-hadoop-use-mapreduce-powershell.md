---
title: Använda MapReduce och PowerShell med Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder PowerShell för att köra MapReduce-jobb via fjärranslutning med Apache Hadoop på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129033"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med hjälp av PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell kör ett MapReduce-jobb i ett Hadoop på HDInsight-kluster.

## <a id="prereq"></a>Förhandskrav

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Ett kluster i Azure HDInsight (Hadoop på HDInsight)**

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

## <a id="powershell"></a>Kör ett MapReduce-jobb

Azure PowerShell tillhandahåller *cmdletar* som gör det möjligt att köra MapReduce-jobb via fjärranslutning på HDInsight. Internt PowerShell gör REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidigare kallad Templeton) körs på HDInsight-klustret.

Följande cmdletar som används när du kör MapReduce-jobb i ett fjärranslutet HDInsight-kluster.

* **Connect-AzAccount**: Autentiserar Azure PowerShell på Azure-prenumerationen.

* **New-AzHDInsightMapReduceJobDefinition**: Skapar en ny *jobbet definition* med hjälp av den angivna MapReduce-informationen.

* **Start-AzHDInsightJob**: Skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.

* **Wait-AzHDInsightJob**: Använder objektet för att kontrollera status för jobbet. Den ska vänta tills jobbet har slutförts eller väntetiden har överskridits.

* **Get-AzHDInsightJobOutput**: Används för att hämta utdata för jobbet.

Följande steg visar hur du använder dessa cmdletar för att köra ett jobb i HDInsight-klustret.

1. Med hjälp av en redigerare, spara följande kod som **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Öppna ett nytt **Azure PowerShell** Kommandotolken. Ändra kataloger till platsen för den **mapreducejob.ps1** filen och sedan använda följande kommando för att köra skriptet:

        .\mapreducejob.ps1

    När du kör skriptet uppmanas du namnet på HDInsight-kluster och klusterinloggning. Du kan också uppmanas att autentisera till din Azure-prenumeration.

3. När jobbet har slutförts visas utdata som liknar följande text:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Den här utdata visar att jobbet har slutförts.

    > [!NOTE]
    > Om den **ExitCode** är ett värde annat än 0, se [felsökning](#troubleshooting).

    Det här exemplet lagras också de hämta filerna till en **output.txt** fil i den katalog som du kör skriptet från.

### <a name="view-output"></a>Visa utdata

Om du vill se ord och antalet som produceras av jobbet, öppna den **output.txt** filen i en textredigerare.

> [!NOTE]
> Utdatafilerna i ett MapReduce-jobb är inte kan ändras. Så om du kör det här exemplet måste du ändra namnet på utdatafilen.

## <a id="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet har slutförts, kan du visa fel för jobbet. Om du vill visa information om fel för det här jobbet att lägga till följande kommando i slutet av den **mapreducejob.ps1** filen, spara den och sedan köra det igen.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar informationen som har skrivits till STDERR när jobbet körs.

## <a id="summary"></a>Sammanfattning

Som du kan se är det enkelt att köra MapReduce-jobb på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata i Azure PowerShell.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
