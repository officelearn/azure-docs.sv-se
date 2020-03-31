---
title: Använda MapReduce och PowerShell med Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder PowerShell för att fjärrköra MapReduce-jobb med Apache Hadoop på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830079"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Det här dokumentet är ett exempel på hur du använder Azure PowerShell för att köra ett MapReduce-jobb i ett Hadoop-kluster i HDInsight.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

* PowerShell [Az-modulen](https://docs.microsoft.com/powershell/azure/overview) är installerad.

## <a name="run-a-mapreduce-job"></a>Köra ett MapReduce-jobb

Azure PowerShell innehåller *cmdlets* som gör att du kan fjärrköra MapReduce-jobb på HDInsight. Internt gör PowerShell REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidigare kallat Templeton) som körs på HDInsight-klustret.

Följande cmdlets används när MapReduce-jobb körs i ett hdinsight-kluster för fjärrstyrd.

|Cmdlet | Beskrivning |
|---|---|
|Anslut-AzAccount|Autentiserar Azure PowerShell till din Azure-prenumeration.|
|Ny-AzHDInsightMapReduceJobDefinition|Skapar en ny *jobbdefinition* med hjälp av den angivna MapReduce-informationen.|
|Start-AzHDInsightJob|Skickar jobbdefinitionen till HDInsight och startar jobbet. Ett *jobbobjekt* returneras.|
|Vänta-AzHDInsightJob|Använder jobbobjektet för att kontrollera status för jobbet. Den väntar tills jobbet är klart eller väntetiden överskrids.|
|Get-AzHDInsightJobOutput|Används för att hämta utdata för jobbet.|

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i ditt HDInsight-kluster.

1. Spara följande kod med hjälp av en redigerare som **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Öppna en ny kommandotolk i **Azure PowerShell.** Ändra kataloger till platsen för **mapreducejob.ps1-filen** och använd sedan följande kommando för att köra skriptet:

        .\mapreducejob.ps1

    När du kör skriptet uppmanas du att ange namnet på HDInsight-klustret och klusterinloggningen. Du kan också uppmanas att autentisera till din Azure-prenumeration.

3. När jobbet är klart får du utdata som liknar följande text:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Det här utdata anger att jobbet har slutförts.

    > [!NOTE]  
    > Om **ExitCode** är ett annat värde än 0 läser du [Felsöka](#troubleshooting).

    I det här exemplet lagras även de nedladdade filerna till en **output.txt-fil** i katalogen som du kör skriptet från.

### <a name="view-output"></a>Visa utdata

Om du vill visa de ord och antal som skapas av jobbet öppnar du **filen output.txt** i en textredigerare.

> [!NOTE]  
> Utdatafilerna för ett MapReduce-jobb är oföränderliga. Så om du kör det här exemplet igen måste du ändra namnet på utdatafilen.

## <a name="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet är slutfört visar du fel för jobbet. Om du vill visa felinformation för det här jobbet lägger du till följande kommando i slutet av **mapreducejob.ps1-filen.** Spara sedan filen och kör skriptet igen.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Den här cmdleten returnerar informationen som skrevs till STDERR när jobbet körs.

## <a name="next-steps"></a>Nästa steg

Som du kan se är Azure PowerShell ett enkelt sätt att köra MapReduce-jobb på ett HDInsight-kluster, övervaka jobbstatusen och hämta utdata. Mer information om andra sätt kan du arbeta med Hadoop på HDInsight:

* [Använd MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
