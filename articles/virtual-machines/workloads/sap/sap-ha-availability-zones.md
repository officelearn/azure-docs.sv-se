---
title: SAP-arbetsbelastningskonfigurationer med Azure-tillgänglighetszoner | Microsoft-dokument
description: Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver med hjälp av Azure-tillgänglighetszoner
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675613"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-arbetsbelastningskonfigurationer med Azure-tillgänglighetszoner
[Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) är en av de funktioner med hög tillgänglighet som Azure tillhandahåller. Med hjälp av tillgänglighetszoner förbättrar den övergripande tillgängligheten för SAP-arbetsbelastningar på Azure. Den här funktionen är redan tillgänglig i vissa [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). I framtiden kommer den att finnas tillgänglig i fler regioner.

Den här bilden visar den grundläggande arkitekturen för SAP hög tillgänglighet:

![Standardkonfiguration med hög tillgänglighet](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP-programlagret distribueras över en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). För hög tillgänglighet för SAP Central Services kan du distribuera två virtuella datorer i en separat tillgänglighetsuppsättning. Använd Windows Server Failover Clustering eller Pacemaker (Linux) som ett ramverk med hög tillgänglighet med automatisk redundans vid problem med infrastruktur eller programvara. Mer information om dessa distributioner finns i:

- [Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en klusterdelad disk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Kluster en SAP ASCS/SCS-instans i ett Windows redundanskluster med hjälp av filresurs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

En liknande arkitektur gäller för DBMS-lagret av SAP NetWeaver-, S/4HANA- eller Hybris-system. Du distribuerar DBMS-lagret i ett aktivt/passivt läge med en redundansklusterlösning för att skydda mot infrastruktur- eller programvarufel. Redundansklusterlösningen kan vara ett DBMS-specifikt redundansramverk, Windows Server Redundanskluster eller Pacemaker.

Om du vill distribuera samma arkitektur med hjälp av Azure-tillgänglighetszoner måste du göra vissa ändringar i arkitekturen som beskrivits tidigare. I den här artikeln beskrivs dessa ändringar.

## <a name="considerations-for-deploying-across-availability-zones"></a>Överväganden för distribution över tillgänglighetszoner


Tänk på följande när du använder tillgänglighetszoner:

- Det finns inga garantier för avstånden mellan olika tillgänglighetszoner inom en Azure-region.
- Tillgänglighetszoner är inte en idealisk DR-lösning. Naturkatastrofer kan orsaka omfattande skador i världens regioner, inklusive stora skador på kraftinfrastrukturer. Avstånden mellan olika zoner kanske inte är tillräckligt stora för att utgöra en riktig DR-lösning.
- Nätverksfördröjningen över tillgänglighetszoner är inte densamma i alla Azure-regioner. I vissa fall kan du distribuera och köra SAP-programlagret över olika zoner eftersom nätverksfördröjningen från en zon till den aktiva DBMS-virtuella datorn är acceptabel. Men i vissa Azure-regioner kanske svarstiden mellan den aktiva DBMS-datorn och SAP-programinstansen, när den distribueras i olika zoner, inte vara godtagbar för SAP-affärsprocesser. I dessa fall måste distributionsarkitekturen vara annorlunda, med en aktiv/aktiv arkitektur för programmet eller en aktiv/passiv arkitektur där nätverksfördröjningen mellan zoner är för hög.
- När du bestämmer var tillgänglighetszonerna ska användas baserar du ditt beslut på nätverksfördröjningen mellan zonerna. Nätverksfördröjning spelar en viktig roll inom två områden:
    - Svarstid mellan de två DBMS-instanser som behöver ha synkron replikering. Ju högre nätverksfördröjning, desto troligare är det att det påverkar skalbarheten för din arbetsbelastning.
    - Skillnaden i nätverksfördröjning mellan en virtuell dator som kör en SAP-dialoginstans i zonen med den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. När den här skillnaden ökar ökar också påverkan på drifttiden för affärsprocesser och batchjobb, beroende på om de körs i zonen med DBMS eller i en annan zon.

När du distribuerar virtuella Azure-datorer över tillgänglighetszoner och upprättar redundanslösningar inom samma Azure-region gäller vissa begränsningar:

- Du måste använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) när du distribuerar till Azure-tillgänglighetszoner. 
- Mappningen av zonuppräkningar till de fysiska zonerna har åtgärdats på Azure-prenumerationsbasis. Om du använder olika prenumerationer för att distribuera dina SAP-system måste du definiera de idealiska zonerna för varje prenumeration.
- Du kan inte distribuera Azure-tillgänglighetsuppsättningar i en Azure-tillgänglighetszon om du inte använder [Azure Proximity Placement Group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Hur du kan distribuera SAP DBMS-lagret och de centrala tjänsterna över zoner och samtidigt distribuera SAP-programlagret med hjälp av tillgänglighetsuppsättningar och ändå uppnå närhet till de virtuella datorerna dokumenteras i artikeln [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md). Om du inte använder Azure-närhetsplaceringsgrupper måste du välja det ena eller det andra som ett distributionsramverk för virtuella datorer.
- Du kan inte använda en [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) för att skapa redundansklusterlösningar baserade på Windows Server Redundanskluster eller Linux Pacemaker. I stället måste du använda [Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Den perfekta kombinationen av tillgänglighetszoner
Innan du bestämmer hur du ska använda tillgänglighetszoner måste du bestämma:

- Nätverksfördröjningen bland de tre zonerna i en Azure-region. På så sätt kan du välja de zoner som har minst nätverksfördröjning i nätverkstrafik mellan zoner.
- Skillnaden mellan svarstid för virtuell dator till virtuell dator inom en av zonerna, som du väljer, och nätverksfördröjningen mellan två zoner som du väljer.
- En bestämning av om de VM-typer som du behöver distribuera är tillgängliga i de två zoner som du har valt. Med vissa virtuella datorer, särskilt virtuella datorer i M-serien, kan du stöta på situationer där vissa SKU:er är tillgängliga i endast två av de tre zonerna.

## <a name="network-latency-between-and-within-zones"></a>Nätverksfördröjning mellan och inom zoner
För att bestämma svarstiden mellan de olika zonerna måste du:

- Distribuera den VM SKU som du vill använda för DBMS-instansen i alla tre zonerna. Kontrollera att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat när du gör den här mätningen.
- När du hittar de två zonerna med minst nätverksfördröjning, distribuera ytterligare tre virtuella datorer i VM SKU som du vill använda som programlager VM över de tre tillgänglighetszonerna. Mät nätverksfördröjningen mot de två DBMS-virtuella datorerna i de två DBMS-zoner som du har valt. 
- Använd **niping** som mätverktyg. Det här verktyget, från SAP, beskrivs i SAP-stödanteckningar [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på de kommandon som dokumenterats för svarstidsmätningar. Eftersom **ping** inte fungerar via azure accelerated networking-kodsökvägarna rekommenderar vi inte att du använder den.

Du behöver inte utföra dessa tester manuellt. Du kan hitta en [PowerShell-procedur Availability Zone Lateency Test](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) som automatiserar svarstidstesterna som beskrivs. 

Baserat på dina mått och tillgängligheten för vm-skuorna i tillgänglighetszonerna måste du fatta några beslut:

- Definiera de idealiska zonerna för DBMS-lagret.
- Bestäm om du vill distribuera ditt aktiva SAP-programlager över en, två eller alla tre zonerna, baserat på skillnader i nätverksfördröjning i zon jämfört med mellan zoner.
- Bestäm om du vill distribuera en aktiv/passiv konfiguration eller en aktiv/aktiv konfiguration, från ett programperspektiv. (Dessa konfigurationer förklaras senare i den här artikeln.)

När du fattar dessa beslut, ta också hänsyn till SAP: s rekommendationer nätverksfördröjning, som dokumenteras i SAP not [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De mått och beslut du fattar gäller för den Azure-prenumeration du använde när du tog mätningarna. Om du använder en annan Azure-prenumeration måste du upprepa måtten. Mappningen av uppräknade zoner kan vara annorlunda för en annan Azure-prenumeration.


> [!IMPORTANT]
> Det förväntas att de mätningar som beskrivs tidigare ger olika resultat i alla Azure-regioner som stöder [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Även om dina krav på nätverksfördröjning är desamma kan du behöva anta olika distributionsstrategier i olika Azure-regioner eftersom nätverksfördröjningen mellan zoner kan vara olika. I vissa Azure-regioner kan nätverksfördröjningen mellan de tre olika zonerna vara väldigt olika. I andra regioner kan nätverksfördröjningen mellan de tre olika zonerna vara mer enhetlig. Påståendet att det alltid finns en nätverksfördröjning mellan 1 och 2 millisekunder är inte korrekt. Nätverksfördröjningen i tillgänglighetszoner i Azure-regioner kan inte generaliseras.

## <a name="activeactive-deployment"></a>Aktiv/aktiv distribution
Den här distributionsarkitekturen kallas aktiv/aktiv eftersom du distribuerar dina aktiva SAP-programservrar över två eller tre zoner. SAP Central Services-instansen som använder enqueue-replikering kommer att distribueras mellan två zoner. Detsamma gäller för DBMS-lagret, som kommer att distribueras över samma zoner som SAP Central Service.

När du överväger den här konfigurationen måste du hitta de två tillgänglighetszonerna i din region som erbjuder nätverksfördröjning mellan zoner som är godtagbar för din arbetsbelastning och din synkrona DBMS-replikering. Du vill också vara säker på att deltat mellan nätverksfördröjningen inom de zoner du har valt och nätverksfördröjningen mellan zonen inte är för stor. Detta beror på att du inte vill ha stora variationer, beroende på om ett jobb körs i zonen med DBMS-servern eller mellan zoner, under drifttiderna för dina affärsprocesser eller batch-jobb. Vissa variationer är acceptabla, men inte faktorer av skillnad.

Ett förenklat schema för en aktiv/aktiv distribution över två zoner kan se ut så här:

![Aktiv/aktiv zondistribution](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Använd inte [Azure Proximity Placement Group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)behandlar du Azure-tillgänglighetszonerna som fel- och uppdateringsdomäner för alla virtuella datorer eftersom tillgänglighetsuppsättningar inte kan distribueras i Azure-tillgänglighetszoner.
- Om du vill kombinera zondistributioner för DBMS-lagret och centrala tjänster, men vill använda Azure-tillgänglighetsuppsättningar för programlagret, måste du använda Azure-närhetsgrupper enligt beskrivningen i artikeln [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- För belastningsutjämnare för redundanskluster för SAP Central Services och DBMS-lagret måste du använda [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte mellan zoner.
- Det virtuella Azure-nätverk som du distribuerade för att vara värd för SAP-systemet, tillsammans med dess undernät, sträcker sig över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterat diskar stöds inte för zondistributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av lagringsreplikering över zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmntkatalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
  - För Windows, en klusterlösning som använder SIOS DataKeeper, som dokumenteras i [Cluster en SAP ASCS/SCS-instans på ett Windows redundanskluster med hjälp av en klusterdelad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - För SUSE Linux, en NFS-resurs som är byggd som dokumenterad i [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    För närvarande stöds inte lösningen som använder Microsoft Scale-Out File Server, som dokumenteras i [Prepare Azure-infrastruktur för SAP-hög tillgänglighet med hjälp av ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), över zoner.
- Den tredje zonen används för att vara värd för SBD-enheten om du skapar ett [SUSE Linux Pacemaker-kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.
- Om du vill uppnå körtidskonsekvens för kritiska affärsprocesser kan du försöka dirigera vissa batchjobb och användare till programinstanser som är i zonen med den aktiva DBMS-instansen med hjälp av SAP-batchservergrupper, SAP-inloggningsgrupper eller RFC-grupper. När det gäller en zonvisunda redundans måste du dock manuellt flytta dessa grupper till instanser som körs på virtuella datorer som är i zonen med den aktiva DB-virtuella datorn.  
- Du kanske vill distribuera vilande dialoginstanser i var och en av zonerna. Detta för att möjliggöra en omedelbar återgång till den tidigare resurskapaciteten om en zon som används av en del av programinstanserna inte är i drift.

> [!IMPORTANT]
> I det här aktiva/aktiva scenariot meddelas ytterligare avgifter för bandbredd från och med 2020-01-04. Kontrollera information om prissättning av dokument [bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). Dataöverföringen mellan SAP-programskiktet och SAP DBMS-lagret är ganska intensiv. Därför kan det aktiva/aktiva scenariot bidra till kostnaderna en hel del. Håll koll på den här artikeln för att få de exakta kostnaderna 


## <a name="activepassive-deployment"></a>Aktiv/passiv distribution
Om du inte hittar ett godtagbart delta mellan nätverksfördröjningen inom en zon och svarstiden för nätverkstrafik mellan zoner kan du distribuera en arkitektur som har ett aktivt/passivt tecken från SAP-programlagersynpunkt. Du definierar en *aktiv* zon, som är den zon där du distribuerar hela programlagret och där du försöker köra både den aktiva DBMS- och SAP Central Services-instansen. Med en sådan konfiguration måste du se till att du inte har extrema körtidsvariationer, beroende på om ett jobb körs i zonen med den aktiva DBMS-instansen eller inte, i affärstransaktioner och batch-jobb.

Arkitekturens grundläggande layout ser ut så här:

![Aktiv/passiv zondistribution](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Tillgänglighetsuppsättningar kan inte distribueras i Azure-tillgänglighetszoner. För att kompensera för det kan du använda Azure proximity placement grupper som dokumenteras i artikeln [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen noga och försöka behålla de aktiva DBMS- och SAP Central Services-instanserna i samma zon som ditt distribuerade programlager. Vid en redundansväxling av SAP Central Service eller DBMS-instansen vill du vara säker på att du manuellt kan växla tillbaka till zonen med SAP-programlagret distribuerat så snabbt som möjligt.
- För belastningsutjämnare för redundanskluster för SAP Central Services och DBMS-lagret måste du använda [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte mellan zoner.
- Det virtuella Azure-nätverk som du distribuerade för att vara värd för SAP-systemet, tillsammans med dess undernät, sträcker sig över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterat diskar stöds inte för zondistributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av lagringsreplikering över zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmntkatalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
    - För Windows, en klusterlösning som använder SIOS DataKeeper, som dokumenteras i [Cluster en SAP ASCS/SCS-instans på ett Windows redundanskluster med hjälp av en klusterdelad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - För SUSE Linux, en NFS-resurs som är byggd som dokumenterad i [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  För närvarande stöds inte lösningen som använder Microsoft Scale-Out File Server, som dokumenteras i [Prepare Azure-infrastruktur för SAP-hög tillgänglighet med hjälp av ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), över zoner.
- Den tredje zonen används för att vara värd för SBD-enheten om du skapar ett [SUSE Linux Pacemaker-kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.
- Du bör distribuera vilande virtuella datorer i den passiva zonen (från en DBMS-synvinkel) så att du kan starta programresurser i händelse av ett zonfel.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kan för närvarande inte replikera aktiva virtuella datorer till vilande virtuella datorer mellan zoner. 
- Du bör investera i automatisering som gör att du, i händelse av ett zonfel, automatiskt startar SAP-programlagret i den andra zonen.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinerad konfiguration med hög tillgänglighet och haveriberedskap
Microsoft delar inte någon information om geografiska avstånd mellan de anläggningar som är värd för olika Azure-tillgänglighetszoner i en Azure-region. Ändå använder vissa kunder zoner för en kombinerad HA- och DR-konfiguration som lovar ett återställningspunktsmål (RPO) på noll. Det innebär att du inte bör förlora några genomförda databastransaktioner även vid haveriberedskap. 

> [!NOTE]
> Vi rekommenderar att du använder en sådan konfiguration endast under vissa omständigheter. Du kan till exempel använda den när data inte kan lämna Azure-regionen av säkerhets- eller efterlevnadsskäl. 

Här är ett exempel på hur en sådan konfiguration kan se ut:

![Kombinerad DR med hög tillgänglighet i zoner](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande överväganden gäller för den här konfigurationen:

- Du antar antingen att det finns ett stort avstånd mellan de anläggningar som är värdar för en tillgänglighetszon eller att du tvingas stanna inom en viss Azure-region. Tillgänglighetsuppsättningar kan inte distribueras i Azure-tillgänglighetszoner. För att kompensera för det kan du använda Azure proximity placement grupper som dokumenteras i artikeln [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen noga och försöka behålla de aktiva DBMS- och SAP Central Services-instanserna i samma zon som ditt distribuerade programlager. Vid en redundansväxling av SAP Central Service eller DBMS-instansen vill du vara säker på att du manuellt kan växla tillbaka till zonen med SAP-programlagret distribuerat så snabbt som möjligt.
- Du bör ha förinstallerade produktionsprograminstanser i de virtuella datorer som kör de aktiva QA-programinstanserna.
- Om ett zonfel skulle vara fallet stänger du av QA-programinstanserna och startar produktionsinstanserna i stället. Observera att du måste använda virtuella namn för programinstanserna för att få det här att fungera.
- För belastningsutjämnare för redundanskluster för SAP Central Services och DBMS-lagret måste du använda [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte mellan zoner.
- Det virtuella Azure-nätverk som du distribuerade för att vara värd för SAP-systemet, tillsammans med dess undernät, sträcker sig över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterat diskar stöds inte för zondistributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av lagringsreplikering över zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmntkatalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
    - För Windows, en klusterlösning som använder SIOS DataKeeper, som dokumenteras i [Cluster en SAP ASCS/SCS-instans på ett Windows redundanskluster med hjälp av en klusterdelad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - För SUSE Linux, en NFS-resurs som är byggd som dokumenterad i [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  För närvarande stöds inte lösningen som använder Microsoft Scale-Out File Server, som dokumenteras i [Prepare Azure-infrastruktur för SAP-hög tillgänglighet med hjälp av ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), över zoner.
- Den tredje zonen används för att vara värd för SBD-enheten om du skapar ett [SUSE Linux Pacemaker-kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.





## <a name="next-steps"></a>Nästa steg
Här är några nästa steg för distribution över Azure-tillgänglighetszoner:

- [Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Förbereda Azure-infrastruktur för SAP-hög tillgänglighet med hjälp av ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






