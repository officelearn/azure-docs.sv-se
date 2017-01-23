---
title: "Apache Storm-kurs: Komma igång med Linux-baserat Storm på HDInsight | Microsoft Docs"
description: "Kom igång med analyser av stordata med Apache Storm och Storm Starter-exempel på Linux-baserade HDInsight. Ta reda på hur du använder Storm till att bearbeta data i realtid."
keywords: "apache storm, apache storm självstudier, stordata-analys, storm starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: b9fda8b5f4ffa6679cc8ca9696a4c51084c80645
ms.openlocfilehash: 7c3d73ca6f4f567247ec9796199e68f764a52808


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Apache Storm-kurs: Komma igång med Storm Starter-exempel för analys av stordata i HDInsight

Apache Storm är ett skalbart, feltolerant och distribuerat system för beräkningar i realtid för bearbetning av dataströmmar. Du kan skapa ett molnbaserat Storm-kluster som utför analyser av stordata i realtid med Storm på Azure HDInsight.

> [!NOTE]
> Anvisningarna i den här artikeln gäller för att skapa ett Linux-baserat HDInsight-kluster. Anvisningar om hur du skapar Windows-baserade Storm i HDInsight-kluster finns i [Apache Storm-kurs: Komma igång med Storm Starter-exempel med hjälp av dataanalys i HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## <a name="prerequisites"></a>Krav

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

För den här kursen om Apache Storm krävs:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **kunskap om SSH och SCP**. Mer information om hur du använder SSH och SCP med HDInsight:
  
    * **Linux, Unix eller OS X-klienter**: Läs [Använda SSH med Linux-baserade Hadoop på HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Windows-klienter**: Läs [Använda SSH (PuTTY) med Linux-baserat Hadoop i HDInsight från Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Skapa ett Storm-kluster

I det här avsnittet skapar du ett HDInsight-kluster av version 3.5 (Storm-version 1.0.1) med hjälp av en Azure Resource Manager-mall. Information om HDInsight-versioner och deras serviceavtal finns i [Versionshantering för HDInsight-komponenter](hdinsight-component-versioning.md). Information om andra metoder för att skapa kluster finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Mallen finns i den offentliga blob-behållaren *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 

2. Från bladet __Anpassad distribution__, anger du följande:
   
    * __Resursgrupp__: Resursgruppen som klustrets har skapats i.

    * **Klusternamn**: Hadoop-klustrets namn.

    * __Klustrets inloggningsnamn__ och __lösenord__: Inloggningsnamnet är som standard admin.
    
    * __SSH-användarnamn__ och __-lösenord__: Användare och lösenord för att koppla klustret med SSH.

    * __Plats__: Klustrets geografiska plats.
     
     Skriv ned dessa värden.  Du behöver dem senare under kursen.
     
     > [!NOTE]
     > SSH används för att få fjärråtkomst till HDInsight-klustret med hjälp av en kommandorad. Det användarnamn och lösenord du använder här används när du ansluter till klustret via SSH. Användarnamnet för SSH måste dessutom vara unikt, eftersom det skapar ett användarkonto på alla HDInsight-klusternoder. Nedan följer några av de kontonamn som är reserverade för användning av tjänsterna i klustret och inte kan användas som SSH-användarnamn:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Mer information om hur du använder SSH med HDInsight finns i följande artiklar:
     > 
     > * [Använd SSH med Linux-baserad Hadoop på HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Använda SSH (PuTTY) med Linux-baserat Hadoop i HDInsight från Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Välj __Jag godkänner villkoren som anges ovan__ och klicka på **OK** och välj sedan __Fäst till instrumentpanelen__

6. Klicka på **Köp**. En ny panel visas med rubriken Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Köra ett Storm Starter-exempel i HDInsight

Exemplen i [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) ingår i HDInsight-klustret. I följande steg ska du köra WordCount-exemplet.

1. Anslut till HDInsight-klustret med hjälp av SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Om du skyddat SSH-användarkontot med lösenord uppmanas du att ange det. Om du använde en offentlig nyckel kan du behöva använda `-i`-parametern för att ange motsvarande privata nyckel. Till exempel `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Mer information om hur du använder SSH med Linux-baserat HDInsight finns i följande artiklar:
   
    * [Använd SSH med Linux-baserad Hadoop på HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Använda SSH (PuTTY) med Linux-baserat Hadoop i HDInsight från Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Använd följande kommando för att starta en exempeltopologi:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > I tidigare versioner av HDInsight är topologins klassnamn `storm.starter.WordCountTopology` i stället för `org.apache.storm.starter.WordCountTopology`.
   
    Exempeltopologin för WordCount (ordräkning) startas i klustret, med ett eget namn för 'wordcount'. Den genererar meningar slumpmässigt och räknar antal förekomster av varje ord i meningarna.
   
    > [!NOTE]
    > När du skickar in dina egna topologier till klustret måste du först kopiera jar-filen som innehåller klustret innan du använder kommandot `storm`. Du kan göra det med hjälp av kommandot `scp` från klienten där filen finns. Till exempel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount-exemplet och andra exempel i Storm Starter ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Om du är intresserad av att visa Storm Starter-exemplens källa finns koden på [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Länken är till Storm 1.0.x, som medföljer HDInsight 3.5. Om du vill välja någon annan version av Storm väljer du version med knappen __Gren__ överst på sidan.

## <a name="monitor-the-topology"></a>Övervaka topologin

Storm-användargränssnittet innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster.

Genomför följande för att övervaka topologin med hjälp av Storm-användargränssnittet:

1. Öppna https://CLUSTERNAME.azurehdinsight.net/stormui i en webbläsare. Istället för **CLUSTERNAME** skriver du namnet på ditt kluster. Användargränssnittet för Storm öppnas.
    
    > [!NOTE]
    > Om du uppmanas att ange ett användarnamn och lösenord, anger du klusteradministratören (admin) och lösenordet du använde när du skapade klustret.

2. Under **Topology summary** (Topologiöversikt) väljer du posten **wordcount** (ordräkning) i kolumnen **Name** (namn). Mer information om topologin visas.
    
    ![Storm-instrumentpanel med Storm Starter-information för WordCount-topologi.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    På sidan finns följande information:
    
    * **Topology stats** (Topologistatistik) – grundläggande information om topologins prestanda ordnad i tidsfönster.
     
        > [!NOTE]
        > När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.

    * **Spouts** (Kanaler) – grundläggande information om kanaler, inklusive det senaste fel som returnerats för varje kanal.
    
    * **Bolts** (Bultar) – grundläggande information om bultar.
    
    * **Topologi configuration** (Topologikonfiguration) – detaljerad information om topologins konfiguration.
     
    På sidan anges också åtgärder som kan göras på topologin:
   
    * **Activate** (Aktivera) – återupptar bearbetningen av en inaktiverad topologi.
    
    * **Deactivate ** (Inaktivera) – pausar en topologi som körs.
    
    * **Rebalance** (Balansera) – justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Det gör att topologin kan justera parallelliteten och kompensera för det ökade/minskade antalet noder i klustret. Mer information finns i [Förstå parallellitet i en Storm-topologi](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Kill** (Avsluta) – avslutar en Storm-topologi efter en angiven tidsgräns.

3. På den här sidan väljer du en post från avsnittet **Spouts** (Kanaler) eller **Bolts** (Bultar). Information om den valda komponenten visas.
   
    ![Storm-instrumentpanelen med information om valda komponenter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    På sidan visas följande information:
   
    * **Spout/Bolt stats** (Statistik för kanaler/bultar) – grundläggande information om komponentens prestanda, ordnad i tidsfönster.
     
        > [!NOTE]
        > När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.
     
    * **Input stats** (Statistik för indata) (endast bultar) – information om komponenter som producerar de data som används av bulten.
    
    * **Output stats** (Statistik för utdata) – information om data som sänds av bulten.
    
    * **Executors** (Utförare) – information om komponentens instanser.
    
    * **Errors** (Fel) – fel som komponenten ger upphov till.

4. När du visar information om en kanal eller en bult väljer du en post i kolumnen **Port** i avsnittet **Executors** (Utförare) för att visa information för en viss komponentinstans.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    I dessa data kan du se att ordet **seven** (sju) förekommer 1493957 gånger. Det är antalet gånger ordet har påträffats sedan topologin startades.

## <a name="stop-the-topology"></a>Stoppa topologin

Gå tillbaka till sidan **Topology summary** (Topologiöversikt) för ordräkningstopologin och välj knappen **Kill** (Avsluta) i avsnittet **Topology actions** (Topologiåtgärder). När du uppmanas ange antal sekunder innan topologin stoppas, anger du 10. När tidsgränsen uppnåtts visas topologin inte längre när du går in på avsnittet **Storm UI** (Storm-användargränssnitt) på instrumentpanelen.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>Nästa steg

I den här kursen om Apache Storm har du fått använda Storm Starter för att lära dig hur du skapar en Storm i HDInsight-klustret och använda Storm-instrumentpanelen till att distribuera, övervaka och hantera Storm-topologier. Härnäst får du lära dig att [Utveckla Java-baserade topologier med Maven](hdinsight-storm-develop-java-topology.md).

Om du redan är bekant med att utveckla Java-baserad topologier och vill distribuera en befintlig topologi till HDInsight kan du läsa [Distribuera och hantera Apache Storm-topologier i HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

Om du är en .NET-utvecklare kan du skapa C#- eller C#/Java-hybridtopologier med Visual Studio. Mer information finns i [Utveckla C#-topologier för Apache Storm på HDInsight med Hadoop-verktyg för Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Se följande exempel för exempeltopologier som kan användas med Storm på HDInsight:

* [Exempeltopologier för Storm på HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO1-->


