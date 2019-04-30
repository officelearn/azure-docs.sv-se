---
title: Använda interaktiv fråga med Azure HDInsight
description: Lär dig hur du använder Interactive Query (LLAP Hive) med HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c7cee3dfd3b091d75f4dadcaa62513fddf0c0e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126604"
---
# <a name="use-interactive-query-with-hdinsight"></a>Använda interaktiv fråga med HDInsight
Interaktiv fråga (kallas även Apache Hive-LLAP eller [med låg latens Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är ett Azure HDInsight [klustertyp](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktiv fråga har stöd för cachelagring i minnet, vilket gör Apache Hive-frågor snabbare och mycket mer interaktivt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Ett interaktivt frågekluster skiljer sig från ett Apache Hadoop-kluster. Den innehåller endast Hive-tjänsten. 

> [!NOTE]  
> Du kan komma åt tjänsten Hive i klustret för interaktiv fråga endast via Apache Ambari Hive-vy, Beeline och Microsoft Hive Open Database Connectivity-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, klassiska Azure-CLI eller Azure PowerShell. 

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt frågekluster
Information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj klustertyp interaktiv fråga.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Kör Apache Hive-frågor från interaktiv fråga
För att köra Hive-frågor, har du följande alternativ:

* Use Microsoft Power BI

    Se [visualisera Interactive Query Apache Hive-data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Se [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Använda Apache Zeppelin

    Se [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).

* Använd Visual Studio

    Se [Anslut till Azure HDInsight och köra Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Använd Visual Studio Code

    Se [använda Visual Studio Code för Apache Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).
* Kör Apache Hive med hjälp av Apache Ambari Hive-vy.
  
    Se [använda Apache Hive-vyn med Apache Hadoop i Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Kör Apache Hive genom att använda Beeline.
  
    Se [använda Apache Hive med Apache Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Du kan använda Beeline från antingen huvudnoden eller från en tom edge-nod. Vi rekommenderar att du använder Beeline från en tom edge-nod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom edge-nod finns i [använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).
* Kör Apache Hive med Hive ODBC.
  
    Se [Anslut Excel till Apache Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Hitta anslutningssträngen Java Database Connectivity (JDBC):

1. Logga in på Apache Ambari med hjälp av följande URL: https://\<klusternamnet\>. AzureHDInsight.net.
2. I den vänstra menyn väljer du **Hive**.
3. Välj ikonen Urklipp för att kopiera URL: en:
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa interaktiva frågekluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använder Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Lär dig hur du [kör Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Lär dig hur du [använder HDInsight Tools för Visual Studio Code](../hdinsight-for-vscode.md).
* Lär dig hur du [använda Apache Hive-vy med Apache Hadoop i HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Lär dig hur du [använda Beeline för att skicka Apache Hive-frågor i HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Lär dig hur du [ansluta Excel till Apache Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

