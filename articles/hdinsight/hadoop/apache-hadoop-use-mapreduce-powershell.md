---
title: Använda MapReduce och PowerShell med Hadoop - Azure HDInsight
description: Lär dig hur du använder PowerShell för att köra MapReduce-jobb via fjärranslutning med Hadoop på HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.openlocfilehash: cab6fc652fa11db7dd1e9e9ae7f0a1a634dca3b0
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591828"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Köra MapReduce-jobb med Hadoop på HDInsight med hjälp av PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell kör ett MapReduce-jobb i ett Hadoop på HDInsight-kluster.

## <a id="prereq"></a>Förhandskrav

* **Ett kluster i Azure HDInsight (Hadoop på HDInsight)**

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

## <a id="powershell"></a>Kör ett MapReduce-jobb

Azure PowerShell tillhandahåller *cmdletar* som gör det möjligt att köra MapReduce-jobb via fjärranslutning på HDInsight. Internt PowerShell gör REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidigare kallad Templeton) körs på HDInsight-klustret.

Följande cmdletar som används när du kör MapReduce-jobb i ett fjärranslutet HDInsight-kluster.

* **Connect-AzureRmAccount**: autentiserar Azure PowerShell på Azure-prenumerationen.

* **Ny AzureRmHDInsightMapReduceJobDefinition**: skapar en ny *jobbet definition* med hjälp av den angivna MapReduce-informationen.

* **Start-AzureRmHDInsightJob**: skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.

* **Vänta AzureRmHDInsightJob**: använder objektet för att kontrollera status för jobbet. Den ska vänta tills jobbet har slutförts eller väntetiden har överskridits.

* **Get-AzureRmHDInsightJobOutput**: används för att hämta utdata för jobbet.

Följande steg visar hur du använder dessa cmdletar för att köra ett jobb i HDInsight-klustret.

1. Med hjälp av en redigerare, spara följande kod som **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

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
Get-AzureRmHDInsightJobOutput `
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

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
