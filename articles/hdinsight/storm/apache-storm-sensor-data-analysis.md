---
title: Analysera sensordata med Apache Storm och HBase | Microsoft Docs
description: "Lär dig hur du ansluter till Apache Storm med ett virtuellt nätverk. Använda Storm med HBase att bearbeta sensordata från en händelsehubb och visualisera med D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 87c2aece68c5de06d683abf971b6c7ccf7f67a54
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analysera sensordata med Apache Storm, Event Hub och HBase i HDInsight (Hadoop)

Lär dig använda Apache Storm på HDInsight för att bearbeta sensordata från Azure Event Hub. Data lagras i Apache HBase i HDInsight och visualiseras med D3.js.

Azure Resource Manager-mallen som används i det här dokumentet visar hur du skapar flera Azure-resurser i en resursgrupp. Mallen skapar ett Azure Virtual Network, två HDInsight-kluster (Storm och HBase) och en Webbapp i Azure. En node.js-implementering av en webbinstrumentpanel i realtid distribueras automatiskt till webbappen.

> [!NOTE]
> Informationen i det här dokumentet och exemplet i det här dokumentet kräver HDInsight version 3,6.
>
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="architecture"></a>Arkitektur

![Arkitekturdiagram](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

Det här exemplet består av följande komponenter:

* **Händelsehubbar i Azure**: innehåller data som samlas in från sensorer.
* **Storm på HDInsight**: ger realtidsbearbetning av data från Event Hub.
* **HBase på HDInsight**: ger en beständig NoSQL-databas för data när den har bearbetats av Storm.
* **Azure Virtual Network service**: möjliggör säker kommunikation mellan Storm på HDInsight och HBase på HDInsight-kluster.
  
  > [!NOTE]
  > Ett virtuellt nätverk krävs när du använder API för Java HBase-klienten. Exponeras inte via den offentliga gatewayen för HBase-kluster. Installera HBase och Storm-kluster i samma virtuella nätverk kan Storm-kluster (eller andra system i det virtuella nätverket) för direkt åtkomst till HBase med klient-API.

* **Instrumentpanelen webbplats**: ett exempel instrumentpanel diagram data i realtid.
  
  * Webbplatsen är implementerat i Node.js.
  * [Socket.IO](http://socket.io/) används för kommunikation mellan Storm-topologi och webbplatsen i realtid.
    
    > [!NOTE]
    > Med Socket.io för kommunikation är en implementering detaljer. Du kan använda alla kommunikationssystem som oformaterade WebSockets eller SignalR.

  * [D3.js](http://d3js.org/) används för att ett diagram över de data som skickas till webbplatsen.

> [!IMPORTANT]
> Två kluster krävs, eftersom det finns ingen metod som stöds för att skapa ett HDInsight-kluster för både Storm och HBase.

Topologi läser data från Event Hub med hjälp av den `org.apache.storm.eventhubs.spout.EventHubSpout` klass och skriver data till HBase med hjälp av den `org.apache.storm.hbase.bolt.HBaseBolt` klass. Kommunikation med webbplatsen åstadkoms med hjälp av [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

Följande diagram beskriver layouten för topologin:

![diagram över topologi](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Det här diagrammet är en förenklad vy av topologin. En instans av varje komponent skapas för varje partition i din Event Hub. Dessa instanser är fördelade på noderna i klustret och data dirigeras mellan dem på följande sätt:
> 
> * Data från kanal till parsern är Utjämning av nätverksbelastning.
> * Data från parsern till instrumentpanelen och HBase är grupperad efter enhets-ID, så att meddelanden från samma enhet alltid flödar till samma komponent.

### <a name="topology-components"></a>Topologi komponenter

* **Event Hub-kanalen**: kanal har angetts som en del av Apache Storm-version 0.10.0 och högre.
  
  > [!NOTE]
  > Event Hub-kanal som används i det här exemplet kräver ett Storm på HDInsight-kluster av version 3.5 eller 3,6.

* **ParserBolt.java**: data som genereras av kanal rådata JSON, och ibland fler än en händelse genereras i taget. Bulten läser data från kanal och Parsar JSON-meddelandet. Bulten skickar sedan data som en tuppel som innehåller flera fält.
* **DashboardBolt.java**: den här komponenten visar hur du använder Socket.io-klientbibliotek för Java för att skicka data i realtid till web-instrumentpanelen.
* **Ingen hbase.yaml**: topologi definitionen används vid körning i lokalt läge. Den använder inte HBase-komponenter.
* **med hbase.yaml**: topologi definitionen används när körs topologin i klustret. Den använder HBase-komponenter.
* **dev.Properties**: konfigurationsinformation för Event Hub-kanal, HBase bult och instrumentpanelskomponenter.

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du använder det här exemplet måste du förbereda din utvecklingsmiljö. Du måste också skapa en Azure-Händelsehubb som används av det här exemplet.

Du behöver följande installerad på din utvecklingsmiljö:

* [Node.js](http://nodejs.org/): används för att förhandsgranska web instrumentpanelen lokalt på din utvecklingsmiljö.
* [Java och JDK-1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): används för att utveckla Storm-topologi.
* [Maven](http://maven.apache.org/what-is-maven.html): används för att bygga och kompileras projektet.
* [Git](http://git-scm.com/): används för att hämta projektet från GitHub.
* En **SSH** klienten: används för att ansluta till Linux-baserade HDInsight-kluster. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Om du vill skapa en händelsehubb, Följ stegen i [skapar en händelsehubb](../../event-hubs/event-hubs-create.md) dokumentet.

> [!IMPORTANT]
> Spara händelsehubbens namn och namnområde nyckeln för RootManageSharedAccessKey. Den här informationen används för att konfigurera Storm-topologi.

Du behöver inte ett HDInsight-kluster. Stegen i det här dokumentet ger en Azure Resource Manager-mall som skapar de resurser som krävs av det här exemplet. Mallen skapas i följande resurser:
 
* Azure-nätverk
* Ett Storm på HDInsight-kluster (Linux-baserade två arbetarnoder)
* En HBase på HDInsight-kluster (Linux-baserade två arbetarnoder)
* En Azure-Webbapp som är värd för webb-instrumentpanelen

## <a name="download-and-configure-the-project"></a>Hämta och konfigurera projektet

Använd följande för att hämta projektet från GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

När kommandot har slutförts kan ha du följande katalogstruktur:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Det här dokumentet går inte till fullständig information om koden i det här exemplet. Koden är helt kommenterad.

Om du vill konfigurera projektet för att läsa från Event Hub, öppna den `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` filen och Lägg till din Event Hub-information i följande rader:

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Kompilera och testa lokalt

> [!IMPORTANT]
> Topologin kräver lokalt en fungerande Storm-utvecklingsmiljö. Mer information finns i [ställa in en Storm-utvecklingsmiljö](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) på Apache.org.

Innan du testar, måste du starta instrumentpanelen för att visa resultatet av topologi och generera data som lagras i Event Hub.

> [!IMPORTANT]
> Komponenten HBase i den här topologin är inte aktiv när du testar lokalt. Java-API: et för HBase-kluster kan inte nås från utanför det virtuella Azure-nätverket som innehåller klustren.

### <a name="start-the-web-application"></a>Starta webbprogrammet

1. Öppna en kommandotolk och ändra kataloger till `hdinsight-eventhub-example/dashboard`. Använd följande kommando för att installera de beroenden som krävs av webbprogrammet:
   
    ```bash
    npm install
    ```

2. Använd följande kommando för att starta webbprogrammet:
   
    ```bash
    node server.js
    ```
   
    Du ser ett meddelande som liknar följande:
   
        Server listening at port 3000

3. Öppna en webbläsare och ange `http://localhost:3000/` som adressen. En sida som liknar följande bild visas:
   
    ![instrumentpanel för webbprogram](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Lämna den här Kommandotolken öppen. När du testar kan använda Ctrl-C för att stoppa webbservern.

### <a name="generate-data"></a>Generera data

> [!NOTE]
> Stegen i det här avsnittet använder Node.js så att de kan användas på alla plattformar. Andra språk-exempel finns i `SendEvents` directory.

1. Öppna en ny fråga, shell eller terminal och ändra kataloger till `hdinsight-eventhub-example/SendEvents/nodejs`. Om du vill installera beroenden som behövs av programmet, använder du följande kommando:

    ```bash
    npm install
    ```

2. Öppna den `app.js` filen i en textredigerare och Lägg till Event Hub-information som du tidigare hämtade:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > Det här exemplet förutsätter att du använder `RootManageSharedAccessKey` åtkomst till din event hub.

3. Använd följande kommando för att infoga nya poster i Event Hub:
   
    ```bash
    node app.js
    ```
   
    Du ser flera rader på utdata som innehåller de data som skickas till Händelsehubben:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Skapa och starta topologin

1. Öppna en ny kommandotolk och ändra kataloger till `hdinsight-eventhub-example/TemperatureMonitor`. För att bygga och paketera topologi, använder du följande kommando: 

    ```bash
    mvn clean package
    ```

2. Om du vill starta topologin i lokalt läge, använder du följande kommando:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local`startar topologin i lokalt läge.
    * `--filter`använder den `dev.properties` filen att fylla i parametrar i topologin definition.
    * `resources/no-hbase.yaml`använder den `no-hbase.yaml` topologi definition.
 
   När den har startat, topologi läser poster från Event Hub och skickar dem till den instrumentpanel som körs på den lokala datorn. Du bör se raderna som ska visas på webben instrumentpanelen liknar följande bild:
   
    ![instrumentpanel med data](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. När instrumentpanelen körs, kan du använda den `node app.js` kommandot från föregående steg att skicka nya data i Händelsehubbar. Eftersom värdena temperatur slumpmässigt har skapats bör uppdatera diagrammet om du vill visa stora förändringar i temperatur.
   
   > [!NOTE]
   > Du måste vara i den **hdinsight-eventhub-exempel/SendEvents/Nodejs** directory när du använder den `node app.js` kommando.

3. Stoppa topologin med Ctrl + C när du har verifierat att instrumentpanelen uppdateringarna. Du kan använda Ctrl + C för att stoppa lokala webbservern också.

## <a name="create-a-storm-and-hbase-cluster"></a>Skapa ett Storm och HBase-kluster

Stegen i det här avsnittet används en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md) att skapa ett virtuellt Azure-nätverk och en Storm och HBase-kluster på det virtuella nätverket. Mallen även skapar en Azure Web App och distribuerar en kopia av instrumentpanelen till den.

> [!NOTE]
> Ett virtuellt nätverk används så att den topologi som körs på Storm-kluster kan kommunicera direkt med HBase-kluster med hjälp av HBase Java API.

Resource Manager-mallen som används i det här dokumentet finns i en offentlig blob-behållare på **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Klicka på följande om du vill logga in på Azure och öppna Resource Manager-mall i Azure-portalen.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Från den **anpassad distribution** ange följande värden:
   
    ![HDInsight-parametrar](./media/apache-storm-sensor-data-analysis/parameters.png)
   
   * **Basera klusternamnet**: det här värdet används som huvudnamnet för Storm och HBase-kluster. Ange till exempel **abc** skapar en Storm-kluster med namnet **storm abc** och ett HBase-kluster med namnet **hbase abc**.
   * **Klustrets inloggningsnamn**: admin användarnamn Storm och HBase-kluster.
   * **Klustret inloggningslösenordet**: administratörslösenord för Storm och HBase-kluster.
   * **SSH-användarnamn**: SSH-användare skapa för Storm och HBase-kluster.
   * **SSH-lösenordet**: lösenord för SSH-användare för Storm och HBase-kluster.
   * **Plats**: den region som kluster skapas i.
     
     Spara parametrarna genom att klicka på **OK**.

3. Använd den **grunderna** avsnittet för att skapa en resursgrupp eller välj en befintlig.
4. I den **resursgruppsplats** listrutan väljer du samma plats som du valt för den **plats** parameter i den **inställningar** avsnitt.
5. Läs villkoren och välj sedan **jag samtycker till villkoren som anges ovan**.
6. Kontrollera slutligen **fäst på instrumentpanelen** och välj sedan **inköp**. Det tar ungefär 20 minuter för att skapa kluster.

När resurserna som har skapats visas information om resursgruppen.

![Resursgruppen för virtuella nätverk och kluster](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Lägg märke till att namnen på HDInsight-kluster **storm-BASENAME** och **hbase BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i ett senare steg när du ansluter till kluster. Även Observera att namnet på webbplatsen instrumentpanelen är **basename instrumentpanelen**. Det här värdet används senare i det här dokumentet.

## <a name="configure-the-dashboard-bolt"></a>Konfigurera instrumentpanelen bult

Om du vill skicka data till instrumentpanelen distribueras som en webbapp, måste du ändra följande rad i den `dev.properties`filen:

```yaml
dashboard.uri: http://localhost:3000
```

Ändra `http://localhost:3000` till `http://BASENAME-dashboard.azurewebsites.net` och spara filen. Ersätt **BASENAME** med det grundläggande namnet du angav i föregående steg. Du kan också använda resursgruppen som skapats tidigare för att markera instrumentpanelen och visa URL: en.

## <a name="create-the-hbase-table"></a>Skapa HBase-tabellen

Vi måste först skapa en tabell för att lagra data i HBase. Skapa Storm behöver skriva till resurser, som försök att skapa resurser från inuti ett Storm topologi kan resultera i flera instanser försök att skapa samma resurs. Skapa resurser utanför topologin och använda Storm för läsning/skrivning och analyser.

1. Använda SSH för att ansluta till HBase-kluster som använder SSH-användare och lösenord som du angav i mallen när klustret skapas. Om du ansluter till exempel den `ssh` kommando du använder du följande syntax:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Ersätt `sshuser` med SSH-användarnamn som du angav när du skapar klustret. Ersätt `clustername` med HBase klustrets namn.

2. Starta HBase-gränssnittet från SSH-session.
   
    ```bash
    hbase shell
    ```
   
    När gränssnittet har lästs in, finns en `hbase(main):001:0>` prompt.

3. Ange följande kommando för att skapa en tabell för att lagra dessa sensordata från HBase-gränssnittet:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Kontrollera att tabellen har skapats med hjälp av följande kommando:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Information returneras liknar följande exempel som visar att det finns 0 rader i tabellen.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Ange `exit` avsluta HBase-gränssnittet:

## <a name="configure-the-hbase-bolt"></a>Konfigurera HBase bult

Om du vill skriva till HBase från Storm-kluster, måste du ange bulten HBase med konfigurationsinformation för HBase-kluster.

1. Använd någon av följande exempel för att hämta Zookeeper kvorum för HBase-kluster:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Ersätt `your_HDInsight_cluster_name` med namnet på HDInsight-klustret. Mer information om hur du installerar den `jq` -verktyget, se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > När du uppmanas, ange lösenordet för HDInsight admin inloggningen.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Ersätt ”your_HDInsight_cluster_name med namnet på ditt HDInsight-kluster. När du uppmanas, ange lösenordet för HDInsight admin inloggningen.
    >
    > Det här exemplet kräver Azure PowerShell. Mer information om hur du använder Azure PowerShell finns [Kom igång med Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    Informationen som returneras av de här exemplen liknar följande:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Den här informationen används av Storm för att kommunicera med HBase-kluster.

2. Ändra den `dev.properties` filen och Lägg till Zookeeper kvorum information till följande rad:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Skapa paket och distribuera lösningen till HDInsight

Använd följande steg för att distribuera Storm-topologi till storm-kluster i din utvecklingsmiljö.

1. Från den `TemperatureMonitor` directory, Använd följande kommando för att utföra en ny version och skapa en JAR paketet från projektet:
   
        mvn clean package
   
    Det här kommandot skapar en fil med namnet `TemperatureMonitor-1.0-SNAPSHOT.jar in the `mål-katalogen i ditt projekt.

2. Använda scp för att överföra den `TemperatureMonitor-1.0-SNAPSHOT.jar` och `dev.properties` filer till ditt Storm-kluster. I följande exempel ersätter `sshuser` med SSH-användare som du angav när du skapar klustret, och `clustername` med namnet på Storm-kluster. När du uppmanas, anger du lösenordet för SSH-användare.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Det kan ta flera minuter att överföra filer.

    Mer information om hur du använder den `scp` och `ssh` kommandon med HDInsight finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

3. När filen har överförts, ansluta till Storm-kluster med SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Ersätt `sshuser` med SSH-användarnamn. Ersätt `clustername` med Storm klustrets namn.

4. Starta topologin med följande kommando från SSH-session:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote`skickar topologi till Nimbus-tjänsten som distribueras till handledarens noder i klustret.
    * `--filter`använder den `dev.properties` filen att fylla i parametrar i topologin definition.
    * `-R /with-hbase.yaml`använder den `with-hbase.yaml` topologi som ingår i paketet.

5. Öppna en webbläsare när topologin har startats på webbplatsen som du har publicerat på Azure, använda den `node app.js` kommando för att skicka data till Händelsehubben. Du bör se web instrumentpanelen uppdateras för att visa information.
   
    ![instrumentpanel](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visa data i HBase

Använd följande steg för att ansluta till HBase och kontrollera att data har skrivits till tabellen:

1. Använda SSH för att ansluta till HBase-kluster.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Ersätt `sshuser` med SSH-användarnamn. Ersätt `clustername` med HBase klustrets namn.

2. Starta HBase-gränssnittet från SSH-session.
   
    ```bash
    hbase shell
    ```
   
    När gränssnittet har lästs in, finns en `hbase(main):001:0>` prompt.

3. Visa rader från tabellen:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Det här kommandot returnerar informationen som liknar följande text, som anger att det finns data i tabellen.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Åtgärden genomsökning returnerar högst 10 raderna från tabellen.

## <a name="delete-your-clusters"></a>Ta bort dina kluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Ta bort kluster, lagring och webbprogrammet i taget genom att ta bort resursgruppen som innehåller dem.

## <a name="next-steps"></a>Nästa steg

Fler exempel på Storm-topologier med HDInsight finns i [exempeltopologier för Storm på HDInsight](apache-storm-example-topology.md)

Mer information om Apache Storm finns i [Apache Storm](https://storm.incubator.apache.org/) plats.

Mer information om HBase på HDInsight finns i [HBase med HDInsight Overview](../hbase/apache-hbase-overview.md).

Mer information om Socket.io finns i [socket.io](http://socket.io/) plats.

Läs mer om D3.js [D3.js - Data drivs dokument](http://d3js.org/).

Information om hur du skapar topologier i Java finns [utveckla Java-topologier för Apache Storm på HDInsight](apache-storm-develop-java-topology.md).

Information om hur du skapar topologier i .NET finns [utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
