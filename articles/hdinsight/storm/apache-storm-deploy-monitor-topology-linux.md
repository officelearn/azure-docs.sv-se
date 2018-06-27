---
title: Distribuera och hantera Apache Storm-topologier på Linux-baserade HDInsight | Microsoft Docs
description: Lär dig mer om att distribuera, övervaka och hantera Apache Storm-topologier med Storm-instrumentpanelen på Linux-baserade HDInsight. Använda Hadoop-verktyg för Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: larryfr
ms.openlocfilehash: 8b4584e9ccd17eba385f2068709f5bbaea6df20b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019967"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Distribuera och hantera Apache Storm-topologier på HDInsight

I detta dokument, lär du dig grunderna för att hantera och övervaka Storm-topologier som körs på Storm på HDInsight-kluster.

> [!IMPORTANT]
> Stegen i den här artikeln kräver ett Linux-baserade Storm på HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Mer information om distribuera och övervaka topologier på Windows-baserade HDInsight finns [distribuera och hantera Apache Storm-topologier på Windows-baserade HDInsight](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Förutsättningar

* **En Linux-baserade Storm på HDInsight-kluster**: se [Kom igång med Apache Storm på HDInsight](apache-storm-tutorial-get-started-linux.md) anvisningar om hur du skapar ett kluster

* (Valfritt) **Kunskap om SSH och SCP**: Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Valfritt) **Visual Studio**: Azure SDK 2.5.1 eller senare och Data Lake-verktyg för Visual Studio. Mer information finns i [komma igång med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    En av följande versioner av Visual Studio:

  * Visual Studio 2012 med Update 4

  * Visual Studio 2013 med Update 4 eller [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (någon utgåva)

  * 2017 för Visual Studio (någon utgåva). Data Lake-verktyg för Visual Studio 2017 installeras som en del av Azure-arbetsbelastning.

## <a name="submit-a-topology-visual-studio"></a>Skicka en topologi: Visual Studio

HDInsight-verktyg kan användas för att skicka eller hybrid C#-topologier till ditt Storm-kluster. Följande steg använder ett exempelprogram. Information om hur du skapar på med HDInsight Tools finns [utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Om du inte redan har installerat den senaste versionen av Data Lake-verktyg för Visual Studio finns [komma igång med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Data Lake-verktyg för Visual Studio kallades HDInsight Tools för Visual Studio.
    >
    > Data Lake-verktyg för Visual Studio ingår i den __Azure arbetsbelastning__ för Visual Studio-2017.

2. Öppna Visual Studio, markera **filen** > **ny** > **projekt**.

3. I den **nytt projekt** dialogrutan Expandera **installerad** > **mallar**, och välj sedan **HDInsight**. Välj i listan över mallar **Storm exempel**. Ange ett namn för programmet längst ned i dialogrutan.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. I **Solution Explorer**, högerklicka på projektet och välj **skicka till Storm på HDInsight**.

   > [!NOTE]
   > Om du uppmanas ange inloggningsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration kan du logga in på den tabell som innehåller ditt Storm på HDInsight-kluster.

5. Välj ditt Storm på HDInsight-kluster från de **Storm-kluster** listrutan och välj sedan **skicka**. Du kan övervaka om överföring har genomförts med den **utdata** fönster.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Skicka en topologi: SSH och Storm-kommando

1. Använda SSH för att ansluta till HDInsight-klustret. Ersätt **användarnamn** namnet på SSH-inloggning. Ersätt **KLUSTERNAMN** med ditt HDInsight-klustrets namn:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Mer information om hur du använder SSH för att ansluta till ditt HDInsight-kluster finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommando för att starta en exempeltopologi:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret. Den här topologin genererar meningar slumpmässigt och räknar förekomst av varje ord i meningarna.

   > [!NOTE]
   > När du skickar in topologin till klustret måste du först kopiera jar-filen som innehåller klustret innan du använder kommandot `storm`. Du kan använda för att kopiera filen till klustret i `scp` kommando. Till exempel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > WordCount-exemplet och andra exempel i Storm Starter ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Skicka en topologi: programmässigt

Programmässigt kan du distribuera en topologi som använder Nimbus-tjänsten. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) ger ett exempel på Java-program som visar hur du distribuerar och startar en topologi via Nimbus-tjänsten.

## <a name="monitor-and-manage-visual-studio"></a>Övervaka och hantera: Visual Studio

När en topologi skickas med Visual Studio den **Storm-topologier** vyn visas. Välj topologi från listan för att visa information om topologin som körs.

![Övervakare för Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Du kan också visa **Storm-topologier** från **Server Explorer** genom att expandera **Azure** > **HDInsight**, och sedan Högerklicka på ett Storm på HDInsight-kluster och välja **visa Storm-topologier**.

Markera formen för kanaler eller bultar att visa information om dessa komponenter. För varje vald öppnas ett nytt fönster.

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Inaktivera en topologi pausar den förrän den har avslutats eller återaktivera. Använd för att utföra dessa åtgärder i __inaktivera__ och __återaktivera__ knappar överst i den __Topology Summary__.

### <a name="rebalance"></a>Balansera

Ombalansering en topologi kan ändra topologins parallellitet. Om du har ändrat storlek klustret för att lägga till anteckningar kan ombalansering exempelvis en topologi att se de nya noderna.

För att balansera en topologi använder den __ombalansera__ längst upp i den __Topology Summary__.

> [!WARNING]
> Ombalansering en topologi först inaktiverar topologi, sedan distribuerar arbetare jämnt över klustret och sedan returnerar slutligen topologin till tillståndet den var i innan ombalansering inträffade. Så om topologin var aktiv blir aktiva igen. Om det har inaktiverats, förblir den inaktiverade.

### <a name="kill-a-topology"></a>Avsluta en topologi

Storm-topologier fortsätta köras förrän de har stoppats eller ta bort klustret. Om du vill stoppa en topologi använder den __Kill__ längst upp i den __Topology Summary__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Övervaka och hantera: SSH och Storm-kommando

Den `storm` verktyget kan du arbeta med topologier som körs från kommandoraden. Använd `storm -h` för en fullständig lista över kommandon.

### <a name="list-topologies"></a>Lista över topologier

Använd följande kommando för att lista alla topologier som körs:

    storm list

Det här kommandot returnerar information liknande följande text:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Inaktivera en topologi pausar den förrän den har avslutats eller återaktivera. Använd följande kommando för att inaktivera och återaktivera:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Avsluta en topologi som körs

Storm-topologier, startas en gång, fortsätta köras tills stoppades. Om du vill stoppa en topologi, använder du följande kommando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Balansera

Ombalansering en topologi kan ändra topologins parallellitet. Om du har ändrat storlek klustret för att lägga till anteckningar kan ombalansering exempelvis en topologi att se de nya noderna.

> [!WARNING]
> Ombalansering en topologi först inaktiverar topologi, sedan distribuerar arbetare jämnt över klustret och sedan returnerar slutligen topologin till tillståndet den var i innan ombalansering inträffade. Så om topologin var aktiv blir aktiva igen. Om det har inaktiverats, förblir den inaktiverade.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Övervaka och hantera: Storm UI

Storm-användargränssnittet innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster. Använda en webbläsare för att visa Användargränssnittet för Storm, öppna **https://CLUSTERNAME.azurehdinsight.net/stormui**, där **KLUSTERNAMN** är namnet på klustret.

> [!NOTE]
> Om du uppmanas att ange ett användarnamn och lösenord, anger du klusteradministratören (admin) och lösenordet du använde när du skapade klustret.

### <a name="main-page"></a>Huvudsida

Huvudsidan för Storm-Användargränssnittet innehåller följande information:

* **Översikt över kluster**: grundläggande information om Storm-kluster.
* **Översikt över topologi**: en lista över topologier som körs. Använd länkarna i det här avsnittet om du vill visa mer information om specifika topologier.
* **Översikt över handledarens**: Information om Storm-administratören.
* **Nimbus configuration**: Nimbus-konfiguration för klustret.

### <a name="topology-summary"></a>Topologi sammanfattning

Att välja en länk från den **Topology summary** avsnitt visar följande information om topologin:

* **Översikt över topologi**: grundläggande information om topologin.
* **Topologi åtgärder**: hanteringsåtgärder som du kan utföra för topologin.

  * **Aktivera**: återupptar bearbetningen av en inaktiverad topologi.
  * **Inaktivera**: pausar en topologi som körs.
  * **Balansera**: justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Den här åtgärden gör att topologin kan justera parallelliteten och kompensera för ökar eller minskar antalet noder i klustret.

    Mer information finns i <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Förstå parallellitet i en Storm-topologi</a>.
  * **Avsluta**: avslutar en Storm-topologi efter en angiven tidsgräns.
* **Topology stats**: statistik om topologin. Om du vill ange tidsperioden för de återstående posterna på sidan i länkarna i den **fönstret** kolumn.
* **Spouts**: kanaler som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika kanaler.
* **Bolts**: bultar som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika bultar.
* **Topologi configuration**: konfigurationen av den valda topologin.

### <a name="spout-and-bolt-summary"></a>Kanal och bult sammanfattning

Att välja en kanal från den **Spouts** eller **Bolts** avsnitt visar följande information om det markerade objektet:

* **Sammanfattning**: grundläggande information om den kanal eller en bult.
* **Kanal/bult stats**: statistik om den kanal eller en bult. Om du vill ange tidsperioden för de återstående posterna på sidan i länkarna i den **fönstret** kolumn.
* **Input stats** (endast bultar): Information om de inkommande dataströmmar som används av bulten.
* **Utdata stats**: Information om strömmar sänds av den kanal eller en bult.
* **Executors**: Information om instanser av den kanal eller en bult. Välj den **Port** post för en specifik utförare att visa en logg över diagnostikinformation produceras för den här instansen.
* **Fel**: någon information om fel för den kanal eller en bult.

## <a name="monitor-and-manage-rest-api"></a>Övervaka och hantera: REST API

Storm-Användargränssnittet är byggt på REST-API, så att du kan göra liknande hantering och övervakning av funktioner med hjälp av REST API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Storm UI REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Följande information gäller med hjälp av REST-API med Apache Storm på HDInsight.

> [!IMPORTANT]
> Storm REST API är inte offentligt tillgängliga via internet och måste användas med en SSH-tunnel till HDInsight-klustrets huvudnod. Mer information om hur du skapar och använder en SSH-tunnel finns [Använd SSH-tunnlar för att komma åt Ambari-webbgränssnittet, resurshanteraren, jobbhistorik, NameNode, Oozie och andra webb-gränssnittet för](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Bas-URI

Bas-URI för REST-API på Linux-baserade HDInsight-kluster finns i huvudnod vid **https://HEADNODEFQDN:8744/api/v1/**. Domännamnet för huvudnoden genereras när klustret skapas och är inte statisk.

Du hittar det fullständigt kvalificerade domännamnet (FQDN) för klustrets huvudnod på flera olika sätt:

* **Från en SSH-session**: Använd kommandot `headnode -f` från en SSH-session till klustret.
* **Ambari Web**: Välj **Services** högst upp på sidan Välj **Storm**. Från den **sammanfattning** väljer **Storm UI Server**. FQDN för den nod som värd för Storm-Användargränssnittet och REST API visas överst på sidan.
* **Från Ambari REST API**: Använd kommandot `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` att hämta information om den nod som Storm-Användargränssnittet och REST API körs på. Ersätt **KLUSTERNAMN** med klustrets namn. När du uppmanas, ange lösenordet för inloggningskontot (admin). I svaret innehåller posten ”värddatornamn” FQDN för noden.

### <a name="authentication"></a>Autentisering

REST API-begäranden måste använda **grundläggande autentisering**, så att du använder HDInsight-klustrets administratörsnamn och lösenord.

> [!NOTE]
> Eftersom grundläggande autentisering skickas i klartext, bör du **alltid** använder HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returnera värden

Information som returneras från REST-API kan endast användas från inom klustret. Till exempel är det fullständigt domännamnet (FQDN) returneras för Zookeeper-servrar inte tillgänglig från Internet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [utveckla Java-baserade topologier med Maven](apache-storm-develop-java-topology.md).

En lista över flera exempeltopologier finns [exempeltopologier för Storm på HDInsight](apache-storm-example-topology.md).
