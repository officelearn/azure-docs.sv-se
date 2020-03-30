---
title: 'Snabbstart: Skapa/hantera Apache Storm-topologi - Azure HDInsight'
description: I snabbstarten kan du lära dig hur du skapar och övervakar en Apache Storm-topologi i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241217"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Snabbstart: Skapa och övervaka en Apache Storm-topologi i Azure HDInsight

Apache Storm är ett skalbart, feltolerant och distribuerat system för beräkningar i realtid för bearbetning av dataströmmar. Du kan skapa ett molnbaserat Storm-kluster som utför analyser av stordata i realtid med Storm på Azure HDInsight.

I den här snabbstarten använder du ett exempel från Apache [stormstartprojekt](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) för att skapa och övervaka en Apache Storm-topologi till ett befintligt Apache Storm-kluster.

## <a name="prerequisites"></a>Krav

* Ett Apache Storm-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **klustertyp**.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Skapa topologin

1. Anslut till stormklustret. Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med namnet på Storm-klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WordCount-exemplet** finns i ditt HDInsight-kluster på `/usr/hdp/current/storm-client/contrib/storm-starter/`. Topologin genererar slumpmässiga meningar och räknar hur många gånger ord förekommer. Använd följande kommando för **wordcount** att starta wordcount-topologin i klustret:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Övervaka topologin

Storm tillhandahåller ett webbgränssnitt för att arbeta med löptopologier och ingår i ditt HDInsight-kluster.

Genomför följande för att övervaka topologin med hjälp av Storm-användargränssnittet:

1. Visa Storm-användargränssnittet genom att öppna en webbläsare på `https://CLUSTERNAME.azurehdinsight.net/stormui`. Ersätt `CLUSTERNAME` med namnet på klustret.

2. Markera **wordcount-posten** i kolumnen **Namn** under **Topologisammanfattning.** Detta visar information om topologin.

    ![Storm Dashboard med storm-starter, topologisk information om WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Den nya sidan innehåller följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Topologi statistik|Grundläggande information om topologiprestanda, organiserad i tidsfönster. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Pipar|Grundläggande information om pipar, inklusive det senaste felet som returnerades av varje pip.|
    |Bultar|Grundläggande information om bultar.|
    |Topologi konfiguration|Detaljerad information om topologikonfigurationen.|
    |Aktivera|Återupptar bearbetningen av en inaktiverad topologi.|
    |Inaktivera|Pausar en löpande topologi.|
    |Balansera|Justerar topologins parallellism. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Ombalansering justerar parallelliteten och kompenserar för det ökade/minskade antalet noder i klustret. Mer information finns [i Förstå parallellismen för en Apache Storm-topologi](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Döda|Avslutar en Storm-topologi efter den angivna tidsgränsen.|

3. På den här sidan väljer du en post från avsnittet **Spouts** (Kanaler) eller **Bolts** (Bultar). Detta visar information om den valda komponenten.

    ![Storm-instrumentpanelen med information om valda komponenter.](./media/apache-storm-quickstart/hdi-component-summary.png)

    På den nya sidan visas följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Spout / Bolt statistik|Grundläggande information om komponentprestanda, ordnad i tidsfönster. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Indatastatistik (endast bult)|Information om komponenter som producerar data som förbrukas av bulten.|
    |Utdatastatistik|Information om data som avges av denna bult.|
    |Testamentsexekutorer|Information om instanser av den här komponenten.|
    |Fel|Fel som orsakas av den här komponenten.|

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

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett exempel från Apache [stormstartprojekt](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) för att skapa och övervaka en Apache Storm-topologi till ett befintligt Apache Storm-kluster. Gå vidare till nästa artikel för att lära dig grunderna i att hantera och övervaka Apache Storm topologier.

> [!div class="nextstepaction"]
>[Distribuera och hantera Apache Storm-topologier på Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)