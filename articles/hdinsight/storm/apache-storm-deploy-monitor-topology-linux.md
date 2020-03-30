---
title: Distribuera och hantera Apache Storm-topologier på Azure HDInsight
description: Lär dig hur du distribuerar, övervakar och hanterar Apache Storm-topologier med hjälp av Storm Dashboard på Linux-baserad HDInsight. Använd Hadoop-verktyg för Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271907"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuera och hantera Apache Storm-topologier på Azure HDInsight

I det här dokumentet lär du dig grunderna i att hantera och övervaka Apache Storm-topologier som körs på Storm på HDInsight-kluster. [Apache Storm](https://storm.apache.org/)

## <a name="prerequisites"></a>Krav

* Ett Apache Storm-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **klustertyp**.

* (Valfritt) Förtrogenhet med Secure Shell (SSH) och Secure Copy (SCP). Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Valfritt) Visual Studio, Azure SDK 2.5.1 eller nyare och DataSjöverktyg för Visual Studio. Mer information finns i [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Skicka en topologi med Visual Studio

Du kan använda DataSjöverktyg för Visual Studio för att skicka C# eller hybridtopologier till stormklustret. I följande steg används ett exempelprogram. Information om skapande av topologi med hjälp av DataSjöverktyg finns i [Apache Storm-topologier med Visual Studio och C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Om du inte redan har installerat den senaste versionen av DataSjöverktygen för Visual Studio läser du [Använda datasjöverktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Azure Data Lake och Stream Analytics Tools kallades tidigare HDInsight Tools för Visual Studio.
    >
    > Azure Data Lake och Stream Analytics Tools för Visual Studio ingår i **Azure-utvecklingsarbetsbelastningen** för Visual Studio 2019.

1. Starta Visual Studio.

1. Välj Skapa ett **nytt projekt**i **startfönstret** .

1. Markera sökrutan i fönstret **Skapa ett nytt** `Storm`projekt och skriv . Välj sedan **Stormprov** i resultatlistan och välj **Nästa**.

1. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn**och går till eller skapar en **plats** att spara det nya projektet i. Välj sedan **Skapa**.

    ![Konfigurera det nya projektfönstret Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Högerklicka på **Azure** från **Server Explorer**och välj Anslut till **Microsoft Azure-prenumeration...** och slutför inloggningsprocessen.

1. Högerklicka på projektet från **Solution Explorer**och välj Skicka till Storm **på HDInsight**.

    > [!NOTE]  
    > Om du uppmanas till det anger du inloggningsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration loggar du in på den som innehåller ditt Storm på HDInsight-klustret.

1. Välj listan Storm på HDInsight under listrutan **Storm-kluster** i dialogrutan **Skicka topologi** och välj sedan **Skicka**. Du kan övervaka om överföringen lyckas genom att visa **utdatafönstret.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Skicka in en topologi med kommandot SSH och storm

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från ssh-sessionen använder du följande kommando för att starta **WordCount-exempeltopologin:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret. Den här topologin genererar slumpmässigt meningar och räknar sedan förekomsten av varje ord i meningarna.

    > [!NOTE]  
    > När du skickar topologi till klustret måste du först kopiera `storm` JAR-filen som innehåller klustret innan du använder kommandot. Om du vill kopiera filen till `scp` klustret kan du använda kommandot. Ange till exempel `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > *WordCount-exemplet* och andra stormstartexempel finns redan `/usr/hdp/current/storm-client/contrib/storm-starter/`i klustret på .

## <a name="submit-a-topology-programmatically"></a>Skicka in en topologi programmatiskt

Du kan programmatiskt distribuera en topologi med hjälp av Nimbus-tjänsten. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)innehåller ett exempel på Java-program som visar hur du distribuerar och startar en topologi via Nimbus-tjänsten.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Övervaka och hantera en topologi i Visual Studio

När du skickar in en topologi med Visual Studio visas fönstret **Storm Topologies View.** Välj topologin i listan om du vill visa information om den löpande topologin.

![Övervaka topologi, stormtopologier vyfönster, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Du kan också visa **Storm Topologies** från **Server Explorer**. Expandera **Azure** > **HDInsight**, högerklicka på ett Storm-kluster på HDInsight och välj sedan Visa **stormtopologier**.

Markera formen för piparna eller bultarna om du vill visa information om dessa komponenter. Ett knapptips med komponentinformation visas för det markerade objektet.

### <a name="deactivate-and-reactivate-a-topology"></a>Inaktivera och återaktivera en topologi

Om du inaktiverar en topologi pausas den tills topologin dödas eller återaktiveras. Om du vill utföra dessa åtgärder använder du knapparna **Inaktivera** och **Återaktivera** i **åtgärdsområdet** högst upp i fönstret **Storm Topologies View.**

### <a name="rebalance-a-topology"></a>Balansera om en topologi

Ombalansering en topologi gör det möjligt för systemet att revidera parallellism av topologin. Om du till exempel har återskapat klustret för att lägga till fler anteckningar, tillåter ombalansering en topologi för att se de nya noderna.

Om du vill balansera om en topologi använder du knappen **Balansera om** i **åtgärdsområdet** i fönstret **Storm Topologies View.**

> [!WARNING]  
> Ombalansering en topologi inaktiverar topologin, omfördelar arbetare jämnt över klustret och returnerar sedan topologin till det tillstånd den befann sig i innan ombalansering inträffade. Om topologin var aktiv blir den aktiv igen. Om topologin inaktiverades förblir den inaktiverad.

### <a name="kill-a-running-topology"></a>Döda en löpande topologi

Stormtopologier fortsätter att köras tills de stoppas eller klustret tas bort. Om du vill stoppa en topologi använder du knappen **Döda** i **åtgärdsområdet.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Övervaka och hantera en topologi med kommandot SSH och Storm

Verktyget `storm` kan du arbeta med att köra topologier från kommandoraden. Används `storm -h` för en fullständig lista med kommandon.

### <a name="list-topologies"></a>Lista topologier

Använd följande kommando för att lista alla löptopologier:

```shell
storm list
```

Det här kommandot returnerar information liknande följande text:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Inaktivera och återaktivera en topologi

Om du inaktiverar en topologi pausas den tills topologin dödas eller återaktiveras. Använd följande kommandon för att inaktivera eller återaktivera:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Döda en löpande topologi

Stormtopologier, en gång började, fortsätta springa tills stoppas. Om du vill stoppa en topologi använder du följande kommando:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Balansera om en topologi

Ombalansering en topologi gör det möjligt för systemet att revidera parallellism av topologin. Om du till exempel har återskapat klustret för att lägga till fler anteckningar, tillåter ombalansering en topologi för att se de nya noderna.

> [!WARNING]  
> Ombalansering en topologi inaktiverar topologin, omfördelar arbetare jämnt över klustret och returnerar sedan topologin till det tillstånd den befann sig i innan ombalansering inträffade. Om topologin var aktiv blir den aktiv igen. Om den inaktiverades förblir den inaktiverad.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Övervaka och hantera en topologi med hjälp av stormgränssnittet

Storm UI tillhandahåller ett webbgränssnitt för att arbeta med att köra topologier, och det ingår i ditt HDInsight-kluster. Om du vill visa användargränssnittet Storm `https://CLUSTERNAME.azurehdinsight.net/stormui`använder du en webbläsare för att öppna , där *CLUSTERNAME* är namnet på klustret.

> [!NOTE]  
> Om du uppmanas att ange ett användarnamn och lösenord anger du användarnamn och lösenord för klusteradministratören som du använde när du skapade klustret.

### <a name="storm-ui-main-page"></a>Huvudsida för stormgränssnitt

Huvudsidan i användargränssnittet för Storm innehåller följande information:

| Section | Beskrivning |
| --- | --- |
| Klustersammanfattning| Grundläggande information om stormklustret. |
| Nimbus sammanfattning | En lista över grundläggande Nimbus information. |
| Sammanfattning av topologi | En lista över löptopologier. Om du vill visa mer information om en viss topologi markerar du dess länk i kolumnen **Namn.** |
| Sammanfattning av handledare | Information om Storm-chefen. Om du vill visa de arbetsresurser som är associerade med en viss övervakare väljer du dess länk i kolumnen **Värd** eller **ID.** |
| Nimbus konfiguration | Nimbus-konfiguration för klustret. |

Huvudsidan för Storm UI liknar den här webbsidan:

![Huvudsida, Storm UI, Apache Storm-topologier, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Sammanfattning av topologi

Om du väljer en länk i sammanfattningsavsnittet **för topologin** visas följande information om topologin:

| Section | Beskrivning |
| --- | --- |
| Sammanfattning av topologi | Grundläggande information om topologin. |
| Topologi åtgärder| Hanteringsåtgärder som du kan göra för topologin. De tillgängliga åtgärderna beskrivs senare i det här avsnittet. |
| Topologi statistik | Statistik om topologin. Om du vill ange tidsramen för en post i det här avsnittet markerar du dess länk i kolumnen **Fönster.** |
| Pipar *(tidsram)* | Piparna som används av topologin. Om du vill visa mer information om en viss pip markerar du dess länk i **ID-kolumnen.** |
| Bultar *(tidsram)* | Bultarna som används av topologin. Om du vill visa mer information om en viss bult markerar du dess länk i **ID-kolumnen.** |
| Arbetsresurser | En lista över arbetsresurser. Om du vill visa mer information om en viss arbetsresurs markerar du dess länk i kolumnen **Värd.** |
| Visualisering av topologi | En **visa visualiseringsknapp** som visar en visualisering av topologin. |
| Topologi konfiguration | Konfigurationen av den valda topologin. |

Sammanfattningssidan för Storm-topologin liknar den här webbsidan:

![Sammanfattningssida för topologi, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

I avsnittet **Topologiåtgärder** kan du välja följande knappar för att utföra en åtgärd:

| Button (Knapp) | Beskrivning |
| --- | --- |
| Aktivera | Återupptar bearbetningen av en inaktiverad topologi. |
| Inaktivera | Pausar en löpande topologi. |
| Balansera | Justerar topologins parallellism. Du bör balansera om körningstopologier när du har ändrat antalet noder i klustret. Med den här åtgärden kan topologin justera parallellism för att kompensera för det ytterligare eller minskade antalet noder i klustret.<br/><br/>Mer information finns <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">i Förstå parallellismen för en Apache Storm-topologi</a>.
| Döda | Avslutar en Storm-topologi efter den angivna tidsgränsen. |
| Felsökning | Startar en felsökningssession för den löpande topologin. |
| Stoppa felsökning | Avslutar felsökningssessionen för den löpande topologin. |
| Ändra loggnivå | Ändrar felsökningsloggnivån. |

##### <a name="spout-and-bolt-summary"></a>Spout och bult sammanfattning

Om du väljer en pip från avsnitten **Pip eller** **Bultar** visas följande information om det markerade objektet:

| Section | Beskrivning |
| --- | --- |
| Sammanfattning av komponent | Grundläggande information om pipen eller bulten. |
| Komponentåtgärder | **Felsöka** och **stoppa felsökningsknappar.** |
| Pip statistik eller bult statistik | Statistik om pipen eller bulten. Om du vill ange tidsramen för en post i det här avsnittet markerar du dess länk i kolumnen **Fönster.** |
| (Endast bult)<br/>Indatastatistik *(tidsram)* | Information om indataströmmar som förbrukas av bulten. |
| Utdatastatistik *(tidsram)* | Information om de strömmar som avges av pipen eller bulten. |
| Profilering och felsökning | Kontroller för profilering och felsökning av komponenterna på den här sidan. Du kan ange värdet **Status/Timeout (Minuter)** och du kan välja knappar för **JStack**, **Starta om arbetare**och **Heap**. |
| Utförare *(tidsram)* | Information om instanserna av pipen eller bulten. Om du vill visa en logg med diagnostikinformation som produceras för den här instansen väljer du **portposten** för en viss utförare. Du kan också se de arbetsresurser som är associerade med en viss utförare genom att välja dess länk i kolumnen **Värd.** |
| Fel | Eventuell felinformation för pipen eller bulten. |

Sammanfattningssidan för Stormbulten liknar den här webbsidan:

![Sammanfattningssida för bult, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Övervaka och hantera topologin med REST API

Det stormgränssnitt som finns bygger ovanpå REST API: et, så att du kan göra liknande hanterings- och övervakningsuppgifter med hjälp av REST API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Följande information är specifik för att använda REST API med Apache Storm på HDInsight.

> [!IMPORTANT]  
> Storm REST API är inte offentligt tillgängligt via internet. Den måste nås med hjälp av en SSH-tunnel till HDInsight-klusterhuvudnoden. Information om hur du skapar och använder en SSH-tunnel finns i [Använda SSH-tunnlar för att komma åt Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Bas URI

Bas-URI för REST API på Linux-baserade HDInsight-kluster `https://HEADNODEFQDN:8744/api/v1/`finns på URL-adress , där du ersätter *HEADNODEFQDN* med huvudnoden. Domännamnet för huvudnoden genereras när klustret skapas och är inte statiskt.

Du hittar det fullständigt kvalificerade domännamnet (FQDN) för klusterhuvudnoden på flera sätt:

| FQDN-identifieringsmetod | Beskrivning |
| --- | --- |
| SSH-session | Använd kommandot `headnode -f` från en SSH-session till klustret. |
| Ambari webben | På webbsidan Ambari-kluster (`https://CLUSTERNAME.azurehdinsight.net`) väljer du **Tjänster** högst upp på sidan och väljer sedan **Storm**. Välj **Storm UI Server**på fliken **Sammanfattning** . FQDN för noden som är värd för Storm UI och REST API visas högst upp på sidan. |
| Ambari REST API | Använd kommandot `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` för att hämta information om noden som Storm UI och REST API körs på. Ersätt de två instanserna av *CLUSTERNAME* med klusternamnet. När du uppmanas att ange lösenordet för användarens (admin)-kontot. I svaret innehåller "host_name" posten för JSON-utdata nodenS FQDN. |

### <a name="authentication"></a>Autentisering

Begäranden till REST API måste använda *grundläggande autentisering,* så du måste använda administratörsnamnet och lösenordet för HDInsight-klustret.

> [!NOTE]  
> Eftersom grundläggande autentisering skickas med hjälp av klartext bör du *alltid* använda HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returvärden

Information som returneras från REST API kan endast användas inifrån klustret. Det fullständigt kvalificerade domännamnet (FQDN) som returneras för [Apache ZooKeeper-servrar](https://zookeeper.apache.org/) är till exempel inte tillgängligt från internet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [utvecklar Java-baserade topologier med Apache Maven](apache-storm-develop-java-topology.md).

En lista över fler exempel på topologier finns [i Exempel på Apache Storm-topologier i Azure HDInsight](apache-storm-example-topology.md).
