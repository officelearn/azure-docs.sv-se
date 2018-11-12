---
title: Hög tillgänglighet för Hadoop - Azure HDInsight
description: Lär dig hur HDInsight-kluster använder för att förbättra tillförlitlighet och tillgänglighet genom att använda en ytterligare huvudnod. Lär dig hur detta påverkar Hadoop-tjänster, till exempel Ambari och Hive, och hur ansluta individuellt till varje huvudnod via SSH.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
keywords: hög tillgänglighet för hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: d3326ef4bba5649f5420c1d92b6117d44edba47b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281990"
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Tillgänglighet och pålitlighet för Hadoop-kluster i HDInsight

HDInsight-kluster tillhandahåller två huvudnoder för att öka tillgänglighet och pålitlighet för Hadoop-tjänster och jobb som körs.

Hadoop uppnår hög tillgänglighet och tillförlitlighet genom att replikera data och tjänster över flera noder i ett kluster. Men standard Hadoop-distributioner har vanligtvis bara en enda huvudnod. Eventuella avbrott i enkel huvudnoden kan orsaka att klustret slutar fungera. HDInsight tillhandahåller två huvudnoder för att förbättra Hadoops tillgänglighet och tillförlitlighet.

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="availability-and-reliability-of-nodes"></a>Tillgänglighet och tillförlitlighet för noder

Noder i ett HDInsight-kluster implementeras med hjälp av Azure virtuella datorer. I följande avsnitt beskrivs enskilda nodtyperna användas med HDInsight. 

> [!NOTE]
> Inte alla nodtyper används för en typ av kluster. En typ av Hadoop-kluster har till exempel inte några Nimbus-noder. Mer information om noder som används av HDInsight-klustertyper finns i avsnittet Cluster typer av den [skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentet.

### <a name="head-nodes"></a>Huvudnoder

HDInsight tillhandahåller två huvudnoder för att garantera hög tillgänglighet för Hadoop-tjänster. Både huvudnoderna är aktiv och körs i ett HDInsight-kluster samtidigt. Vissa tjänster, till exempel HDFS eller YARN, är endast aktiv på en huvudnod vid en given tidpunkt. Andra tjänster, till exempel HiveServer2 eller Hive-Metaarkiv är aktiva på både huvudnoderna på samma gång.

Huvudnoder (och andra noder i HDInsight) har ett numeriskt värde som en del av värdnamnet för noden. Till exempel `hn0-CLUSTERNAME` eller `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Koppla inte det numeriska värdet till om en nod är primär eller sekundär. Det numeriska värdet finns bara att ange ett unikt namn för varje nod.

### <a name="nimbus-nodes"></a>Nimbus-noder

Nimbus-noder är tillgängliga med Storm-kluster. Nimbus-noder ger liknande funktionalitet till Hadoop JobTracker genom att distribuera och övervaka bearbetning över arbetsnoder. HDInsight tillhandahåller två Nimbus-noder för Storm-kluster

### <a name="zookeeper-nodes"></a>Zookeeper-noder

[ZooKeeper](http://zookeeper.apache.org/) noder som används för val av ledare av master tjänster på huvudnoder. De används också för att se till att tjänster, datanoder (worker) och gatewayer vet vilka huvudnoden en master tjänst är aktiv på. Som standard innehåller tre ZooKeeper-noder i HDInsight.

### <a name="worker-nodes"></a>Arbetsnoder

Arbetsnoder utföra den faktiska dataanalysen när ett jobb skickas till klustret. Om en arbetsnod misslyckas skickas den aktivitet som processen utförde till en annan worker-nod. Som standard skapar fyra arbetsnoder i HDInsight. Du kan ändra antalet så att det passar dina behov, både under och när klustret har skapats.

### <a name="edge-node"></a>Gränsnod

En kantnod deltar inte aktivt i dataanalys i klustret. Den används av utvecklare och datavetare när du arbetar med Hadoop. Kantnod fungerar finns i Azure samma virtuella nätverk som de andra noderna i klustret och har direkt åtkomst till andra noder. Kantnoden kan användas utan stjäl resurser från kritiska Hadoop-tjänster eller analysis-jobb.

ML-tjänster på HDInsight är för närvarande den enda typ av kluster som innehåller en kantnod som standard. Kantnoden används för ML-tjänster på HDInsight, test R-kod lokalt på noden innan den skickas till klustret för distribuerad databehandling.

Information om hur du använder en kantnod med andra klustertyper finns i den [använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md) dokumentet.

## <a name="accessing-the-nodes"></a>Åtkomst till noderna

Åtkomst till klustret via internet är tillgängligt via en offentlig gateway. Åtkomst är begränsad till ansluter till huvudnoderna och (om en sådan finns) kantnoden. Åtkomst till tjänster som körs på huvudnoderna påverkas inte genom att låta flera huvudnoder. Den offentliga gatewayen dirigerar begäranden till huvudnoden som är värd för den begärda tjänsten. Till exempel om Ambari finns på den sekundära huvudnoden, dirigerar gatewayen inkommande begäranden för Ambari till noden.

Åtkomst via den offentliga gatewayen är begränsad till port 443 (HTTPS), 22 och 23.

* Port __443__ används för att komma åt Ambari och andra webb-UI eller REST API: er på huvudnoderna.

* Port __22__ används för att få åtkomst till den primära huvudnoden eller edge node med SSH.

* Port __23__ används för att få åtkomst till den sekundära huvudnoden med SSH. Till exempel `ssh username@mycluster-ssh.azurehdinsight.net` ansluter till den primära huvudnoden av kluster med namnet **mycluster**.

Mer information om hur du använder SSH finns i den [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentet.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interna fullständigt kvalificerade domännamn (FQDN)

Noder i ett HDInsight-kluster har en intern IP-adress och FQDN som kan bara kommas åt från klustret. Vid åtkomst till tjänster på klustret med den interna FQDN eller IP-adressen, bör du använda Ambari för att verifiera IP-Adressen eller FQDN för att använda vid åtkomst till tjänsten.

Exempelvis Oozie-tjänsten kan endast köras på en huvudnod och med hjälp av den `oozie` kommando från en SSH-session kräver URL till tjänsten. Den här URL: en kan hämtas från Ambari med hjälp av följande kommando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Det här kommandot returnerar ett värde som liknar följande kommando, som innehåller den interna URL: en ska användas med den `oozie` kommando:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Mer information om hur du arbetar med Ambari REST API finns i [övervaka och hantera HDInsight med hjälp av Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Åtkomst till andra nodtyper

Du kan ansluta till noder som inte är direkt åtkomliga via internet med hjälp av följande metoder:

* **SSH**: anslutit till en huvudnod via SSH kan du sedan använda SSH från klustrets huvudnod för att ansluta till andra noder i klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH-Tunnel**: Om du behöver komma åt en webbtjänst som finns på en av noderna som inte är exponerad för internet, måste du använda en SSH-tunnel. Mer information finns i den [använder en SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

* **Azure-nätverk**: om ditt HDInsight-kluster är en del av Azure Virtual Network, alla resurser på samma virtuella nätverk direkt åtkomst till alla noder i klustret. Mer information finns i den [utöka HDInsight med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentet.

## <a name="how-to-check-on-a-service-status"></a>Hur du kontrollerar på en Tjänststatus

Använda Ambari-Webbgränssnittet eller Ambari REST API för att kontrollera status för tjänster som körs på huvudnoderna.

### <a name="ambari-web-ui"></a>Ambari-webbgränssnittet

Ambari-Webbgränssnittet kan visas på https://CLUSTERNAME.azurehdinsight.net. Ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas, anger du HTTP-autentiseringsuppgifterna för ditt kluster. HTTP-Standardanvändarnamnet är **admin** och lösenordet är lösenordet du angav när klustret skapas.

När du kommer på sidan Ambari visas installerade tjänster till vänster på sidan.

![Installerade tjänster](./media/hdinsight-high-availability-linux/services.png)

Det finns en serie med ikoner som kan visas bredvid en tjänst för att ange status. Alla aviseringar som rör en tjänst kan granskas med den **aviseringar** länken längst upp på sidan. Du kan välja varje tjänst och visa mer information om den.

När sidan innehåller information om status och konfiguration för varje tjänst, innehåller inte information som på vilka huvudnoden tjänsten körs på. Du kan visa den här informationen i **värdar** länken längst upp på sidan. Den här sidan visar värdarna i klustret, inklusive huvudnoderna.

![värdarna](./media/hdinsight-high-availability-linux/hosts.png)

Om du väljer länken för en av huvudnoderna visar de tjänster och komponenter som körs på noden.

![Komponentstatus](./media/hdinsight-high-availability-linux/nodeservices.png)

Läs mer om hur du använder Ambari [övervaka och hantera HDInsight med hjälp av Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API är tillgänglig via internet. Den offentliga gatewayen HDInsight hanterar dirigering av begäranden till huvudnoden som för närvarande är värd för REST API.

Du kan använda följande kommando för att kontrollera tillståndet för en tjänst via Ambari REST API:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Ersätt **lösenord** med HTTP-lösenordet för användarkontot (admin).
* Ersätt **CLUSTERNAME** med namnet på klustret.
* Ersätt **SERVICENAME** med namnet på den tjänst du vill kontrollera status för.

Till exempel för att kontrollera status för den **HDFS** på ett kluster med namnet **mycluster**, med ett lösenord för **lösenord**, skulle du använda följande kommando:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Svaret liknar följande JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

URL: en talar om för oss att tjänsten körs på en huvudnod med namnet **hn0 CLUSTERNAME**.

Tillståndet talar om för oss att tjänsten körs för närvarande, eller **startad**.

Om du inte vet vilka tjänster som är installerade på klustret, använder du följande kommando för att hämta en lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Mer information om hur du arbetar med Ambari REST API finns i [övervaka och hantera HDInsight med hjälp av Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Tjänstkomponenter

Tjänsterna kan innehålla komponenter som du vill kontrollera status för individuellt. Till exempel innehåller HDFS NameNode-komponenten. Om du vill visa information om en komponent, skulle kommandot vara:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Om du inte vet vilka komponenter som tillhandahålls som en tjänst, använder du följande kommando för att hämta en lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Hur du kommer åt loggfilerna på huvudnoderna

### <a name="ssh"></a>SSH

När du är ansluten till en huvudnod via SSH, loggfiler finns under **/var/log**. Till exempel **/var/log/hadoop-yarn/yarn** innehålla loggar för YARN.

Varje huvudnod kan ha unika loggposter, så du bör kontrollera loggfilerna på båda.

### <a name="sftp"></a>SFTP

Du kan också ansluta till klustrets huvudnod med hjälp av SSH File Transfer Protocol eller SFTP Secure File Transfer Protocol () och ladda ned loggfiler direkt.

Ungefär som att använda en SSH-klient när du ansluter till klustret som du måste ange SSH-användarkontonamnet och SSH-adressen för klustret. Till exempel `sftp username@mycluster-ssh.azurehdinsight.net`. Ange lösenordet för kontot när du uppmanas att göra eller ange en offentlig nyckel med hjälp av den `-i` parametern.

När du är ansluten, visas en `sftp>` prompten. Från det här meddelandet kan du ändra katalog, ladda upp och ladda ned filer. Till exempel följande kommandon ändrar du katalog till den **/var/log/hadoop/hdfs** directory och sedan ladda ned alla filer i katalogen.

    cd /var/log/hadoop/hdfs
    get *

En lista över tillgängliga kommandon, ange `help` på den `sftp>` prompten.

> [!NOTE]
> Det finns även grafiska gränssnitt som gör det möjligt att visualisera filsystemet när du är ansluten med SFTP. Till exempel [MobaXTerm](http://mobaxterm.mobatek.net/) kan du bläddra filsystemet med ett gränssnitt som liknar Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]
> För att komma åt loggfiler med hjälp av Ambari, måste du använda en SSH-tunnel. Webbgränssnitt för enskilda tjänster exponeras inte offentligt på Internet. Information om hur du använder en SSH-tunnel finns i den [använda SSH-tunnlar](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

Ambari-Webbgränssnittet väljer du tjänsten som du vill visa loggarna för (till exempel YARN). Använd sedan **snabblänkar** att välja vilka huvudnod för att visa loggar.

![Använda snabblänkar för att visa loggar](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Så här konfigurerar du nodstorlek

Storleken på en nod kan endast väljas när klustret skapas. Du hittar en lista över de olika storlekarna som är tillgängliga för HDInsight på den [HDInsight prissättningssidan](https://azure.microsoft.com/pricing/details/hdinsight/).

När du skapar ett kluster kan ange du storleken på noderna. Följande information innehåller råd om hur du anger en storlek med hjälp av den [Azure-portalen][preview-portal], [Azure PowerShell][azure-powershell], och [Azure klassiskt CLI][azure-cli]:

* **Azure-portalen**: du kan ange storleken på de noder som används av klustret när du skapar ett kluster:

    ![Bild av guiden med val av klusternod storlek för att skapa kluster](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure klassiskt CLI**: när du använder den `azure hdinsight cluster create` kommandot, du kan ange storleken på head, arbetsroller och ZooKeeper-noder med hjälp av den `--headNodeSize`, `--workerNodeSize`, och `--zookeeperNodeSize` parametrar.

* **Azure PowerShell**: när du använder den `New-AzureRmHDInsightCluster` cmdlet, du kan ange storleken på head, arbetsroller och ZooKeeper-noder med hjälp av den `-HeadNodeVMSize`, `-WorkerNodeSize`, och `-ZookeeperNodeSize` parametrar.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta mer om saker som nämns i det här dokumentet.

* [Ambari REST-referens](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installera och konfigurera den klassiska Azure-CLI](../cli-install-nodejs.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Hantera HDInsight med Ambari](hdinsight-hadoop-manage-ambari.md)
* [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
