---
title: "Använda Pig med Hadoop med PowerShell i HDInsight - Azure | Microsoft Docs"
description: "Lär dig mer om att skicka Pig-jobb till ett Hadoop-kluster i HDInsight med hjälp av Azure PowerShell."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 90b9b2b35ef0e4b7fde60d4a3ae2634cf449dd01
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Kör jobb för Pig med HDInsight med hjälp av Azure PowerShell

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Det här dokumentet innehåller ett exempel på hur Azure PowerShell för att skicka Pig-jobb till en Hadoop på HDInsight-kluster. Pig kan du skriva MapReduce-jobb med hjälp av ett språk (Pig Latin) som modeller Datatransformationer i stället för att mappa och minska funktioner.

> [!NOTE]
> Det här dokumentet ger inte en detaljerad beskrivning av vad de Pig Latin-rapporterna som används i exemplen göra. Information om den Pig Latin som används i det här exemplet finns [använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Förhandskrav

* **Ett Azure HDInsight-kluster**

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En arbetsstation med Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>Köra Pig-jobb med hjälp av PowerShell

Azure PowerShell innehåller *cmdlets* som gör det möjligt att köra Pig-jobb via fjärranslutning på HDInsight. Internt, PowerShell använder REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) körs på HDInsight-klustret.

Följande cmdlets används när Pig-jobb som körs på en fjärransluten HDInsight-kluster:

* **Login-AzureRmAccount**: autentiserar Azure PowerShell för att din Azure-prenumeration.
* **Nya AzureRmHDInsightPigJobDefinition**: skapar ett *jobbet definition* med hjälp av de angivna Pig Latin-instruktionerna.
* **Start-AzureRmHDInsightJob**: skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.
* **Vänta AzureRmHDInsightJob**: använder jobbobjektet för att kontrollera status för jobbet. Den väntar tills jobbet har slutförts eller väntetiden har överskridits.
* **Get-AzureRmHDInsightJobOutput**: används för att hämta utdata för jobbet.

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb på ditt HDInsight-kluster.

1. Med hjälp av en redigerare, spara följande kod som **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Öppna en ny Windows PowerShell-kommandotolk. Ändra kataloger till platsen för den **pigjob.ps1** filen och sedan använder du följande kommando för att köra skriptet:

        .\pigjob.ps1

    Du uppmanas att logga in på Azure-prenumerationen. Du uppmanas sedan HTTPs/Admin kontonamn och lösenord för HDInsight-kluster.

2. När jobbet är slutfört, bör den returnera information som liknar följande text:

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

Om ingen information returneras när jobbet är slutfört, visa felloggarna. Om du vill visa information om fel för det här jobbet att lägga till följande kommando i slutet av den **pigjob.ps1** filen, spara den och kör det igen.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Denna cmdlet returnerar information som har skrivits till STDERR under bearbetningen av jobbet.

## <a id="summary"></a>Sammanfattning
Du kan se ger Azure PowerShell ett enkelt sätt att köra Pig-jobb på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Pig i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
