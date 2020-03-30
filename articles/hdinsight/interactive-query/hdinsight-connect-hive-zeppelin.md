---
title: 'Snabbstart: Apache Hive i Azure HDInsight med Apache Zeppelin'
description: I den här snabbstarten får du lära dig hur du använder Apache Zeppelin för att köra Apache Hive-frågor.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367932"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snabbstart: Kör Apache Hive-frågor i Azure HDInsight med Apache Zeppelin

I den här snabbstarten får du lära dig hur du använder Apache Zeppelin för att köra [Apache Hive-frågor](https://hive.apache.org/) i Azure HDInsight. HDInsight Interactive Query-kluster innehåller [Apache Zeppelin-anteckningsböcker](https://zeppelin.apache.org/) som du kan använda för att köra interaktiva Hive-frågor.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Ett HDInsight Interactive Query-kluster. Se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) för att skapa ett HDInsight-kluster.  Se till att välja klustertypen **Interaktiv fråga.**

## <a name="create-an-apache-zeppelin-note"></a>Skapa en Apache Zeppelin-anteckning

1. Ersätt `CLUSTERNAME` med namnet på klustret `https://CLUSTERNAME.azurehdinsight.net/zeppelin`i följande URL . Ange sedan webbadressen i en webbläsare.

2. Ange användarnamn och lösenord för klusterinloggning. På Zeppelin-sidan kan du antingen skapa en ny anteckning eller öppna befintliga anteckningar. **HiveSample** innehåller några exempel hive-frågor.  

    ![HDInsight interaktiv fråga zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Välj **Skapa ny anteckning**.

4. Skriv eller välj följande värden i dialogrutan **Skapa ny anteckning:**

    - Namn: Ange ett namn på anteckningen.
    - Standardtolk: Välj **jdbc** i listrutan.

5. Välj **Skapa anteckning**.

6. Ange följande Hive-fråga i kodavsnittet och tryck sedan på **Skift + Retur:**

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query zeppelin kör fråga](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%jdbc(hive)-satsen** på den första raden talar om för anteckningsboken att använda Hive JDBC-tolken.

    Frågan ska returnera en Hive-tabell som kallas **hivesampletable**.

    Följande är ytterligare två Hive-frågor som du kan köra mot **hivesampletable:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Om du jämför med den traditionella Hive måste frågeresultaten komma tillbaka snabbare.

### <a name="additional-examples"></a>Ytterligare exempel

1. Skapa en tabell. Kör koden nedan i Den zeppelinska anteckningsboken:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Läs in data i den nya tabellen. Kör koden nedan i Den zeppelinska anteckningsboken:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Infoga en enda post. Kör koden nedan i Den zeppelinska anteckningsboken:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Läs [språkhandboken](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) för Hive för ytterligare syntax.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight. Om du vill veta mer om Hive-frågor visar nästa artikel hur du kör frågor med Visual Studio.

> [!div class="nextstepaction"]
> [Ansluta till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
