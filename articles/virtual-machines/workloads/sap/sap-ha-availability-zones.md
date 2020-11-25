---
title: SAP-konfigurationer för arbets belastning med Azure-tillgänglighetszoner | Microsoft Docs
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
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad9820cc3227c788582a9c2a452abe1886a4a2d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019335"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-arbetsbelastningskonfigurationer med Azure-tillgänglighetszoner
[Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md) är en av de funktioner för hög tillgänglighet som Azure tillhandahåller. Med Tillgänglighetszoner förbättras övergripande tillgänglighet för SAP-arbetsbelastningar på Azure. Den här funktionen är redan tillgänglig i vissa [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). I framtiden kommer det att vara tillgängligt i fler regioner.

Den här bilden visar den grundläggande arkitekturen i SAP hög tillgänglighet:

![Standard konfiguration för hög tillgänglighet](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP-Programskiktet distribueras över en Azures [tillgänglighets uppsättning](../../manage-availability.md). För hög tillgänglighet för SAP Central Services kan du distribuera två virtuella datorer i en separat tillgänglighets uppsättning. Använd Windows Server-redundanskluster eller pacemaker (Linux) som ett ramverk med hög tillgänglighet med automatisk redundans vid en infrastruktur eller program varu problem. Mer information om de här distributionerna finns i:

- [Klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en klusterdelad disk](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av fil resurs](./sap-high-availability-guide-wsfc-file-share.md)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](./high-availability-guide-suse.md)
- [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](./high-availability-guide-rhel.md)

En liknande arkitektur gäller för DBMS-skiktet i SAP NetWeaver, S/4HANA eller Hybris system. Du distribuerar DBMS-skiktet i ett aktivt/passivt läge med en lösning för redundanskluster som skyddar mot infrastruktur-eller program varu fel. Lösningen för kluster för växling vid fel kan vara ett DBMS-speciellt failover-ramverk, redundanskluster för Windows Server eller pacemaker.

Om du vill distribuera samma arkitektur med hjälp av Azure-tillgänglighetszoner måste du göra några ändringar i arkitekturen som beskrivs tidigare. I den här artikeln beskrivs dessa ändringar.

## <a name="considerations-for-deploying-across-availability-zones"></a>Att tänka på när du distribuerar i Tillgänglighetszoner


Tänk på följande när du använder Tillgänglighetszoner:

- Det finns inga garantier avseende avstånden mellan olika Tillgänglighetszoner inom en Azure-region.
- Tillgänglighetszoner är inte en idealisk DR-lösning. Natur katastrofer kan orsaka omfattande skador i världs regioner, inklusive tung skada på energi infrastrukturer. Avstånden mellan olika zoner kanske inte är tillräckligt stora för att utgöra en korrekt DR-lösning.
- Nätverks fördröjningen i Tillgänglighetszoner är inte samma i alla Azure-regioner. I vissa fall kan du distribuera och köra SAP-program skiktet över olika zoner eftersom nätverks fördröjningen från en zon till den aktiva DBMS-datorn är acceptabel. Men i vissa Azure-regioner kanske svars tiden mellan den aktiva DBMS-datorn och SAP-programinstansen, när de distribueras i olika zoner, inte är acceptabla för SAP-affärsprocesser. I dessa fall måste distributions arkitekturen vara olika, med en aktiv/aktiv arkitektur för programmet eller en aktiv/passiv arkitektur där nätverks fördröjningen för flera zoner är för hög.
- När du bestämmer var du ska använda Tillgänglighetszoner baserar du ditt beslut om nätverks fördröjningen mellan zonerna. Nätverks fördröjningen spelar en viktig roll på två områden:
    - Fördröjning mellan de två DBMS-instanser som behöver ha synkron replikering. Ju högre nätverks fördröjning, desto sannolikare kommer det att påverka arbets Belastningens skalbarhet.
    - Skillnaden i nätverks fördröjningen mellan en virtuell dator som kör en SAP-dialogruta i zonen med den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. När den här skillnaden ökar, ökar även den tid som affärs processer och batch-jobb körs, beroende på om de körs i zon med DBMS eller i en annan zon.

När du distribuerar virtuella Azure-datorer i Tillgänglighetszoner och upprättar lösningar för redundans inom samma Azure-region gäller vissa begränsningar:

- Du måste använda [Azure-Managed disks](https://azure.microsoft.com/services/managed-disks/) när du distribuerar till Azure-tillgänglighetszoner. 
- Mappningen av zon uppräkningar till fysiska zoner är fast för en Azure-prenumeration. Om du använder olika prenumerationer för att distribuera dina SAP-system måste du definiera de idealiska zonerna för varje prenumeration.
- Du kan inte distribuera Azures tillgänglighets uppsättningar i en tillgänglighets zon för Azure om du inte använder [Azure närhets placerings grupp](../../linux/co-location.md). Hur du kan distribuera SAP-DBMS-skiktet och de centrala tjänsterna över zoner och samtidigt distribuera SAP-programlagret med tillgänglighets uppsättningar och fortfarande uppnå nära de virtuella datorerna finns dokumenterade i artikelernas [närhets grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md). Om du inte använder Azure närhets placerings grupper måste du välja en eller en annan som distributions ramverk för virtuella datorer.
- Du kan inte använda ett [Azure Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) för att skapa lösningar för redundanskluster som baseras på Windows Server-redundanskluster eller Linux-pacemaker. I stället måste du använda [Azure standard load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Den perfekta Tillgänglighetszoner kombinationen
Innan du bestämmer hur du ska använda Tillgänglighetszoner måste du fastställa:

- Nätverks fördröjningen bland de tre zonerna i en Azure-region. Detta gör att du kan välja zoner med minsta nätverks svars tid i nätverks trafiken mellan zoner.
- Skillnaden mellan VM-till-VM-svars tid inom en av zonerna, om du väljer och nätverks fördröjningen i två zoner som du väljer.
- En bestämning av huruvida de VM-typer som du behöver distribuera är tillgängliga i de två zoner som du har valt. Med vissa virtuella datorer, särskilt virtuella datorer i M-serien, kan det uppstå situationer där vissa SKU: er endast är tillgängliga i två av de tre zonerna.

## <a name="network-latency-between-and-within-zones"></a>Nätverks fördröjning mellan och inom zoner
För att fastställa svars tiden mellan olika zoner måste du:

- Distribuera VM-SKU: n som du vill använda för din DBMS-instans i alla tre zoner. Kontrol lera att [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat när du utför den här mätningen.
- När du hittar de två zonerna med minsta nätverks svars tid distribuerar du ytterligare tre virtuella datorer av VM-SKU: n som du vill använda som den virtuella program skiktet i de tre Tillgänglighetszoner. Mät nätverks fördröjningen mot de två virtuella DBMS-datorerna i de två DBMS-zonerna som du har valt. 
- Använd **niping** som Mät verktyg. Det här verktyget, från SAP, beskrivs i SAP support Notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på de kommandon som dokumenteras för svars tids mätningar. Eftersom **ping** inte fungerar via Sök vägarna för Azure accelererade nätverks koder rekommenderar vi inte att du använder den.

Du behöver inte utföra de här testerna manuellt. Du hittar ett svar på en [fördröjning för tillgänglighets zonen](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) i PowerShell-proceduren som automatiserar svars tiderna som beskrivs. 

Utifrån dina mått och tillgängligheten för dina VM-SKU: er i Tillgänglighetszoner måste du fatta några beslut:

- Definiera de idealiska zonerna för DBMS-skiktet.
- Bestäm om du vill distribuera ditt aktiva SAP-programlager över en, två eller alla tre zonerna, baserat på skillnader i nätverks fördröjningen i zonen eller mellan zoner.
- Bestäm om du vill distribuera en aktiv/passiv konfiguration eller en aktiv/aktiv-konfiguration, från en program plats i vyn. (Dessa konfigurationer beskrivs senare i den här artikeln.)

Vid besluts fattandet bör du också ta del av rekommendationerna för nätverks fördröjning i SAP, enligt beskrivningen i SAP Obs [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De mätningar och beslut du gör är giltiga för den Azure-prenumeration du använde när du tog måtten. Om du använder en annan Azure-prenumeration måste du upprepa måtten. Mappningen av uppräknade zoner kan vara olika för en annan Azure-prenumeration.


> [!IMPORTANT]
> Det förväntas att de mått som beskrivs ovan ger olika resultat i varje Azure-region som har stöd för [Tillgänglighetszoner](../../../availability-zones/az-overview.md). Även om kraven på nätverks fördröjning är desamma, kan du behöva använda olika distributions strategier i olika Azure-regioner eftersom nätverks fördröjningen mellan zoner kan vara olika. I vissa Azure-regioner kan nätverks fördröjningen mellan de tre olika zonerna vara mycket annorlunda. I andra regioner kan nätverks fördröjningen mellan de tre olika zonerna vara mer enhetlig. Anspråket att det alltid finns en nätverks fördröjning mellan 1 och 2 millisekunder är inte korrekt. Det går inte att generalisera nätverks fördröjningen i Tillgänglighetszoner i Azure-regioner.

## <a name="activeactive-deployment"></a>Aktiv/aktiv-distribution
Den här distributions arkitekturen kallas aktiv/aktiv eftersom du distribuerar dina aktiva SAP-programservrar över två eller tre zoner. SAP Central Services-instansen som använder en transaktionskö kommer att distribueras mellan två zoner. Detsamma gäller för DBMS-skiktet, som kommer att distribueras över samma zoner som den centrala SAP-tjänsten.

När du överväger den här konfigurationen måste du hitta de två Tillgänglighetszoner i din region som erbjuder nätverks svars tid i flera zoner som är acceptabla för arbets belastningen och din synkrona DBMS-replikering. Du vill också vara säker på att delta mellan nätverks fördröjning i de zoner som du har valt och nätverks fördröjningen för flera zoner inte är för stor. Detta beror på att du inte vill ha stora variationer, beroende på om ett jobb körs i zon med DBMS-servern eller mellan zoner, under körnings tiden för dina affärs processer eller batch-jobb. Vissa variationer är acceptabla, men inte faktorer av skillnad.

Ett förenklat schema för en aktiv/aktiv-distribution över två zoner kan se ut så här:

![Distribution av aktiva/aktiva zoner](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Genom att inte använda [Azure närhets placerings grupp](../../linux/co-location.md)hanterar du Azure-tillgänglighetszoner som fel-och uppdaterings domäner för alla virtuella datorer eftersom det inte går att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner.
- Om du vill kombinera zonindelade-distributioner för DBMS-skiktet och centrala tjänster, men vill använda Azures tillgänglighets uppsättningar för program lagret, måste du använda Azure närhets grupper enligt beskrivningen i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage och [Ultra SSD Storage](../../disks-types.md#ultra-disk) stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
  - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) eller ytterligare program instanser.
- För att uppnå körnings tids konsekvens för kritiska affärs processer kan du försöka dirigera vissa batch-jobb och användare till program instanser som finns i zoner med den aktiva DBMS-instansen med hjälp av SAP batch Server-grupper, SAP-inloggnings grupper eller RFC-grupper. Men om du vill ha en zonindelade redundans måste du manuellt flytta grupperna till instanser som körs på virtuella datorer som finns i zoner med den aktiva DB-datorn.  
- Du kanske vill distribuera inaktiva dialog instanser i varje zon. Detta är att aktivera en omedelbar återgång till den tidigare resurs kapaciteten om en zon som används av en del av program instanserna inte är i tjänst.

> [!IMPORTANT]
> I det här aktiva/aktiva scenariot annonseras ytterligare avgifter för bandbredd av Microsoft från 04/01/2020 på. Se [pris informationen för dokument bandbredden](https://azure.microsoft.com/pricing/details/bandwidth/). Data överföringen mellan SAP-program skiktet och SAP-DBMS-skiktet är väldigt intensiv. Därför kan det aktiva/aktiva scenariot bidra till kostnader som är ganska lite. Fortsätt att kontrol lera den här artikeln för att få exakta kostnader 


## <a name="activepassive-deployment"></a>Aktiv/passiv distribution
Om du inte hittar en acceptabel förändring mellan nätverks fördröjningen i en zon och fördröjningen för nätverks trafik över flera zoner kan du distribuera en arkitektur som har ett aktivt/passivt tecken från SAP-programmets lager plats. Du definierar en *aktiv* zon, som är den zon där du distribuerar det fullständiga program lagret och där du försöker köra både det aktiva DBMS-och SAP Central Services-instansen. Med en sådan konfiguration måste du se till att du inte har extrema körnings tids variationer, beroende på om ett jobb körs i zon med den aktiva DBMS-instansen eller inte, i affärs transaktioner och batch-jobb.

Grundläggande layout för arkitekturen ser ut så här:

![Distribution av aktiva/passiva zoner](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Det går inte att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner. För att kompensera för detta kan du använda Azure närhets placerings grupper som dokumenteras i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen nära och försöka behålla instanserna aktiva DBMS och SAP Central Services i samma zon som det distribuerade program lagret. Om det är en redundansväxling av SAP central service eller DBMS-instansen, vill du se till att du manuellt växlar tillbaka till zonen med SAP-program lagret distribuerat så snabbt som möjligt.
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage och [Ultra SSD Storage](../../disks-types.md#ultra-disk) stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
    - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) eller ytterligare program instanser.
- Du bör distribuera vilande virtuella datorer i den passiva zonen (från en DBMS-vy) så att du kan starta program resurser i händelse av ett zon haveri.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kan för närvarande inte replikera aktiva virtuella datorer till inaktiva virtuella datorer mellan zoner. 
- Du bör investera i Automation som gör det möjligt att automatiskt starta lagret SAP-program i den andra zonen.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Konfiguration av kombinerad hög tillgänglighet och haveri beredskap
Microsoft delar inte information om geografiska avstånd mellan de anläggningar som är värdar för olika Azure-tillgänglighetszoner i en Azure-region. Vissa kunder använder sig av zoner för en kombinerad HA-och DR-konfiguration som lovar ett återställnings punkt mål (återställnings punkt mål) på noll. Det innebär att du inte förlorar några allokerade databas transaktioner, även om haveri beredskap återskapas. 

> [!NOTE]
> Vi rekommenderar att du använder en konfiguration som bara i vissa fall. Du kan till exempel använda den när data inte kan lämna Azure-regionen av säkerhets-eller efterlevnadsprinciper. 

Här är ett exempel på hur en sådan konfiguration kan se ut:

![Kombinerad hög tillgänglighet för DR i zoner](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande överväganden gäller för den här konfigurationen:

- Du antar att det finns ett betydande avstånd mellan de anläggningar som är värdar för en tillgänglighets zon eller om du tvingas att stanna kvar inom en viss Azure-region. Det går inte att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner. För att kompensera för detta kan du använda Azure närhets placerings grupper som dokumenteras i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen nära och försöka behålla instanserna aktiva DBMS och SAP Central Services i samma zon som det distribuerade program lagret. Om det är en redundansväxling av SAP central service eller DBMS-instansen, vill du se till att du manuellt växlar tillbaka till zonen med SAP-program lagret distribuerat så snabbt som möjligt.
- Du bör vara förinstallerade för instanser av produktions program i de virtuella datorer som kör de aktiva program instanserna för frågor och svar.
- Om det uppstår ett zon haveri stänger du program instanserna för frågor och svar och startar produktions instanserna i stället. Observera att du måste använda virtuella namn för program instanserna för att det ska fungera.
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage och [Ultra SSD Storage](../../disks-types.md#ultra-disk) stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
    - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) eller ytterligare program instanser.





## <a name="next-steps"></a>Nästa steg
Här följer några exempel på hur du distribuerar i Azure-tillgänglighetszoner:

- [Klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Förbered Azure-infrastrukturen för SAP med hög tillgänglighet genom att använda ett Windows-redundanskluster och en fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md)