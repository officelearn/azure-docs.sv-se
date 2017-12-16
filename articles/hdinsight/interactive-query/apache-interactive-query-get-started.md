---
title: "Använd interaktiva frågan med Azure HDInsight | Microsoft Docs"
description: "Lär dig mer om att använda interaktiva fråga (Hive LLAP) med HDInsight."
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jgao
ms.openlocfilehash: 9d9d9556c37cfa5a1a740569b4c7fd4fd07a467a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="use-interactive-query-with-hdinsight"></a>Använd interaktiva frågan med HDInsight
Interaktiva fråga (kallas även Hive LLAP eller [Live långa och processen](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är en Azure HDInsight [kluster typen](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktiva Query stöder cachelagra i minnet, vilket gör Hive-frågor snabbare och mycket mer interaktiva.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Ett kluster för interaktiva frågan skiljer sig från ett Hadoop-kluster. Den innehåller endast Hive-tjänsten. 

> [!NOTE]
> Du kan komma åt tjänsten Hive i klustret interaktiva frågan endast via Ambari Hive-vy, Beeline och Microsoft Hive ODBC-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, Azure kommandoradsverktyget (Azure CLI) eller Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Skapa ett kluster som interaktiv fråga
Information om hur du skapar ett HDInsight-kluster finns i [skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj den interaktiva frågan klustret.

## <a name="execute-hive-queries-from-interactive-query"></a>Köra Hive-frågor från interaktiva fråga
Om du vill köra Hive-frågor, har du följande alternativ:

* Använda Powerbi

    Se [visualisera stora data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Använd Zeppelin

    Se [använda Zeppelin köra Hive-frågor i Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Använd Visual Studio

    Se [Anslut till Azure HDInsight och kör Hive-frågor med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Använd Visual Studio Code

    Se [används Visual Studio-koden för Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).
* Kör Hive genom att använda Ambari Hive-vy.
  
    Se [med Hadoop i Azure HDInsight Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Kör Hive Beeline.
  
    Se [använda Hive med Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Du kan använda Beeline från antingen huvudnoden eller från en tom kantnod. Vi rekommenderar att du använder Beeline från en tom kantnod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom kantnod finns [använda tom edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).
* Kör Hive Hive ODBC.
  
    Se [ansluta Excel till Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Hitta anslutningssträngen anslutningen JDBC (Java Database):

1. Logga in på Ambari genom att använda följande URL: https://\<klusternamnet\>. AzureHDInsight.net.
2. Välj i den vänstra menyn **Hive**.
3. Välj ikonen Urklipp för att kopiera URL-Adressen:
   
   ![HDInsight Hadoop interaktiva frågan LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa interaktiva frågan kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du [visualisera stora data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).
* Lär dig hur du [kör Hive-frågor med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Lär dig hur du [använda HDInsight Tools för Visual Studio Code](../hdinsight-for-vscode.md).
* Lär dig hur du [med Hadoop i HDInsight Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Lär dig hur du [använder Beeline för att skicka Hive-frågor i HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Lär dig hur du [ansluta Excel till Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

