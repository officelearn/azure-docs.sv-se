---
title: 'Snabb start: Apache Hive i Azure HDInsight med Apache Zeppelin'
description: I den här snabb starten får du lära dig hur du använder Apache Zeppelin för att köra Apache Hive-frågor.
keywords: HDInsight, Hadoop, Hive, interaktiv fråga, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494296"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snabb start: köra Apache Hive frågor i Azure HDInsight med Apache Zeppelin

I den här snabb starten får du lära dig hur du använder Apache Zeppelin för att köra [Apache Hive](https://hive.apache.org/) frågor i Azure HDInsight. HDInsight-interaktiva Query-kluster innehåller [Apache Zeppelin](https://zeppelin.apache.org/) -anteckningsböcker som du kan använda för att köra interaktiva Hive-frågor.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

An-HDInsight interaktiv fråga-kluster. Se [skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) för att skapa ett HDInsight-kluster.  Se till att välja kluster typen **interaktiv fråga** .

## <a name="create-an-apache-zeppelin-note"></a>Skapa en Apache Zeppelin-anteckning

1. Ersätt `CLUSTERNAME` med namnet på klustret i följande URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Ange sedan URL-adressen i en webbläsare.

2. Ange ditt användar namn och lösen ord för kluster inloggning. På sidan Zeppelin kan du antingen skapa en ny anteckning eller öppna befintliga anteckningar. **HiveSample** innehåller några exempel på Hive-frågor.  

    ![Zeppelin för interaktiv HDInsight-fråga](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Välj **Skapa ny anteckning**.

4. I dialog rutan **Skapa ny anteckning** skriver eller väljer du följande värden:

    - Kommentars namn: Ange ett namn för anteckningen.
    - Standard tolk: Välj **JDBC** i list rutan.

5. Välj **Skapa anteckning**.

6. Ange följande Hive-fråga i avsnittet Code och tryck sedan på **SKIFT + RETUR**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Zeppelin kör fråga för interaktiva frågor i HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **% JDBC (Hive)** -instruktionen på den första raden instruerar den bärbara datorn att använda Hive JDBC-tolken.

    Frågan ska returnera en Hive-tabell med namnet **hivesampletable**.

    Följande är två ytterligare Hive-frågor som du kan köra mot **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Genom att jämföra med den traditionella registrerings data filen måste frågeresultaten gå snabbare.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Apache Zeppelin för att köra Apache Hive frågor i Azure HDInsight. Om du vill veta mer om Hive-frågor visar nästa artikel hur du kan köra frågor med Visual Studio.

> [!div class="nextstepaction"]
> [Anslut till Azure HDInsight och kör Apache Hive frågor med Data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
