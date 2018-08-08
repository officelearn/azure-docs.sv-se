---
title: Använda interaktiv fråga med Azure HDInsight
description: Lär dig hur du använder Interactive Query (LLAP Hive) med HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 77e150e56125d12cddfdbb2a34adb913a5ae9c78
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595924"
---
# <a name="use-interactive-query-with-hdinsight"></a>Använda interaktiv fråga med HDInsight
Interaktiv fråga (kallas även Hive-LLAP eller [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är ett Azure HDInsight [klustertyp](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktiv fråga har stöd för cachelagring i minnet, vilket gör Hive-frågor snabbare och mycket mer interaktivt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Ett interaktivt frågekluster skiljer sig från ett Hadoop-kluster. Den innehåller endast Hive-tjänsten. 

> [!NOTE]
> Du kan komma åt tjänsten Hive i klustret för interaktiv fråga endast via Ambari Hive-vy, Beeline och Microsoft Hive Open Database Connectivity-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, Azure kommandoradsverktyget (Azure CLI) eller Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt frågekluster
Information om hur du skapar ett HDInsight-kluster finns i [skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj klustertyp interaktiv fråga.

## <a name="execute-hive-queries-from-interactive-query"></a>Köra Hive-frågor från interaktiv fråga
För att köra Hive-frågor, har du följande alternativ:

* Använda Power BI

    Se [visualisera Interactive Query som Hive-data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Se [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Använd Zeppelin

    Se [Använd Zeppelin för att köra Hive-frågor i Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Använd Visual Studio

    Se [Anslut till Azure HDInsight och köra Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Använd Visual Studio Code

    Se [använda Visual Studio Code för Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).
* Köra Hive genom att använda Ambari Hive-vy.
  
    Se [med Hadoop i Azure HDInsight Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Kör Hive Beeline.
  
    Se [använda Hive med Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Du kan använda Beeline från antingen huvudnoden eller från en tom edge-nod. Vi rekommenderar att du använder Beeline från en tom edge-nod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom edge-nod finns i [använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).
* Köra Hive med Hive ODBC.
  
    Se [Anslut Excel till Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Hitta anslutningssträngen Java Database Connectivity (JDBC):

1. Logga in på Ambari med hjälp av följande URL: https://\<klusternamnet\>. AzureHDInsight.net.
2. I den vänstra menyn väljer du **Hive**.
3. Välj ikonen Urklipp för att kopiera URL: en:
   
   ![HDInsight Hadoop interaktiv fråga LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa interaktiva frågekluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).
* Lär dig hur du [köra Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Lär dig hur du [använder HDInsight Tools för Visual Studio Code](../hdinsight-for-vscode.md).
* Lär dig hur du [använda Hive-vy med Hadoop i HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Lär dig hur du [använda Beeline för att skicka Hive-frågor i HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Lär dig hur du [ansluta Excel till Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

