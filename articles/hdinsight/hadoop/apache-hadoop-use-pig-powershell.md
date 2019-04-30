---
title: Använda Apache Pig med PowerShell i HDInsight - Azure
description: Lär dig mer om att skicka Apache Pig-jobb till ett Apache Hadoop-kluster på HDInsight med Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 9ad788989273f28f38ee95f8d669fdf17f1fd785
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105046"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Använd Azure PowerShell för att köra Apache Pig-jobb med HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell för att skicka Apache Pig-jobb till en Apache Hadoop på HDInsight-kluster. Pig kan du skriva MapReduce-jobb med hjälp av ett språk (Pig Latin) som modeller dataomvandlingar, snarare än mappa och minska funktioner.

> [!NOTE]  
> Det här dokumentet ger inte en detaljerad beskrivning av vad de satser i Pig Latin i exemplen göra. Information om den Pig Latin i det här exemplet finns i [använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Förhandskrav

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Ett Azure HDInsight-kluster**

  > [!IMPORTANT]  
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

## <a id="powershell"></a>Kör ett Apache Pig-jobb

Azure PowerShell tillhandahåller *cmdletar* som gör det möjligt att köra Pig-jobb via fjärranslutning på HDInsight. Internt PowerShell använder REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) körs på HDInsight-klustret.

När du kör Pig-jobb i ett fjärranslutet HDInsight-kluster används följande cmdletar:

* **Connect-AzAccount**: Autentiserar Azure PowerShell på Azure-prenumerationen.
* **New-AzHDInsightPigJobDefinition**: Skapar en *jobbet definition* med hjälp av de angivna satser i Pig Latin.
* **Start-AzHDInsightJob**: Skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.
* **Wait-AzHDInsightJob**: Använder objektet för att kontrollera status för jobbet. Den ska vänta tills jobbet har slutförts eller väntetiden har överskridits.
* **Get-AzHDInsightJobOutput**: Används för att hämta utdata för jobbet.

Följande steg visar hur du använder dessa cmdletar för att köra ett jobb i ditt HDInsight-kluster.

1. Med hjälp av en redigerare, spara följande kod som **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Öppna en ny Windows PowerShell-kommandotolk. Ändra kataloger till platsen för den **pigjob.ps1** filen och sedan använda följande kommando för att köra skriptet:

        .\pigjob.ps1

    Du uppmanas att logga in på Azure-prenumerationen. Du uppmanas sedan HTTPs/Admin-kontonamn och lösenord för HDInsight-klustret.

2. När jobbet har slutförts bör den returnera information liknande följande text:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet har slutförts kan visa felloggarna. Om du vill visa information om fel för det här jobbet att lägga till följande kommando i slutet av den **pigjob.ps1** filen, spara den och sedan köra det igen.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Denna cmdlet returnerar informationen som har skrivits till STDERR under bearbetningen av jobbet.

## <a id="summary"></a>Sammanfattning
Som du kan se är det enkelt att köra Pig-jobb på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata i Azure PowerShell.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Pig i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)
