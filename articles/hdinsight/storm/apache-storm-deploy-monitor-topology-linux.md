---
title: Distribuera och hantera Apache Storm topologier på Azure HDInsight
description: Lär dig att distribuera, övervaka och hantera Apache Storm topologier med storm-instrumentpanelen på Linux-baserade HDInsight. Använd Hadoop-verktyg för Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915108"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuera och hantera Apache Storm topologier på Azure HDInsight 

I det här dokumentet lär du dig grunderna för att hantera och övervaka [Apache Storm](https://storm.apache.org/) topologier som körs på storm i HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.


* Valfritt Kunskap om SSH och SCP: Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* Valfritt Visual Studio: Azure SDK-2.5.1 eller nyare och Data Lake verktyg för Visual Studio. Mer information finns i [Kom igång med data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    En av följande versioner av Visual Studio:

  * Visual Studio 2012 med uppdatering 4

  * Visual Studio 2013 med uppdatering 4 eller [Visual studio 2013 community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (vilken utgåva som helst)

  * Visual Studio 2017 (vilken version som helst). Data Lake verktyg för Visual Studio 2017 installeras som en del av Azure-arbetsbelastningen.

## <a name="submit-a-topology-visual-studio"></a>Skicka en topologi: Visual Studio

HDInsight-verktygen kan användas för att skicka C# eller hybrid topologier till Storm-klustret. I följande steg används ett exempel program. Information om hur du skapar med hjälp av HDInsight-verktygen finns i [ C# utveckla topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Om du inte redan har installerat den senaste versionen av Data Lake verktyg för Visual Studio, se [Kom igång med data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Data Lake Tools för Visual Studio kallades tidigare HDInsight-verktyg för Visual Studio.
    >
    > Data Lake verktyg för Visual Studio ingår i Azure- __arbetsbelastningen__ för visual Studio 2017.

2. Öppna Visual Studio, Välj **fil** > **nytt** > **projekt**.

3. I dialog rutan **nytt projekt** expanderar du **installerade** > **mallar**och väljer sedan **HDInsight**. Välj **Storm-exempel**i listan med mallar. Längst ned i dialog rutan anger du ett namn för programmet.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. I **Solution Explorer**högerklickar du på projektet och väljer **Skicka till storm på HDInsight**.

   > [!NOTE]  
   > Ange inloggnings uppgifter för din Azure-prenumeration om du uppmanas att göra det. Om du har mer än en prenumeration loggar du in på den som innehåller din storm i HDInsight-klustret.

5. Välj ditt storm på HDInsight-kluster i list rutan **Storm-kluster** och välj sedan **Skicka**. Du kan övervaka om överföringen lyckas genom att använda fönstret **utdata** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Skicka en topologi: SSH och Storm-kommandot

1. Använd SSH för att ansluta till HDInsight-klustret. Ersätt **username** med namnet på din SSH-inloggning. Ersätt **kluster** namn med ditt HDInsight-kluster namn:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Mer information om hur du använder SSH för att ansluta till ditt HDInsight-kluster finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommando för att starta en exempeltopologi:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret. Den här topologin skapar slumpmässigt meningar och räknar antalet förekomster av varje ord i meningarna.

   > [!NOTE]  
   > När du skickar in topologin till klustret måste du först kopiera jar-filen som innehåller klustret innan du använder kommandot `storm`. Om du vill kopiera filen till klustret kan du använda `scp` kommandot. Till exempel, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > WordCount-exemplet och andra exempel i Storm Starter ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Skicka en topologi: program mässigt

Du kan distribuera en topologi program mässigt med Nimbus-tjänsten. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)innehåller ett exempel på ett Java-program som visar hur du distribuerar och startar en topologi via Nimbus-tjänsten.

## <a name="monitor-and-manage-visual-studio"></a>Övervaka och hantera: Visual Studio

När en topologi skickas med hjälp av Visual Studio visas vyn **Storm-topologier** . Välj topologin i listan om du vill visa information om topologi som körs.

![Visual Studio-övervakare](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Du kan också visa **Storm-topologier** från **Server Explorer** genom att expandera **Azure** > **HDInsight**och sedan högerklicka på en storm på HDInsight-kluster och välja **Visa Storm-topologier**.

Välj formen för kanaler eller bultarna om du vill visa information om dessa komponenter. Ett nytt fönster öppnas för varje markerat objekt.

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Om du inaktiverar en topologi pausas den tills den avlivas eller återaktiveras. Om du vill utföra dessa åtgärder använder du knapparna __inaktivera__ och __återaktivera__ överst i __topologin Sammanfattning__.

### <a name="rebalance"></a>Balansera om

Ombalansering av en topologi gör att systemet kan ändra topologins parallellitet. Om du till exempel har ändrat storlek på klustret för att lägga till fler anteckningar, tillåter ombalansering att en topologi visar de nya noderna.

Om du vill balansera om en topologi använder du knappen för __ombalans__ överst i __topologins Sammanfattning__.

> [!WARNING]  
> Ombalansering av en topologi gör att topologin först inaktive ras och sedan distribuerar om arbetare jämnt över klustret och returnerar sedan topologin till det tillstånd som den var i innan ombalanseringen gjordes. Så om topologin var aktiv aktive ras den igen. Om den är inaktive rad förblir den inaktive rad.

### <a name="kill-a-topology"></a>Avsluta en topologi

Storm-topologier fortsätter att köras tills de stoppas eller också tas klustret bort. Om du vill stoppa en topologi använder du knappen __Kill__ överst i __topologins Sammanfattning__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Övervaka och hantera: SSH och Storm-kommandot

Med `storm` verktyget kan du arbeta med topologier som körs från kommando raden. Använd `storm -h` för en fullständig lista över kommandon.

### <a name="list-topologies"></a>Lista topologier

Använd följande kommando för att visa en lista över alla topologier som körs:

    storm list

Det här kommandot returnerar information liknande följande text:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktivera och återaktivera

Om du inaktiverar en topologi pausas den tills den avlivas eller återaktiveras. Använd följande kommando för att inaktivera och återaktivera:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Avsluta en topologi som körs

Storm-topologier, när de har startats, fortsätter att köras tills den stoppats Om du vill stoppa en topologi använder du följande kommando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Balansera om

Ombalansering av en topologi gör att systemet kan ändra topologins parallellitet. Om du till exempel har ändrat storlek på klustret för att lägga till fler anteckningar, tillåter ombalansering att en topologi visar de nya noderna.

> [!WARNING]  
> Ombalansering av en topologi gör att topologin först inaktive ras och sedan distribuerar om arbetare jämnt över klustret och returnerar sedan topologin till det tillstånd som den var i innan ombalanseringen gjordes. Så om topologin var aktiv aktive ras den igen. Om den är inaktive rad förblir den inaktive rad.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Övervaka och hantera: Storm-användargränssnitt

Storm-användargränssnittet innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster. Om du vill se Storm-användargränssnittet använder du en webbläsare för **https://CLUSTERNAME.azurehdinsight.net/stormui** att öppna, där **kluster** namn är namnet på klustret.

> [!NOTE]  
> Om du uppmanas att ange ett användarnamn och lösenord, anger du klusteradministratören (admin) och lösenordet du använde när du skapade klustret.

### <a name="main-page"></a>Huvud sida

Huvud sidan i storm-ANVÄNDARGRÄNSSNITTET innehåller följande information:

* **Kluster Sammanfattning**: Grundläggande information om Storm-klustret.
* **Topologi-Sammanfattning**: En lista över topologier som körs. Använd länkarna i det här avsnittet om du vill visa mer information om vissa topologier.
* **Sammanfattning av ansvarig**: Information om Storm-ansvariga.
* **Nimbus-konfiguration**: Nimbus-konfiguration för klustret.

### <a name="topology-summary"></a>Översikt över topologi

Om du väljer en länk från avsnittet **topologi Sammanfattning** visas följande information om topologin:

* **Topologi-Sammanfattning**: Grundläggande information om topologin.
* **Åtgärder för topologi**: Hanterings åtgärder som du kan utföra för topologin.

  * **Aktivera**: Återupptar bearbetning av en inaktive rad topologi.
  * **Inaktivera**: Pausar en topologi som körs.
  * **Balansera**om: Justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Med den här åtgärden kan topologin justera parallellt för att kompensera för det ökade eller minskade antalet noder i klustret.

    Mer information finns i <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">förstå parallellitet för en Apache Storm topologi</a>.
  * **Kill**: Avslutar en Storm-topologi efter den angivna tids gränsen.
* **Topology-statistik**: Statistik om topologin. Om du vill ange tidsram för de återstående posterna på sidan, använder du länkarna i **fönster** kolumnen.
* **Kanaler**: Kanaler som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om vissa kanaler.
* **Bultar**: Bultarna som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om enskilda bultar.
* **Konfiguration av topologi**: Konfigurationen för den valda topologin.

### <a name="spout-and-bolt-summary"></a>Sammanfattning av kanalen och bult

Om du väljer en kanalen i avsnittet **kanaler** eller **bultar** visas följande information om det valda objektet:

* **Sammanfattning av komponent**: Grundläggande information om kanalen eller bult.
* **Kanalen/bult-statistik**: Statistik om kanalen eller bult. Om du vill ange tidsram för de återstående posterna på sidan, använder du länkarna i **fönster** kolumnen.
* **Inmatad statistik** (endast bult): Information om de indata strömmar som förbrukas av bulten.
* **Utmatnings statistik**: Information om strömmar som genereras av kanalen eller bult.
* **Körningar**: Information om instanser av kanalen eller bult. Välj **port** posten för en speciell utförar om du vill visa en logg med diagnostisk information som skapats för den här instansen.
* **Fel**: Eventuell fel information för kanalen eller bult.

## <a name="monitor-and-manage-rest-api"></a>Övervaka och hantera: REST-API

Storm-ANVÄNDARGRÄNSSNITTET skapas ovanpå REST API, så du kan utföra liknande hanterings-och övervaknings funktioner med hjälp av REST API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Följande information är speciell för att använda REST API med Apache Storm i HDInsight.

> [!IMPORTANT]  
> Storm REST API är inte offentligt tillgänglig via Internet och måste nås via en SSH-tunnel till HDInsight-klustrets huvud nod. Information om hur du skapar och använder en SSH-tunnel finns i [använda SSH-tunnlar för att komma åt Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Apache Oozie och andra Web UIS](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Bas-URI

Bas-URI: n för de REST API på Linux-baserade HDInsight-kluster finns tillgänglig på Head-noden på **https\/:/HEADNODEFQDN: 8744/API/v1/** . Huvud nodens domän namn genereras när klustret skapas och är inte statiskt.

Du kan hitta det fullständigt kvalificerade domän namnet (FQDN) för noden kluster huvud på flera olika sätt:

* **Från en SSH-session**: Använd kommandot `headnode -f` från en SSH-session till klustret.
* **Från Ambari Web**: Välj **tjänster** överst på sidan och välj **Storm**. Välj **Storm UI Server**på fliken **Sammanfattning** . FQDN för den nod som är värd för Storm-ANVÄNDARGRÄNSSNITTET och REST API visas längst upp på sidan.
* **Från Ambari REST API**: Använd kommandot `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` för att hämta information om noden som Storm-användargränssnittet och REST API körs på. Ersätt **kluster** namn med kluster namnet. När du uppmanas till det anger du lösen ordet för inloggnings kontot (admin). I svaret innehåller posten "host_name" det fullständiga domän namnet för noden.

### <a name="authentication"></a>Authentication

Begär anden till REST API måste använda **grundläggande autentisering**, så du använder HDInsight-kluster administratörens namn och lösen ord.

> [!NOTE]  
> Eftersom grundläggande autentisering skickas med klartext bör du **alltid** använda HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Retur värden

Information som returneras från REST API kan bara användas i klustret. Till exempel är det fullständigt kvalificerade domän namnet (FQDN) som returnerades för [Apache ZooKeeper](https://zookeeper.apache.org/) -servrar inte tillgängligt från Internet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [utvecklar Java-baserade topologier med Apache maven](apache-storm-develop-java-topology.md).

En lista över fler exempel topologier finns i [exempel topologier för Apache storm i HDInsight](apache-storm-example-topology.md).
