---
title: "Komma igång med Apache Storm i Azure HDInsight | Microsoft Docs"
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
ms.date: 03/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5d74f2c130eeddb1022acf9673c6a2006af2db58
ms.lasthandoff: 03/25/2017

---
#<a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-linux-based-hdinsight"></a>Kom igång med Storm Starter-exempel för analys av stordata i Linux-baserade HDInsight

Apache Storm är ett skalbart, feltolerant och distribuerat system för beräkningar i realtid för bearbetning av dataströmmar. Du kan skapa ett molnbaserat Storm-kluster som utför analyser av stordata i realtid med Storm på Azure HDInsight.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i avsnittet om [utfasningen av HDInsight i Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Krav

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **kunskap om SSH och SCP**. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Skapa ett Storm-kluster

Använd följande steg om du vill skapa en Storm i HDInsight-klustret:

1. Från [Azure Portal](https://portal.azure.com) väljer du **+ NY**, **Intelligence + Analytics** och väljer sedan **HDInsight**.
   
    ![Skapa ett HDInsight-kluster](./media/hdinsight-apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. Ange följande information i bladet **Grundläggande inställningar**:

    * **Klusternamn**: Namnet på HDInsight-klustret.
    * **Prenumeration**: Välj den prenumeration som du vill använda.
    * **Användarnamn för klusterinloggning** och **Lösenord för klusterinloggning**: Inloggningen vid åtkomst till klustret via HTTPS. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till tjänster som Ambari-webbgränssnittet eller REST API.
    * **Secure Shell-användarnamn (SSH)**: Den inloggning som används vid åtkomst till klustret via SSH. Som standard är lösenordet detsamma som lösenordet för klusterinloggning.
    * **Resursgrupp**: Resursgruppen som klustret ska skapas i.
    * **Plats**: Azure-region som klustret ska skapas i.
   
    ![Välj en prenumeration](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Välj **Klustertyp** och ange följande värden på bladet **Klusterkonfiguration**:
   
    * **Typ av kluster**: Storm

    * **Operativsystem**: Linux

    * **Version**: Storm 1.0.1 (HDI 3.5)

    * **Klusternivå**: Standard
     
    Slutligen kan spara inställningarna med kommandot **Välj**.
     
    ![Välj klustertyp](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. När du har valt klustertypen anger du klustertypen med hjälp av knappen __Välj__. Använd sedan knappen __Nästa__ och slutföra den grundläggande konfigurationen.

5. Gå till bladet **Lagring** och välj eller skapa ett lagringskonto. Lämna övriga fält på det här bladet på standardvärden för stegen i det här dokumentet. Spara lagringskonfigurationen genom att klicka på __Nästa__.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. Gå till bladet **Sammanfattning** och granska konfigurationen för klustret. Använd länkarna __Redigera__ om du behöver ändra eventuella inställningar som är felaktiga. Till sist skapar du klustret genom att klicka på Skapa.
   
    ![Sammanfattning av klusterkonfiguration](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Det kan ta upp till 20 minuter att skapa klustret.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Köra ett Storm Starter-exempel i HDInsight

1. Anslut till HDInsight-klustret med hjälp av SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Om du skyddat SSH-användarkontot med lösenord uppmanas du att ange det. Om du använde en offentlig nyckel kan du behöva använda `-i`-parametern för att ange motsvarande privata nyckel. Till exempel `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att starta en exempeltopologi:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > I tidigare versioner av HDInsight är topologins klassnamn `storm.starter.WordCountTopology` i stället för `org.apache.storm.starter.WordCountTopology`.
   
    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret, med ett eget namn för 'wordcount'. Den genererar meningar slumpmässigt och räknar antal förekomster av varje ord i meningarna.
   
    > [!NOTE]
    > När du skickar in dina egna topologier till klustret måste du först kopiera jar-filen som innehåller klustret innan du använder kommandot `storm`. Använd `scp`-kommandot för att kopiera filen. Till exempel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount-exemplet och andra exempel i Storm Starter ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Om du är intresserad av att visa Storm Starter-exemplens källa finns koden på [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Länken är till Storm 1.0.x, som medföljer HDInsight 3.5. Om du vill välja någon annan version av Storm väljer du version med knappen __Gren__ överst på sidan.

## <a name="monitor-the-topology"></a>Övervaka topologin

Storm-användargränssnittet innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster.

Genomför följande för att övervaka topologin med hjälp av Storm-användargränssnittet:

1. Öppna https://CLUSTERNAME.azurehdinsight.net/stormui i en webbläsare för att visa användargränssnittet för Storm. Ersätt **CLUSTERNAME** med namnet på klustret.
    
    > [!NOTE]
    > Om du uppmanas att ange ett användarnamn och lösenord, anger du klusteradministratören (admin) och lösenordet du använde när du skapade klustret.

2. Under **Topology summary** (Topologiöversikt) väljer du posten **wordcount** (ordräkning) i kolumnen **Name** (namn). Detta visar information om topologin.
    
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
    
    * **Deactivate** (Inaktivera) – pausar en topologi som körs.
    
    * **Rebalance** (Balansera) – justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Ombalansering justerar parallelliteten och kompenserar för det ökade/minskade antalet noder i klustret. Mer information finns i [Förstå parallellitet i en Storm-topologi](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Kill** (Avsluta) – avslutar en Storm-topologi efter en angiven tidsgräns.

3. På den här sidan väljer du en post från avsnittet **Spouts** (Kanaler) eller **Bolts** (Bultar). Detta visar information om den valda komponenten.
   
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
   
    I det här exemplet förekommer ordet **seven** (sju) 1 493 957 gånger. Det är antalet gånger ordet har påträffats sedan topologin startades.

## <a name="stop-the-topology"></a>Stoppa topologin

Gå tillbaka till sidan **Topology summary** (Topologiöversikt) för ordräkningstopologin och välj knappen **Kill** (Avsluta) i avsnittet **Topology actions** (Topologiåtgärder). När du uppmanas ange antal sekunder innan topologin stoppas, anger du 10. När tidsgränsen uppnåtts visas topologin inte längre när du går in på avsnittet **Storm UI** (Storm-användargränssnitt) på instrumentpanelen.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Nästa steg

I den här självstudien för Apache Storm har du lärt dig grunderna för att arbeta med Storm på HDInsight. Härnäst får du lära dig att [Utveckla Java-baserade topologier med Maven](hdinsight-storm-develop-java-topology.md).

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

