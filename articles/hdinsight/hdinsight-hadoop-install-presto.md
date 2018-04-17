---
title: Installera Presto på Azure HDInsight Linux-kluster | Microsoft Docs
description: Lär dig hur du installerar Presto och Airpal på Linux-baserade HDInsight Hadoop-kluster med hjälp av skriptåtgärder.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Installera och använda Presto på HDInsight Hadoop-kluster

Lär dig hur du installerar Presto på HDInsight Hadoop-kluster med hjälp av skriptåtgärder i det här dokumentet. Du också lära dig hur du installerar Airpal på ett befintligt Presto HDInsight-kluster.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver en **HDInsight 3.5 Hadoop-kluster** som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-versioner](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Vad är Presto?
[Presto](https://prestodb.io/overview.html) är en snabb distribuerade SQL-frågemotor för stordata. Presto är lämplig för interaktiva frågor till petabyte data. Mer information om komponenter av Presto och hur de fungerar finns i [Presto begrepp](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut och Microsoft-supporten hjälper att isolera och lösa problem relaterade till komponenterna.
> 
> Anpassade komponenter, till exempel Presto kan få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för öppen källkod där djup expertis för att teknik finns. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Apache-projekt har också project-webbplatser [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Installera Presto med skriptåtgärder

Det här avsnittet innehåller instruktioner om hur du använder exempelskriptet när du skapar ett nytt kluster med hjälp av Azure portal. 

1. Börja etablera ett kluster med hjälp av stegen i [etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md). Se till att du skapar klustret med den **anpassad** klustret skapas flödet. Klustret måste uppfylla följande krav.

    * Det måste vara ett Hadoop-kluster med HDInsight version 3.5.

    * Den måste använda Azure Storage som dataarkiv. Med Presto på ett kluster som använder Azure Data Lake Store som lagringsalternativet stöds inte ännu. 

    ![HDInsight-kluster med anpassade alternativ](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. På den **avancerade inställningar** väljer **skriptåtgärder**, och ange informationen nedan:
   
   * **NAMNET**: Ange ett eget namn för skriptåtgärden.
   * **Bash-skript-URI**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: Markera det här alternativet
   * **WORKER**: Markera det här alternativet
   * **ZOOKEEPER**: avmarkerar du kryssrutan
   * **PARAMETRARNA**: lämna fältet tomt


3. Längst ned i den **skriptåtgärder** området klickar du på den **Välj** för att spara konfigurationen. Klicka slutligen på den **Välj** knappen längst ned i den **avancerade inställningar** område för att spara konfigurationsinformationen.

4. Fortsätta etablering klustret enligt beskrivningen i [etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure Resource Manager-mallar kan också användas för att tillämpa skriptåtgärder. Du kan även gälla skriptåtgärder kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Använda Presto med HDInsight

Om du vill arbeta med Presto i ett HDInsight-kluster, använder du följande steg:

1. Anslut till HDInsight-klustret med hjälp av SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
     

2. Starta Presto shell med följande kommando.
   
        presto --schema default

3. Köra en fråga på en exempeltabell **hivesampletable**, som är tillgänglig i alla HDInsight-kluster som standard.
   
        select count (*) from hivesampletable;
   
    Som standard [Hive](https://prestodb.io/docs/current/connector/hive.html) och [TPCH](https://prestodb.io/docs/current/connector/tpch.html) kopplingar för Presto har redan konfigurerats. Hive-anslutningen är konfigurerad för att använda installerat Hive standardinstallationen, så alla tabeller från Hive visas automatiskt i Presto.

    Mer information finns i [Presto dokumentationen](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Använda Airpal med Presto

[Airpal](https://github.com/airbnb/airpal#airpal) är ett gränssnitt för öppen källkod webbaserade frågan för Presto. Mer information om Airpal finns [Airpal dokumentationen](https://github.com/airbnb/airpal#airpal).

Använd följande steg för att installera Airpal på kantnoden:

1. Med SSH, Anslut till headnode i HDInsight-kluster som har installerat Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du är ansluten, kör du följande kommando.

        sudo slider registry  --name presto1 --getexp presto 
   
    Du kan se utdata som liknar följande JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Anteckna värdet för utdata i **värdet** egenskapen. Du behöver det här värdet när du installerar Airpal på edgenode för klustret. Från resultatet ovan är det värde som du behöver **10.0.0.12:9090**.

4. Använd mallen **[här](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** att skapa ett HDInsight-kluster edgenode och ange värden som visas i följande skärmbild.

    ![Installera HDInsight Airpal på Presto klustret](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Klicka på **Köp**.

6. När ändringarna tillämpas på klusterkonfigurationen, du kan komma åt webbgränssnittet Airpal med hjälp av följande steg.

    1. I dialogrutan klustret klickar du på **program**.

        ![HDInsight systemstart Airpal Presto kluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Från den **installerade appar** området klickar du på **Portal** mot airpal.

        ![HDInsight systemstart Airpal Presto kluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. När du uppmanas ange administratörsautentiseringsuppgifter som du angav när du skapar HDInsight Hadoop-kluster.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Anpassa en Presto installation på HDInsight-kluster

Om du vill anpassa installationen använder du följande steg:

1. Med SSH, Anslut till headnode i HDInsight-kluster som har installerat Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Ändrar du konfigurationen i filen `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Mer information om Presto konfiguration finns [Presto konfigurationen för YARN-baserade kluster](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Stoppa och avsluta den aktuella körs instansen av Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Starta en ny instans av Presto med anpassningen.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Vänta tills den nya instansen redo och anteckna presto coordinator adress.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generera benchmark-data för HDInsight-kluster som kör Presto

TPC DS är branschstandard för mätning av prestanda i många beslut support-system, inklusive system för stordata. Du kan använda Presto att generera data och utvärdera hur jämförs med dina egna HDInsight benchmark-data. Mer information finns i [här](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Se också
* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md). Hue är ett webbgränssnitt som gör det enkelt att skapa, köra, och spara Pig och Hive-jobb.

* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd anpassning av klustret för att installera Giraph på HDInsight Hadoop-kluster. Giraph kan du utföra bearbetning med hjälp av Hadoop och kan användas med Azure HDInsight.

* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). Använd anpassning av klustret för att installera Solr på HDInsight Hadoop-kluster. Solr kan du utföra kraftfulla sökningar på lagrade data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
