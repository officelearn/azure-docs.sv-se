---
title: Distribuera och hantera Apache Storm-topologier i Azure HDInsight
description: Lär dig mer om att distribuera, övervaka och hantera Apache Storm-topologier på Linux-baserade HDInsight Storm-instrumentpanelen. Använda Hadoop-verktyg för Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 61f5f0d0b9e88174f82e960eb5d92db99d0cae71
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582861"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuera och hantera Apache Storm-topologier i Azure HDInsight 

I det här dokumentet lär du dig grunderna för att hantera och övervaka [Apache Storm](http://storm.apache.org/) topologier som körs på Storm i HDInsight-kluster.

> [!IMPORTANT]
> Stegen i den här artikeln kräver ett Linux-baserade Storm på HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Information om distribution och övervakning topologier i Windows-baserade HDInsight finns i [distribuera och hantera Apache Storm-topologier i Windows-baserade HDInsight](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Förutsättningar

* **Ett Linux-baserade Storm på HDInsight-kluster**: se [Kom igång med Apache Storm på HDInsight](apache-storm-tutorial-get-started-linux.md) anvisningar om hur du skapar ett kluster

* (Valfritt) **Bekant med SSH och SCP**: Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Valfritt) **Visual Studio**: Azure SDK 2.5.1 eller senare och Data Lake Tools för Visual Studio. Mer information finns i [Kom igång med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    En av följande versioner av Visual Studio:

  * Visual Studio 2012 med uppdatering 4

  * Visual Studio 2013 med uppdatering 4 eller [Visual Studio Community 2013](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (alla versioner)

  * Visual Studio 2017 (vilken version som helst). Data Lake Tools för Visual Studio 2017 installeras som en del av arbetsbelastningen i Azure.

## <a name="submit-a-topology-visual-studio"></a>Skicka en topologi: Visual Studio

HDInsight-verktyg kan användas för att skicka C#- eller topologier för Storm-kluster. Följande steg använder ett exempelprogram. Information om hur du skapar på med hjälp av HDInsight-verktyg finns i [utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Om du inte redan har installerat den senaste versionen av Data Lake tools för Visual Studio finns i [Kom igång med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Data Lake Tools för Visual Studio kallades HDInsight-verktyg för Visual Studio.
    >
    > Data Lake Tools för Visual Studio som ingår i den __Azure-arbetsbelastningen__ för Visual Studio 2017.

2. Öppna Visual Studio, Välj **filen** > **New** > **projekt**.

3. I den **nytt projekt** dialogrutan Expandera **installerad** > **mallar**, och välj sedan **HDInsight**. Välj i listan över mallar **Storm-exempel**. Skriv ett namn för programmet längst ned i dialogrutan.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. I **Solution Explorer**, högerklicka på projektet och välj **skicka till Storm på HDInsight**.

   > [!NOTE]
   > Om du uppmanas, anger du inloggningsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration kan du logga in på det som innehåller ditt Storm på HDInsight-kluster.

5. Välj ditt Storm på HDInsight-kluster från den **Storm-kluster** listrutan och välj sedan **skicka**. Du kan övervaka om överföringen har genomförts med den **utdata** fönster.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Skicka en topologi: SSH och Storm-kommando

1. Använda SSH för att ansluta till HDInsight-kluster. Ersätt **användarnamn** namnet på din SSH-inloggning. Ersätt **CLUSTERNAME** med namnet på ditt HDInsight-kluster:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Mer information om hur du använder SSH för att ansluta till HDInsight-kluster finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommando för att starta en exempeltopologi:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret. Den här topologin genererar meningar slumpmässigt och räknar förekomster av varje ord i meningarna.

   > [!NOTE]
   > När du skickar in topologin till klustret måste du först kopiera jar-filen som innehåller klustret innan du använder kommandot `storm`. Du kan använda för att kopiera filen till klustret, den `scp` kommando. Till exempel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > WordCount-exemplet och andra exempel i Storm Starter ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Skicka en topologi: programmässigt

Programmässigt kan du distribuera en topologi med hjälp av tjänsten Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) innehåller ett exempel Java-program som visar hur du distribuerar och startar en topologi genom Nimbus-tjänsten.

## <a name="monitor-and-manage-visual-studio"></a>Övervaka och hantera: Visual Studio

När en topologi skickas med hjälp av Visual Studio, den **Storm-topologier** vy visas. Välj topologi i listan att visa information om topologin som körs.

![Övervakare för Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Du kan också visa **Storm-topologier** från **Server Explorer** genom att expandera **Azure** > **HDInsight**, och sedan att högerklicka på ett Storm på HDInsight-kluster och välja **Zobrazit Topologie Stormu**.

Markera formen för kanaler eller bultar att visa information om dessa komponenter. Ett nytt fönster öppnas för varje objekt har valts.

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Inaktivera en topologi pausar den förrän den har avslutats eller återaktiverats. Använd för att utföra dessa åtgärder i __inaktivera__ och __återaktivera__ knappar överst i den __Topology Summary__.

### <a name="rebalance"></a>Balansera om

Ombalansering en topologi gör att systemet kan ändra topologins parallellitet. Om du har ändrat storlek i klustret för att lägga till fler anteckningar kan ombalansering exempelvis en topologi att se de nya noderna.

För att balansera om en topologi, använda den __balansera om__ längst upp på den __Topology Summary__.

> [!WARNING]
> Ombalansering en topologi först inaktiverar du topologin, och sedan distribuerar arbetare jämnt över klustret och sedan slutligen återgår topologin till tillståndet innan ombalansering inträffade. Så om topologin var aktiv, blir aktiva igen. Om det har inaktiverats kan fortfarande det inaktiverad.

### <a name="kill-a-topology"></a>Avsluta en topologi

Storm-topologier fortsätter att köras tills de stoppas eller klustret tas bort. Om du vill stoppa en topologi, använda den __Kill__ längst upp på den __Topology Summary__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Övervaka och hantera: SSH och Storm-kommando

Den `storm` verktyget kan du arbeta med topologier som körs från kommandoraden. Använd `storm -h` för en fullständig lista över kommandon.

### <a name="list-topologies"></a>Lista topologier

Använd följande kommando för att lista alla topologier som körs:

    storm list

Det här kommandot returnerar information liknande följande text:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Inaktivera en topologi pausar den förrän den har avslutats eller återaktiverats. Använd följande kommando för att inaktivera och återaktivera:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Avsluta en topologi som körs

Storm-topologier som en gång startats, fortsätta köra tills de stoppas. Om du vill stoppa en topologi, använder du följande kommando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Balansera om

Ombalansering en topologi gör att systemet kan ändra topologins parallellitet. Om du har ändrat storlek i klustret för att lägga till fler anteckningar kan ombalansering exempelvis en topologi att se de nya noderna.

> [!WARNING]
> Ombalansering en topologi först inaktiverar du topologin, och sedan distribuerar arbetare jämnt över klustret och sedan slutligen återgår topologin till tillståndet innan ombalansering inträffade. Så om topologin var aktiv, blir aktiva igen. Om det har inaktiverats kan fortfarande det inaktiverad.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Övervaka och hantera: Storm UI

Storm-användargränssnittet innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster. Om du vill visa Storm-Användargränssnittet, Använd en webbläsare för att öppna **https://CLUSTERNAME.azurehdinsight.net/stormui**, där **CLUSTERNAME** är namnet på klustret.

> [!NOTE]
> Om du uppmanas att ange ett användarnamn och lösenord, anger du klusteradministratören (admin) och lösenordet du använde när du skapade klustret.

### <a name="main-page"></a>Huvudsida

Huvudsidan för Storm-Användargränssnittet innehåller följande information:

* **Klustersammanfattning**: grundläggande information om Storm-kluster.
* **Sammanfattning av topologi**: en lista över topologier som körs. Använd länkarna i det här avsnittet om du vill visa mer information om specifika topologier.
* **Övervakaren sammanfattning**: Information om Storm-övervakaren.
* **Nimbus configuration**: Nimbus-konfigurationen för klustret.

### <a name="topology-summary"></a>Sammanfattning av topologi

Att välja en länk från den **Topology summary** avsnittet visar följande information om topologin:

* **Sammanfattning av topologi**: grundläggande information om topologin.
* **Topologi åtgärder**: hanteringsåtgärder som du kan utföra för topologin.

  * **Aktivera**: återupptar bearbetningen av en inaktiverad topologi.
  * **Inaktivera**: pausar en topologi som körs.
  * **Balansera om**: justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Den här åtgärden gör att topologin kan justera parallelliteten och kompensera för ökat eller minskat antalet noder i klustret.

    Mer information finns i <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">förstå parallellitet i en Apache Storm-topologi</a>.
  * **Avsluta**: avslutar en Storm-topologi efter en angiven tidsgräns.
* **Topology stats**: statistik om topologin. Om du vill ange en mer specifik tidsram för de övriga objekten på sidan i länkarna i den **fönstret** kolumn.
* **Spouts**: kanaler som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika kanaler.
* **Bolts**: bultar som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika bultar.
* **Topologikonfiguration**: konfigurationen av den valda topologin.

### <a name="spout-and-bolt-summary"></a>Spout och bult sammanfattning

Att välja en kanal från den **Spouts** eller **Bolts** avsnitt visar följande information om det markerade objektet:

* **Översikt över komponenterna**: grundläggande information om den kanal eller en bult.
* **Spout/bult stats**: statistik om den kanal eller en bult. Om du vill ange en mer specifik tidsram för de övriga objekten på sidan i länkarna i den **fönstret** kolumn.
* **Input stats** (endast bultar): Information om indataströmmar som används av bulten.
* **Utdata stats**: Information om hur du strömmar som orsakats av den kanal eller en bult.
* **Executors**: Information om instanser av den kanal eller en bult. Välj den **Port** post för en specifik executor att visa en logg över diagnostikinformation produceras för den här instansen.
* **Fel**: någon information om fel för den kanal eller en bult.

## <a name="monitor-and-manage-rest-api"></a>Övervaka och hantera: REST API

Storm-Användargränssnittet är byggt på REST API, så att du kan utföra liknande övervaknings- och funktioner med hjälp av REST-API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Apache Storm UI REST API](http://storm.apache.org/releases/current/STORM-UI-REST-API.html). Följande information gäller med hjälp av REST-API med Apache Storm på HDInsight.

> [!IMPORTANT]
> Storm REST API är inte allmänt tillgängliga via internet och måste användas med en SSH-tunnel till HDInsight-klustrets huvudnod. Mer information om hur du skapar och använder en SSH-tunnel finns i [använda SSH-tunnlar till Apache Ambari-webbgränssnittet, resurshanteraren, JobHistory, NameNode, Apache Oozie och andra web UIs](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Bas-URI

Bas-URI för REST-API: et på Linux-baserade HDInsight-kluster är tillgängliga på klustrets huvudnod vid **https://HEADNODEFQDN:8744/api/v1/**. Domännamnet för huvudnoden genereras när klustret skapas och är inte statiska.

Du kan hitta det fullständigt kvalificerade domännamnet (FQDN) för klustrets huvudnod på flera olika sätt:

* **Från en SSH-session**: Använd kommandot `headnode -f` från en SSH-session till klustret.
* **Från Ambari Web**: Välj **Services** högst upp på sidan Välj **Storm**. Från den **sammanfattning** fliken **Storm UI Server**. Det fullständiga Domännamnet för den nod som är värd för Storm-Användargränssnittet och REST API visas överst på sidan.
* **Från Ambari REST API**: Använd kommandot `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` att hämta information om den nod som Storm-Användargränssnittet och REST API körs på. Ersätt **CLUSTERNAME** med klustrets namn. När du uppmanas, anger du lösenordet för inloggningskonto (admin). I svaret innehåller posten ”värddatornamn” FQDN för noden.

### <a name="authentication"></a>Autentisering

REST API-begäranden måste använda **grundläggande autentisering**, så att du använder HDInsight-kluster databasadministratörens namn och lösenord.

> [!NOTE]
> Eftersom grundläggande autentisering skickas i klartext, bör du **alltid** använder HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returvärden

Information som returneras från REST-API: et kan bara användas från i klustret. Till exempel det fullständigt kvalificerade domännamnet (FQDN) returneras för [Apache ZooKeeper](https://zookeeper.apache.org/) servrar kan inte nås från Internet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [utveckla Java-baserade topologier med Apache Maven](apache-storm-develop-java-topology.md).

En lista över flera exempeltopologier finns i [exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md).
