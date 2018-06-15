---
title: Använda MapReduce och PowerShell med Hadoop - Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder PowerShell fjärrköra MapReduce-jobb med Hadoop i HDInsight.
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
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 7416d064f89515feb04523ca6d4ea73f37c14e38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33938546"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Kör jobb för MapReduce med Hadoop i HDInsight med hjälp av PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Det här dokumentet innehåller ett exempel på hur Azure PowerShell för att köra ett MapReduce-jobb i en Hadoop på HDInsight-kluster.

## <a id="prereq"></a>Förhandskrav

* **Ett kluster i Azure HDInsight (Hadoop på HDInsight)**

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

## <a id="powershell"></a>Kör ett MapReduce-jobb

Azure PowerShell innehåller *cmdlets* som gör det möjligt att fjärrköra MapReduce-jobb i HDInsight. Internt, PowerShell anropar REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (kallades Templeton) körs på HDInsight-klustret.

Följande cmdlets används när du kör MapReduce-jobb i en fjärransluten HDInsight-kluster.

* **Ansluta AzureRmAccount**: autentiserar till Azure-prenumeration i Azure PowerShell.

* **Nya AzureRmHDInsightMapReduceJobDefinition**: skapar en ny *jobbet definition* med hjälp av angivna MapReduce-informationen.

* **Start-AzureRmHDInsightJob**: skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.

* **Vänta AzureRmHDInsightJob**: använder jobbobjektet för att kontrollera status för jobbet. Den väntar tills jobbet slutförs eller väntetiden har överskridits.

* **Get-AzureRmHDInsightJobOutput**: används för att hämta utdata för jobbet.

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i HDInsight-kluster.

1. Med hjälp av en redigerare, spara följande kod som **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Öppna ett nytt **Azure PowerShell** kommandotolk. Ändra kataloger till platsen för den **mapreducejob.ps1** filen och sedan använder du följande kommando för att köra skriptet:

        .\mapreducejob.ps1

    När du kör skriptet tillfrågas om namnet på HDInsight-kluster och klustret inloggningen. Du kan också uppmanas att autentisera till din Azure-prenumeration.

3. När jobbet har slutförts visas utdata som liknar följande:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Den här utdatan anger att jobbet har slutförts.

    > [!NOTE]
    > Om den **ExitCode** är ett värde än 0, se [felsökning](#troubleshooting).

    Det här exemplet lagras också hämtade filer till en **output.txt** fil i katalogen som du kör skriptet från.

### <a name="view-output"></a>Visa utdata

Om du vill se ord och antal produceras av jobbet, öppna den **output.txt** i en textredigerare.

> [!NOTE]
> Utdatafilerna till ett MapReduce-jobb är oföränderliga. Så om du kör det här exemplet måste ändra namnet på utdatafilen.

## <a id="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet är slutfört, visa fel för jobbet. Om du vill visa information om fel för det här jobbet att lägga till följande kommando i slutet av den **mapreducejob.ps1** filen, spara den och kör det igen.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar informationen som skrivs till STDERR jobbet körs.

## <a id="summary"></a>Sammanfattning

Du kan se ger Azure PowerShell ett enkelt sätt att köra MapReduce-jobb på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på Hadoop och HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
