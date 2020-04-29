---
title: Hög tillgänglighet för Hadoop – Azure HDInsight
description: Lär dig hur HDInsight-kluster förbättrar tillförlitligheten och tillgängligheten genom att använda en ytterligare Head-nod. Lär dig hur detta påverkar Hadoop-tjänster som Ambari och Hive, samt hur du ansluter till varje Head-nod med hjälp av SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop hög tillgänglighet
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381400"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight

HDInsight-kluster ger två huvudnoder för att öka tillgängligheten och tillförlitligheten för Apache Hadoop tjänster och jobb som körs.

Hadoop uppnår hög tillgänglighet och tillförlitlighet genom att replikera tjänster och data över flera noder i ett kluster. Standard distributioner av Hadoop har dock vanligt vis bara en enda Head-nod. Eventuella avbrott i noden för den enkla huvudnoden kan orsaka att klustret slutar fungera. HDInsight ger två huvudnoderna för att förbättra Hadoop: s tillgänglighet och tillförlitlighet.

## <a name="availability-and-reliability-of-nodes"></a>Tillgänglighet och tillförlitlighet för noder

Noder i ett HDInsight-kluster implementeras med hjälp av Azure Virtual Machines. I följande avsnitt beskrivs de olika nodtyper som används med HDInsight.

> [!NOTE]  
> Alla typer av noder används inte för en kluster typ. En Hadoop-kluster typ har till exempel inte några Nimbus-noder. Mer information om noder som används av kluster typer av HDInsight finns i avsnittet kluster typer i [skapa Linux-baserade Hadoop-kluster i HDInsight-](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) dokument.

### <a name="head-nodes"></a>Huvudnoder

HDInsight erbjuder två huvudnoder för att säkerställa hög tillgänglighet för Hadoop-tjänster. Båda Head-noderna är aktiva och körs i HDInsight-klustret samtidigt. Vissa tjänster, som Apache HDFS eller Apache Hadoop garn, är bara aktiva på en head-nod vid en viss tidpunkt. Andra tjänster som HiveServer2 eller Hive Metaarkiv är aktiva på båda Head-noderna samtidigt.

Använd [Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)för att hämta värdnamn för olika nodtyper i klustret.

> [!IMPORTANT]  
> Associera inte det numeriska värdet med om en nod är primär eller sekundär. Det numeriska värdet finns bara för att ange ett unikt namn för varje nod.

### <a name="nimbus-nodes"></a>Nimbus-noder

Nimbus-noder är tillgängliga med Apache Storm-kluster. Nimbus-noderna ger liknande funktionalitet till Hadoop-JobTracker genom att distribuera och övervaka bearbetning över arbetsnoder. HDInsight innehåller två Nimbus-noder för Storm-kluster

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper-noder

[ZooKeeper](https://zookeeper.apache.org/) -noder används för val av ledare för huvud tjänster på huvud-noder. De används också för att kontrol lera att tjänsterna, data (Worker)-noder och gatewayer vet vilken Head-nod en huvud tjänst är aktiv på. Som standard tillhandahåller HDInsight tre ZooKeeper-noder.

### <a name="worker-nodes"></a>Arbetsnoder

Arbetsnoder utför den faktiska data analysen när ett jobb skickas till klustret. Om en arbetsnod Miss lyckas skickas den aktivitet som den utförde till en annan arbetsnod. Som standard skapar HDInsight fyra arbetsnoder. Du kan ändra det här värdet så att det passar dina behov både under och efter att klustret har skapats.

### <a name="edge-node"></a>Edge-nod

En Edge-nod deltar inte aktivt i data analysen i klustret. Den används av utvecklare eller data experter när de arbetar med Hadoop. Edge-noden finns i samma Azure-Virtual Network som de andra noderna i klustret och kan komma åt alla andra noder direkt. Du kan använda Edge-noden utan att ta resurser bort från kritiska Hadoop-tjänster eller analys jobb.

För närvarande är ML-tjänster i HDInsight den enda kluster typen som tillhandahåller en Edge-nod som standard. För ML-tjänster i HDInsight används test R-koden lokalt på noden innan den skickas till klustret för distribuerad bearbetning.

Information om hur du använder en Edge-nod med andra kluster typer finns i [använda Edge-noder i HDInsight](hdinsight-apps-use-edge-node.md) -dokument.

## <a name="accessing-the-nodes"></a>Åtkomst till noderna

Åtkomst till klustret via Internet tillhandahålls via en offentlig Gateway. Åtkomst är begränsad för anslutning till huvudnoderna och, om det finns en sådan, Edge-noden. Åtkomst till tjänster som körs på huvudnoderna påverkas inte av att ha flera Head-noder. Den offentliga gatewayen dirigerar begär anden till Head-noden som är värd för den begärda tjänsten. Om t. ex. apache Ambari för närvarande finns på den sekundära Head-noden dirigerar gatewayen inkommande begär Anden för Ambari till noden.

Åtkomst över den offentliga gatewayen är begränsad till portarna 443 (HTTPS), 22 och 23.

|Port |Beskrivning |
|---|---|
|443|Används för att få åtkomst till Ambari och andra webb gränssnitt eller REST-API: er som finns på huvudnoderna.|
|22|Används för att få åtkomst till den primära Head-noden eller Edge-noden med SSH.|
|23|Används för att få åtkomst till den sekundära Head-noden med SSH. `ssh username@mycluster-ssh.azurehdinsight.net` Ansluter till exempel den primära huvudnoden i klustret med namnet IT- **kluster**.|

Mer information om hur du använder SSH finns i dokumentet [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) .

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interna fullständigt kvalificerade domän namn (FQDN)

Noder i ett HDInsight-kluster har en intern IP-adress och ett fullständigt domän namn som bara kan nås från klustret. När du ansluter till tjänster i klustret med hjälp av den interna FQDN-adressen eller IP-adressen bör du använda Ambari för att kontrol lera vilken IP eller FQDN som ska användas för att komma åt tjänsten.

Till exempel kan Apache Oozie-tjänsten endast köras på en head-nod och med hjälp av `oozie` kommandot från en SSH-session krävs URL: en till tjänsten. URL: en kan hämtas från Ambari med hjälp av följande kommando:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Det här kommandot returnerar ett värde som liknar följande, som innehåller den interna URL: en som ska `oozie` användas med kommandot:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Mer information om hur du arbetar med Ambari REST API finns i [övervaka och hantera HDInsight med Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Åtkomst till andra nodtyper

Du kan ansluta till noder som inte är direkt tillgängliga via Internet med hjälp av följande metoder:

|Metod |Beskrivning |
|---|---|
|SSH|När du har anslutit till en head-nod med SSH kan du sedan använda SSH från Head-noden för att ansluta till andra noder i klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|SSH-tunnel|Om du behöver åtkomst till en webb tjänst som finns på en av noderna som inte är utsatta för Internet, måste du använda en SSH-tunnel. Mer information finns i [använda en SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) -dokument.|
|Azure Virtual Network|Om ditt HDInsight-kluster ingår i en Azure-Virtual Network kan alla resurser på samma Virtual Network få direkt åtkomst till alla noder i klustret. Mer information finns i avsnittet [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md) -dokument.|

## <a name="how-to-check-on-a-service-status"></a>Så här kontrollerar du en tjänst status

Om du vill kontrol lera statusen för tjänster som körs på huvudnoderna använder du Ambari-webbgränssnittet eller Ambari-REST API.

### <a name="ambari-web-ui"></a>Ambari webb gränssnitt

Ambari webb gränssnitt kan visas på `https://CLUSTERNAME.azurehdinsight.net`. Ersätt **kluster** namn med namnet på klustret. Ange HTTP-användarautentiseringsuppgifter för klustret om du uppmanas till det. Standard namnet för HTTP-användare är **administratör** och lösen ordet är det lösen ord som du angav när du skapade klustret.

När du kommer till sidan Ambari visas de installerade tjänsterna till vänster på sidan.

![Apache Ambari-installerade tjänster](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Det finns en serie ikoner som kan visas bredvid en tjänst för att indikera status. Aviseringar som är relaterade till en tjänst kan visas med hjälp av länken **aviseringar** överst på sidan.  Ambari erbjuder flera fördefinierade aviseringar.

Följande aviseringar hjälper dig att övervaka tillgängligheten för ett kluster:

| Aviseringsnamn                               | Beskrivning                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status för mått övervakare                    | Den här varningen anger status för den Mät övervaknings process som definieras av skriptet övervaka status.                                                                                   |
| Ambari agent-pulsslag                   | Den här aviseringen utlöses om servern har förlorat kontakt med en agent.                                                                                                                        |
| Process för ZooKeeper-Server                 | Den här varningen på värdnivå utlöses om ZooKeeper-serverns process inte kan fastställas för att vara igång och lyssna på nätverket.                                                               |
| Status för IOCache           | Den här varningen på värdnivå utlöses om IOCache-metadatatjänsten inte kan fastställas och svarar på klient begär Anden                                                            |
| JournalNode webb gränssnitt                       | Den här aviseringen om värd nivå utlöses om JournalNode-webbgränssnittet inte kan kommas åt.                                                                                                                 |
| Spark2 Thrift-Server                     | Den här varningen på värdnivå utlöses om Spark2 Thrift-servern inte kan fastställas.                                                                                                |
| Process för historik Server                   | Den här varningen på värdnivå utlöses om det inte går att upprätta historik Server processen för att bli igång och lyssna på nätverket.                                                                |
| Webb gränssnitt för historik Server                    | Den här varningen på värdnivå utlöses om webb gränssnittet för historik servern inte kan kontaktas.                                                                                                              |
| `ResourceManager`Webb gränssnitt                   | Den här varningen på `ResourceManager` värdnivå utlöses om webb gränssnittet inte kan kontaktas.                                                                                                             |
| NodeManager hälso översikt               | Den här aviseringen på tjänst nivå utlöses om det finns) Nodemanagers som inte är felfria                                                                                                                    |
| Webb gränssnitt för app-tidslinje                      | Den här varningen på värdnivå utlöses om det inte går att komma åt appens tids linje webb gränssnitt.                                                                                                         |
| DataNode hälso översikt                  | Den här aviseringen på tjänst nivå utlöses om det finns DataNodes som inte är felfria                                                                                                                       |
| NameNode webb gränssnitt                          | Den här aviseringen om värd nivå utlöses om NameNode-webbgränssnittet inte kan kommas åt.                                                                                                                    |
| Process för ZooKeeper-redundansväxling    | Den här varningen på värdnivå utlöses om processen för ZooKeeper-redundansväxling inte kan bekräftas för att vara igång och lyssna på nätverket.                                                   |
| Webb gränssnitt för Oozie-Server                      | Den här varningen på värdnivå utlöses om Oozie Server-webbgränssnittet inte kan kontaktas.                                                                                                                |
| Status för Oozie-Server                      | Den här varningen på värdnivå utlöses om Oozie-servern inte kan fastställas och svarar på klient begär Anden.                                                                      |
| Process för Hive-Metaarkiv                   | Den här varningen på värdnivå utlöses om Hive Metaarkiv-processen inte kan fastställas för att vara igång och lyssna på nätverket.                                                                 |
| HiveServer2 process                      | Den här varningen på värdnivå utlöses om HiveServer inte kan fastställas och svarar på klient begär Anden.                                                                        |
| Status för WebHCat-Server                    | Den här aviseringen om värd nivå utlöses om `templeton` Server statusen inte är felfri.                                                                                                            |
| Procent ZooKeeper-servrar tillgängliga      | Den här aviseringen utlöses om antalet ZooKeeper-servrar i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av ZooKeeper process-kontroller.     |
| Spark2 livy-Server                       | Den här aviseringen om värd nivå utlöses om Livy2-servern inte kan fastställas.                                                                                                        |
| Spark2 historik Server                    | Den här varningen på värdnivå utlöses om Spark2 historik Server inte kan fastställas.                                                                                               |
| Insamlings process för mått                | Den här aviseringen utlöses om insamlaren av måtten inte kan bekräftas att vara igång och lyssna på den konfigurerade porten för ett antal sekunder som är lika med tröskelvärdet.                                 |
| Mått insamlare – HBase Master process | Den här aviseringen utlöses om mått insamlarens HBase huvud processer inte kan bekräftas att vara igång och lyssna på nätverket för det konfigurerade kritiska tröskelvärdet, angivet i sekunder. |
| Procent mått övervakare tillgängliga       | Den här aviseringen utlöses om en procent andel mått övervakare inte är igång och lyssnar på nätverket efter den konfigurerade varningen och de kritiska tröskelvärdena.                             |
| Procent) Nodemanagers tillgängliga           | Den här aviseringen utlöses om antalet) Nodemanagers i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av NodeManager process-kontroller.        |
| NodeManager-hälsa                       | Den här varningen på värdnivå kontrollerar egenskapen för nodens hälso tillstånd som är tillgänglig från NodeManager-komponenten.                                                                                              |
| NodeManager webb gränssnitt                       | Den här aviseringen om värd nivå utlöses om NodeManager-webbgränssnittet inte kan kommas åt.                                                                                                                 |
| NameNode-hälsa för hög tillgänglighet        | Den här aviseringen på tjänst nivå utlöses om antingen den aktiva NameNode eller vänte läges NameNode inte körs.                                                                                     |
| DataNode process                         | Den här varningen på värdnivå utlöses om de enskilda DataNode-processerna inte kan upprättas för att bli igång och lyssna på nätverket.                                                         |
| DataNode webb gränssnitt                          | Den här aviseringen om värd nivå utlöses om DataNode-webbgränssnittet inte kan kommas åt.                                                                                                                    |
| Procent Journalnodes available tillgängliga           | Den här aviseringen utlöses om antalet Journalnodes available i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av JournalNode process-kontroller.        |
| Procent DataNodes tillgängliga              | Den här aviseringen utlöses om antalet DataNodes i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av DataNode process-kontroller.              |
| Status för Zeppelin-Server                   | Den här varningen på värdnivå utlöses om Zeppelin-servern inte kan fastställas och svarar på klient begär Anden.                                                                   |
| HiveServer2 interaktiv process          | Den här varningen på värdnivå utlöses om HiveServerInteractive inte kan fastställas och svarar på klient begär Anden.                                                             |
| LLAP-program                         | Den här aviseringen utlöses om LLAP-programmet inte kan fastställas för att svara på begär Anden.                                                                                    |

Du kan välja varje tjänst om du vill visa mer information om den.

Även om tjänst sidan innehåller information om statusen och konfigurationen för varje tjänst ger den inte information om vilken Head-nod tjänsten körs på. Om du vill visa den här informationen använder du länken **värdar** överst på sidan. Den här sidan visar värdar i klustret, inklusive Head-noderna.

![Apache Ambari huvudnoden hosts-lista](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Om du väljer länken för en av huvudnoderna visas tjänsterna och komponenterna som körs på noden.

![Apache Ambari-komponent status](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Mer information om hur du använder Ambari finns i [övervaka och hantera HDInsight med Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari-REST API är tillgängligt via Internet. Den offentliga HDInsight-gatewayen hanterar vägvals begär anden till huvudnoden som för närvarande är värd för REST API.

Du kan använda följande kommando för att kontrol lera status för en tjänst via Ambari-REST API:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Ersätt **Password** med konto lösen ordet för http-användare (admin).
* Ersätt **CLUSTERNAME** med namnet på klustret.
* Ersätt **SERVICENAME** med namnet på den tjänst som du vill kontrol lera status för.

Om du till exempel vill kontrol lera status för **HDFS** -tjänsten på ett kluster med namnet ' IT **cluster**', med lösen **ordet lösen ord, använder**du följande kommando:

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

URL: en talar om för oss att tjänsten körs på en head-nod med namnet **Uncluster. wutj3h4ic1zejluqhxzvckxq0g**.

Tillstånds information talar om för oss att tjänsten körs eller **startas**.

Om du inte vet vilka tjänster som är installerade i klustret kan du använda följande kommando för att hämta en lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Mer information om hur du arbetar med Ambari REST API finns i [övervaka och hantera HDInsight med Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Tjänst komponenter

Tjänster kan innehålla komponenter som du vill kontrol lera statusen för enskilda. HDFS innehåller till exempel NameNode-komponenten. Om du vill visa information om en komponent blir kommandot:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Om du inte vet vilka komponenter som tillhandahålls av en tjänst kan du använda följande kommando för att hämta en lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Så här kommer du åt loggfiler på huvudnoderna

### <a name="ssh"></a>SSH

När du är ansluten till en head-nod via SSH kan du hitta loggfiler under **/var/log**. Till exempel innehåller **/var/log/Hadoop-yarn/yarn** loggar för garn.

Varje Head-nod kan ha unika logg poster, så du bör kontrol lera loggarna på båda.

### <a name="sftp"></a>SFTP

Du kan också ansluta till Head-noden med SSH-File Transfer Protocol eller säkra File Transfer Protocol (SFTP) och hämta loggfilerna direkt.

På samma sätt som med en SSH-klient måste du ange SSH-användarnamnet och SSH-adressen för klustret när du ansluter till klustret. Till exempel `sftp username@mycluster-ssh.azurehdinsight.net`. Ange lösen ordet för kontot när du uppmanas till det, eller ange en offentlig `-i` nyckel med hjälp av parametern.

När du är ansluten visas en `sftp>` uppvarning. I den här frågan kan du ändra kataloger, ladda upp och ladda ned filer. Följande kommandon ändrar till exempel kataloger till katalogen **/var/log/Hadoop/HDFS** och laddar ned alla filer i katalogen.

    cd /var/log/hadoop/hdfs
    get *

Om du vill visa en lista över tillgängliga `help` kommandon anger `sftp>` du vid prompten.

> [!NOTE]  
> Det finns också grafiska gränssnitt som gör att du kan visualisera fil systemet när du är ansluten med SFTP. Med [MobaXTerm](https://mobaxterm.mobatek.net/) kan du till exempel bläddra i fil systemet med ett gränssnitt som liknar Utforskaren i Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Om du vill komma åt loggfiler med Ambari måste du använda en SSH-tunnel. Webb gränssnitten för enskilda tjänster visas inte offentligt på Internet. Information om hur du använder en SSH-tunnel finns i [använda SSH-tunnlar](hdinsight-linux-ambari-ssh-tunnel.md) .

Välj den tjänst som du vill visa loggar för i Ambari-webbgränssnittet (till exempel garn). Använd sedan **snabb länkar** för att välja vilken Head-nod som ska visa loggarna för.

![Använda snabb Länkar för att visa loggar](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Så här konfigurerar du nodens storlek

Storleken på en nod kan bara väljas när klustret skapas. Du hittar en lista över de olika VM-storlekar som är tillgängliga för HDInsight på [sidan med pris](https://azure.microsoft.com/pricing/details/hdinsight/)information för HDInsight.

När du skapar ett kluster kan du ange storleken på noderna. Följande information ger vägledning om hur du anger storleken med [Azure Portal](https://portal.azure.com/), [Azure PowerShell modul AZ](/powershell/azureps-cmdlets-docs)och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

* **Azure Portal**: när du skapar ett kluster kan du ange storleken på noderna som används av klustret:

    ![Bild av guiden skapa kluster med val av Node-storlek](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: när du använder [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) kommandot kan du ange storleken på noderna Head, Work och ZooKeeper med hjälp av parametrarna `--headnode-size`, `--workernode-size`och. `--zookeepernode-size`

* **Azure PowerShell**: när du använder cmdleten [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) kan du ange storleken på noderna Head, Work och ZooKeeper med parametrarna `-HeadNodeSize`, `-WorkerNodeSize`och. `-ZookeeperNodeSize`

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

* [REST-referens för Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installera och konfigurera Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installera och konfigurera Azure PowerShell modul AZ](/powershell/azure/overview)
* [Hantera HDInsight med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
