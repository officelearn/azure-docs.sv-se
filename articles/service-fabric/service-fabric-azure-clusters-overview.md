---
title: Skapa kluster på Windows Server och Linux
description: Service Fabric-kluster körs på Windows Server och Linux, vilket innebär att du kan distribuera och vara värd för Service Fabric-program var du än kan köra Windows Server eller Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614680"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Översikt över Service Fabric-kluster på Azure
Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret balanserar Service Fabric om tjänstpartitionsrepliker och instanser över det ökade antalet noder. Övergripande programprestanda förbättras och konkurrens om åtkomst till minne minskar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsrepliker och instanser över det minskade antalet noder för att bättre använda maskinvaran på varje nod.

En nodtyp definierar storlek, tal och egenskaper för en uppsättning noder (virtuella datorer) i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen. 

## <a name="cluster-components-and-resources"></a>Klusterkomponenter och resurser
Ett Service Fabric-kluster på Azure är en Azure-resurs som använder och interagerar med andra Azure-resurser:
* Virtuella datorer och virtuella nätverkskort
* VM-skalningsuppsättningar
* virtuella nätverk
* lastbalanserare
* lagringskonton
* offentliga IP-adresser

![Kluster för serviceinfrastruktur][Image]

### <a name="virtual-machine"></a>Virtuell dator
En [virtuell dator](/azure/virtual-machines/) som ingår i ett kluster kallas en nod men tekniskt sett är en klusternod en Service Fabric-körningsprocess. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel [placeringsegenskaper](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Varje dator eller virtuell dator har en tjänst för automatisk start, *FabricHost.exe*, som börjar köras vid start och sedan startar två körbara filer, *Fabric.exe* och *FabricGateway.exe*, som utgör noden. En produktionsdistribution är en nod per fysisk eller virtuell dator. För testscenarier kan du vara värd för flera noder på en enda dator eller virtuell dator genom att köra flera instanser av *Fabric.exe* och *FabricGateway.exe*.

Varje virtuell dator är associerad med ett virtuellt nätverkskort (NIC) och varje nätverkskort tilldelas en privat IP-adress.  En virtuell dator tilldelas ett virtuellt nätverk och en lokal balanserare via nätverkskortet.

Alla virtuella datorer i ett kluster placeras i ett virtuellt nätverk.  Alla noder i samma nodtyp/skalningsuppsättning placeras i samma undernät i det virtuella nätverket.  Dessa noder har bara privata IP-adresser och är inte direkt adresserbara utanför det virtuella nätverket.  Klienter kan komma åt tjänster på noderna via Azure load balancer.

### <a name="scale-setnode-type"></a>Typ av skaluppsättning/nod
När du skapar ett kluster definierar du en eller flera nodtyper.  Noderna, eller virtuella datorer, i en nodtyp har samma storlek och egenskaper som antal processorer, minne, antal diskar och disk-I/O.  En nodtyp kan till exempel vara för små start-och frontend-datorer med portar som är öppna för internet medan en annan nodtyp kan vara för stora, backend-virtuella datorer som bearbetar data. I Azure-kluster mappas varje nodtyp till en [skalningsuppsättning för virtuella datorer](/azure/virtual-machine-scale-sets/).

Du kan använda skalningsuppsättningar för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skalningsuppsättning. Service Fabric-körningen är bootstrapped på varje virtuell dator i skalningsuppsättningen med Azure VM-tillägg. Du kan självständigt skala varje nodtyp uppåt eller nedåt, ändra OS SKU som körs på varje klusternod, ha olika uppsättningar portar öppna och använda olika kapacitetsmått. En skalningsuppsättning har fem [uppgraderingsdomäner](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) och fem [feldomäner](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) och kan ha upp till 100 virtuella datorer.  Du skapar kluster med mer än 100 noder genom att skapa flera skalningsuppsättningar/nodtyper.

> [!IMPORTANT]
> Att välja antalet nodtyper för klustret och egenskaperna för varje nodtyp (storlek, primär, internetvänd, antal virtuella datorer osv.) är en viktig uppgift.  Mer information finns i [överväganden om klusterkapacitetsplanering](service-fabric-cluster-capacity.md).

Mer information finns i [Nodtyper för Service Fabric och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-instanser är kopplade bakom en [Azure belastningsutjämnare](/azure/load-balancer/load-balancer-overview), som är associerad med en [offentlig IP-adress](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) och DNS-etikett.  När du etablerar ett kluster med * &lt;&gt;klusternamn*, DNS-namn, * &lt;&gt;clustername .&lt; plats&gt;.cloudapp.azure.com* är den DNS-etikett som är associerad med belastningsutjämnaren framför skalningsuppsättningen.

Virtuella datorer i ett kluster har bara [privata IP-adresser](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Trafik- och trafikstyrning dirigeras genom den offentliga belastningsutjämnaren.  Nätverkstrafik dirigeras till dessa datorer via NAT-regler (klienter ansluter till specifika noder/instanser) eller belastningsutjämningsregler (trafiken går till virtuella datorer round robin).  En belastningsutjämnare har en associerad offentlig IP med ett DNS-namn i formatet: * &lt;clustername&gt;.&lt; plats&gt;.cloudapp.azure.com*.  En offentlig IP är en annan Azure-resurs i resursgruppen.  Om du definierar flera nodtyper i ett kluster skapas en belastningsutjämnare för varje nodtyp/skalningsuppsättning. Du kan också ställa in en enda belastningsutjämnare för flera nodtyper.  Den primära nodtypen har * &lt;DNS-etikettklusternamnet&gt;.&lt; plats&gt;.cloudapp.azure.com*har andra nodtyper * &lt;nodtypen&gt;-&lt;&gt;NODEtyp&lt; för DNS-etikettkluster. plats&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Lagringskonton
Varje klusternodtyp stöds av ett [Azure-lagringskonto](/azure/storage/common/storage-introduction) och hanterade diskar.

## <a name="cluster-security"></a>Klustersäkerhet
Ett Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare ansluter till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret. 

### <a name="node-to-node-security"></a>Nod-till-nod säkerhet
Nod-till-nod-säkerhet skyddar kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhetsscenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdprogram och tjänster i klustret. Service Fabric använder X.509-certifikat för att skydda ett kluster och tillhandahålla programsäkerhetsfunktioner.  Ett klustercertifikat krävs för att skydda klustertrafik och tillhandahålla kluster- och serverautentisering.  Självsignerade certifikat kan användas för testkluster, men ett certifikat från en betrodd certifikatutfärdare bör användas för att skydda produktionskluster.

Mer information finns [i Node-till-nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Säkerhet för klient-till-nod
Klient-till-nod-säkerhet autentiserar klienter och hjälper till att skydda kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienter identifieras unikt genom antingen sina X.509-certifikatsäkerhetsautentiseringsuppgifter. Valfritt antal valfria klientcertifikat kan användas för att autentisera administratörs- eller användarklienter med klustret.

Förutom klientcertifikat kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns i [säkerhet för klient-till-nod](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Rollbaserad Access Control
Med RBAC (Role-Based Access Control) kan du tilldela detaljerade åtkomstkontroller för Azure-resurser.  Du kan tilldela olika åtkomstregler till prenumerationer, resursgrupper och resurser.  RBAC-regler ärvs längs resurshierarkin om de inte åsidosätts på en lägre nivå.  Du kan tilldela alla användar- eller användargrupper på din AAD till RBAC-regler så att utsedda användare och grupper kan ändra klustret.  Mer information finns i [Azure RBAC översikt](/azure/role-based-access-control/overview).

Service Fabric stöder också åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper. Detta bidrar till att göra klustret säkrare. Två åtkomstkontrolltyper stöds för klienter som ansluter till ett kluster: Administratörsroll och Användarroll.  

Mer information finns i [RBAC (Service Fabric Role-Based Access Control).](service-fabric-cluster-security.md#role-based-access-control-rbac)

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper 
NSG-grupper (Network Security Groups) styr inkommande och utgående trafik i ett undernät, virtuell dator eller ett visst nätverkskort.  Som standard, när flera virtuella datorer läggs på samma virtuella nätverk kan de kommunicera med varandra via vilken port som helst.  Om du vill begränsa kommunikationen mellan datorerna kan du definiera NSG:er för att segmentera nätverket eller isolera virtuella datorer från varandra.  Om du har flera nodtyper i ett kluster kan du använda NSG:er på undernät för att förhindra att datorer som tillhör olika nodtyper kommunicerar med varandra.  

Mer information finns i [om säkerhetsgrupper](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Skalning

Ansökan kräver förändring med tiden. Du kan behöva öka klusterresurserna för att möta ökad programarbetsbelastning eller nätverkstrafik eller minska klusterresurser när efterfrågan sjunker. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra nodernas resurser). Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret. När klustret skalas skalas även dina program automatiskt.

Mer information finns i [Skala Azure-kluster](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uppgradering
Ett Azure Service Fabric-kluster är en resurs som du äger, men hanteras delvis av Microsoft. Microsoft ansvarar för att korrigera det underliggande operativsystemet och utföra service fabric-körningsuppgraderingar i klustret. Du kan ange att klustret ska ta emot automatiska körningsuppgraderingar när Microsoft släpper en ny version eller välja en runtime-version som stöds. Förutom uppgraderingar av körning kan du även uppdatera klusterkonfiguration som certifikat eller programportar.

Mer information finns i [Uppgradera kluster](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Du kan skapa kluster på virtuella datorer som kör dessa operativsystem:

| Operativsystem | Tidigaste Service Fabric-versionen som stöds |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |

Mer information finns [i klusterversioner som stöds i Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Om du bestämmer dig för att distribuera Service Fabric på Windows Server 1709, observera att (1) det inte är en långsiktig servicegren, så du kan behöva flytta versioner i framtiden, och (2) om du distribuerar behållare, behållare byggda på Windows Server 2016 fungerar inte på Windows Server 1709, och vice versa (du måste bygga om dem för att distribuera dem).
>


## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du skyddar,](service-fabric-cluster-security.md) [skalning](service-fabric-cluster-scaling.md)och [uppgradering av](service-fabric-cluster-upgrade.md) Azure-kluster.

Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
