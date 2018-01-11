---
title: "Hög tillgänglighet för Hadoop - Azure HDInsight | Microsoft Docs"
description: "Lär dig mer om hur HDInsight-kluster bättre tillförlitlighet och tillgänglighet med hjälp av en ytterligare huvudnod. Lär dig hur detta påverkar Hadoop-tjänster, till exempel Ambari och Hive, och hur du ansluter individuellt till varje huvudnod via SSH."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
keywords: "hög tillgänglighet för hadoop"
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/08/2017
ms.author: larryfr
ms.openlocfilehash: bf1840f77ebd39761a5ba1fd8fd1f98891dd8eca
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Tillgänglighet och pålitlighet för Hadoop-kluster i HDInsight

HDInsight-kluster tillhandahåller två huvudnoderna för att öka tillgänglighet och tillförlitlighet för Hadoop-tjänster och jobb som körs.

Hadoop ger hög tillgänglighet och tillförlitlighet genom att replikera data och tjänster över flera noder i ett kluster. Men standard Hadoop-distributioner har vanligtvis bara en huvudnod. Eventuella avbrott i enda huvudnoden kan göra att klustret slutar fungera. HDInsight tillhandahåller två headnodes för att förbättra tillgänglighet och tillförlitlighet för Hadoop's.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Tillgänglighet och tillförlitlighet för noder

Noder i ett HDInsight-kluster implementeras med hjälp av Azure virtuella datorer. I följande avsnitt beskrivs de enskilda nodtyper som används med HDInsight. 

> [!NOTE]
> Inte alla nodtyper används för en typ av kluster. Till exempel har en typ av Hadoop-kluster inte några Nimbus-noder. Mer information om noder som används av HDInsight-klustertyper finns i avsnittet kluster typer av den [skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentet.

### <a name="head-nodes"></a>HEAD-noder

HDInsight tillhandahåller två huvudnoderna för att säkerställa hög tillgänglighet för Hadoop-tjänster. Båda huvudnoderna är aktiv och körs i HDInsight-klustret samtidigt. Vissa tjänster, till exempel HDFS eller YARN, är bara aktiva i en huvudnod vid en given tidpunkt. Andra tjänster som HiveServer2 eller Hive MetaStore är aktiva på båda head noder på samma gång.

HEAD-noder (och andra noder i HDInsight) har ett numeriskt värde som en del av värdnamnet på noden. Till exempel `hn0-CLUSTERNAME` eller `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Koppla inte det numeriska värdet till om en nod är primär eller sekundär. Det numeriska värdet finns bara att ange ett unikt namn för varje nod.

### <a name="nimbus-nodes"></a>Nimbus-noder

Nimbus-noder är tillgängliga med Storm-kluster. Nimbus-noder ger liknande funktionalitet till Hadoop JobTracker genom att distribuera och övervaka bearbetning över arbetsnoder. HDInsight tillhandahåller två Nimbus-noder för Storm-kluster

### <a name="zookeeper-nodes"></a>Zookeeper-noder

[ZooKeeper](http://zookeeper.apache.org/) noder används för ledande val av master-tjänster på huvudnoderna. De används också till att säkerställa att tjänster, (worker) datanoder och gateways vet vilken huvudnod en master tjänst är aktiv på. Standard tillhandahåller HDInsight tre ZooKeeper-noder.

### <a name="worker-nodes"></a>Arbetsnoder

Arbetarnoder analysera den faktiska data när ett jobb som skickas till klustret. Om en arbetsnod misslyckas, skickas uppgiften som den utförde till en annan worker-nod. Som standard skapar HDInsight fyra arbetsnoderna. Du kan ändra det här numret så att de passar dina behov både under och efter klustret skapas.

### <a name="edge-node"></a>Gränsnod

En kantnod deltar inte aktivt i dataanalys i klustret. Den används av utvecklare eller datavetare när du arbetar med Hadoop. Kantnoden bor i Azure samma virtuella nätverk som de andra noderna i klustret och direkt åtkomst till alla andra noder. Kantnoden kan användas utan att göra resurser från kritiska Hadoop-tjänster eller analys jobb.

R Server på HDInsight är för närvarande den enda typen i klustret som tillhandahåller en kantnod som standard. R Server på HDInsight kantnoden används testkod R lokalt på nod innan den skickas till klustret för distribuerad databehandling.

Information om hur du använder en kantnod med klustertyper än R Server finns i [använder edge-noder i HDInsight](hdinsight-apps-use-edge-node.md) dokumentet.

## <a name="accessing-the-nodes"></a>Åtkomst till noderna

Åtkomst till klustret via internet tillhandahålls via en gemensam gateway. Åtkomst är begränsad för att ansluta till huvudnoderna och (om sådan finns) kantnoden. Åtkomst till tjänster som körs på huvudnoderna genomförs inte genom att låta flera huvudnoderna. Offentlig-gateway dirigerar begäran till huvudnod som är värd för den begärda tjänsten. Till exempel om Ambari är för närvarande finns på den andra huvudnoden, de-gatewayen dirigerar inkommande begäranden för Ambari till noden.

Åtkomst via offentliga gatewayen är begränsad till port 443 (HTTPS), 22 och 23.

* Port __443__ används för att komma åt Ambari och andra webb-Gränssnittet eller REST API: er finns på huvudnoderna.

* Port __22__ används för åtkomst till den primära huvudnod eller kantnod med SSH.

* Port __23__ används för åtkomst till andra huvudnod med SSH. Till exempel `ssh username@mycluster-ssh.azurehdinsight.net` ansluter till klustret med namnet primära huvudnod **mycluster**.

Mer information om hur du använder SSH finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentet.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Internt fullständigt kvalificerade domännamn (FQDN)

Noder i ett HDInsight-kluster har en intern IP-adress och FQDN som kan bara kommas åt från klustret. Vid åtkomst till tjänsterna i klustret med hjälp av interna FQDN eller IP-adress, bör du använda Ambari för att verifiera IP eller FQDN för att använda vid åtkomst till tjänsten.

Till exempel Oozie-tjänsten kan endast köras på en huvudnod och använder den `oozie` kommando från en SSH-session kräver URL till tjänsten. URL: en kan hämtas från Ambari med hjälp av följande kommando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Det här kommandot returnerar ett värde som liknar följande kommando, som innehåller den interna URL som ska användas med den `oozie` kommando:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Mer information om hur du arbetar med Ambari REST API finns [övervaka och hantera HDInsight med Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Åtkomst till andra nodtyper

Du kan ansluta till noder som inte är direkt åtkomliga via internet med hjälp av följande metoder:

* **SSH**: när du är ansluten till en huvudnod via SSH, du kan sedan använda SSH från huvudnod för att ansluta till andra noder i klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH-Tunnel**: Om du behöver åtkomst till en webbtjänst som finns på en av de noder som inte exponeras till internet, måste du använda en SSH-tunnel. Mer information finns i [använder en SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

* **Virtuella Azure-nätverket**: om HDInsight-kluster är en del av ett Azure Virtual Network, alla resurser på samma virtuella nätverk direkt åtkomst till alla noder i klustret. Mer information finns i [utöka HDInsight med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentet.

## <a name="how-to-check-on-a-service-status"></a>Så här kontrollerar du Tjänststatus

Använda Ambari-Webbgränssnittet eller Ambari REST API för att kontrollera status för tjänster som körs på huvudnoderna.

### <a name="ambari-web-ui"></a>Ambari-webbgränssnittet

Ambari-Webbgränssnittet kan visas på https://CLUSTERNAME.azurehdinsight.net. Ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas ange HTTP-autentiseringsuppgifter för klustret. HTTP-Standardanvändarnamnet är **admin** och lösenordet är det lösenord du angav när du skapar klustret.

När du kommer på sidan Ambari visas installerade tjänster till vänster på sidan.

![Installerade tjänster](./media/hdinsight-high-availability-linux/services.png)

Det finns ett antal ikoner som visas bredvid en tjänst för att ange status. Alla aviseringar som rör en tjänst kan visas med den **aviseringar** längst upp på sidan. Du kan markera varje tjänst om du vill visa mer information på den.

När sidan innehåller information om status och konfiguration för varje tjänst, innehåller inte information som på vilka huvudnod tjänsten körs på. Du kan visa den här informationen i **värdar** längst upp på sidan. Den här sidan visar värdar inom klustret, inklusive huvudnoderna.

![lista över värdar](./media/hdinsight-high-availability-linux/hosts.png)

Om du väljer länken för en huvudnoderna visar tjänster och komponenter som körs på noden.

![Komponentstatus](./media/hdinsight-high-availability-linux/nodeservices.png)

Läs mer om hur du använder Ambari [övervaka och hantera HDInsight med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API är tillgänglig via internet. HDInsight offentliga gateway hanterar routning förfrågningar till huvudnod som för närvarande är värd för REST-API.

Du kan använda följande kommando för att kontrollera tillståndet för en tjänst genom Ambari REST API:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Ersätt **lösenord** med HTTP (admin) användarkontots lösenord.
* Ersätt **CLUSTERNAME** med namnet på klustret.
* Ersätt **SERVICENAME** med namnet på den tjänst som du vill kontrollera status för.

Till exempel för att kontrollera status för den **HDFS** på ett kluster med namnet **mycluster**, med ett lösenord för **lösenord**, använder du följande kommando:

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

Tillståndet talar om för oss att tjänsten körs, eller **igång**.

Om du inte vet vilka tjänster som är installerade på klustret använder du följande kommando för att hämta en lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Mer information om hur du arbetar med Ambari REST API finns [övervaka och hantera HDInsight med Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Tjänstens komponenter

Tjänsterna kan innehålla komponenter som du vill kontrollera status för individuellt. HDFS innehåller till exempel komponenten NameNode. Om du vill visa information om en komponent, skulle kommandot se ut:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Om du inte vet vilka komponenter som tillhandahålls av en tjänst använder du följande kommando för att hämta en lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Hur du kommer åt loggfilerna på huvudnoderna

### <a name="ssh"></a>SSH

När du är ansluten till huvudnod via SSH loggfiler finns under **/var/log**. Till exempel **/var/log/hadoop-yarn/yarn** innehåller loggarna för YARN.

Varje huvudnod kan ha unika loggposter, så du bör kontrollera loggfilerna på båda.

### <a name="sftp"></a>SFTP

Du kan också ansluta till huvudnod med SSH File Transfer Protocol eller SFTP Secure File Transfer Protocol () och ladda ned filerna direkt.

Ungefär som att använda en SSH-klienten när du ansluter till klustret som du måste ange SSH-användarnamn för kontot och SSH-adressen för klustret. Till exempel `sftp username@mycluster-ssh.azurehdinsight.net`. Ange lösenordet för kontot när du uppmanas, eller ange en offentlig nyckel med hjälp av den `-i` parameter.

När du är ansluten, visas en `sftp>` prompt. I det här meddelandet kan du ändra kataloger, ladda upp och hämta filer. Till exempel följande kommandon ändrar du katalog till den **/var/log/hadoop/hdfs** directory och sedan ladda ned alla filer i katalogen.

    cd /var/log/hadoop/hdfs
    get *

En lista över tillgängliga kommandon, ange `help` på den `sftp>` prompt.

> [!NOTE]
> Det finns också grafiska gränssnitt som gör det möjligt att visualisera filsystemet när ansluten via SFTP. Till exempel [MobaXTerm](http://mobaxterm.mobatek.net/) kan du bläddra filsystemet med hjälp av ett gränssnitt som liknar Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Om du vill öppna loggfiler med Ambari, måste du använda en SSH-tunnel. Webbgränssnitt för enskilda tjänster visas inte offentligt på Internet. Information om hur du använder en SSH-tunnel finns i [använda SSH-tunnlar](hdinsight-linux-ambari-ssh-tunnel.md) dokumentet.

Ambari-Webbgränssnittet, Välj tjänsten som du vill visa loggar för (till exempel YARN). Använd sedan **snabblänkar** att välja vilka huvudnod att visa i loggarna.

![Använda snabblänkar för att visa loggar](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Så här konfigurerar du nodstorlek

Storleken på en nod kan endast väljas när klustret skapas. Du hittar en lista över de olika tillgängliga storlekarna på VM för HDInsight på den [HDInsight sida med priser](https://azure.microsoft.com/pricing/details/hdinsight/).

När du skapar ett kluster, kan du ange storleken på noderna. Följande information innehåller information om hur du anger en storlek med hjälp av den [Azure-portalen][preview-portal], [Azure PowerShell][azure-powershell], och [Azure CLI][azure-cli]:

* **Azure-portalen**: när du skapar ett kluster, kan du ange storleken på de noder som används av klustret:

    ![Bild av guiden för kluster skapas med val av storlek](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: när du använder den `azure hdinsight cluster create` kommandot, du kan ange storleken på head, arbetare och ZooKeeper-noder med hjälp av den `--headNodeSize`, `--workerNodeSize`, och `--zookeeperNodeSize` parametrar.

* **Azure PowerShell**: när du använder den `New-AzureRmHDInsightCluster` cmdlet, kan du ange storleken på head, arbetare och ZooKeeper-noder med den `-HeadNodeVMSize`, `-WorkerNodeSize`, och `-ZookeeperNodeSize` parametrar.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta mer om vad som nämns i det här dokumentet.

* [Ambari REST-referens](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installera och konfigurera Azure CLI](../cli-install-nodejs.md)
* Installera och konfigurera [Azure PowerShell](/powershell/azure/overview)
* [Hantera HDInsight med Ambari](hdinsight-hadoop-manage-ambari.md)
* [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
