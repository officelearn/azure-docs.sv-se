---
title: Installera Presto på Azure HDInsight Linux-kluster
description: Lär dig mer om att installera Presto och Airpal på Linux-baserade HDInsight Hadoop-kluster med skriptåtgärder.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 60ff63a049f225886d69c1a89a2930671e533d78
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910921"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Installera och använda Presto på Hadoop-baserade HDInsight-kluster

Den här artikeln beskriver hur du installerar Presto på Hadoop-baserade Adobe HDInsight-kluster med skriptåtgärder. Du också lära dig hur du installerar Airpal på ett befintligt Presto HDInsight-kluster.

HDInsight erbjuder även stjärnexplosion Presto programmet för Apache Hadoop-kluster. Mer information finns i [installera från tredje part Apache Hadoop-program på Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Stegen i den här artikeln kräver ett HDInsight 3.5 Hadoop-kluster som använder Linux. Linux är det enda operativsystem som används på HDInsight version 3.4 och senare. Mer information finns i [HDInsight versioner](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Vad är Presto?
[Presto](https://prestodb.io/overview.html) är en fast-distribuerad SQL-frågemotor för stordata. Presto lämpar sig för interaktiva frågor på petabyte data. Mer information om komponenter av Presto och hur de fungerar tillsammans finns i [Presto begrepp](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
> 
> Anpassade komponenter som Presto får kommersiellt rimlig support hjälpa dig att felsöka problemet ytterligare. Det här stödet kan lösa problemet. Eller du kan bli ombedd att engagera tillgängliga kanaler för öppen källkod-teknik där djup kompetens för den tekniken hittas. Det finns många community-webbplatser som kan användas. Exempel är [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Overflow](https://stackoverflow.com). 
>
> Apache-projekt har också project-webbplatser på den [Apache webbplats](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Installera Presto med hjälp av skriptåtgärder

Det här avsnittet beskrivs hur du använder exempelskriptet när du skapar ett nytt kluster med hjälp av Azure portal: 

1. Börja etablera ett kluster genom att utföra stegen [skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md). Se till att skapa klustret med hjälp av den **anpassad** kluster skapa flödet. Klustret måste uppfylla följande krav:

    * Det måste vara ett Hadoop-kluster med HDInsight version 3.6.

    * Det måste använda Azure Storage som datalager. Med Presto på ett kluster som använder Azure Data Lake Storage som alternativet är inte ett alternativ för ännu.

    ![HDInsight, anpassad (storlek, inställningar, appar)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. I den **avancerade inställningar** Välj **skriptåtgärder**. Ange följande information. Du kan också välja den **installera Presto** alternativ för Skripttyp:
   
   * **NAMN PÅ**. Ange ett eget namn för skriptåtgärden.
   * **Bash-skript-URI: N**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HEAD**. Välj det här alternativet.
   * **WORKER**. Välj det här alternativet.
   * **ZOOKEEPER**. Lämna den här kryssrutan tom.
   * **PARAMETRAR**. Lämna det här fältet tomt.


3. Längst ned på den **skriptåtgärder** området, Välj den **Välj** för att spara konfigurationen. Välj slutligen den **Välj** knappen längst ned på den **avancerade inställningar** område för att spara konfigurationsinformationen.

4. Fortsätta att etablera klustret enligt beskrivningen i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Azure PowerShell, den klassiska Azure-CLI, HDInsight .NET SDK eller Azure Resource Manager-mallar kan också användas för att tillämpa skriptåtgärder. Du kan också använda skriptåtgärder för kluster som körs redan. Mer information finns i [anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Använda Presto med HDInsight

Om du vill arbeta med Presto i ett HDInsight-kluster, gör du följande:

1. Anslut till HDInsight-klustret med hjälp av SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Starta Presto gränssnittet genom att köra följande kommando:
   
    `presto --schema default`

3. Köra en fråga på en exempeltabell **hivesampletable**, som finns tillgängligt på alla HDInsight-kluster som standard:
   
    `select count (*) from hivesampletable;`
   
    Som standard [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) och [TPCH](https://prestodb.io/docs/current/connector/tpch.html) anslutningsappar för Presto har redan konfigurerats. Hive-anslutning är konfigurerad för att använda Hive standardinstallationen. Det innebär att alla tabeller från Hive visas automatiskt i Presto.

    Mer information finns i [Presto dokumentation](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Använda Airpal med Presto

[Airpal](https://github.com/airbnb/airpal#airpal) är en öppen källkod webbaserade-gränssnitt för Presto. Läs mer på Airpal [Airpal dokumentation](https://github.com/airbnb/airpal#airpal).

Vidta följande steg för att installera Airpal på gränsnoden:

1. Ansluta till huvudnoden på HDInsight-klustret som Presto har installerats med hjälp av SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. När du är ansluten, kör du följande kommando:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Du ser utdata som liknar följande JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Anteckna värdet för utdata i **värdet** egenskapen. Du behöver det här värdet när du installerar Airpal på en klustergränsnoden. Från ovanstående utdata värdet som du behöver är **10.0.0.12:9090**.

4. Använd [den här mallen](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) att skapa en kantnod för HDInsight-kluster. Ange de värden som visas i följande skärmbild.

    ![Anpassad distribution](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Välj **Köp**.

6. När ändringarna tillämpas på klusterkonfigurationen, åt Airpal webbgränssnitt genom att utföra följande steg från den [Azure-portalen](https://portal.azure.com):

    1. I den vänstra menyn, Välj **alla tjänster**.

    1. Under **ANALYTICS**väljer **HDInsight-kluster**.

    1. Välj ditt kluster från listan, vilket öppnar standardvyn.

    1. Från standardvyn under **inställningar**väljer **program**.

        ![HDInsight, program](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Från den **installerade appar** , letar du upp tabellposten för **airpal**. Välj **Portal**.

        ![Installerade appar](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. När du uppmanas, anger du autentiseringsuppgifter som administratör som du angav när du skapade i Hadoop-baserade HDInsight-klustret.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Anpassa en Presto installation på HDInsight-kluster

Om du vill anpassa installationen, gör du följande:

1. Ansluta till huvudnoden på HDInsight-klustret som Presto har installerats med hjälp av SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Gör dina ändringar i konfigurationen i filen `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Läs mer på Presto konfiguration, [Presto konfigurationsalternativ för YARN-baserade kluster](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Stoppa och avsluta den aktuella pågående instansen av Presto:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Starta en ny instans av Presto med anpassningen:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Vänta tills den nya instansen är klar. Observera Presto coordinator-adress:


    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generera benchmark-data för HDInsight-kluster som kör Presto

TPC DS-är branschstandard för att mäta prestanda för många stöd för beslut system, inklusive system för stordata. Du kan använda Presto kan generera data och utvärdera hur jämförs med dina egna HDInsight benchmark-data. Mer information finns i [tpcds hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Nästa steg
* [Installera och använda Hue på HDInsight Hadoop-kluster](hdinsight-hadoop-hue-linux.md). Hue är ett webbgränssnitt som gör det enkelt att skapa, köra och spara Apache Pig och Hive-jobb.

* [Installera Apache Giraph på HDInsight Hadoop-kluster och använda Giraph för att bearbeta storskaliga diagram](hdinsight-hadoop-giraph-install-linux.md). Använd kluster anpassning för att installera Giraph på Hadoop-baserade HDInsight-kluster. Med Giraph kan utföra du diagrambearbetning med hjälp av Hadoop. Det kan också användas med Azure HDInsight.

* [Installera och använda Apache Solr på HDInsight Hadoop-kluster](hdinsight-hadoop-solr-install-linux.md). Använd kluster anpassning för att installera Solr på Hadoop-baserade HDInsight-kluster. Genom att använda Solr kan utföra du kraftfulla sökningar på lagrade data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
