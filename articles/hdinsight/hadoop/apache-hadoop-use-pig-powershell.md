---
title: Använda Pig med Hadoop med PowerShell i HDInsight - Azure
description: Lär dig mer om att skicka Pig-jobb till ett Hadoop-kluster i HDInsight med Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: e50c551381a065e8651ab7970ef9cf18c6c3a765
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011025"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Använd Azure PowerShell för att köra Pig-jobb med HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell för att skicka Pig-jobb till ett Hadoop på HDInsight-kluster. Pig kan du skriva MapReduce-jobb med hjälp av ett språk (Pig Latin) som modeller dataomvandlingar, snarare än mappa och minska funktioner.

> [!NOTE]
> Det här dokumentet ger inte en detaljerad beskrivning av vad de satser i Pig Latin i exemplen göra. Information om den Pig Latin i det här exemplet finns i [använda Pig med Hadoop på HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Förhandskrav

* **Ett Azure HDInsight-kluster**

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

## <a id="powershell"></a>Köra ett Pig-jobb

Azure PowerShell tillhandahåller *cmdletar* som gör det möjligt att köra Pig-jobb via fjärranslutning på HDInsight. Internt PowerShell använder REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) körs på HDInsight-klustret.

När du kör Pig-jobb i ett fjärranslutet HDInsight-kluster används följande cmdletar:

* **Connect-AzureRmAccount**: autentiserar Azure PowerShell på Azure-prenumerationen.
* **Ny AzureRmHDInsightPigJobDefinition**: skapar en *jobbet definition* med hjälp av de angivna satser i Pig Latin.
* **Start-AzureRmHDInsightJob**: skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.
* **Vänta AzureRmHDInsightJob**: använder objektet för att kontrollera status för jobbet. Den ska vänta tills jobbet har slutförts eller väntetiden har överskridits.
* **Get-AzureRmHDInsightJobOutput**: används för att hämta utdata för jobbet.

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
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Denna cmdlet returnerar informationen som har skrivits till STDERR under bearbetningen av jobbet.

## <a id="summary"></a>Sammanfattning
Som du kan se är det enkelt att köra Pig-jobb på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata i Azure PowerShell.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Pig i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
