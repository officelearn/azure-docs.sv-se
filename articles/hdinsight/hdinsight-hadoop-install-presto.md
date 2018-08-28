---
title: Installera Presto på Azure HDInsight Linux-kluster
description: Lär dig mer om att installera Presto och Airpal på Linux-baserat HDInsight Hadoop-kluster med skriptåtgärder.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 1569b5931a24048fa7a88c4a546bbf88547208d1
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109624"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Installera och använda Presto på HDInsight Hadoop-kluster

I det här dokumentet du lära dig hur du installerar Presto på HDInsight Hadoop-kluster med skriptåtgärd. Du också lära dig hur du installerar Airpal på ett befintligt Presto HDInsight-kluster.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver en **HDInsight 3.5 Hadoop-kluster** som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight versioner](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Vad är Presto?
[Presto](https://prestodb.io/overview.html) är en snabb distribuerade SQL-frågemotor för stordata. Presto lämpar sig för interaktiva frågor på petabyte data. Mer information om komponenter av Presto och hur de fungerar tillsammans finns i [Presto begrepp](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
> 
> Anpassade komponenter, till exempel Presto, får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Installera Presto med skriptåtgärd

Det här avsnittet innehåller instruktioner om hur du använder exempelskriptet när du skapar ett nytt kluster med hjälp av Azure portal. 

1. Börja etablera ett kluster med hjälp av stegen i [etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md). Se till att skapa klustret med den **anpassad** kluster skapa flödet. Klustret måste uppfylla följande krav.

    * Det måste vara ett Hadoop-kluster med HDInsight version 3.6.

    * Det måste använda Azure Storage som datalager. Med Presto på ett kluster som använder Azure Data Lake Store som alternativet stöds inte ännu. 

    ![Skapa för HDInsight-kluster med anpassade alternativ](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. På den **avancerade inställningar** Välj **skriptåtgärder**, och ange information nedan:
   
   * **NAMN på**: Ange ett eget namn för skriptåtgärden.
   * **Bash-skript-URI**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: Markera det här alternativet
   * **WORKER**: Markera det här alternativet
   * **ZOOKEEPER**: avmarkerar du kryssrutan
   * **PARAMETRAR**: lämna fältet tomt


3. Längst ned på den **skriptåtgärder** området klickar du på den **Välj** för att spara konfigurationen. Klicka slutligen på den **Välj** knappen längst ned på den **avancerade inställningar** område för att spara konfigurationsinformationen.

4. Fortsätta att etablera klustret enligt beskrivningen i [etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure Resource Manager-mallar kan också användas för att tillämpa skriptåtgärder. Du kan också använda skriptåtgärder för kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Använda Presto med HDInsight

Använd följande steg för att fungera Presto i ett HDInsight-kluster med:

1. Anslut till HDInsight-klustret med hjälp av SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
     

2. Starta Presto gränssnittet med följande kommando.
   
        presto --schema default

3. Köra en fråga på en exempeltabell **hivesampletable**, som finns tillgängligt på alla HDInsight-kluster som standard.
   
        select count (*) from hivesampletable;
   
    Som standard [Hive](https://prestodb.io/docs/current/connector/hive.html) och [TPCH](https://prestodb.io/docs/current/connector/tpch.html) anslutningsappar för Presto har redan konfigurerats. Hive-anslutning är konfigurerad för att använda installerat Hive standardinstallationen, så att alla tabeller från Hive automatiskt synliga i Presto.

    Mer information finns i [Presto dokumentation](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Använda Airpal med Presto

[Airpal](https://github.com/airbnb/airpal#airpal) är en öppen källkod webbaserade-gränssnitt för Presto. Läs mer på Airpal [Airpal dokumentation](https://github.com/airbnb/airpal#airpal).

Använd följande steg för att installera Airpal på gränsnoden:

1. Anslut till huvudnoden på HDInsight-klustret som har installerat Presto med SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du är ansluten, kör du följande kommando.

        sudo slider registry  --name presto1 --getexp presto 
   
    Du ser utdata som liknar följande JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Anteckna värdet för utdata i **värdet** egenskapen. Du behöver det här värdet när du installerar Airpal på edgenode för klustret. Från utdatan ovan är det värde som du behöver **10.0.0.12:9090**.

4. Använda mallen **[här](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** att skapa ett HDInsight-kluster edgenode och ange värdena som visas i följande skärmbild.

    ![Installera HDInsight Airpal på Presto kluster](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Klicka på **Köp**.

6. När ändringarna tillämpas på klusterkonfigurationen, du kan komma åt webbgränssnittet Airpal med hjälp av följande steg.

    1. Kluster-dialogrutan klickar du på **program**.

        ![HDInsight starta Airpal på Presto kluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Från den **installerade appar** området klickar du på **Portal** mot airpal.

        ![HDInsight starta Airpal på Presto kluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. När du uppmanas, anger du de autentiseringsuppgifter som administratör som du angav när du skapar HDInsight Hadoop-kluster.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Anpassa en Presto installation på HDInsight-kluster

Om du vill anpassa installationen, använder du följande steg:

1. Anslut till huvudnoden på HDInsight-klustret som har installerat Presto med SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Gör dina ändringar i konfigurationen i filen `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Läs mer på Presto konfiguration, [Presto konfiguration för YARN-baserade kluster](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Avbryt och avsluta den aktuella pågående instansen av Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Starta en ny instans av Presto med anpassningen.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Vänta tills den nya instansen är klar och anteckna presto coordinator-adressen.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generera benchmark-data för HDInsight-kluster som kör Presto

TPC DS-är branschstandard för att mäta prestanda för många beslut support system, inklusive system för stordata. Du kan använda Presto kan generera data och utvärdera hur jämförs med dina egna HDInsight benchmark-data. Mer information finns i [här](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Se också
* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md). Hue är ett webbgränssnitt som gör det enkelt att skapa, köra, och spara Pig och Hive-jobb.

* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd kluster anpassning för att installera Giraph på HDInsight Hadoop-kluster. Giraph kan du utföra diagrambearbetning med hjälp av Hadoop och kan användas med Azure HDInsight.

* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). Använd kluster anpassning för att installera Solr på HDInsight Hadoop-kluster. Solr kan du utföra kraftfulla sökningar på lagrade data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
