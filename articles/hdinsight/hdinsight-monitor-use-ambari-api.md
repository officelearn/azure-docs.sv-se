---
title: Övervaka Hadoop-kluster i HDInsight med Ambari-API – Azure
description: 'Använd Apache Ambari APIs för att skapa, hantera och övervaka Hadoop-kluster. Intuitiva operatörsverktyg och API: er döljer komplexiteten i Hadoop.'
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ff6601042c82cef2b0101833117f17aca8b463dc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223265"
---
# <a name="monitor-apache-hadoop-clusters-in-hdinsight-using-the-apache-ambari-api"></a>Övervaka Apache Hadoop-kluster i HDInsight med hjälp av Apache Ambari API
Lär dig mer om att övervaka HDInsight-kluster med hjälp av Apache Ambari APIs.

> [!NOTE]  
> Informationen i den här artikeln är främst för Windows-baserade HDInsight-kluster som innehåller en skrivskyddad version av Ambari REST API. Linux-baserade kluster, se [hantera Apache Hadoop-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>Vad är Ambari?
[Apache Ambari] [ ambari-home] används för att etablera, hantera och övervaka Apache Hadoop-kluster. Det innehåller en intuitiv samling operatörsverktyg och en stabil uppsättning API:er som döljer komplexiteten hos Hadoop och förenklar klusteranvändningen. Läs mer om API: erna [Ambari API-referens][ambari-api-reference]. 

HDInsight stöder för närvarande endast övervakningsfunktionen Ambari. Ambari API 1.0 stöds av HDInsight version 3.0 och 2.1-kluster. Den här artikeln beskriver åtkomst till Ambari APIs på HDInsight version 3.1 och 2.1-kluster. Den viktigaste skillnaden mellan två är att några av komponenterna har ändrats med introduktionen av nya funktioner (t.ex historik jobbserver). 

**Förutsättningar**

Innan du börjar den här självstudiekursen behöver du följande:

* **En arbetsstation med Azure PowerShell**.
* (Valfritt) [cURL][curl]. För att installera den, se [cURL versioner och nedladdningar][curl-download].
  
  > [!NOTE]  
  > När du använder cURL-kommando i Windows, Använd dubbla citattecken i stället för citattecken för värden för alternativ.

* **Ett Azure HDInsight-kluster**. Anvisningar om att etablera klustret finns i [komma igång med HDInsight] [ hdinsight-get-started] eller [etablera HDInsight-kluster][hdinsight-provision]. Följande data gå igenom självstudien behöver du:
  
  | Kluster-egenskapen | Variabelnamn för Azure PowerShell | Värde | Beskrivning |
  | --- | --- | --- | --- |
  |   HDInsight-klusternamnet |$clusterName | |Namnet på ditt HDInsight-kluster. |
  |   Användarnamn |$clusterUsername | |Användaren klusternamnet anges när klustret skapades. |
  |   Klusterlösenord |$clusterPassword | |Användarlösenord för klustret. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Jump-start
Det finns flera sätt att använda Ambari och övervaka HDInsight-kluster.

**Använda Azure PowerShell**

Följande Azure PowerShell-skript hämtar MapReduce spåraren Jobbinformationen *i ett HDInsight 3.5-kluster.*  Den viktigaste skillnaden är att vi hämta informationen från den YARN-tjänsten (snarare än MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Följande PowerShell-skript hämtar MapReduce spåraren Jobbinformationen *i ett kluster i HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Utdata är:

![Jobtracker utdata][img-jobtracker-output]

**Använda cURL**

I följande exempel hämtar information om klustret med hjälp av cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Utdata är:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**För version 2014-10-8**:

När du använder Ambari-slutpunkten ”https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}” i *värddatornamn* fält Returnerar det fullständigt kvalificerade domännamnet (FQDN) för noden i stället för värdnamnet. Före 10/8/2014-versionen i det här exemplet returneras bara ”**headnode0**”. Efter 10/8/2014-versionen kan du hämta det fullständiga Domännamnet ”**headnode0. { ClusterDNS} gt; .azurehdinsight .net**”, enligt exemplet ovan. Den här ändringen krävdes för att underlätta scenarier där flera klustertyper (till exempel HBase och Hadoop) kan distribueras i ett virtuellt nätverk (VNET). Detta inträffar exempelvis när du använder HBase som en backend-plattform för Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari övervaka API: er
I följande tabell visas några av de vanligaste Ambari övervakning av API-anrop. Mer information om API: et finns i [Apache Ambari API-referens][ambari-api-reference].

| Övervaka API-anrop | URI | Beskrivning |
| --- | --- | --- |
| Hämta kluster |`/api/v1/clusters` | |
| Hämta klusterinformation. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |kluster, tjänster, värdar |
| Hämta tjänster |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Tjänsterna omfattar: hdfs, mapreduce |
| Hämta info för tjänster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Hämta tjänstkomponenter |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Hämta info för komponenten. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo värd-komponenter, mått |
| Hämta värdar |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0 workernode0 |
| Hämta värdinformationen. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Hämta värd-komponenter |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Hämta värden komponent-information. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles komponent, värden, mått |
| Hämta konfigurationer |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Config-typer: core-plats, hdfs-plats, mapred-plats, hive-plats |
| Få information om serverkonfigurationen. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Config-typer: core-plats, hdfs-plats, mapred-plats, hive-plats |

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Apache Ambari övervakning API-anrop. Du kan läsa mer här:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Hantera HDInsight-kluster med Azure PowerShell][hdinsight-admin-powershell]
* [Hantera HDInsight-kluster med hjälp av kommandoradsgränssnittet][hdinsight-admin-cli]
* [HDInsight-dokumentation][hdinsight-documentation]
* [Kom igång med HDInsight][hdinsight-get-started]

[ambari-home]: https://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: https://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
