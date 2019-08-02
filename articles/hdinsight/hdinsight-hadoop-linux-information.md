---
title: Tips för att använda Hadoop på Linux-baserade HDInsight – Azure
description: Få implementerings tips för att använda Linux-baserade HDInsight-kluster (Hadoop) i en välbekant Linux-miljö som körs i Azure-molnet.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1904ab07a188e4e877a4fb2f2b7682d923c08fb2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442000"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Information om hur du använder HDInsight på Linux

Azure HDInsight-kluster ger Apache Hadoop på en välbekant Linux-miljö, som körs i Azure-molnet. För de flesta saker bör det fungera exakt som vilken annan Hadoop-on-Linux-installation som helst. Det här dokumentet anropar vissa skillnader som du bör vara medveten om.

## <a name="prerequisites"></a>Förutsättningar

Många av stegen i det här dokumentet använder följande verktyg, som kan behöva installeras i systemet.

* [sväng](https://curl.haxx.se/) -används för att kommunicera med webbaserade tjänster.
* **JQ**, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – används för att fjärrhantera Azure-tjänster.
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Användare

Om [den](./domain-joined/hdinsight-security-overview.md)inte är domänansluten bör HDInsight betraktas som ett system med en **användare** . Ett enda SSH-användarkonto skapas med klustret med behörighet som administratörs nivå. Ytterligare SSH-konton kan skapas, men de har också administratörs åtkomst till klustret.

Domänanslutna HDInsight stöder flera användare och mer detaljerade behörigheter och roll inställningar. Mer information finns i [Hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Domännamn

Det fullständigt kvalificerade domän namnet (FQDN) som ska användas vid anslutning till klustret från Internet är `CLUSTERNAME.azurehdinsight.net` eller `CLUSTERNAME-ssh.azurehdinsight.net` (endast för SSH).

Internt har varje nod i klustret ett namn som tilldelas under kluster konfigurationen. Du hittar kluster namnen på sidan **värdar** i Ambari-webbgränssnittet. Du kan också använda följande för att returnera en lista över värdar från Ambari-REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Ersätt `CLUSTERNAME` med namnet på klustret. När du uppmanas till det anger du lösen ordet för administratörs kontot. Det här kommandot returnerar ett JSON-dokument som innehåller en lista över värdarna i klustret. [JQ](https://stedolan.github.io/jq/) används för att extrahera `host_name` elementets värde för varje värd.

Om du behöver hitta namnet på noden för en speciell tjänst kan du fråga Ambari för den komponenten. Om du till exempel vill hitta värdarna för noden HDFS Name, använder du följande kommando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Det här kommandot returnerar ett JSON-dokument som beskriver tjänsten och hämtar [](https://stedolan.github.io/jq/) sedan bara `host_name` värdet för värdarna JQ.

## <a name="remote-access-to-services"></a>Fjärråtkomst till tjänster

* **Ambari (webb)**  - https://CLUSTERNAME.azurehdinsight.net

    Autentisera med hjälp av kluster administratörs användare och lösen ord och logga sedan in på Ambari.

    Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

    > [!IMPORTANT]  
    > Några av de webb UIs som är tillgängliga via Ambari-åtkomst-noder med hjälp av ett internt domän namn. Interna domän namn är inte offentligt tillgängliga via Internet. Fel meddelandet "servern hittades inte" visas när du försöker få åtkomst till vissa funktioner via Internet.
    >
    > Om du vill använda alla funktioner i Ambari-webbgränssnittet använder du en SSH-tunnel för att dirigera webb trafik till klustrets huvud nod. Se [använda SSH-tunnlar för att komma åt Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie och andra webb-UIS](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Autentisera med hjälp av kluster administratörs användare och lösen ord.
    >
    > Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **WebHCat (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Autentisera med hjälp av kluster administratörs användare och lösen ord.
    >
    > Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.net på port 22 eller 23. Port 22 används för att ansluta till den primära huvudnoden, medan 23 används för att ansluta till den sekundära. Mer information om huvudnoderna finns i [tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Du kan bara komma åt kluster huvud-noder via SSH från en klient dator. När du är ansluten kan du sedan komma åt arbetsnoderna med hjälp av SSH från en huvudnoden.

Mer information finns i portarna som [används av Apache Hadoop Services i HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokument.

## <a name="file-locations"></a>Filplatser

Hadoop-relaterade filer hittar du på klusternoderna på `/usr/hdp`. Den här katalogen innehåller följande under kataloger:

* **2.6.5.3006-29**: Katalog namnet är den version av Hortonworks Data Platform som används av HDInsight. Antalet på klustret kan vara ett annat än det som anges här.
* **aktuell**: Den här katalogen innehåller länkar till under kataloger i **2.6.5.3006-29-** katalogen. Katalogen finns så att du inte behöver komma ihåg versions numret.

Du hittar exempel data och jar-filer på Hadoop Distributed File System `/example` på och. `/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage och Data Lake Storage

I de flesta Hadoop-distributioner lagras data i HDFS, som backas upp av lokal lagring på datorerna i klustret. Användning av lokala lagrings enheter kan vara kostsamt för en molnbaserad lösning där du debiteras per timme eller per minut för beräknings resurser.

När du använder HDInsight lagras datafilerna på ett skalbart och flexibelt sätt i molnet med hjälp av Azure Blob Storage och eventuellt Azure Data Lake Storage. Dessa tjänster ger följande fördelar:

* Korttids långsiktig lagring.
* Hjälpmedel från externa tjänster som webbplatser, verktyg för fil uppladdning/hämtning, olika språk-SDK: er och webbläsare.
* Stor fil kapacitet och stort skalbart lagrings utrymme.

Mer information finns i [förstå blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) och [data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

När du använder antingen Azure Storage eller Data Lake Storage behöver du inte göra något särskilt från HDInsight för att komma åt data. Till exempel visar följande kommando filer i `/example/data` mappen oavsett om de lagras på Azure Storage eller data Lake Storage:

    hdfs dfs -ls /example/data

I HDInsight frigörs data lagrings resurserna (Azure Blob Storage och Azure Data Lake Storage) från beräknings resurser. Därför kan du skapa HDInsight-kluster för att utföra beräkningarna efter behov, och senare ta bort klustret när arbetet är klart, samtidigt som dina datafiler sparas säkert i moln lagringen så länge du behöver.


### <a name="URI-and-scheme"></a>URI och schema

Vissa kommandon kan kräva att du anger schemat som en del av URI: n vid åtkomst till en fil. Till exempel kräver Storm-HDFS-komponenten att du anger schemat. När du använder lagrings utrymme som inte är standard (lagring läggs till som ytterligare lagrings utrymme i klustret), måste du alltid använda schemat som en del av URI: n.

Använd något av följande URI-scheman när du använder __Azure Storage__:

* `wasb:///`: Få åtkomst till standard lagring med okrypterad kommunikation.

* `wasbs:///`: Få åtkomst till standard lagring med krypterad kommunikation.  Wasbs-schemat stöds endast från HDInsight version 3,6 och senare.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Används vid kommunikation med ett lagrings konto som inte är standard. Till exempel när du har ett ytterligare lagrings konto eller när du har åtkomst till data som lagras i ett offentligt tillgängligt lagrings konto.

Använd något av följande URI-scheman när du använder __Azure Data Lake Storage Gen2__:

* `abfs:///`: Få åtkomst till standard lagring med okrypterad kommunikation.

* `abfss:///`: Få åtkomst till standard lagring med krypterad kommunikation.  Abfss-schemat stöds endast från HDInsight version 3,6 och senare.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Används vid kommunikation med ett lagrings konto som inte är standard. Till exempel när du har ett ytterligare lagrings konto eller när du har åtkomst till data som lagras i ett offentligt tillgängligt lagrings konto.

Använd något av följande URI-scheman när du använder __Azure Data Lake Storage gen1__:

* `adl:///`: Få åtkomst till standard Data Lake Storage för klustret.

* `adl://<storage-name>.azuredatalakestore.net/`: Används vid kommunikation med ett Data Lake Storage som inte är standard. Används också för att komma åt data utanför rot katalogen i ditt HDInsight-kluster.

> [!IMPORTANT]  
> När du använder Data Lake Storage som standard Arkiv för HDInsight måste du ange en sökväg i arkivet som ska användas som roten för HDInsight-lagring. Standard Sök vägen är `/clusters/<cluster-name>/`.
>
> När du `/` använder `adl:///` eller för att komma åt data, kan du bara komma åt data som lagras i `/clusters/<cluster-name>/`roten (till exempel) i klustret. Använd `adl://<storage-name>.azuredatalakestore.net/` formatet för att komma åt data var som helst i arkivet.

### <a name="what-storage-is-the-cluster-using"></a>Vilket lagrings utrymme är klustret använder

Du kan använda Ambari för att hämta standard lagrings konfigurationen för klustret. Använd följande kommando för att hämta information om HDFS-konfiguration med hjälp av sväng och filtrera den med [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Det här kommandot returnerar den första konfigurationen som tillämpas på servern`service_config_version=1`() som innehåller den här informationen. Du kan behöva visa en lista över alla konfigurations versioner för att hitta den senaste versionen.

Det här kommandot returnerar ett värde som liknar följande URI: er:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`Om du använder ett Azure Storage-konto.

    Konto namnet är namnet på Azure Storage kontot. Behållar namnet är BLOB-behållaren som är roten i kluster lagringen.

* `adl://home`Om du använder Azure Data Lake Storage. Använd följande REST-anrop för att hämta Data Lake Storage namn:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Det här kommandot returnerar följande värdnamn: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Använd följande REST-anrop för att hämta katalogen i arkivet som är roten för HDInsight:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Det här kommandot returnerar en sökväg som liknar följande sökväg: `/clusters/<hdinsight-cluster-name>/`.

Du kan också hitta lagrings informationen med hjälp av Azure Portal med hjälp av följande steg:

1. Välj ditt HDInsight-kluster från [Azure Portal](https://portal.azure.com/).

2. I avsnittet **Egenskaper** väljer du **lagrings konton**. Lagrings informationen för klustret visas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hur gör jag för att komma åt filer från externa HDInsight

Det finns olika sätt att komma åt data utanför HDInsight-klustret. Följande är några länkar till verktyg och SDK: er som kan användas för att arbeta med dina data:

Om du använder __Azure Storage__, se följande länkar för hur du kan komma åt dina data:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Kommando rads kommandon för att arbeta med Azure. När du har installerat använder `az storage` du kommandot för att få hjälp med att `az storage blob` använda lagring, eller för BLOB-/regionsspecifika kommandon.
* [blobxfer.py](https://github.com/Azure/blobxfer): Ett Python-skript för att arbeta med blobbar i Azure Storage.
* Olika SDK: er:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [NET](https://github.com/Azure/azure-sdk-for-net)
    * [Lagrings REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Om du använder __Azure Data Lake Storage__, se följande länkar för hur du kan komma åt dina data:

* [Webbläsare](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Skala klustret

Med funktionen för kluster skalning kan du dynamiskt ändra antalet datanoder som används av ett kluster. Du kan utföra skalnings åtgärder medan andra jobb eller processer körs i ett kluster.  Se även [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md)

De olika kluster typerna påverkas av skalning enligt följande:

* **Hadoop**: När du skalar ned antalet noder i ett kluster startas vissa av tjänsterna i klustret om. Skalnings åtgärder kan orsaka att jobb körs eller väntar på att gå sönder vid slutförandet av skalnings åtgärden. Du kan skicka jobben igen när åtgärden har slutförts.
* **HBase**: Regionala servrar bal anse ras automatiskt inom några minuter, när skalnings åtgärden har slutförts. Använd följande steg för att balansera regionala servrar manuellt:

    1. Anslut till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

    2. Använd följande för att starta HBase-gränssnittet:

            hbase shell

    3. När HBase-gränssnittet har lästs in använder du följande för att balansera de regionala servrarna manuellt:

            balancer

* **Storm**: Du bör balansera om alla Storm-topologier som körs när en skalnings åtgärd har utförts. Med ombalansering kan topologin justera inställningar för parallellitet baserat på det nya antalet noder i klustret. Använd något av följande alternativ för att balansera om topologier som körs:

    * **SSH**: Anslut till servern och Använd följande kommando för att balansera om en topologi:

            storm rebalance TOPOLOGYNAME

        Du kan också ange parametrar för att åsidosätta de parallella tips som ursprungligen tillhandahölls av topologin. Till exempel `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` omkonfigureras topologin till 5 arbets processer, 3 körningar för Blue-kanalen-komponenten och 10 körningar för den gula-bult-komponenten.

    * **Storm-gränssnitt**: Använd följande steg för att balansera om en topologi med storm-ANVÄNDARGRÄNSSNITTET.

        1. Öppna `https://CLUSTERNAME.azurehdinsight.net/stormui` i webbläsaren, där `CLUSTERNAME` är namnet på ditt Storm-kluster. Om du uppmanas till det anger du namnet på HDInsight-klusterresursen (admin) och lösen ordet du angav när du skapade klustret.
        2. Välj den topologi du vill balansera om och välj sedan knappen **balansera** om. Ange fördröjningen innan ombalanserings åtgärden utförs.

* **Kafka**: Du bör balansera om partition repliker efter skalnings åtgärder. Mer information finns i dokumentet [med hög tillgänglighet för data med Apache Kafka på HDInsight](./kafka/apache-kafka-high-availability.md) -dokument.

För detaljerad information om skalning av HDInsight-klustret, se:

* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hur gör jag för att installera nyans (eller en annan Hadoop-komponent)?

HDInsight är en hanterad tjänst. Om Azure identifierar ett problem med klustret kan det ta bort noden som Miss lyckas och skapa en nod för att ersätta den. Om du installerar saker manuellt i klustret, sparas de inte när den här åtgärden utförs. Använd i stället [HDInsight-skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md). En skript åtgärd kan användas för att göra följande ändringar:

* Installera och konfigurera en tjänst eller webbplats.
* Installera och konfigurera en komponent som kräver konfigurations ändringar på flera noder i klustret.

Skript åtgärder är Bash-skript. Skripten körs när klustret skapas och används för att installera och konfigurera ytterligare komponenter. Exempel skript finns för att installera följande komponenter:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Jar-filer

Vissa Hadoop-tekniker tillhandahålls i fristående jar-filer som innehåller funktioner som används som en del av ett MapReduce-jobb eller inifrån gris eller Hive. De kräver ofta inte några inställningar och kan laddas upp till klustret när de har skapats och använts direkt. Om du vill vara säker på att komponenten överleva en åter avbildning av klustret kan du lagra jar-filen i standard lagrings utrymmet för klustret (WASB eller ADL).

Om du till exempel vill använda den senaste versionen av [Apache DataFu](https://datafu.incubator.apache.org/)kan du ladda ned en jar-fil som innehåller projektet och ladda upp den till HDInsight-klustret. Följ sedan DataFu-dokumentationen om hur du använder den från gris eller Hive.

> [!IMPORTANT]  
> Vissa komponenter som är fristående jar-filer tillhandahålls med HDInsight, men finns inte i sökvägen. Om du letar efter en enskild komponent kan du söka efter den i klustret med hjälp av följande:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Det här kommandot returnerar sökvägen till alla matchande jar-filer.

Om du vill använda en annan version av en komponent laddar du upp den version du behöver och använder den i dina jobb.

> [!IMPORTANT]
> Komponenter som ingår i HDInsight-klustret stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex.: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser [https://apache.org](https://apache.org)på, till exempel: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache gris med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hadoop/hdinsight-use-mapreduce.md)
