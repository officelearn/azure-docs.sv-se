---
title: "Tips om att använda Hadoop på Linux-baserade HDInsight - Azure | Microsoft Docs"
description: "Hämta implementering tips för att använda kluster för Linux-baserat HDInsight (Hadoop) på en välbekant miljö för Linux körs i Azure-molnet."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: befd03d94f816cb2b59219cd9f1f9af238949592
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="information-about-using-hdinsight-on-linux"></a>Information om hur du använder HDInsight på Linux

Azure HDInsight-kluster tillhandahåller Hadoop på en välbekant miljö för Linux, körs i Azure-molnet. För de flesta saker, bör det fungerar precis som andra Hadoop på Linux-installationen. Det här dokumentet visar specifika skillnader som du bör vara medveten om.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Krav

Många av stegen i det här dokumentet använder följande verktyg, som kan behöva installeras på datorn.

* [cURL](https://curl.haxx.se/) – används för att kommunicera med webbtjänster
* [jq](https://stedolan.github.io/jq/) – används för att parsa JSON-dokument
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (förhandsvisning) – används för att hantera Azure-tjänster

## <a name="users"></a>Användare

Om inte [domänanslutna](./domain-joined/apache-domain-joined-introduction.md), HDInsight ska betraktas som en **enanvändarläge** system. En SSH-användarkontot skapas med i klustret, med administratörsbehörighet för nivån. Ytterligare SSH-konton kan skapas, men de har också administratörsåtkomst till klustret.

Domänanslutna HDInsight har stöd för flera användare och mer detaljerade inställningar för behörighet och roll. Mer information finns i [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Domännamn

Det fullständigt kvalificerade domännamnet (FQDN) ska användas vid anslutning till klustret från internet är  **&lt;klusternamn >. azurehdinsight.net** eller (SSH)  **&lt;klusternamn-ssh >. azurehdinsight.NET**.

Varje nod i klustret har internt, ett namn som tilldelas under klusterkonfigurationen. Du hittar klusternamn den **värdar** sida på Ambari-Webbgränssnittet. Du kan också använda följande för att returnera en lista över värdar från Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Ersätt **CLUSTERNAME** med namnet på klustret. När du uppmanas, ange lösenordet för administratörskontot. Det här kommandot returnerar ett JSON-dokument som innehåller en lista över värdar i klustret. Jq används för att extrahera den `host_name` elementvärde för varje värd.

Om du behöver att hitta namnet på noden för en specifik tjänst kan fråga du Ambari för den komponenten. Till exempel för att hitta värdar för noden HDFS namn, använder du följande kommando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Det här kommandot returnerar ett JSON-dokument som beskriver tjänsten och sedan jq hämtar endast den `host_name` värde för värdarna.

## <a name="remote-access-to-services"></a>Fjärråtkomst till tjänster

* **Ambari (webb)** -https://&lt;klusternamn >. azurehdinsight.net

    Autentisera med hjälp av kluster-administratör och lösenordet och sedan logga in på Ambari.

    Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

    > [!IMPORTANT]
    > Vissa av användargränssnitt som är tillgängliga via Ambari åt noder med hjälp av ett internt domännamn. Interna domännamn är inte offentligt tillgänglig via internet. Felmeddelandet ”Det gick inte att hitta” fel vid försök att komma åt vissa funktioner via Internet.
    >
    > Om du vill använda den fullständiga funktionaliteten hos Ambari-webbgränssnittet, använda en SSH-tunnel till proxy webbtrafik till klustrets huvudnod. Se [Använd SSH-tunnlar för att komma åt Ambari-webbgränssnittet, resurshanteraren, jobbhistorik, NameNode, Oozie och andra webb-användargränssnitt](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;klusternamn >.azurehdinsight.net/ambari

    > [!NOTE]
    > Autentisera med hjälp av kluster-administratör och lösenord.
    >
    > Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

* **WebHCat (Templeton)** -https://&lt;klusternamn >.azurehdinsight.net/templeton

    > [!NOTE]
    > Autentisera med hjälp av kluster-administratör och lösenord.
    >
    > Autentisering är klartext - alltid använda HTTPS för att säkerställa att anslutningen är säker.

* **SSH** - &lt;klusternamn >-ssh.azurehdinsight.net på port 22 eller 23. Att ansluta till den primära headnode 23 används för att ansluta till sekundärt används port 22. Mer information om huvudnoderna finns i [Tillgänglighet och tillförlitlighet för Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Du kan bara komma åt klustrets huvudnoder via SSH från en klientdator. När du är ansluten, du kan sedan komma åt arbetsnoderna med hjälp av SSH från en headnode.

## <a name="file-locations"></a>Sökvägar

Hadoop-relaterade filer kan hittas på klusternoder på `/usr/hdp`. Den här katalogen innehåller följande undermappar:

* **2.2.4.9-1**: katalognamnet är versionen av Hortonworks Data Platform som används av HDInsight. Numret på ditt kluster kan vara annorlunda än den som anges här.
* **aktuella**: den här katalogen innehåller länkar till underkataloger på den **2.2.4.9-1** directory. Denna katalog finns så att du inte behöver komma ihåg versionsnumret.

Exempeldata och JAR-filer kan hittas på Hadoop Distributed File System på `/example` och`/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS och Azure Storage Data Lake Store

I de flesta Hadoop distributioner stöds HDFS av lokal lagring på datorer i klustret. Använder lokal lagring kan vara kostsamma för en molnbaserad lösning där du debiteras timvis eller per minut för beräkningsresurser.

HDInsight använder antingen blobbar i Azure Storage eller Azure Data Lake Store som standard Arkiv. Dessa tjänster ger följande fördelar:

* Billig långsiktig lagring
* Åtkomst från externa tjänster, till exempel webbplatser, filen överför/hämta verktyg, SDK: er med olika språk och webbläsare

> [!WARNING]
> HDInsight stöder endast __allmänna__ Azure Storage-konton. För närvarande stöder inte den __Blob storage__ kontotyp.

Ett Azure Storage-konto kan innehålla upp till 4,75 TB, även om enskilda blobbar (eller filer från ett HDInsight-perspektiv) kan du bara gå upp till 195 GB. Azure Data Lake Store kan växa dynamiskt för att rymma BILJONTALS filer med enskilda filer som är större än en petabyte. Mer information finns i [förstå blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) och [Datasjölager](https://azure.microsoft.com/services/data-lake-store/).

När du använder antingen Azure Storage eller Azure Data Lake Store kan du inte vidta några särskilda åtgärder från HDInsight för att komma åt data. Till exempel följande kommando visar filer i den `/example/data` mappen oavsett om den är lagrad på Azure Storage eller Azure Data Lake Store:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI: N och schema

Vissa kommandon kan kräva att ange schemat som en del av URI vid åtkomst till en fil. Till exempel måste Storm-HDFS-komponent du ange schemat. När du använder icke-förvalt lagring (storage lagts till som ”ytterligare” lagringsutrymme i klustret), måste du alltid använda schemat som en del av URI: N.

När du använder __Azure Storage__, Använd en av de följande URI-scheman:

* `wasb:///`: Åtkomst standardlagring använder okrypterad kommunikation.

* `wasbs:///`: Standard lagring med krypterad kommunikation.  Wasbs-scheman stöds endast från HDInsight version 3,6 och senare.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Används vid kommunikation med ett icke-förvalt storage-konto. Till exempel om du har ett konto för ytterligare lagringsutrymme eller när åtkomst till data lagras i ett offentligt tillgänglig storage-konto.

När du använder __Datasjölager__, Använd en av de följande URI-scheman:

* `adl:///`: Komma åt standard Data Lake Store för klustret.

* `adl://<storage-name>.azuredatalakestore.net/`: Används vid kommunikation med en icke-förvalt Data Lake Store. Används också för att komma åt data utanför rotkatalogen för din HDInsight-kluster.

> [!IMPORTANT]
> När du använder Data Lake Store som standardlagringsplatsen för HDInsight, måste du ange en sökväg i arkivet för att använda som rot för HDInsight-lagringen. Standardsökvägen är `/clusters/<cluster-name>/`.
>
> När du använder `/` eller `adl:///` komma åt data kan du bara komma åt data som lagras i roten (till exempel `/clusters/<cluster-name>/`) i klustret. Komma åt data var som helst i arkivet kan använda den `adl://<storage-name>.azuredatalakestore.net/` format.

### <a name="what-storage-is-the-cluster-using"></a>Vilka lagring klustret använder

Du kan använda Ambari för att hämta standardkonfigurationen för lagring för klustret. Använd följande kommando för att hämta konfigurationsinformation för HDFS med curl och filtrera den med hjälp av [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Det här kommandot returnerar den första konfigurationen tillämpas på servern (`service_config_version=1`), som innehåller den här informationen. Du kan behöva lista alla versioner för konfigurationen hittar det senaste.

Det här kommandot returnerar ett värde som liknar följande URI: er:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`Om du använder ett Azure Storage-konto.

    Kontonamnet är namnet på det Azure Storage-kontot. Behållarens namn är blob-behållaren som är roten till klusterlagringen.

* `adl://home`Om du använder Azure Data Lake Store. Använd följande REST-anrop för att få Data Lake Store-namn:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Det här kommandot returnerar följande värdnamn: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Använd följande REST-anrop för att hämta katalogen i arkivet som är rot för HDInsight:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Det här kommandot returnerar en sökväg liknar följande sökväg: `/clusters/<hdinsight-cluster-name>/`.

Du kan också hitta i informationen med Azure-portalen med hjälp av följande steg:

1. I den [Azure-portalen](https://portal.azure.com/), Välj ditt HDInsight-kluster.

2. Från den **egenskaper** väljer **Lagringskonton**. Storage-informationen för klustret visas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hur kommer jag åt filer från utanför HDInsight

Det finns olika sätt att få åtkomst till data utanför HDInsight-klustret. Följande är några länkar till verktyg och SDK: er som kan användas för att arbeta med dina data:

Om du använder __Azure Storage__, se följande länkar för olika sätt att du kan komma åt dina data:

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): kommandoradsgränssnittet kommandon för att arbeta med Azure. När du har installerat, Använd den `az storage` kommandot för hjälp med att använda lagring, eller `az storage blob` för blob-fil.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): en python-skript för att arbeta med blobbar i Azure Storage.
* Olika SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Om du använder __Azure Data Lake Store__, se följande länkar för olika sätt att du kan komma åt dina data:

* [Webbläsare](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI 2.0](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake-verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Skalning av klustret

Klustret skalning funktionen kan du ändra antalet datanoder som används av ett kluster dynamiskt. Du kan utföra skalning åtgärder när andra jobb eller processer som körs på ett kluster.

Olika klustertyper påverkas av skalning enligt följande:

* **Hadoop**: vid skalning av antalet noder i ett kluster, en del av tjänsterna i klustret har startats om. Skalning åtgärder kan orsaka jobb körs eller väntar misslyckas vid skalning åtgärden slutfördes. Du kan skicka jobb när åtgärden har slutförts.
* **HBase**: regionala servrar balanseras automatiskt inom några minuter när skalning åtgärden har slutförts. Om du vill utjämna manuellt regionala servrar, Använd följande steg:

    1. Anslut till HDInsight-klustret via SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

    2. Använd följande för att starta HBase-gränssnittet:

            hbase shell

    3. När HBase-gränssnittet har lästs in, Använd följande för att balansera regionala servrar manuellt:

            balancer

* **Storm**: du bör balansera alla Storm-topologier som körs när en skalning åtgärden har utförts. Ombalansering gör att topologin kan justera parallellitet inställningar baserat på det nya antalet noder i klustret. För att balansera om topologier som körs, använder du något av följande alternativ:

    * **SSH**: ansluta till servern och använder du följande kommando för att balansera en topologi:

            storm rebalance TOPOLOGYNAME

        Du kan också ange parametrar för att åsidosätta parallellitet tipsen ursprungligen tillhandahålls av topologin. Till exempel `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` konfigurerar topologi 5 arbetsprocesser, 3 Utförarna för komponenten blå kanal och 10 Utförarna för komponenten gul bulten.

    * **Storm-Användargränssnittet**: Använd följande steg för att balansera en topologi med hjälp av Storm-Användargränssnittet.

        1. Öppna **https://CLUSTERNAME.azurehdinsight.net/stormui** i webbläsaren, där KLUSTERNAMN är namnet på Storm-kluster. Om du uppmanas ange HDInsight-kluster administratör (admin) namnet och lösenordet du angav när du skapar klustret.
        2. Välj den topologi som du inte vill att balansera och välj sedan den **balansera** knappen. Ange fördröjningen innan balansera åtgärden utförs.

Specifik information om att skala HDInsight-kluster, se:

* [Hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hur installerar jag Hue (eller andra Hadoop-komponenter)?

HDInsight är en hanterad tjänst. Om Azure upptäcker ett problem med klustret, kan det ta bort noden misslyckas och skapa en nod för att ersätta den. Du kan manuellt installera saker på klustret, är inte beständiga när den här åtgärden genomförs. Använd i stället [HDInsight-skriptåtgärder](hdinsight-hadoop-customize-cluster.md). En skriptåtgärd kan användas för att göra följande ändringar:

* Installera och konfigurera en tjänst eller en webbplats.
* Installera och konfigurera en komponent som kräver konfigurationsändringar på flera noder i klustret.

Skriptåtgärder är Bash-skript. Skripten kör när klustret skapas och används för att installera och konfigurera ytterligare komponenter. Exempelskript tillhandahålls för att installera följande komponenter:

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>JAR-filer

Vissa Hadoop-teknik finns i självständiga jar-filer som innehåller funktioner som används som en del av ett MapReduce-jobb, eller från inuti Pig- eller Hive. De kan ofta inte kräver några inställningar och överföras till klustret när den har skapats och användas direkt. Om du vill kontrollera att komponenten FRISKRIVNINGEN återställning av avbildning i klustret kan du lagra jar-filen i standardlagring för klustret (WASB eller ADL).

Om du vill använda den senaste versionen av till exempel [DataFu](http://datafu.incubator.apache.org/), du kan hämta en jar som innehåller projektet och överföra den till HDInsight-klustret. Följ sedan DataFu-dokumentationen om hur du använder från Pig eller Hive.

> [!IMPORTANT]
> Vissa komponenter som är fristående jar-filer med HDInsight, men är inte i sökvägen. Om du letar efter en viss komponent använder du följande för att söka efter den i klustret:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Det här kommandot returnerar sökvägen till några matchande jar-filer.

Överför den version som du behöver och använda i dina jobb för att använda en annan version av en komponent.

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut och Microsoft-supporten hjälper att isolera och lösa problem relaterade till komponenterna.
>
> Anpassade komponenter få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för öppen källkod där djup expertis för att teknik finns. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Apache-projekt har också project-webbplatser [http://apache.org](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Nästa steg

* [Migrera från Windows-baserade HDInsight till Linux-baserade](hdinsight-migrate-from-windows-to-linux.md)
* [Använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hadoop/hdinsight-use-mapreduce.md)
