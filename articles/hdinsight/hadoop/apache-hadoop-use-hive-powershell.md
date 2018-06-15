---
title: Använda Hadoop Hive med PowerShell i HDInsight - Azure | Microsoft Docs
description: Använd PowerShell för att köra Hive-frågor i Hadoop på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d72d5223373043648aeb3d783477781dbce35f6d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164582"
---
# <a name="run-hive-queries-using-powershell"></a>Köra Hive-frågor med hjälp av PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Det här dokumentet innehåller ett exempel på att använda Azure PowerShell i Azure-resursgrupp läge för att köra Hive-frågor i en Hadoop på HDInsight-kluster.

> [!NOTE]
> Det här dokumentet ger inte en detaljerad beskrivning av HiveQL-instruktioner som används i exemplen gör. Information om HiveQL som används i det här exemplet finns [använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Förutsättningar

* En Linux-baserade Hadoop på HDInsight-kluster av version 3.4 eller större.

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En klient med Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Azure PowerShell innehåller *cmdlets* som gör det möjligt att köra Hive-frågor via fjärranslutning på HDInsight. Internt, cmdletarna gör REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) på HDInsight-kluster.

Följande cmdlets som används när du kör Hive-frågor i en fjärransluten HDInsight-kluster:

* `Connect-AzureRmAccount`: Autentiserar Azure PowerShell på Azure-prenumerationen.
* `New-AzureRmHDInsightHiveJobDefinition`: Skapar ett *jobbet definition* med hjälp av de angivna HiveQL-instruktionerna.
* `Start-AzureRmHDInsightJob`: Skickar jobbdefinitionen till HDInsight och startar jobbet. En *jobbet* objekt returneras.
* `Wait-AzureRmHDInsightJob`: Använder jobbobjektet för att kontrollera status för jobbet. Den väntar tills jobbet slutförs eller väntetiden har överskridits.
* `Get-AzureRmHDInsightJobOutput`: Används för att hämta utdata för jobbet.
* `Invoke-AzureRmHDInsightHiveJob`: Används för att köra HiveQL-instruktioner. Den här cmdlet-block frågan har slutförts, och returnerar sedan resultatet.
* `Use-AzureRmHDInsightCluster`: Anger det aktuella klustret ska användas för den `Invoke-AzureRmHDInsightHiveJob` kommando.

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i HDInsight-kluster:

1. Med hjälp av en redigerare, spara följande kod som `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Öppna ett nytt **Azure PowerShell** kommandotolk. Ändra kataloger till platsen för den `hivejob.ps1` filen och sedan använder du följande kommando för att köra skriptet:

        .\hivejob.ps1

    När skriptet körs, uppmanas du att ange klusternamnet och HTTPS/Admin-autentiseringsuppgifter för klustret. Du kan också uppmanas att logga in på Azure-prenumerationen.

3. När jobbet är slutfört returnerar den information liknar följande:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Som tidigare nämnts `Invoke-Hive` kan användas för att köra en fråga och vänta på svar. Använd följande skript för att se hur Invoke-Hive fungerar:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Det ser ut som följande utdata:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Du kan använda Azure PowerShell för längre HiveQL frågor **här strängar** cmdlet eller HiveQL skriptfiler. Följande utdrag visar hur du använder den `Invoke-Hive` för att köra en HiveQL-skriptfil. HiveQL skriptfilen måste överföras till wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Mer information om **här strängar**, se <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">med hjälp av Windows PowerShell här-strängar</a>.

## <a name="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet är slutfört, visa felloggarna. Om du vill visa information om fel för det här jobbet, lägger du till följande i slutet av den `hivejob.ps1` filen, spara den och kör det igen.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar informationen som skrivs till STDERR under bearbetningen av jobbet.

## <a name="summary"></a>Sammanfattning

Du kan se ger Azure PowerShell ett enkelt sätt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobbstatus och hämta utdata.

## <a name="next-steps"></a>Nästa steg

Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
