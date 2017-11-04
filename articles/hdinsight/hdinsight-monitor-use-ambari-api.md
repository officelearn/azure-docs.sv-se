---
title: "Övervaka Hadoop-kluster i HDInsight med Ambari API - Azure | Microsoft Docs"
description: "Använd Apache Ambari APIs för att skapa, hantera och övervaka Hadoop-kluster. Döljer komplexiteten hos Hadoop intuitiva operatorn verktyg och API: er."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 57b63490037760c9150fd4b8fdb66267739f20ae
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Övervaka Hadoop-kluster i HDInsight Ambari API
Lär dig hur du övervakar HDInsight-kluster med Ambari APIs.

> [!NOTE]
> Informationen i den här artikeln är främst för Windows-baserade HDInsight-kluster som innehåller en skrivskyddad version av Ambari REST API. Linux-baserade kluster, se [hantera Hadoop-kluster med Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Vad är Ambari?
[Apache Ambari] [ ambari-home] används för etablering, hantering och övervakning av Apache Hadoop-kluster. Det innehåller en intuitiv samling operatörsverktyg och en stabil uppsättning API:er som döljer komplexiteten hos Hadoop och förenklar klusteranvändningen. Mer information om API: erna finns [Ambari API-referens][ambari-api-reference]. 

HDInsight stöder för närvarande endast övervakningsfunktion Ambari. Ambari API 1.0 stöds av version 3.0 och 2.1 HDInsight-kluster. Den här artikeln täcker inte att komma åt Ambari APIs på HDInsight version 3.1 och 2.1-kluster. Den viktigaste skillnaden mellan två är att vissa av komponenterna har ändrats av nya funktioner (t.ex historik jobbserver). 

**Förutsättningar**

Innan du börjar den här självstudiekursen behöver du följande:

* **En arbetsstation med Azure PowerShell**.
* (Valfritt) [cURL][curl]. Om du vill installera den [cURL versioner och hämtningsbara filer][curl-download].
  
  > [!NOTE]
  > När du använder cURL-kommando i Windows, Använd dubbla citattecken i stället för citattecken för alternativvärden.
  > 
  > 
* **Ett Azure HDInsight-kluster**. Instruktioner om hur klusteretablering finns [komma igång med HDInsight] [ hdinsight-get-started] eller [etablera HDInsight-kluster][hdinsight-provision]. Du behöver följande data gå igenom kursen:
  
  | Kluster-egenskap | Variabelnamn för Azure PowerShell | Värde | Beskrivning |
  | --- | --- | --- | --- |
  |   HDInsight-klustrets namn |$clusterName | |Namnet på ditt HDInsight-kluster. |
  |   Användarnamn för kluster |$clusterUsername | |Användaren klusternamnet anges när klustret skapades. |
  |   Kluster-lösenord |$clusterPassword | |Användarlösenordet för klustret. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Komma igång
Det finns flera sätt att använda Ambari och övervaka HDInsight-kluster.

**Använda Azure PowerShell**

Följande Azure PowerShell-skript hämtar MapReduce spåraren jobbinformation *i ett kluster i HDInsight 3.5.*  Den viktigaste skillnaden är att vi hämtar informationen från den YARN-tjänsten (i stället MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Följande PowerShell-skript hämtar MapReduce spåraren jobbinformation *i ett kluster i HDInsight 2.1*:

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

**2014-10-8-versionen**:

När du använder Ambari-slutpunkten ”https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}” den *värddatornamn* field returnerar det fullständigt kvalificerade domännamnet (FQDN) för noden i stället för värdnamnet. Före version 2014-10-8, det här exemplet returneras bara ”**headnode0**”. Efter 2014-10-8-version du hämta det fullständiga Domännamnet ”**headnode0. { ClusterDNS}. azurehdinsight.NET .net**”, som visas i föregående exempel. Den här ändringen krävdes för att underlätta scenarier där flera klustertyper (till exempel HBase och Hadoop) kan distribueras i ett virtuellt nätverk (VNET). Detta inträffar exempelvis när du använder HBase som en backend-plattform för Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari övervakning API: er
I följande tabell visas några av de vanligaste Ambari övervakning API-anrop. Mer information om API finns [Ambari API-referens][ambari-api-reference].

| Övervakare för API-anrop | URI: N | Beskrivning |
| --- | --- | --- |
| Hämta kluster |`/api/v1/clusters` | |
| Hämta klusterinformation. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |kluster, tjänster, värdar |
| Hämta tjänster |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Tjänsterna omfattar: hdfs, mapreduce |
| Hämta information för tjänster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Hämta tjänstkomponenter |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Hämta information för komponenten. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo värd-komponenter, mått |
| Hämta värdar |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0 workernode0 |
| Hämta information om värden. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Hämta värden komponenter |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Hämta värden komponenten information. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles komponent, värd, mått |
| Hämta konfigurationer |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Config-typer: core-plats, hdfs-plats, mapred-plats, hive-plats |
| Hämta konfigurationsinformation. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Config-typer: core-plats, hdfs-plats, mapred-plats, hive-plats |

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Ambari övervakning API-anrop. Du kan läsa mer här:

* [Hantera HDInsight-kluster med Azure-portalen][hdinsight-admin-portal]
* [Hantera HDInsight-kluster med Azure PowerShell][hdinsight-admin-powershell]
* [Hantera HDInsight-kluster med hjälp av kommandoradsgränssnittet][hdinsight-admin-cli]
* [HDInsight-dokumentation][hdinsight-documentation]
* [Komma igång med HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
