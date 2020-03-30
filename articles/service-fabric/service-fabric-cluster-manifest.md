---
title: Konfigurera ditt fristående Azure Service Fabric-kluster
description: Lär dig hur du konfigurerar ditt fristående eller lokala Azure Service Fabric-kluster.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458370"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Konfigurationsinställningar för ett fristående Windows-kluster
I den här artikeln beskrivs konfigurationsinställningar för ett fristående Azure Service Fabric-kluster som kan anges i *filen ClusterConfig.json.* Du kommer att använda den här filen för att ange information om klustrets noder, säkerhetskonfigurationer samt nätverkstopologin när det gäller fel- och uppgraderingsdomäner.  När du har ändrat eller lagt till konfigurationsinställningar kan du antingen [skapa ett fristående kluster](service-fabric-cluster-creation-for-windows-server.md) eller uppgradera [konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).

När du [hämtar det fristående Service Fabric-paketet](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)ingår även ClusterConfig.json-exempel. Exemplen som har "DevCluster" i sina namn skapar ett kluster med alla tre noder på samma dator med hjälp av logiska noder. Av dessa noder måste minst en markeras som en primär nod. Den här typen av kluster är användbart för utvecklings- eller testmiljöer. Det stöds inte som ett produktionskluster. Exemplen som har "MultiMachine" i sina namn hjälper till att skapa kluster av produktionsresultat, med varje nod på en separat dator. Antalet primära noder för dessa kluster baseras på klustrets [tillförlitlighetsnivå](#reliability). I utgåva 5.7, API Version 05-2017, tog vi bort egenskapen tillförlitlighetsnivå. I stället beräknar vår kod den mest optimerade tillförlitlighetsnivån för ditt kluster. Försök inte att ange ett värde för den här egenskapen i version 5.7 och framåt.

* ClusterConfig.Unsecure.DevCluster.json och ClusterConfig.Unsecure.MultiMachine.json visar hur du skapar ett osäkert test eller produktionskluster.

* ClusterConfig.Windows.DevCluster.json och ClusterConfig.Windows.MultiMachine.json visar hur du skapar test- eller produktionskluster som skyddas med hjälp av [Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json och ClusterConfig.X509.MultiMachine.json visar hur du skapar test- eller produktionskluster som är säkrade med hjälp av [X509 certifikatbaserad säkerhet](service-fabric-windows-cluster-x509-security.md).

Nu ska vi undersöka de olika delarna av en ClusterConfig.json-fil.

## <a name="general-cluster-configurations"></a>Allmänna klusterkonfigurationer
Allmänna klusterkonfigurationer täcker de breda klusterspecifika konfigurationerna, vilket visas i följande JSON-kodavsnitt:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Du kan ge ditt servicetyg-kluster vilket eget namn som helst genom att tilldela det till namnvariabeln. ClusterConfigurationVersion är versionsnumret för klustret. Öka den varje gång du uppgraderar servicetyg-klustret. Lämna apiVersion inställt på standardvärdet.

## <a name="nodes-on-the-cluster"></a>Noder i klustret
Du kan konfigurera noderna i Service Fabric-klustret med hjälp av avsnittet noder, som följande utdrag visar:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Ett Service Fabric-kluster måste innehålla minst tre noder. Du kan lägga till fler noder i det här avsnittet enligt din konfiguration. I följande tabell förklaras konfigurationsinställningarna för varje nod:

| **Nodkonfiguration** | **Beskrivning** |
| --- | --- |
| nodeNamn |Du kan ge noden vilket eget namn som helst. |
| Ip |Ta reda på IP-adressen för din nod `ipconfig`genom att öppna ett kommandofönster och skriva . Observera IPV4-adressen och tilldela den till variabeln iPAddress. |
| nodeTypeRef |Varje nod kan tilldelas en annan nodtyp. [Nodtyperna](#node-types) definieras i följande avsnitt. |
| felDomän |Feldomäner gör det möjligt för klusteradministratörer att definiera de fysiska noder som kan misslyckas samtidigt på grund av delade fysiska beroenden. |
| uppgraderaDomain |Uppgraderingsdomäner beskriver uppsättningar noder som stängs av för uppgraderingar av Service Fabric vid ungefär samma tidpunkt. Du kan välja vilka noder som ska tilldelas vilka uppgraderingsdomäner, eftersom de inte begränsas av några fysiska krav. |

## <a name="cluster-properties"></a>Klusteregenskaper
Egenskapsavsnittet i ClusterConfig.json används för att konfigurera klustret enligt bilden:

### <a name="reliability"></a>Tillförlitlighet
Begreppet tillförlitlighetNivå definierar antalet repliker eller instanser av Service Fabric-systemtjänsterna som kan köras på klustrets primära noder. Den bestämmer tillförlitligheten hos dessa tjänster och därmed klustret. Värdet beräknas av systemet vid skapande och uppgradering av kluster.

### <a name="diagnostics"></a>Diagnostik
I avsnittet diagnosticsStore kan du konfigurera parametrar för diagnostik och felsökning av nod- eller klusterfel, vilket visas i följande kodavsnitt: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata är en beskrivning av klusterdiagnostiken och kan ställas in enligt din konfiguration. Dessa variabler hjälper till att samla in ETW-spårningsloggar och kraschdumpar samt prestandaräknare. Mer information om ETW-spårningsloggar finns i [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) och [ETW tracing](https://msdn.microsoft.com/library/ms751538.aspx). Alla loggar, inklusive [kraschdumpar](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) och [prestandaräknare,](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)kan dirigeras till mappen connectionString på datorn. Du kan också använda AzureStorage för att lagra diagnostik. Se följande exempelavsnitt:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Säkerhet
Säkerhetsavsnittet är nödvändigt för ett säkert fristående Service Fabric-kluster. Följande kodavsnitt visar en del av det här avsnittet:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata är en beskrivning av ditt säkra kluster och kan ställas in enligt din konfiguration. ClusterCredentialType och ServerCredentialType bestämmer vilken typ av säkerhet som klustret och noderna implementerar. De kan ställas in på antingen *X509* för en certifikatbaserad säkerhet eller *Windows* för Active Directory-baserad säkerhet. Resten av säkerhetsavsnittet baseras på typen av säkerhet. Information om hur du fyller i resten av säkerhetsavsnittet finns [i Certifikatbaserad säkerhet i ett fristående kluster](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet i ett fristående kluster](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Nodtyper
Avsnittet nodeTypes beskriver vilken typ av noder som klustret har. Minst en nodtyp måste anges för ett kluster, vilket visas i följande kodavsnitt: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Namnet är det egna namnet för den här nodtypen. Om du vill skapa en nod av den här nodtypen tilldelar du dess eget namn till nodenTypeRef-variabeln för den noden, som [tidigare nämnts](#nodes-on-the-cluster). För varje nodtyp definierar du de anslutningsslutpunkter som används. Du kan välja valfritt portnummer för dessa anslutningsslutpunkter, så länge de inte står i konflikt med några andra slutpunkter i det här klustret. I ett multinodkluster finns det en eller flera primära noder (det vill ha is, isPrimary är inställt på *true),* beroende på [tillförlitlighetenNivå](#reliability). Mer information om primära och icke-primära nodtyper finns i [Service Fabric kluster kapacitetsplanering överväganden](service-fabric-cluster-capacity.md) för information om nodeTypes och tillförlitlighetLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Slutpunkter som används för att konfigurera nodtyperna
* clientConnectionEndpointPort är den port som används av klienten för att ansluta till klustret när klient-API:er används. 
* clusterConnectionEndpointPort är den port där noderna kommunicerar med varandra.
* leaseDriverEndpointPort är den port som används av klusterhyresdrivrutinen för att ta reda på om noderna fortfarande är aktiva. 
* serviceConnectionEndpointPort är den port som används av de program och tjänster som distribueras på en nod för att kommunicera med Service Fabric-klienten på den specifika noden.
* httpGatewayEndpointPort är den port som används av Service Fabric Explorer för att ansluta till klustret.
* efemäraportar åsidosätter de [dynamiska portar som används av operativsystemet](https://support.microsoft.com/kb/929851). Service Fabric använder en del av dessa portar som programportar och de återstående är tillgängliga för operativsystemet. Det här området mappar också det här intervallet till det befintliga intervallet som finns i operativsystemet, så för alla ändamål kan du använda de intervall som anges i exempeljsonfilerna. Se till att skillnaden mellan start- och slutportarna är minst 255. Du kan stöta på konflikter om skillnaden är för låg, eftersom det här intervallet delas med operativsystemet. Om du vill se det `netsh int ipv4 show dynamicport tcp`konfigurerade dynamiska portintervallet kör du .
* applicationPorts är de portar som används av Service Fabric-programmen. Programportintervallet bör vara tillräckligt stort för att täcka slutpunktskravet för dina program. Det här intervallet bör vara exklusivt från det dynamiska portintervallet på datorn, det vill säga efemäraportintervallet som anges i konfigurationen. Service Fabric använder dessa portar när nya portar krävs och tar hand om att öppna brandväggen för dessa portar. 
* reverseProxyEndpointPort är en valfri omvänd proxyslutpunkt. Mer information finns i [Omvänd proxy för Service Fabric.](service-fabric-reverseproxy.md) 

### <a name="log-settings"></a>Logginställningar
I avsnittet fabricSettings kan du ställa in rotkataloger för Service Fabric-data och loggar. Du kan bara anpassa dessa kataloger under den första klusterskapandet. Se följande exempelavsnitt i det här avsnittet:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Vi rekommenderar att du använder en icke-OS-enhet som FabricDataRoot och FabricLogRoot. Det ger mer tillförlitlighet när du undviker situationer när operativsystemet slutar svara. Om du bara anpassar dataroten placeras loggroten en nivå under dataroten.

### <a name="stateful-reliable-services-settings"></a>Tillståndskänsliga inställningar för tillförlitliga tjänster
I avsnittet KtlLogger kan du ställa in de globala konfigurationsinställningarna för tillförlitliga tjänster. Mer information om dessa inställningar finns i [Konfigurera tillståndskänsliga tillförlitliga tjänster](service-fabric-reliable-services-configuration.md). I följande exempel visas hur du ändrar loggen för delade transaktioner som skapas för att stödja alla tillförlitliga samlingar för tillståndskänsliga tjänster:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Tilläggsfunktioner
Konfigurera apiVersion som 04-2017 eller högre om du vill konfigurera tilläggsfunktioner och konfigurera tilläggen enligt nedan:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Alla tillgängliga tilläggsfunktioner kan ses i [Service Fabric REST API Reference](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Stöd för containrar
Om du vill aktivera behållarstöd för både Windows Server-behållare och Hyper-V-behållare för fristående kluster måste dnsservice-tilläggsfunktionen vara aktiverad.

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat en komplett *ClusterConfig.json-fil* enligt din fristående klusterkonfiguration kan du distribuera klustret. Följ stegen i [Skapa ett fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md). 

Om du har ett fristående kluster distribuerat kan du också [uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Lär dig hur du [visualiserar klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

