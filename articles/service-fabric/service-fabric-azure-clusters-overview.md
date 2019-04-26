---
title: Skapa Azure Service Fabric-kluster i Windows Server och Linux | Microsoft Docs
description: Service Fabric-kluster som körs på Windows Server och Linux, vilket innebär att du ska kunna distribuera och värden Service Fabric-program var som helst som du kan köra Windows Server eller Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310931"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Översikt över Service Fabric-kluster på Azure
Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, balanserar Service Fabric service partitionsrepliker och instanser i det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker och instanser över minskade antalet noder för att bättre utnyttja maskinvaran på varje nod.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning noder (virtuella datorer) i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen. 

## <a name="cluster-components-and-resources"></a>Komponenter i serverkluster och resurser
Service Fabric-kluster på Azure är en Azure-resurs som använder och samverkar med andra Azure-resurser:
* Virtuella datorer och virtuella nätverkskort
* VM-skalningsuppsättningar
* virtuella nätverk
* lastbalanserare
* lagringskonton
* offentliga ip-adresser

![Service Fabric-kluster][Image]

### <a name="virtual-machine"></a>Virtuell dator
En [VM](/azure/virtual-machines/) som ingår i ett kluster kallas en nod om tekniskt sett en nod är en process för Service Fabric runtime. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel [placeringsegenskaper](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Varje dator eller virtuell dator har en autostarttjänst *FabricHost.exe*, som börjar köras när datorn startas och börjar sedan två körbara filer, *Fabric.exe* och *FabricGateway.exe* , som utgör noden. En Produktionsdistribution är en nod per fysisk eller virtuell dator. För att testa scenarier, du kan ha flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av *Fabric.exe* och *FabricGateway.exe*.

Varje virtuell dator som är associerad med ett virtuellt nätverkskort (NIC) och varje nätverkskort tilldelas en privat IP-adress.  En virtuell dator är tilldelad till ett virtuellt nätverk och lokala belastningsutjämnaren till nätverkskortet.

Alla virtuella datorer i ett kluster placeras i ett virtuellt nätverk.  Alla noder i samma nod typ/skalningsuppsättningen är placerade i samma undernät i det virtuella nätverket.  Dessa noder kan du bara har privata IP-adresser och är inte direkt adresserbara utanför det virtuella nätverket.  Klienter kan komma åt tjänster på noderna genom Azure-belastningsutjämnaren.

### <a name="scale-setnode-type"></a>Scale set/nodtyp
När du skapar ett kluster kan definiera du en eller flera nodtyper.  Ha samma storlek och egenskaper, till exempel antalet processorer, minne, antalet diskar och diskens i/o-noder eller virtuella datorer i en nodtyp.  En nodtyp kan exempelvis vara för små, frontend-datorer med portar öppna till internet, medan en annan nodtyp kan vara för stora, backend-virtuella datorer som bearbetar data. I Azure-kluster kan varje nodtyp mappas till en [virtual machine scale Sets](/azure/virtual-machine-scale-sets/).

Du kan använda skalningsuppsättningar för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skalningsuppsättning. Service Fabric-körningen är startad på varje virtuell dator i skalningsuppsättningen med Azure VM-tillägg. Du kan oberoende skala varje nodtyp upp eller ned, ändra den operativsystem-SKU som körs på varje nod i klustret, ha olika portar öppna och använda olika kapacitet. En skalningsuppsättning har fem [uppgraderingsdomäner](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) och fem [feldomäner](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) och kan ha upp till 100 virtuella datorer.  Du kan skapa kluster på fler än 100 noder genom att skapa flera scale sets/node-typer.

> [!IMPORTANT]
> En viktig uppgift är att välja antal nodtyper för ditt kluster och egenskaperna för respektive nodtyp (storlek, primär, mot internet, antal virtuella datorer och så vidare).  Mer information finns i [kluster kapacitetsplanering](service-fabric-cluster-capacity.md).

Mer information finns i [Service Fabric-nodtyper och VM-skalningsuppsättningar](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-instanser som är anslutna bakom en [Azure-belastningsutjämnare](/azure/load-balancer/load-balancer-overview), som är associerad med en [offentliga IP-adressen](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) och DNS-etikett.  När du etablerar ett kluster med  *&lt;clustername&gt;*, DNS-namnet  *&lt;clustername&gt;.&lt; plats&gt;. cloudapp.azure.com* är DNS-etikett som är associerade med belastningsutjämnaren framför skalningsuppsättningen.

Virtuella datorer i ett kluster har endast [privata IP-adresser](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Hantering av trafik och -tjänstens trafik dirigeras via offentliga belastningsutjämnare.  Trafik dirigeras till dessa datorer via NAT-regler (klienterna ansluter till specifika noder/instanser) eller belastningsutjämningsregler (trafik skickas till virtuella datorer (round robin)).  En belastningsutjämnare har en associerad offentlig IP-adress med ett DNS-namn i formatet:  *&lt;clustername&gt;.&lt; plats&gt;. cloudapp.azure.com*.  En offentlig IP-adress är en annan Azure-resurser i resursgruppen.  Om du definierar flera nodtyper i ett kluster skapas en belastningsutjämnare för typen/skalningsuppsättning varje nod. Eller så kan du konfigurera en enskild belastningsutjämnare för flera nodtyper.  Den primära nodtypen har DNS-etiketten  *&lt;clustername&gt;.&lt; plats&gt;. cloudapp.azure.com*, andra nodtyper har DNS-etiketten  *&lt;clustername&gt;-&lt;nodetype&gt;.&lt; plats&gt;. cloudapp.azure.com*.

### <a name="storage-accounts"></a>Lagringskonton
Varje nodtyp i klustret som stöds av en [Azure storage-konto](/azure/storage/common/storage-introduction) och hanterade diskar.

## <a name="cluster-security"></a>Klustersäkerhet
Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare från att ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret. 

### <a name="node-to-node-security"></a>Nod-till-nod-säkerhet
Nod-till-nod-säkerhet skyddar kommunikationen mellan virtuella datorer eller datorer i ett kluster. Det här scenariot security säkerställer att endast de datorer som har behörighet att ansluta till klustret kan delta i som är värd för program och tjänster i klustret. Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet.  Ett klustercertifikat krävs för att skydda trafik och ange klustret och server-autentisering.  Självsignerat signerade-certifikat kan användas för testkluster, men ett certifikat från en betrodd certifikatutfärdare som ska användas för att skydda produktionskluster.

Mer information finns [nod till nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Klient-till-nod-säkerhet autentiserar klienter och hjälper till att säker kommunikation mellan en klient och enskilda noder i klustret. Den här typen av security hjälper till att säkerställa att endast behöriga användare har åtkomst till klustret och de program som distribueras i klustret. Klienter som är unikt identifieras via antingen deras säkerhetsreferenser för X.509-certifikat. Valfritt antal valfria klientcertifikat kan användas för att autentisera klienter för administratör eller användare med klustret.

Förutom klientcertifikat, kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns [klient-till-nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Rollbaserad Access Control
Rollbaserad åtkomstkontroll (RBAC) kan du tilldela detaljerade åtkomstkontroller på Azure-resurser.  Du kan tilldela olika åtkomstregler till prenumerationer, resursgrupper och resurser.  RBAC-regler ärvs längs resurs-hierarkin, såvida inte åsidosättas på lägre nivå.  Du kan tilldela alla användare eller användargrupper på din AAD med RBAC regler så att utsedda användare och grupper kan ändra ditt kluster.  Mer information finns i [översikt över Azure RBAC](/azure/role-based-access-control/overview).

Service Fabric stöder också åtkomstkontroll som begränsar åtkomsten till vissa klusteråtgärder för olika grupper av användare. Detta hjälper att skydda klustret. Två typer av access control har stöd för klienter som ansluter till ett kluster: Administratörsrollen och användarrollen.  

Mer information finns i [Service Fabric Role-Based åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper 
Nätverkssäkerhetsgrupper (NSG) styr inkommande och utgående trafik i ett undernät, VM eller specifika nätverkskort.  Som standard när flera virtuella datorer placeras i samma virtuella nätverk kan de kommunicera med varandra via någon annan port.  Om du vill begränsa kommunikationen mellan datorerna som kan du definiera NSG: er för att segmentera nätverket och isolera virtuella datorer från varandra.  Om du har flera nodtyper i ett kluster kan tillämpa Nätverkssäkerhetsgrupper på undernät för att förhindra att datorer som tillhör olika nodtyper från att kommunicera med varandra.  

Mer information finns i avsnittet om [säkerhetsgrupper](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Skalning

Programbegäran ändras med tiden. Du kan behöva öka klusterresurser för att uppfylla ökade programtrafik för arbetsbelastning eller ditt nätverk eller minska klusterresurser när behovet sjunker. När du har skapat ett Service Fabric-kluster, kan du skala klustret horisontellt (ändra antalet noder) eller lodrätt (ändra resurser noder). Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret. När klustret skalas skalas programmen automatiskt samt.

Mer information finns i [skala Azure-kluster](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uppgraderar
Ett Azure Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Microsoft ansvarar för uppdatering av det underliggande Operativsystemet och utför Service Fabric runtime uppgraderingar i klustret. Du kan ange att ta emot automatiska runtime uppgraderingar, när Microsoft publicerar en ny version eller välja en stöds runtime-versionen som du vill. Du kan också uppdatera klusterkonfiguration som certifikat och programportar förutom runtime uppgraderingar.

Mer information finns i [uppgradera kluster](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Du kan skapa kluster på virtuella datorer som kör dessa operativsystem:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (förhandsversionsstöd för)

> [!NOTE]
> Om du vill distribuera Service Fabric i Windows Server 1709, Observera att (1) den inte är en lång sikt installationsbransch, så du kan behöva flytta versioner i framtiden, och (2) om du distribuerar behållare, fungerar behållare som bygger på Windows Server 2016 inte på Windows Server  1709, och vice versa (du måste återskapa dem för att distribuera dem).
>


## <a name="next-steps"></a>Nästa steg
Läs mer om [skydda](service-fabric-cluster-security.md), [skalning](service-fabric-cluster-scaling.md), och [uppgraderar](service-fabric-cluster-upgrade.md) Azure-kluster.

Lär dig mer om [Service Fabric-supportalternativ](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG