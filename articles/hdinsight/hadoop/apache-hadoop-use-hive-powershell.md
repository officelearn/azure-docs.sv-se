---
title: Använda Apache Hive med PowerShell i HDInsight - Azure
description: Använda PowerShell för att köra Apache Hive-frågor i Apache Hadoop i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552601"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Köra Apache Hive-frågor med PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell för att köra Apache Hive-frågor i ett Apache Hadoop-kluster i HDInsight.This document provides an example of using Azure PowerShell to run Apache Hive queries in an Apache Hadoop on HDInsight cluster.

> [!NOTE]  
> Det här dokumentet ger inte en detaljerad beskrivning av vad HiveQL-satserna som används i exemplen gör. Information om HiveQL som används i det här exemplet finns i [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* PowerShell [Az-modulen](https://docs.microsoft.com/powershell/azure/overview) är installerad.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Azure PowerShell innehåller *cmdlets* som gör att du kan fjärrköra Hive-frågor på HDInsight. Internt gör cmdlets REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) i HDInsight-klustret.

Följande cmdlets används när Hive-frågor körs i ett HDInsight-fjärrkluster:

* `Connect-AzAccount`: Autentiserar Azure PowerShell till din Azure-prenumeration.
* `New-AzHDInsightHiveJobDefinition`: Skapar en *jobbdefinition* med hjälp av de angivna HiveQL-satserna.
* `Start-AzHDInsightJob`: Skickar jobbdefinitionen till HDInsight och startar jobbet. Ett *jobbobjekt* returneras.
* `Wait-AzHDInsightJob`: Använder jobbobjektet för att kontrollera status för jobbet. Den väntar tills jobbet är klart eller väntetiden överskrids.
* `Get-AzHDInsightJobOutput`: Används för att hämta utdata för jobbet.
* `Invoke-AzHDInsightHiveJob`: Används för att köra HiveQL-satser. Den här cmdleten blockerar frågan slutförs och returnerar sedan resultaten.
* `Use-AzHDInsightCluster`: Ställer in det aktuella klustret som ska användas för kommandot. `Invoke-AzHDInsightHiveJob`

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i ditt HDInsight-kluster:

1. Spara följande kod som `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Öppna en ny kommandotolk i **Azure PowerShell.** Ändra kataloger till platsen `hivejob.ps1` för filen och använd sedan följande kommando för att köra skriptet:

        .\hivejob.ps1

    När skriptet körs uppmanas du att ange klusternamnet och autentiseringsuppgifterna för HTTPS/Cluster Admin-kontot. Du kan också bli ombedd att logga in på din Azure-prenumeration.

3. När jobbet är slutfört returneras information som liknar följande text:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Som tidigare `Invoke-Hive` nämnts kan användas för att köra en fråga och vänta på svaret. Använd följande skript för att se hur Invoke-Hive fungerar:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Utdata ser ut som följande text:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Längre HiveQL-frågor kan du använda azure **powershell-härsträngar** cmdlet- eller HiveQL-skriptfiler. Följande kodavsnitt visar hur du `Invoke-Hive` använder cmdlet för att köra en HiveQL-skriptfil. HiveQL-skriptfilen måste överföras till wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Mer information om **Härsträngar**finns i <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Använda Windows PowerShell Here-Strings</a>.

## <a name="troubleshooting"></a>Felsökning

Om ingen information returneras när jobbet är klart visar du felloggarna. Om du vill visa felinformation för det här `hivejob.ps1` jobbet lägger du till följande i slutet av filen, sparar det och kör det sedan igen.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Den här cmdleten returnerar den information som skrivs till STDERR under jobbbearbetningen.

## <a name="summary"></a>Sammanfattning

Som du kan se är Azure PowerShell ett enkelt sätt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobbstatusen och hämta utdata.

## <a name="next-steps"></a>Nästa steg

För allmän information om Hive i HDInsight:

* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Mer information om andra sätt kan du arbeta med Hadoop på HDInsight:

* [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
