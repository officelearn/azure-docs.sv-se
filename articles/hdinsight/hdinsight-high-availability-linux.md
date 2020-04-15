---
title: Hög tillgänglighet för Hadoop - Azure HDInsight
description: Lär dig hur HDInsight-kluster förbättrar tillförlitligheten och tillgängligheten med hjälp av ytterligare en huvudnod. Lär dig hur detta påverkar Hadoop-tjänster som Ambari och Hive, samt hur du ansluter individuellt till varje huvudnod med SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop hög tillgänglighet
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381400"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Tillgänglighet och tillförlitlighet för Apache Hadoop-kluster i HDInsight

HDInsight-kluster tillhandahåller två huvudnoder för att öka tillgängligheten och tillförlitligheten hos Apache Hadoop-tjänster och jobb som körs.

Hadoop uppnår hög tillgänglighet och tillförlitlighet genom att replikera tjänster och data över flera noder i ett kluster. Standarddistributioner av Hadoop har dock vanligtvis bara en enda huvudnod. Alla avbrott i noden med ett huvud kan leda till att klustret slutar fungera. HDInsight tillhandahåller två headnodes för att förbättra Hadoops tillgänglighet och tillförlitlighet.

## <a name="availability-and-reliability-of-nodes"></a>Noders tillgänglighet och tillförlitlighet

Noder i ett HDInsight-kluster implementeras med hjälp av virtuella Azure-datorer. I följande avsnitt beskrivs de enskilda nodtyperna som används med HDInsight.

> [!NOTE]  
> Alla nodtyper används inte för en klustertyp. En Hadoop-klustertyp har till exempel inga Nimbus-noder. Mer information om noder som används av HDInsight-klustertyper finns i avsnittet Klustertyper i De [Skapa Linux-baserade Hadoop-klustren i HDInsight-dokumentet.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

### <a name="head-nodes"></a>Huvudnoder

För att säkerställa hög tillgänglighet för Hadoop-tjänster tillhandahåller HDInsight två huvudnoder. Båda huvudnoderna är aktiva och körs i HDInsight-klustret samtidigt. Vissa tjänster, till exempel Apache HDFS eller Apache Hadoop YARN, är bara "aktiva" på en huvudnod vid en given tidpunkt. Andra tjänster som HiveServer2 eller Hive MetaStore är aktiva på båda huvudnoderna samtidigt.

Om du vill hämta värdnamnen för olika nodtyper i klustret använder du [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Associera inte det numeriska värdet med om en nod är primär eller sekundär. Det numeriska värdet finns bara för att ge ett unikt namn för varje nod.

### <a name="nimbus-nodes"></a>Nimbus noder

Nimbus-noder är tillgängliga med Apache Storm-kluster. Nimbus-noderna ger liknande funktioner som Hadoop JobTracker genom att distribuera och övervaka bearbetning över arbetsnoder. HDInsight tillhandahåller två Nimbus-noder för Storm-kluster

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper noder

[ZooKeeper](https://zookeeper.apache.org/) noder används för ledare val av master tjänster på huvudet noder. De används också för att försäkra att tjänster, data (arbetare) noder och gateways vet vilken huvudnod en huvudtjänst är aktiv på. Som standard tillhandahåller HDInsight tre ZooKeeper-noder.

### <a name="worker-nodes"></a>Arbetsnoder

Arbetarnoder utför den faktiska dataanalysen när ett jobb skickas till klustret. Om en arbetsnod misslyckas skickas uppgiften som den utförde till en annan arbetsnod. Som standard skapar HDInsight fyra arbetsnoder. Du kan ändra det här numret så att det passar dina behov både under och efter att klustret har skapats.

### <a name="edge-node"></a>kantnod

En kantnod deltar inte aktivt i dataanalys i klustret. Den används av utvecklare eller datavetar forskare när de arbetar med Hadoop. Kantnoden finns i samma Virtuella Azure-nätverk som de andra noderna i klustret och kan komma åt alla andra noder direkt. Kantnoden kan användas utan att ta resurser från kritiska Hadoop-tjänster eller analysjobb.

För närvarande är ML Services på HDInsight den enda klustertyp som tillhandahåller en kantnod som standard. För ML Services på HDInsight används kantnoden test R-kod lokalt på noden innan den skickas till klustret för distribuerad bearbetning.

Information om hur du använder en kantnod med andra klustertyper finns [i använda kantnoderna i HDInsight-dokumentet.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Komma åt noderna

Åtkomst till klustret via Internet tillhandahålls via en offentlig gateway. Åtkomsten är begränsad till anslutning till huvudnoderna och, om det finns någon, kantnoden. Åtkomst till tjänster som körs på huvudnoderna påverkas inte av flera huvudnoder. Den offentliga gatewayen dirigerar begäranden till huvudnoden som är värd för den begärda tjänsten. Om Apache Ambari till exempel finns på den sekundära huvudnoden dirigerar gatewayen inkommande begäranden för Ambari till den noden.

Åtkomst över den offentliga gatewayen är begränsad till portarna 443 (HTTPS), 22 och 23.

|Port |Beskrivning |
|---|---|
|443|Används för att komma åt Ambari och andra webbgränssnitt eller REST API: er som finns på huvudet noder.|
|22|Används för att komma åt den primära huvudnoden eller kantnoden med SSH.|
|23|Används för att komma åt den sekundära huvudnoden med SSH. Till exempel `ssh username@mycluster-ssh.azurehdinsight.net` ansluter till den primära huvudnoden i klustret som heter **mycluster**.|

Mer information om hur du använder SSH finns i använd [SSH med HDInsight-dokument.](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interna fullständigt kvalificerade domännamn (FQDN)

Noder i ett HDInsight-kluster har en intern IP-adress och FQDN som endast kan nås från klustret. När du öppnar tjänster i klustret med hjälp av den interna FQDN- eller IP-adressen bör du använda Ambari för att verifiera IP- eller FQDN-åtkomsten när du öppnar tjänsten.

Apache Oozie-tjänsten kan till exempel bara köras på `oozie` en huvudnod, och om du använder kommandot från en SSH-session krävs URL:en till tjänsten. Den här WEBBADRESSen kan hämtas från Ambari med hjälp av följande kommando:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Det här kommandot returnerar ett värde som liknar följande, `oozie` som innehåller den interna URL:en som ska användas med kommandot:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Mer information om hur du arbetar med Ambari REST API finns i [Övervaka och hantera HDInsight med apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Komma åt andra nodtyper

Du kan ansluta till noder som inte är direkt tillgängliga via Internet med hjälp av följande metoder:

|Metod |Beskrivning |
|---|---|
|SSH|När du är ansluten till en huvudnod med SSH kan du sedan använda SSH från huvudnoden för att ansluta till andra noder i klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|SSH-tunnel|Om du behöver komma åt en webbtjänst som finns på en av noderna som inte är exponerad för internet måste du använda en SSH-tunnel. Mer information finns i [Använda en SSH-tunnel med HDInsight-dokument.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Azure Virtual Network|Om ditt HDInsight-kluster är en del av ett Virtuellt Azure-nätverk kan alla resurser i samma virtuella nätverk direkt komma åt alla noder i klustret. Mer information finns i [Abonnemang ett virtuellt nätverk för HDInsight-dokument.](hdinsight-plan-virtual-network-deployment.md)|

## <a name="how-to-check-on-a-service-status"></a>Så här kontrollerar du en tjänststatus

Om du vill kontrollera status för tjänster som körs på huvudnoderna använder du Ambari Web UI eller Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari webbgränssnitt

Ambari Webbgränssnittet kan visas `https://CLUSTERNAME.azurehdinsight.net`på . Ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas till det anger du HTTP-användarautentiseringsuppgifterna för klustret. Standard-HTTP-användarnamnet är **administratör** och lösenordet är det lösenord som du angav när du skapade klustret.

När du anländer till Ambari-sidan visas de installerade tjänsterna till vänster på sidan.

![Apache Ambari installerade tjänster](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Det finns en serie ikoner som kan visas bredvid en tjänst för att ange status. Alla aviseringar som är relaterade till en tjänst kan visas med hjälp av länken **Aviseringar** högst upp på sidan.  Ambari erbjuder flera fördefinierade varningar.

Följande aviseringar hjälper till att övervaka tillgängligheten för ett kluster:

| Aviseringsnamn                               | Beskrivning                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status för metrisk övervakare                    | Den här aviseringen anger status för måttövervakaren som bestäms av övervakarstatusskriptet.                                                                                   |
| Ambari agent hjärtslag                   | Den här aviseringen utlöses om servern har förlorat kontakten med en agent.                                                                                                                        |
| ZooKeeper Server Process                 | Den här aviseringen på värdnivå utlöses om ZooKeeper-serverprocessen inte kan fastställas vara uppe och lyssna i nätverket.                                                               |
| IOCache-metadataserverstatus           | Den här aviseringen på värdnivå utlöses om IOCache Metadata Server inte kan fastställas vara uppe och svara på klientbegäranden                                                            |
| Webbgränssnittet JournalNode                       | Den här aviseringen på värdnivå utlöses om webbgränssnittet journalnod inte kan nås.                                                                                                                 |
| Spark2 sparsamhet Server                     | Den här aviseringen på värdnivå utlöses om Spark2 Thrift-servern inte kan fastställas vara uppe.                                                                                                |
| Historikserverprocess                   | Den här aviseringen på värdnivå utlöses om historikserverprocessen inte kan upprättas för att vara uppe och lyssna i nätverket.                                                                |
| Webbgränssnittet för Historikserver                    | Den här aviseringen på värdnivå utlöses om webbgränssnittet för Historikserver inte kan nås.                                                                                                              |
| `ResourceManager`Webbgränssnitt                   | Den här aviseringen på värdnivå utlöses om `ResourceManager` webbgränssnittet inte kan nås.                                                                                                             |
| Hälsosammanfattning för NodeManager               | Den här aviseringen på tjänstnivå utlöses om det finns felaktiga NodeManagers                                                                                                                    |
| Webbgränssnittet för apptidslinjen                      | Den här aviseringen på värdnivå utlöses om webbgränssnittet för App Tidslinjeserver inte kan nås.                                                                                                         |
| Hälsosammanfattning för DataNode                  | Den här aviseringen på tjänstnivå utlöses om det finns felaktiga DataNodes                                                                                                                       |
| Webbgränssnittet NameNode                          | Den här aviseringen på värdnivå utlöses om webbgränssnittet namenode inte kan nås.                                                                                                                    |
| ZooKeeper Redundans Controller Process    | Den här aviseringen på värdnivå utlöses om processen för ZooKeeper Failover Controller inte kan bekräftas vara uppe och lyssnande i nätverket.                                                   |
| Oozie Server Webb-ANVÄNDARGRÄNSSNITT                      | Den här aviseringen på värdnivå utlöses om webbgränssnittet för Oozie-servern inte kan nås.                                                                                                                |
| Oozie Server Status                      | Den här aviseringen på värdnivå utlöses om Oozie-servern inte kan fastställas vara uppe och svara på klientbegäranden.                                                                      |
| Hive Metastore Process                   | Den här aviseringen på värdnivå utlöses om Hive Metastore-processen inte kan fastställas vara uppe och lyssna i nätverket.                                                                 |
| HiveServer2-processen                      | Den här aviseringen på värdnivå utlöses om HiveServer inte kan fastställas vara uppe och svara på klientbegäranden.                                                                        |
| Status för WebHCat-server                    | Den här aviseringen på värdnivå utlöses om serverstatusen `templeton` inte är felfri.                                                                                                            |
| Procent ZooKeeper-servrar tillgängliga      | Den här aviseringen utlöses om antalet nederda ZooKeeper-servrar i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av ZooKeeper processkontroller.     |
| Spark2 Livy Server                       | Den här aviseringen på värdnivå utlöses om Livy2-servern inte kan fastställas vara uppe.                                                                                                        |
| Spark2-historikserver                    | Den här aviseringen på värdnivå utlöses om Spark2-historikservern inte kan fastställas vara uppe.                                                                                               |
| Insamlareprocess för mätvärden                | Den här aviseringen utlöses om Metrics Collector inte kan bekräftas vara uppe och lyssna på den konfigurerade porten för antal sekunder som är lika med tröskelvärdet.                                 |
| Metrics Collector - HBase-hanterare process | Den här aviseringen utlöses om Metrics Collectors HBase-huvudprocesser inte kan bekräftas vara uppe och lyssna i nätverket för det konfigurerade kritiska tröskelvärdet, givet på några sekunder. |
| Procentmått övervakare tillgängliga       | Den här aviseringen utlöses om en procentandel av Måttövervakarprocesserna inte är uppe och lyssnar i nätverket för de konfigurerade varnings- och kritiska tröskelvärdena.                             |
| Procent nodHanagers tillgängliga           | Den här aviseringen utlöses om antalet ned-nodhanterare i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av NodeManager processkontroller.        |
| NodeManager Hälsa                       | Den här aviseringen på värdnivå kontrollerar den hälsoegenskap för nod som är tillgänglig från nodeManager-komponenten.                                                                                              |
| Webbgränssnittet NodeManager                       | Den här aviseringen på värdnivå utlöses om webbgränssnittet nodeManager inte kan nås.                                                                                                                 |
| NameNode hög tillgänglighet Hälsa        | Den här aviseringen på tjänstnivå utlöses om antingen Active NameNode eller Standby NameNode inte körs.                                                                                     |
| DataNodprocess                         | Den här aviseringen på värdnivå utlöses om de enskilda DataNode-processerna inte kan upprättas för att vara uppe och lyssna i nätverket.                                                         |
| Webbgränssnittet för DataNode                          | Den här aviseringen på värdnivå utlöses om webbgränssnittet för DataNode inte kan nås.                                                                                                                    |
| Procentjournalnoder tillgängliga           | Den här aviseringen utlöses om antalet ned journalnoder i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av JournalNode processkontroller.        |
| Procentdatanoder tillgängliga              | Den här aviseringen utlöses om antalet neddatanoder i klustret är större än det konfigurerade kritiska tröskelvärdet. Det sammanställer resultaten av DataNode processkontroller.              |
| Zeppelin-serverstatus                   | Den här aviseringen på värdnivå utlöses om Zeppelin-servern inte kan fastställas vara uppe och svara på klientbegäranden.                                                                   |
| HiveServer2 interaktiv process          | Den här aviseringen på värdnivå utlöses om HiveServerInteractive inte kan fastställas vara uppe och svara på klientbegäranden.                                                             |
| LLAP-program                         | Den här aviseringen utlöses om LLAP-programmet inte kan fastställas vara uppe och svara på begäranden.                                                                                    |

Du kan välja varje tjänst för att visa mer information om den.

Servicesidan innehåller information om status och konfiguration för varje tjänst, men den innehåller ingen information om vilken huvudnod tjänsten körs på. Om du vill visa den här informationen använder du länken **Värdar** högst upp på sidan. På den här sidan visas värdar i klustret, inklusive huvudnoderna.

![Apache Ambari headnode värdar lista](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Om du väljer länken för en av huvudnoderna visas de tjänster och komponenter som körs på noden.

![Apache Ambari-komponentstatus](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Mer information om hur du använder Ambari finns i [Övervaka och hantera HDInsight med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API är tillgängligt över internet. The HDInsight public gateway handles routing requests to the head node that is currently hosting the REST API.

Du kan använda följande kommando för att kontrollera tillståndet för en tjänst via Ambari REST API:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Ersätt **LÖSENORD** med lösenordet för HTTP-användaren (admin).
* Ersätt **CLUSTERNAME** med namnet på klustret.
* Ersätt **SERVICENAME** med namnet på den tjänst som du vill kontrollera status för.

Om du till exempel vill kontrollera **hdfs-tjänstens** status i ett **password**kluster med namnet **mycluster**med lösenordslösenord använder du följande kommando:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Svaret liknar följande JSON:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

Webbadressen berättar att tjänsten för närvarande körs på ett huvud nod som heter **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

Tillståndet anger att tjänsten körs för tillfället, eller **STARTA .**

Om du inte vet vilka tjänster som är installerade i klustret kan du använda följande kommando för att hämta en lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Mer information om hur du arbetar med Ambari REST API finns i [Övervaka och hantera HDInsight med apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Servicekomponenter

Tjänster kan innehålla komponenter som du vill kontrollera status för individuellt. HDFS innehåller till exempel komponenten NameNode. Om du vill visa information om en komponent är kommandot:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Om du inte vet vilka komponenter som tillhandahålls av en tjänst kan du använda följande kommando för att hämta en lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Så här kommer du åt loggfiler på huvudnoderna

### <a name="ssh"></a>SSH

När loggfiler är anslutna till en huvudnod via SSH finns de under **/var/log**. Till exempel innehåller **/var/log/hadoop-yarn/yarn** loggar för YARN.

Varje huvudnod kan ha unika loggposter, så du bör kontrollera loggarna på båda.

### <a name="sftp"></a>SFTP

Du kan också ansluta till huvudnoden med SSH File Transfer Protocol eller Secure File Transfer Protocol (SFTP) och hämta loggfilerna direkt.

På samma sätt som att använda en SSH-klient måste du ange SSH-användarkontots namn och SSH-adressen för klustret när du ansluter till klustret. Till exempel `sftp username@mycluster-ssh.azurehdinsight.net`. Ange lösenordet för kontot när du uppmanas att `-i` göra det eller ange en offentlig nyckel med parametern.

När du är ansluten `sftp>` visas en uppmaning. Från den här prompten kan du ändra kataloger, ladda upp och hämta filer. Följande kommandon ändrar till exempel kataloger till katalogen **/var/log/hadoop/hdfs** och hämtar sedan alla filer i katalogen.

    cd /var/log/hadoop/hdfs
    get *

En lista över tillgängliga kommandon `help` anger `sftp>` du vid prompten.

> [!NOTE]  
> Det finns också grafiska gränssnitt som gör att du kan visualisera filsystemet när du är ansluten med SFTP. [Med MobaXTerm](https://mobaxterm.mobatek.net/) kan du till exempel bläddra i filsystemet med ett gränssnitt som liknar Utforskaren.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> För att komma åt loggfiler med Ambari måste du använda en SSH-tunnel. Webbgränssnitten för de enskilda tjänsterna exponeras inte offentligt på Internet. Information om hur du använder en SSH-tunnel finns i dokumentet [Använd SSH-tunnel.](hdinsight-linux-ambari-ssh-tunnel.md)

I webbgränssnittet i Ambari väljer du den tjänst du vill visa loggar för (till exempel YARN). Använd sedan **Snabblänkar** för att välja vilken huvudnod som du vill visa loggarna för.

![Använda snabblänkar för att visa loggar](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Konfigurera nodstorleken

Storleken på en nod kan bara väljas när klustret skapas. Du kan hitta en lista över de olika VM-storlekar som är tillgängliga för HDInsight på [hdinsightprissidan](https://azure.microsoft.com/pricing/details/hdinsight/).

När du skapar ett kluster kan du ange storleken på noderna. Följande information ger vägledning om hur du anger storleken med [Azure-portalen,](https://portal.azure.com/) [Azure PowerShell-modulen Az](/powershell/azureps-cmdlets-docs)och [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Azure-portal:** När du skapar ett kluster kan du ange storleken på de noder som används av klustret:

    ![Bild av guiden skapa kluster med nodstorleksval](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) När du använder kommandot kan du ange storleken på noderna `--headnode-size` `--workernode-size`head, `--zookeepernode-size` worker och ZooKeeper med hjälp av , och parametrar.

* **Azure PowerShell**: När du använder cmdleten [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) kan du ange storleken på noderna head, worker och ZooKeeper med hjälp av `-HeadNodeSize`, `-WorkerNodeSize`och `-ZookeeperNodeSize` parametrar.

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

* [Apache Ambari REST Referens](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installera och konfigurera Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installera och konfigurera Azure PowerShell-modul Az](/powershell/azure/overview)
* [Hantera HDInsight med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
