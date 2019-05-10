---
title: Konfigurera ditt fristående kluster i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du konfigurerar ditt fristående eller en lokal Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: ae7fbef864634e47866de13384871a98b8ce4675
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209708"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Konfigurationsinställningar för ett fristående Windows-kluster
Den här artikeln beskriver konfigurationsinställningarna för ett fristående Azure Service Fabric-kluster som kan ställas in i den *ClusterConfig.json* fil. Du använder den här filen för att ange information om de klusternoder, säkerhetskonfigurationer, samt nätverkstopologi när det gäller fel- och uppgraderingsdomäner.  När du ändrar eller lägger till konfigurationsinställningar, kan du antingen [skapa ett fristående kluster](service-fabric-cluster-creation-for-windows-server.md) eller [uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).

När du [hämtningspaketet fristående Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), ClusterConfig.json-exempel ingår också. De exempel som har ”DevCluster” i sina namn kan du skapa ett kluster med alla tre noder på samma dator, med logiska noderna. Utanför dessa noder måste minst en markeras som den primära noden. Den här typen av kluster är användbart för utvecklings- och testmiljöer. Det kan inte användas som ett produktionskluster. Med hjälp av exempel som har ”MultiMachine” i sina namn skapar produktion i företagsklass kluster, med varje nod på en separat dator. Antalet primära noder för dessa kluster är baserad på klustrets [tillförlitlighetsnivån](#reliability). I version 5.7, API-Version 2017-05, vi har tagit bort tillförlitlighetsnivån egenskapen. I stället beräknar vår kod mest optimerad tillförlitlighetsnivån för klustret. Försök inte att ange ett värde för den här egenskapen i versioner 5.7 och senare.

* ClusterConfig.Unsecure.DevCluster.json och ClusterConfig.Unsecure.MultiMachine.json visar hur du skapar ett oskyddat test- eller produktionskluster respektive.

* ClusterConfig.Windows.DevCluster.json och ClusterConfig.Windows.MultiMachine.json visar hur du skapar test- eller produktionsmiljö kluster som skyddas med hjälp av [Windows security](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json och ClusterConfig.X509.MultiMachine.json visar hur du skapar test- eller produktionsmiljö kluster som skyddas med hjälp av [X509 certifikatbaserad security](service-fabric-windows-cluster-x509-security.md).

Nu ska vi granska de olika delarna av en ClusterConfig.json-fil.

## <a name="general-cluster-configurations"></a>Allmän klusterkonfigurationer
Allmän klusterkonfigurationer täcker bred klusterspecifika-konfigurationer, som visas i följande JSON-kodfragment:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Du kan ge ett eget namn till Service Fabric-klustret genom att tilldela den till variabeln namn. ClusterConfigurationVersion är det lägre versionsnumret för klustret. Öka den varje gång du uppgraderar Service Fabric-klustret. Lämna apiVersion set standardvärdet.

## <a name="nodes-on-the-cluster"></a>Noder i klustret
Du kan konfigurera noderna i ditt Service Fabric-kluster med hjälp av avsnittet noder som i följande fragment visas:
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

Service Fabric-kluster måste innehålla minst tre noder. Du kan lägga till fler noder i det här avsnittet enligt din konfiguration. Följande tabell förklarar konfigurationsinställningar för varje nod:

| **Nodkonfiguration** | **Beskrivning** |
| --- | --- |
| nodeName |Du kan ge ett eget namn till noden. |
| iPAddress |Ta reda på IP-adressen för noden genom att öppna ett kommandofönster och skriva `ipconfig`. Anteckna IPV4-adressen och tilldela den till variabeln IP-adress. |
| nodeTypeRef |Varje nod kan tilldelas en annan nodtyp. Den [nodtyper](#node-types) definieras i följande avsnitt. |
| faultDomain |Feldomäner kan klustret administratörer definiera fysiska noder som kan misslyckas på grund av delade fysiska beroenden samtidigt. |
| upgradeDomain |Uppgraderingsdomäner beskrivs uppsättningar med noder som stänger för Service Fabric-uppgraderingar på ungefär samma tidpunkt. Du kan välja vilka noder som ska tilldelas vilka uppgraderingsdomäner eftersom de inte är begränsad av alla fysiska krav. |

## <a name="cluster-properties"></a>Egenskaper för klustret
Egenskapsavsnittet i ClusterConfig.json används för att konfigurera klustret enligt:

### <a name="reliability"></a>Tillförlitlighet
Begreppet reliabilityLevel definierar antalet repliker eller instanser av Service Fabric-systemtjänster som kan köras på de primära noderna i klustret. Den avgör tillförlitligheten för dessa tjänster och klustret. Värdet beräknas av systemet när klustret skapas och uppgradering.

### <a name="diagnostics"></a>Diagnostik
Du kan konfigurera parametrar för att aktivera diagnostik och felsökning nod eller det kluster fel, enligt följande kodavsnitt i avsnittet diagnosticsStore: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata är en beskrivning av kluster-diagnostik och kan ställas in enligt din konfiguration. Dessa variabler hjälper dig att samla in ETW-spårningsloggarna och kraschdumpar samt prestandaräknare. Mer information om ETW-spårningsloggar finns i [spårloggen finns](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) och [ETW-spårning](https://msdn.microsoft.com/library/ms751538.aspx). Alla loggar, inklusive [kraschdumpar](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) och [prestandaräknare](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), kan dirigeras till mappen connectionString på din dator. Du kan också använda AzureStorage för att lagra diagnostik. Se följande exempel kodavsnitt:

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
Säkerhets-avsnittet är nödvändigt för säker fristående Service Fabric-kluster. Följande fragment visas en del av det här avsnittet:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata är en beskrivning av det säkra klustret och kan ställas in enligt din konfiguration. ClusterCredentialType och ServerCredentialType bestämmer vilken typ av säkerhet som implementerar klustret och noderna. De kan vara inställd på antingen *X509* för en säkerhets-och certifikatbaserad eller *Windows* för Active Directory-baserad säkerhet. Resten av avsnittet baseras på vilken typ av säkerhet. Information om hur du fyller i resten av Säkerhetsavsnittet finns i [certifikat-baserad säkerhet i ett fristående kluster](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet i ett fristående kluster](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Nodtyper
Avsnittet nodeTypes beskriver vilken typ av noder som klustret har. Minst en nodtyp måste anges för ett kluster som du ser i följande kodavsnitt: 

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

Namnet är det egna namnet för den här viss nodtyp. Om du vill skapa en nod i den här nodtypen tilldela dess eget namn som nodeTypeRef variabeln för den nod som [vi nämnde tidigare](#nodes-on-the-cluster). För varje nod, anger du anslutningens slutpunkter som används. Du kan välja alla portnumret för dessa anslutningsslutpunkter, så länge de inte är i konflikt med andra slutpunkter i det här klustret. Det finns en eller flera primära noder i ett kluster med flera noder, (det vill säga isPrimary är inställd på *SANT*), beroende på den [reliabilityLevel](#reliability). Mer information om primära och icke-primära nodtyper finns [Service Fabric-kluster kapacitetsplanering](service-fabric-cluster-capacity.md) information om nodeTypes och reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Slutpunkter som används för att konfigurera nodtyperna
* clientConnectionEndpointPort är den port som används av klienten för att ansluta till klustret när klientens API: er används. 
* clusterConnectionEndpointPort är den port där noder kommunicerar med varandra.
* leaseDriverEndpointPort är den port som används av klustret lånet drivrutinen för att ta reda på om noderna fortfarande är aktiva. 
* serviceConnectionEndpointPort är den port som används av program och tjänster som distribueras på en nod för att kommunicera med Service Fabric-klienten på just den noden.
* httpGatewayEndpointPort är den port som används av Service Fabric Explorer för att ansluta till klustret.
* ephemeralports får åsidosätta den [dynamiska portar som används av Operativsystemet](https://support.microsoft.com/kb/929851). Service Fabric använder en del av de här portarna som programportar och de återstående är tillgängliga för Operativsystemet. Den visar också det här intervallet till det befintliga intervallet som finns i Operativsystemet, så du kan använda de intervall som anges i exempel-JSON-filer för alla syften. Se till att skillnaden mellan början och slutet portarna är minst 255. Som kan uppstå konflikter om den här skillnaden är för lågt, eftersom det här intervallet delas med Operativsystemet. Om du vill se det konfigurerade intervallet, kör `netsh int ipv4 show dynamicport tcp`.
* applicationports får är de portar som används av Service Fabric-program. Portintervallet för programmet bör vara tillräckligt stor för att täcka behovet för slutpunkten för dina program. Det här intervallet ska vara exklusiv från det dynamiska portintervallet på datorn, det vill säga ephemeralPorts intervallet som angetts i konfigurationen. Service Fabric använder dessa portar när nya portar är obligatoriska och tar hand om du öppnar brandväggen för dessa portar. 
* reverseProxyEndpointPort är en valfri omvänd proxy-slutpunkt. Mer information finns i [Service Fabric omvänd proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Logginställningar
Du kan ange rotkataloger för Service Fabric-data och loggfiler i avsnittet fabricSettings. Du kan anpassa dessa kataloger endast när det första klustret skapandet. Se följande exempel kodavsnitt med det här avsnittet:

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

Vi rekommenderar att du använder en icke-OS-enhet som FabricDataRoot och FabricLogRoot. Det ger bättre tillförlitlighet för att undvika situationer när Operativsystemet slutar svara. Om du har ändrat dataroten placeras log roten på en nivå under dataroten.

### <a name="stateful-reliable-services-settings"></a>Tillståndskänsliga Reliable Services-inställningar
Du kan ange globala konfigurationsinställningarna för Reliable Services i avsnittet KtlLogger. Mer information om dessa inställningar finns i [konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md). I följande exempel visas hur du ändrar delade transaktionsloggen som skapas för att säkerhetskopiera alla tillförlitliga samlingar för tillståndskänsliga tjänster:

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
Konfigurera apiVersion som 04-2017 eller senare för att konfigurera tilläggsfunktioner, och konfigurera addonFeatures som visas här:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Stöd för containrar
Om du vill aktivera stöd för både Windows Server-behållare och Hyper-V-behållare för fristående kluster måste tilläggsfunktion DnsService aktiveras.

## <a name="next-steps"></a>Nästa steg
När du har ett komplett *ClusterConfig.json* fil som har konfigurerats enligt din konfiguration av fristående, kan du distribuera dina kluster. Följ stegen i [skapa fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md). 

Om du har ett fristående kluster som distribueras, kan du också [uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Lär dig hur du [visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

