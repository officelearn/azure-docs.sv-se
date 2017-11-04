---
title: "Konfigurera din fristående Azure Service Fabric-kluster | Microsoft Docs"
description: "Lär dig hur du konfigurerar din fristående eller lokala Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: dc17ba7f8cc1326790b0256de277ccb2eaa20949
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Konfigurationsinställningarna för ett fristående Windows-kluster
Den här artikeln beskriver hur du konfigurerar ett fristående Azure Service Fabric-kluster med hjälp av filen ClusterConfig.JSON. Du kan använda den här filen för att ange information, till exempel Service Fabric-noder och deras IP-adresser och de olika typerna av noder i klustret. Du kan också ange säkerhetskonfigurationer samt nätverkstopologin vad gäller fel/uppgradera domäner för fristående klustret.

När du [hämta fristående Service Fabric-paket](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), några exempel på ClusterConfig.JSON-filen laddas ned till datorn och arbete. De exempel som har DevCluster i sina namn hjälpa dig att skapa ett kluster med alla tre noder på samma dator som logiska noder. Utanför dessa noder måste minst vara markerad som primära noden. Det här klustret är användbart för en miljö för utveckling eller testning. Den stöds inte som ett produktionskluster. De exempel som har MultiMachine i sina namn hjälper dig att skapa ett kluster med hög kvalitet, med varje nod på en separat dator. Antalet primära noder för dessa kluster baseras på den [nivå av tillförlitlighet](#reliability). Vi tagit bort egenskapen tillförlitlighet nivå i version 5.7 API-versionen 05 2017. I stället beräknar koden nivån mest optimal tillförlitlighet för klustret. Använd inte den här egenskapen i koden 5.7 och senare versioner.


* Visar hur du skapar ett oskyddat test eller produktionskluster, respektive ClusterConfig.Unsecure.DevCluster.JSON och ClusterConfig.Unsecure.MultiMachine.JSON.

* ClusterConfig.Windows.DevCluster.JSON och ClusterConfig.Windows.MultiMachine.JSON visar hur du skapar test- eller kluster som skyddas med hjälp av [Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.JSON och ClusterConfig.X509.MultiMachine.JSON visar hur du skapar test- eller kluster som skyddas med hjälp av [X509 certifikatbaserad säkerhet](service-fabric-windows-cluster-x509-security.md).

Nu ska vi undersöka olika delar av en fil i ClusterConfig.JSON.

## <a name="general-cluster-configurations"></a>Allmän klusterkonfigurationer
Allmän klusterkonfigurationer omfatta bred klusterspecifika-konfigurationer, enligt följande kodavsnitt i JSON:

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",

Du kan ge ett eget namn till Service Fabric-kluster genom att tilldela variabeln namn. ClusterConfigurationVersion är versionsnumret för klustret. Öka det varje gång du uppgraderar Service Fabric-klustret. Lämna apiVersion angetts till standardvärdet.

    <a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Noder i klustret
Du kan konfigurera noderna på din Service Fabric-kluster med hjälp av avsnittet noder som i följande fragment visas:

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

Ett Service Fabric-kluster måste innehålla minst tre noder. Du kan lägga till fler noder i det här avsnittet enligt din konfiguration. I följande tabell beskrivs konfigurationsinställningarna för varje nod:

| **Nodkonfiguration** | **Beskrivning** |
| --- | --- |
| Nodnamn |Du kan ge ett eget namn till noden. |
| IP-adress |Ta reda på IP-adressen för noden genom att öppna ett kommandofönster och skriva `ipconfig`. Notera IPV4-adress och tilldela variabeln IP-adress. |
| nodeTypeRef |Varje nod kan tilldelas en annan nod-typen. Den [nodtyper](#node-types) definieras i följande avsnitt. |
| faultDomain |Feldomäner aktivera klusteradministratörer definiera fysiska noder som kan misslyckas på grund av delade fysiska beroenden samtidigt. |
| upgradeDomain |Uppgraderingsdomäner beskrivs uppsättningar med noder som är avstängd för Service Fabric-uppgraderingar på ungefär samma tidpunkt. Du kan välja vilka noder som ska tilldelas vilka uppgraderingsdomäner eftersom de inte är begränsad av alla fysiska krav. |

## <a name="cluster-properties"></a>Egenskaper för klustret
Egenskapsavsnittet i ClusterConfig.JSON används för att konfigurera klustret som visas:

    <a id="reliability"></a>

### <a name="reliability"></a>Tillförlitlighet
Begreppet reliabilityLevel definierar antal repliker eller instanser av tjänsterna för Service Fabric-system som kan köras på de primära noderna i klustret. Den avgör tillförlitligheten för dessa tjänster och därför klustret. Värdet beräknas av systemet när klustret skapas och uppgradering.

### <a name="diagnostics"></a>Diagnostik
Du kan konfigurera parametrar för att aktivera diagnostik- och felsökning av fel på nod eller ett kluster, enligt följande kodavsnitt i avsnittet diagnosticsStore: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Metadata är en beskrivning av kluster-diagnostik och kan anges enligt din konfiguration. Dessa variabler hjälp vid insamling av loggar för ETW-spårning och kraschdumpar samt prestandaräknare. Mer information om ETW spårningsloggar finns [spårloggen finns det](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) och [ETW-spårning](https://msdn.microsoft.com/library/ms751538.aspx). Alla loggar, inklusive [krascher Dumpar](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) och [prestandaräknare](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), dirigeras till mappen connectionString på din dator. Du kan också använda AzureStorage för att lagra diagnostik. Se följande exempel kodavsnitt:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Säkerhet
Avsnittet krävs för en säker fristående Service Fabric-klustret. Följande fragment visas en del av det här avsnittet:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Metadata är en beskrivning av säker klustret och kan anges enligt din konfiguration. Bestämmer vilken typ av säkerhet som klustret och noderna implementera ClusterCredentialType och ServerCredentialType. De kan vara inställd på antingen *X509* för en certifikatbaserad säkerhet eller *Windows* för Azure Active Directory-baserad säkerhet. Resten av avsnittet baseras på typen av säkerhet. Information om hur du fylla i resten av avsnittet finns [certifikat baserade säkerhet i ett fristående kluster](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet i ett kluster med fristående](service-fabric-windows-cluster-windows-security.md).

    <a id="nodetypes"></a>

### <a name="node-types"></a>Nodtyper
Nodetypes får beskrivs typ av noder med klustret. Minst en nodtyp måste anges för ett kluster som visas i följande utdrag: 

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

Namnet är det egna namnet för den här typen av viss nod. Om du vill skapa en nod i den här nodtypen tilldela dess egna namn till variabeln nodeTypeRef för noden, som [tidigare nämnts](#nodes-on-the-cluster). För varje nod, anger du anslutningens slutpunkter som används. Du kan välja ett annat portnummer för dessa slutpunkter för anslutningen, så länge som de inte är i konflikt med andra slutpunkter i det här klustret. Det finns en eller flera primära noder i ett kluster med flera noder (det vill säga isPrimary anges till *SANT*), beroende på den [reliabilityLevel](#reliability). Mer information om primära och nonprimary nodtyper finns [Service Fabric-kluster kapacitetsplaneringsöverväganden](service-fabric-cluster-capacity.md) information om nodetypes får och reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Slutpunkter som används för att konfigurera nodtyperna
* clientConnectionEndpointPort är den port som används av klienten för att ansluta till klustret när klientens API: er används. 
* clusterConnectionEndpointPort är den port där noder kommunicerar med varandra.
* leaseDriverEndpointPort är den port som används av klustret lån drivrutinen för att ta reda på om noderna som fortfarande är aktiva. 
* serviceConnectionEndpointPort är den port som används av program och tjänster som har distribuerats på en nod för att kommunicera med Service Fabric-klienten på just den noden.
* httpGatewayEndpointPort är den port som används av Service Fabric Explorer för att ansluta till klustret.
* ephemeralPorts åsidosätta den [dynamiska portar som används av Operativsystemet](https://support.microsoft.com/kb/929851). Service Fabric använder en del av de här portarna som programmet portar och de återstående är tillgängliga för Operativsystemet. Den visar också det här intervallet till befintliga området finns i Operativsystemet, så du kan använda intervall som anges i JSON exempelfiler för alla ändamål. Se till att skillnaden mellan början och slutportar är minst 255. Om denna skillnad är för låg eftersom det här intervallet delas med Operativsystemet kan du köra i konflikt. Om du vill visa de konfigurerade dynamiskt portintervallet kör `netsh int ipv4 show dynamicport tcp`.
* applicationPorts är de portar som används av Service Fabric-program. Portintervallet programmet bör vara tillräckligt stor för att täcka endpoint behovet av dina program. Det här intervallet ska vara exklusiv från intervallet på datorn, det vill säga ephemeralPorts intervallet som angetts i konfigurationen. Service Fabric använder dessa portar när nya portar är obligatoriska och tar hand om öppna brandväggen för dessa portar. 
* reverseProxyEndpointPort är en valfri omvänd proxy-slutpunkt. Mer information finns i [Service Fabric omvänd proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Logginställningar
Du kan ange rotkataloger för Service Fabric-data och loggfiler i avsnittet fabricSettings. Du kan anpassa dessa kataloger endast när det första klustret skapandet. Se följande exempel fragment av det här avsnittet:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Vi rekommenderar att du använder en icke-OS-enhet som FabricDataRoot och FabricLogRoot. Det ger högre tillförlitlighet för att undvika situationer när Operativsystemet slutar svara. Om du har ändrat dataroten placeras loggen roten på en nivå under dataroten.

### <a name="stateful-reliable-services-settings"></a>Inställningar för tillståndskänsliga Reliable Services
Du kan ange globala konfigurationsinställningarna för Reliable Services i avsnittet KtlLogger. Mer information om dessa inställningar finns [konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md). I följande exempel visas hur du ändrar delade transaktionsloggen som skapas om du vill säkerhetskopiera alla tillförlitliga samlingar för tillståndskänsliga tjänster:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Tilläggsfunktioner
Konfigurera apiVersion som 04 2017 eller högre för att konfigurera tilläggsfunktioner, och konfigurera addonFeatures som visas här:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Stöd för behållaren
Om du vill aktivera behållaren stöd för både Windows Server-behållare och Hyper-V-behållare för fristående kluster måste funktionen DnsService tillägg aktiveras.


## <a name="next-steps"></a>Nästa steg
När du har en hel ClusterConfig.JSON fil som har konfigurerats enligt din fristående konfiguration kan distribuera du ditt kluster. Följ stegen i [skapa ett fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md). Gå sedan vidare till [visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) och följer sedan anvisningarna.

