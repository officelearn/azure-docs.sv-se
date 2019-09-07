---
title: Använda Apache Hive med PowerShell i HDInsight – Azure
description: Använd PowerShell för att köra Apache Hive frågor i Apache Hadoop i Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736072"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Kör Apache Hive frågor med PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell i Azures resurs grupp läge för att köra Hive-frågor i en Apache Hadoop på HDInsight-kluster.

> [!NOTE]  
> Det här dokumentet innehåller ingen detaljerad beskrivning av vad HiveQL-satserna som används i exemplen gör. Information om HiveQL som används i det här exemplet finns i [använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* En Linux-baserad Apache Hadoop på HDInsight-kluster version 3,4 eller senare.

* En klient med Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Azure PowerShell tillhandahåller *cmdletar* som gör att du kan köra Hive-frågor på HDInsight på distans. Internt gör cmdletarna REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) i HDInsight-klustret.

Följande cmdletar används vid körning av Hive-frågor i ett fjärran slutet HDInsight-kluster:

* `Connect-AzAccount`: Autentiserar Azure PowerShell till din Azure-prenumeration.
* `New-AzHDInsightHiveJobDefinition`: Skapar en *jobb definition* med hjälp av de angivna HiveQL-satserna.
* `Start-AzHDInsightJob`: Skickar jobb definitionen till HDInsight och startar jobbet. Ett *jobb* objekt returneras.
* `Wait-AzHDInsightJob`: Använder jobbobjektet för att kontrol lera jobbets status. Den väntar tills jobbet har slutförts eller tills vänte tiden har överskridits.
* `Get-AzHDInsightJobOutput`: Används för att hämta utdata för jobbet.
* `Invoke-AzHDInsightHiveJob`: Används för att köra HiveQL-instruktioner. Denna cmdlet förhindrar att frågan slutförs och returnerar sedan resultaten.
* `Use-AzHDInsightCluster`: Anger det aktuella klustret som ska användas för `Invoke-AzHDInsightHiveJob` kommandot.

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i HDInsight-klustret:

1. Använd en redigerare och spara följande kod som `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Öppna en ny **Azure PowerShell** kommando tolk. Ändra kataloger till `hivejob.ps1` filens plats och Använd sedan följande kommando för att köra skriptet:

        .\hivejob.ps1

    När skriptet körs uppmanas du att ange kluster namnet och autentiseringsuppgifterna för HTTPS/Cluster-administratörskontot. Du kan också uppmanas att logga in på din Azure-prenumeration.

3. När jobbet har slutförts returneras information som liknar följande text:

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
   > Du kan använda Azure PowerShell **hit-Strings-** cmdlet eller HiveQL-skriptfiler för längre HiveQL frågor. Följande fragment visar hur du använder `Invoke-Hive` cmdleten för att köra en HiveQL-skript fil. Skript filen HiveQL måste överföras till wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Mer information om **här – strängar**finns i <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">använda Windows PowerShell här – strängar</a>.

## <a name="troubleshooting"></a>Felsökning

Visa fel loggarna om ingen information returneras när jobbet har slutförts. Om du vill visa fel information för det här jobbet lägger du till följande i slutet `hivejob.ps1` av filen, sparar det och kör det igen.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar den information som skrivs till STDERR under jobb bearbetningen.

## <a name="summary"></a>Sammanfattning

Som du kan se är Azure PowerShell ett enkelt sätt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobb statusen och hämta utdata.

## <a name="next-steps"></a>Nästa steg

Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda Apache gris med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
