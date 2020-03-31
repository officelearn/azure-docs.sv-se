---
title: Tips om hur du använder Hadoop på Linux-baserad HDInsight - Azure
description: Få implementeringstips för att använda Linux-baserade HDInsight-kluster (Hadoop) i en välbekant Linux-miljö som körs i Azure-molnet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272466"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Information om hur du använder HDInsight på Linux

Azure HDInsight-kluster tillhandahåller Apache Hadoop i en välbekant Linux-miljö som körs i Azure-molnet. För det mesta bör det fungera precis som alla andra Hadoop-on-Linux-installation. I det här dokumentet anges specifika skillnader som du bör vara medveten om.

## <a name="prerequisites"></a>Krav

Många av stegen i det här dokumentet använder följande verktyg, som kan behöva installeras på datorn.

* [cURL](https://curl.haxx.se/) - används för att kommunicera med webbaserade tjänster.
* **jq**, en kommandorads-JSON-processor.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - används för att fjärrhantera Azure-tjänster.
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Användare

Om inte [domän-anslutna,](./domain-joined/hdinsight-security-overview.md)HDInsight bör betraktas som en **enda användare** system. Ett enda SSH-användarkonto skapas med klustret, med behörighet på administratörsnivå. Ytterligare SSH-konton kan skapas, men de har också administratörsbehörighet till klustret.

Domänanslutna HDInsight stöder flera användare och mer detaljerade behörighets- och rollinställningar. Mer information finns i [Hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Domännamn

Det fullständigt kvalificerade domännamnet (FQDN) som ska användas vid `CLUSTERNAME.azurehdinsight.net` `CLUSTERNAME-ssh.azurehdinsight.net` anslutning till klustret från Internet är eller (endast för SSH).

Internt har varje nod i klustret ett namn som tilldelas under klusterkonfigurationen. Information om hur du hittar klusternamnen finns på sidan **Värdar** i webbgränssnittet för Ambari. Du kan också använda följande för att returnera en lista över värdar från Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Ersätt `CLUSTERNAME` med namnet på klustret. Ange lösenordet för administratörskontot när du uppmanas att göra det. Det här kommandot returnerar ett JSON-dokument som innehåller en lista över värdarna i klustret. [jq](https://stedolan.github.io/jq/) används för `host_name` att extrahera elementvärdet för varje värd.

Om du behöver hitta namnet på noden för en viss tjänst kan du fråga Ambari för den komponenten. Om du till exempel vill hitta värdarna för HDFS-namnnoden använder du följande kommando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Det här kommandot returnerar ett JSON-dokument som beskriver tjänsten `host_name` och [drar](https://stedolan.github.io/jq/) sedan ut värdet för värdarna.

## <a name="remote-access-to-services"></a>Fjärråtkomst till tjänster

* **Ambari (webb)** - `https://CLUSTERNAME.azurehdinsight.net`

    Autentisera med hjälp av klusteradministratörens användare och lösenord och logga sedan in på Ambari.

    Autentisering är klartext - använd alltid HTTPS för att säkerställa att anslutningen är säker.

    > [!IMPORTANT]  
    > Några av de webb-UIs tillgängliga via Ambari åtkomstnoder med ett internt domännamn. Interna domännamn är inte allmänt tillgängliga via Internet. Felmeddelanden kan visas när du försöker komma åt vissa funktioner via Internet.
    >
    > Om du vill använda alla funktioner i webbgränssnittet i Ambari använder du en SSH-tunnel för att proxywebbtrafik till klusterhuvudnoden. Se [Använda SSH-tunnel för att komma åt Apache Ambari webbgränssnitt, ResourceManager, JobHistory, NameNode, Oozie och andra webb-UIs](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (VILA)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Autentisera med hjälp av klusteradministratörens användare och lösenord.
    >
    > Autentisering är klartext - använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Autentisera med hjälp av klusteradministratörens användare och lösenord.
    >
    > Autentisering är klartext - använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net på port 22 eller 23. Port 22 används för att ansluta till den primära headnoden, medan 23 används för att ansluta till sekundärt. Mer information om huvudnoderna finns i Tillgänglighet och tillförlitlighet för [Apache Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Du kan bara komma åt klusterhuvudnoderna via SSH från en klientdator. När du är ansluten kan du sedan komma åt arbetarnoderna med hjälp av SSH från en headnode.

Mer information finns i [portarna som används av Apache Hadoop-tjänster i HDInsight-dokumentet.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Sökvägar

Hadoop-relaterade filer finns på klusternoderna `/usr/hdp`på . Den här katalogen innehåller följande underkataloger:

* **2.6.5.3009-43**: Katalognamnet är den version av Hadoop-plattformen som används av HDInsight. Numret i klustret kan vara annorlunda än det som anges här.
* **aktuell**: Den här katalogen innehåller länkar till underkataloger under katalogen **2.6.5.3009-43.** Den här katalogen finns så att du inte behöver komma ihåg versionsnumret.

Exempel på data och JAR-filer finns på `/example` Hadoop Distributed File System på och `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage och Data Lake Storage

I de flesta Hadoop-distributioner lagras data i HDFS, som backas upp av lokal lagring på datorerna i klustret. Det kan vara dyrt att använda lokal lagring för en molnbaserad lösning där du debiteras per timme eller per minut för beräkningsresurser.

När du använder HDInsight lagras datafilerna på ett skalbart och flexibelt sätt i molnet med Hjälp av Azure Blob Storage och eventuellt Azure Data Lake Storage. Dessa tjänster ger följande fördelar:

* Billig långtidsförvaring.
* Tillgänglighet från externa tjänster som webbplatser, filuppladdning/nedladdningsverktyg, olika språk-SDK:er och webbläsare.
* Stor filkapacitet och stor skalbar lagring.

Mer information finns i [Förstå blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) och [Lagring av datasjö .](https://azure.microsoft.com/services/storage/data-lake-storage/)

När du använder azure storage eller datasjölagring behöver du inte göra något speciellt från HDInsight för att komma åt data. Följande kommando visar till exempel `/example/data` filer i mappen oavsett om det lagras på Azure Storage eller Data Lake Storage:

    hdfs dfs -ls /example/data

I HDInsight frikopplas datalagringsresurserna (Azure Blob Storage och Azure Data Lake Storage) från beräkningsresurser. Därför kan du skapa HDInsight-kluster för att göra beräkning som du behöver, och senare ta bort klustret när arbetet är klart, under tiden hålla dina datafiler beständiga säkert i molnlagring så länge du behöver.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI och schema

Vissa kommandon kan kräva att du anger schemat som en del av URI:n när du öppnar en fil. Till exempel kräver Storm-HDFS-komponenten att du anger schemat. När du använder lagring som inte är standard (lagring tillagd som "ytterligare" lagring i klustret) måste du alltid använda schemat som en del av URI.When using non-default storage (storage added as "additional" storage to the cluster) måste du alltid använda schemat som en del av URI.

När du använder [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)använder du något av följande URI-scheman:

* `wasb:///`: Komma åt standardlagring med okrypterad kommunikation.

* `wasbs:///`: Få tillgång till standardlagring med hjälp av krypterad kommunikation.  Wasbs-schemat stöds endast från HDInsight version 3.6 och framåt.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Används när du kommunicerar med ett lagringskonto som inte är standard. Till exempel när du har ett ytterligare lagringskonto eller när du öppnar data som lagras i ett offentligt tillgängligt lagringskonto.

När du använder [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)använder du följande URI-schema:

* `abfs://`: Få tillgång till standardlagring med hjälp av krypterad kommunikation.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Används när du kommunicerar med ett lagringskonto som inte är standard. Till exempel när du har ett ytterligare lagringskonto eller när du öppnar data som lagras i ett offentligt tillgängligt lagringskonto.

När du använder [**Azure Data Lake Storage Gen1**](./hdinsight-hadoop-use-data-lake-store.md)använder du något av följande URI-scheman:

* `adl:///`: Öppna standardlagringen för datasjölagring för klustret.

* `adl://<storage-name>.azuredatalakestore.net/`: Används vid kommunikation med en datasjölagring som inte är standard. Används också för att komma åt data utanför rotkatalogen i ditt HDInsight-kluster.

> [!IMPORTANT]  
> När du använder Data Lake Storage som standardlagring för HDInsight måste du ange en sökväg i arkivet som ska användas som rot till HDInsight-lagring. Standardsökvägen `/clusters/<cluster-name>/`är .
>
> När `/` du `adl:///` använder eller kommer åt data kan du bara `/clusters/<cluster-name>/`komma åt data som lagras i klustrets rot (till exempel). Använd formatet för `adl://<storage-name>.azuredatalakestore.net/` att komma åt data var som helst i arkivet.

### <a name="what-storage-is-the-cluster-using"></a>Vilken lagring använder klustret

Du kan använda Ambari för att hämta standardlagringskonfigurationen för klustret. Använd följande kommando för att hämta HDFS-konfigurationsinformation med curl och filtrera den med [jq:](https://stedolan.github.io/jq/)

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Det här kommandot returnerar den`service_config_version=1`första konfigurationen som tillämpas på servern ( ), som innehåller den här informationen. Du kan behöva lista alla konfigurationsversioner för att hitta den senaste.

Det här kommandot returnerar ett värde som liknar följande URI:er:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`om du använder ett Azure Storage-konto.

    Kontonamnet är namnet på Azure Storage-kontot. Behållarnamnet är blob-behållaren som är roten till klusterlagringen.

* `adl://home`om du använder Azure Data Lake Storage. Om du vill hämta datasjölagringsnamnet använder du följande REST-anrop:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Det här kommandot returnerar `<data-lake-store-account-name>.azuredatalakestore.net`följande värdnamn: .

    Om du vill hämta katalogen i arkivet som är roten till HDInsight använder du följande REST-anrop:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Det här kommandot returnerar en `/clusters/<hdinsight-cluster-name>/`bana som liknar följande sökväg: .

Du kan också hitta lagringsinformationen med hjälp av Azure-portalen med hjälp av följande steg:

1. Välj ditt HDInsight-kluster i [Azure-portalen.](https://portal.azure.com/)

2. Välj **Lagringskonton**i avsnittet **Egenskaper** . Lagringsinformationen för klustret visas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hur kommer jag åt filer utanför HDInsight

Det finns olika sätt att komma åt data utanför HDInsight-klustret. Följande är några länkar till verktyg och SDK:er som kan användas för att arbeta med dina data:

Om du använder __Azure Storage__läser du följande länkar för hur du kan komma åt dina data:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Kommandoradsgränssnittskommandon för att arbeta med Azure. När du har `az storage` installerat använder du kommandot `az storage blob` för att få hjälp med att använda lagring eller för blob-specifika kommandon.
* [blobxfer.py](https://github.com/Azure/blobxfer): Ett pythonskript för att arbeta med blobbar i Azure Storage.
* Olika SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [Php](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Lagring REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Om du använder __Azure Data Lake Storage__läser du följande länkar för hur du kan komma åt dina data:

* [Webbläsare](../data-lake-store/data-lake-store-get-started-portal.md)
* [Powershell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [REST-API för WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Datasjöverktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Skala klustret

Med klusterskalningsfunktionen kan du dynamiskt ändra antalet datanoder som används av ett kluster. Du kan utföra skalningsåtgärder medan andra jobb eller processer körs i ett kluster.  Se även [Scale HDInsight-kluster](./hdinsight-scaling-best-practices.md)

De olika klustertyperna påverkas av skalning enligt följande:

* **Hadoop**: När du skalar ned antalet noder i ett kluster startas vissa av tjänsterna i klustret om. Skalningsåtgärder kan orsaka att jobb som körs eller väntar misslyckas när skalningsåtgärden har slutförts. Du kan skicka jobben igen när åtgärden är klar.
* **HBase**: Regionala servrar balanseras automatiskt inom några minuter när skalningsåtgärden är klar. Så här balanserar du regionala servrar manuellt:

    1. Anslut till HDInsight-klustret med SSH. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Använd följande för att starta HBase-skalet:

            hbase shell

    3. När HBase-skalet har lästs in använder du följande för att manuellt balansera de regionala servrarna:

            balancer

* **Storm:** Du bör balansera om alla löpstormtopologier efter att en skalningsoperation har utförts. Ombalansering gör att topologin kan justera parallellitetsinställningar baserat på det nya antalet noder i klustret. Om du vill balansera om körtopologier använder du något av följande alternativ:

    * **SSH**: Anslut till servern och använd följande kommando för att balansera en topologi:

            storm rebalance TOPOLOGYNAME

        Du kan också ange parametrar för att åsidosätta parallellitetstipsen som ursprungligen tillhandahölls av topologin. Om du `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` till exempel konfigurerar om topologin till 5 arbetsprocesser, 3 executors för den blå pipkomponenten och 10 utförare för komponenten med gul bult.

    * **Storm UI**: Använd följande steg för att balansera en topologi med hjälp av Storm UI.

        1. Öppna `https://CLUSTERNAME.azurehdinsight.net/stormui` i webbläsaren, `CLUSTERNAME` där är namnet på stormklustret. Om du uppmanas till det anger du det HDInsight-klusteradministratörsnamn och lösenord som du angav när du skapade klustret.
        2. Välj den topologi som du vill balansera om och välj sedan knappen **Balansera om.** Ange fördröjningen innan ombalanseringen utförs.

* **Kafka**: Du bör balansera om partitionsrepliker efter skalningsåtgärder. Mer information finns i hög [tillgänglighet för data med Apache Kafka i HDInsight-dokumentet.](./kafka/apache-kafka-high-availability.md)

Mer information om hur du skalar HDInsight-klustret finns i:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hur installerar jag Hue (eller annan Hadoop-komponent)?

HDInsight är en hanterad tjänst. Om Azure upptäcker ett problem med klustret kan den ta bort den felaktiga noden och skapa en nod för att ersätta den. Om du installerar saker manuellt i klustret sparas de inte kvar när den här åtgärden inträffar. Använd i stället [HDInsight Script Actions](hdinsight-hadoop-customize-cluster-linux.md). En skriptåtgärd kan användas för att göra följande ändringar:

* Installera och konfigurera en tjänst eller webbplats.
* Installera och konfigurera en komponent som kräver konfigurationsändringar på flera noder i klustret.

Skriptåtgärder är Bash-skript. Skripten körs när klustret skapas och används för att installera och konfigurera ytterligare komponenter. Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Jar-filer

Vissa Hadoop-tekniker finns i fristående jar-filer som innehåller funktioner som används som en del av ett MapReduce-jobb, eller inifrån Pig eller Hive. De kräver ofta inte någon installation och kan överföras till klustret när de har skapats och användas direkt. Om du vill vara säker på att komponenten överlever ominstallation av klustret kan du lagra jar-filen i standardlagringen för klustret (WASB eller ADL).

Om du till exempel vill använda den senaste versionen av [Apache DataFu](https://datafu.incubator.apache.org/)kan du hämta en burk som innehåller projektet och ladda upp den till HDInsight-klustret. Följ sedan DataFu-dokumentationen om hur du använder den från Pig eller Hive.

> [!IMPORTANT]  
> Vissa komponenter som är fristående jar-filer är försedda med HDInsight, men finns inte i sökvägen. Om du letar efter en viss komponent kan du använda följare för att söka efter den i klustret:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Det här kommandot returnerar sökvägen till matchande jar-filer.

Om du vill använda en annan version av en komponent laddar du upp den version du behöver och använder den i dina jobb.

> [!IMPORTANT]
> Komponenter som medföljer HDInsight-klustret stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem som är relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimligt stöd som hjälper dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för öppen källkod teknik där djup expertis för den tekniken finns. Till exempel finns det många community webbplatser som kan användas, som: [https://stackoverflow.com](https://stackoverflow.com) [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Även Apache projekt har [https://apache.org](https://apache.org)projektwebbplatser på , till exempel: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce-jobb med HDInsight](hadoop/hdinsight-use-mapreduce.md)
