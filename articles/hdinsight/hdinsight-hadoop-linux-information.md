---
title: Tips för att använda Hadoop på Linux-baserade HDInsight - Azure
description: Få implementering tips för att använda kluster för Linux-baserat HDInsight (Hadoop) på en välbekant miljö för Linux som körs i Azure-molnet.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/09/2018
ms.openlocfilehash: 95d8825b8359b0ba8649c4c4e145ef488a486b21
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001931"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Information om hur du använder HDInsight på Linux

Azure HDInsight-kluster tillhandahåller Apache Hadoop på en välbekant miljö för Linux, som körs i Azure-molnet. För de flesta saker, bör det fungera precis som andra Hadoop på Linux-installation. Det här dokumentet anropar specifika skillnader som du bör känna till.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Förutsättningar

Många av stegen i det här dokumentet använder följande verktyg, som kan behöva installeras på datorn.

* [cURL](https://curl.haxx.se/) – används för att kommunicera med webbtjänster.
* [jq](https://stedolan.github.io/jq/) – används för att parsa JSON-dokument.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) – används för att hantera Azure-tjänster.

## <a name="users"></a>Användare

Om inte [domänanslutna](./domain-joined/apache-domain-joined-introduction.md), HDInsight ska betraktas som en **enanvändarläge** system. En enkel SSH-användarkontot har skapats med klustret, med nivå administratörsbehörighet. Ytterligare SSH-konton kan skapas, men de har också administratörsåtkomst till klustret.

Domänanslutna HDInsight har stöd för flera användare och mer detaljerade inställningar för behörighet och rollen. Mer information finns i [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Domännamn

Det fullständigt kvalificerade domännamnet (FQDN) ska användas vid anslutning till klustret från internet är  **&lt;klusternamn >. azurehdinsight.net** eller (för SSH)  **&lt;clustername-ssh >. azurehdinsight.NET**.

Varje nod i klustret har internt, ett namn som tilldelas under klusterkonfigurationen. Du hittar klustrets namn i den **värdar** sidan på Ambari-Webbgränssnittet. Du kan också använda följande för att returnera en lista över värdar från Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Ersätt **CLUSTERNAME** med namnet på klustret. När du uppmanas, anger du lösenordet för administratörskontot. Det här kommandot returnerar ett JSON-dokument som innehåller en lista över värdar i klustret. Jq används för att extrahera den `host_name` elementvärde för varje värd.

Om du vill hitta namnet på noden för en specifik tjänst kan fråga du Ambari för respektive komponent. Till exempel för att hitta värdarna för noden HDFS namn, använder du följande kommando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Det här kommandot returnerar ett JSON-dokument som beskriver tjänsten och sedan jq hämtar endast den `host_name` värde för värdarna.

## <a name="remote-access-to-services"></a>Fjärråtkomst till tjänster

* **Ambari (webb)** - https://&lt;klusternamn >. azurehdinsight.net

    Autentisera med hjälp av kluster-administratörsanvändare och lösenord och sedan logga in på Ambari.

    Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

    > [!IMPORTANT]  
    > Några av web UIs som är tillgängliga via Ambari åtkomst till noder med en intern domännamn. Interna domännamn är inte tillgängliga för allmänheten via internet. Felmeddelandet ”Det gick inte att hitta” fel vid åtkomst till vissa funktioner via Internet.
    >
    > Om du vill använda den fullständiga funktionaliteten hos Ambari-webbgränssnittet, använda en SSH-tunnel för proxy webbtrafik till klustrets huvudnod. Se [använda SSH-tunnlar till Apache Ambari-webbgränssnittet, resurshanteraren, JobHistory, NameNode, Oozie och andra web UIs](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;klusternamn >.azurehdinsight.net/ambari

    > [!NOTE]  
    > Autentisera med klustret administratörsanvändare och lösenord.
    >
    > Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

* **WebHCat (Templeton)** - https://&lt;klusternamn >.azurehdinsight.net/templeton

    > [!NOTE]  
    > Autentisera med klustret administratörsanvändare och lösenord.
    >
    > Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

* **SSH** - &lt;klusternamn >-ssh.azurehdinsight.net på port 22 eller 23. Att ansluta till den primära huvudnoden 23 används för att ansluta till sekundärt används port 22. Mer information om huvudnoderna finns i [tillgänglighet och tillförlitlighet för Apache Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Du kan bara komma åt huvudnoderna klustret via SSH från en klientdator. När du är ansluten, kan du sedan komma åt arbetsnoderna genom att använda SSH från en huvudnod.

Mer information finns i den [portar som används av Apache Hadoop-tjänster på HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentet.

## <a name="file-locations"></a>Sökvägar

Hadoop-relaterade filer kan hittas på klusternoder på `/usr/hdp`. Den här katalogen innehåller följande undermappar:

* **2.2.4.9-1**: Katalognamnet är versionen av Hortonworks Data Platform som används av HDInsight. Numret på ditt kluster kan skilja sig från den som anges här.
* **aktuella**: Den här katalogen innehåller länkar till underkataloger på den **2.2.4.9-1** directory. Den här katalogen finns så att du inte behöver komma ihåg det lägre versionsnumret.

Exempeldata och JAR-filerna finns på Hadoop Distributed File System på `/example` och `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage och Data Lake Storage

I de flesta Hadoop-distributioner kan lagras data i HDFS som backas upp av lokal lagring på datorerna i klustret. Använda lokal lagring kan vara kostsamma för en molnbaserad lösning där du debiteras per timme eller minut för beräkningsresurser.

När du använder HDInsight lagras datafiler i ett skalbart och återhämtningsbart sätt i molnet med Azure Blob Storage och eventuellt Azure Data Lake Storage. De här tjänsterna ger följande fördelar:

* Billiga långsiktig lagring.
* Åtkomst från externa tjänster som websites, filen uppladdning/nedladdning verktyg, SDK: er med olika språk och webbläsare.
* Stor filkapacitet och stora skalbar lagring.

Mer information finns i [förstå blobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) och [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

När du använder Azure Storage eller Data Lake Storage kan behöver du inte göra något speciellt från HDInsight för att komma åt data. Till exempel följande kommando visar filer i den `/example/data` mappen oavsett om den är lagrad på Azure Storage eller Data Lake Storage:

    hdfs dfs -ls /example/data

I HDInsight, är lagringsresurser (Azure Blob Storage och Azure Data Lake Storage) fristående från beräkningsresurser. Därför kan du skapa HDInsight-kluster för att göra beräkningar som du behöver och tar bort klustret när arbetet är klart, under tiden att hålla dina filer sparas på ett säkert sätt i molnet minnet så länge du behöver.

### <a name="uri-and-scheme"></a>URI: N och schema

Vissa kommandon kan kräva att du kan ange schemat som en del av URI: N vid åtkomst till en fil. Till exempel måste Storm-HDFS-komponent du ange schemat. När du använder icke-standard storage (lagring har lagts till som ”ytterligare” lagringsutrymme i klustret), måste du alltid använda schemat som en del av URI: N.

När du använder __Azure Storage__, Använd någon av följande URI-scheman:

* `wasb:///`: Åtkomst till standard storage med hjälp av dekrypterade kommunikation.

* `wasbs:///`: Standard storage med krypterad kommunikation.  Wasbs-schemat stöds endast från HDInsight version 3.6 och senare.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Används vid kommunikation med ett icke-standard storage-konto. Till exempel när du har ett annat lagringskonto eller när åtkomst till data som lagras i ett offentligt tillgänglig storage-konto.

När du använder __Data Lake Storage__, Använd någon av följande URI-scheman:

* `adl:///`: Åtkomst till standard Data Lake-lagring för klustret.

* `adl://<storage-name>.azuredatalakestore.net/`: Används vid kommunikation med en icke-standard Data Lake Storage. Används också för att komma åt data utanför rotkatalogen för din HDInsight-kluster.

> [!IMPORTANT]  
> När du använder Data Lake Storage som standardlagringsplatsen för HDInsight, måste du ange en sökväg i arkivet för att använda som rot för HDInsight storage. Standardsökvägen är `/clusters/<cluster-name>/`.
>
> När du använder `/` eller `adl:///` för att komma åt data, kan du bara komma åt data som lagras i roten (till exempel `/clusters/<cluster-name>/`) i klustret. Om du vill komma åt data var som helst i arkivet måste använda den `adl://<storage-name>.azuredatalakestore.net/` format.

### <a name="what-storage-is-the-cluster-using"></a>Vilka storage klustret använder

Du kan använda Ambari för att hämta konfigurationen för standard-lagring för klustret. Använd följande kommando för att hämta konfigurationsinformation för HDFS med curl och filtrera den med hjälp av [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]  
> Det här kommandot returnerar den första konfigurationen tillämpas på servern (`service_config_version=1`), som innehåller den här informationen. Du kan behöva lista alla konfigurationsversionerna hittar det senaste.

Det här kommandot returnerar ett värde som liknar följande URI: er:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Om du använder ett Azure Storage-konto.

    Kontonamnet är namnet på Azure Storage-kontot. Behållarens namn är blobbehållare som är roten för klusterlagring.

* `adl://home` Om du använder Azure Data Lake Storage. Använd följande REST-anrop för att få namnet på Data Lake Storage:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Det här kommandot returnerar följande värdnamn: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Använd följande REST-anrop för att hämta katalogen i arkivet som är roten för HDInsight:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Det här kommandot returnerar en sökväg som liknar följande sökväg: `/clusters/<hdinsight-cluster-name>/`.

Du kan också hitta i informationen med hjälp av Azure portal med hjälp av följande steg:

1. I den [Azure-portalen](https://portal.azure.com/), Välj ditt HDInsight-kluster.

2. Från den **egenskaper** väljer **Lagringskonton**. I informationen för klustret visas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hur jag för att komma åt filer från utanför HDInsight

Det finns en olika sätt att komma åt data från utanför HDInsight-kluster. Här följer några länkar till verktyg och SDK: er som kan användas för att arbeta med dina data:

Om du använder __Azure Storage__, se följande länkar för sätt att du kan komma åt dina data:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Kommandon för att arbeta med Azure. När installationen är klar att använda den `az storage` kommandot för att få hjälp om hur du använder lagring, eller `az storage blob` för blob-fil.
* [blobxfer.PY](https://github.com/Azure/blobxfer): Ett python-skript för att arbeta med blobar i Azure Storage.
* Olika SDK: er:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Om du använder __Azure Data Lake Storage__, se följande länkar för sätt att du kan komma åt dina data:

* [Webbläsare](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Skalning av klustret

Klusterskalning funktionen kan du ändra dynamiskt antalet datanoder som används av ett kluster. Du kan utföra skalningsåtgärder samtidigt som andra jobb eller processer som körs på ett kluster.

Olika klustertyper påverkas genom att skala på följande sätt:

* **Hadoop**: När du skalar ned antalet noder i ett kluster, några av tjänsterna i klustret startas om. Skalningsåtgärder kan orsaka jobb körs eller väntar på misslyckas vid skalning åtgärden slutfördes. Du kan skicka jobb när åtgärden har slutförts.
* **HBase**: Regionservrar balanseras automatiskt inom några minuter när skalning åtgärden har slutförts. För att jämna ut regionservrar manuellt, använder du följande steg:

    1. Ansluta till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

    2. Använd följande för att starta HBase-gränssnittet:

            hbase shell

    3. När HBase-gränssnittet har lästs in, kan du använda följande för att balansera regionservrar manuellt:

            balancer

* **Storm**: Du bör balansera om alla Storm-topologier som körs när en åtgärd för skalning har utförts. Ombalansering gör att topologin kan justera parallelliteten inställningar baserat på det nya antalet noder i klustret. Använd någon av följande alternativ för att balansera om topologier som körs:

    * **SSH**: Ansluta till servern och Använd följande kommando för att balansera om en topologi:

            storm rebalance TOPOLOGYNAME

        Du kan också ange parametrar för att åsidosätta parallellitet tipsen ursprungligen tillhandahålls av topologin. Till exempel `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` konfigurerar om topologin 5 arbetsprocesser, 3 executors för komponenten blå-kanal och 10 executors för komponenten gult bulten.

    * **Storm-Användargränssnittet**: Använd följande steg för att balansera om en topologi med hjälp av Storm-Användargränssnittet.

        1. Öppna **https://CLUSTERNAME.azurehdinsight.net/stormui** i webbläsaren, där KLUSTERNAMN är namnet på ditt Storm-kluster. Om du uppmanas ange HDInsight-klusternamnet administrator (administratör) och lösenordet du angav när klustret skapas.
        2. Välj topologi som du inte vill att balansera om, och välj sedan den **balansera om** knappen. Ange fördröjningen innan ombalanseringen utförs.

* **Kafka**: Du bör balansera om partitionsrepliker efter skalningsåtgärder. Mer information finns i den [hög tillgänglighet för data med Apache Kafka på HDInsight](./kafka/apache-kafka-high-availability.md) dokumentet.

Specifik information om att skala ditt HDInsight-kluster finns i:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hur installerar jag Hue (eller andra Hadoop-komponenterna)?

HDInsight är en hanterad tjänst. Om Azure upptäcker ett problem med klustret, kan det ta bort noden misslyckas eller skapa en nod för att ersätta den. Du kan manuellt installera saker i klustret, är inte beständiga när åtgärden inträffar. Använd i stället [HDInsight-skriptåtgärder](hdinsight-hadoop-customize-cluster.md). Du kan använda en skriptåtgärd för att göra följande ändringar:

* Installera och konfigurera en tjänst eller webbplats.
* Installera och konfigurera en komponent som kräver ändringar i konfigurationen på flera noder i klustret.

Skriptåtgärder är Bash-skript. Skripten kör när klustret skapas och används för att installera och konfigurera ytterligare komponenter. Exempelskript tillhandahålls för att installera följande komponenter:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Apache Solr](hdinsight-hadoop-solr-install-linux.md)

Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>JAR-filer

Vissa Hadoop-teknik finns i fristående jar-filer som innehåller funktioner som används som en del av ett MapReduce-jobb eller från inuti Pig- eller Hive. De kan ofta inte kräver några inställningar och laddas upp till klustret när du har skapat och användas direkt. Om du vill kontrollera att komponenten överlever nedgångar avbildningen av klustret kan du lagra jar-filen i standardlagringen för klustret (WASB eller ADL).

Exempel: Om du vill använda den senaste versionen av [Apache DataFu](https://datafu.incubator.apache.org/), du kan hämta JAR-filen som innehåller projektet och överföra den till HDInsight-klustret. Följ DataFu dokumentationen om hur du använder den från Pig- eller Hive.

> [!IMPORTANT]  
> Vissa komponenter som är fristående jar-filerna tillhandahålls med HDInsight, men är inte i sökvägen. Om du letar efter en viss komponent använder du Följ för att söka efter den i klustret:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Det här kommandot returnerar sökvägen till alla matchande jar-filer.

Om du vill använda en annan version av en komponent att ladda upp den version som du behöver och kan använda i dina jobb.

> [!WARNING]
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ https://apache.org ](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Nästa steg

* [Migrera från Windows-baserade HDInsight till Linux-baserade](hdinsight-migrate-from-windows-to-linux.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hadoop/hdinsight-use-mapreduce.md)
