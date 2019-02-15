---
title: Konfigurationer för SAP-arbetsbelastning med Azure Availability Zones | Microsoft Docs
description: Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver med Azure Availability Zones
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d98a5ab13c2aecd3b3cef590526031f5bdee594
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268321"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurationer för SAP-arbetsbelastning med Tillgänglighetszoner i Azure
[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) är en av de funktioner för hög tillgänglighet som Azure tillhandahåller. Med hjälp av Tillgänglighetszoner förbättrar den övergripande tillgängligheten för SAP-arbetsbelastningar på Azure. Den här funktionen finns redan i vissa [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). I framtiden kommer blir den tillgänglig i fler regioner.

Den här bilden visar grundläggande hög tillgänglighet för SAP-arkitektur:

![Konfiguration för hög tillgänglighet som standard](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP-programnivån distribueras i en Azure [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). För hög tillgänglighet för SAP Central Services kan du distribuera två virtuella datorer i en separat tillgänglighetsuppsättning. Använda Windows Server Failover Clustering eller Pacemaker (Linux) som ett ramverk för hög tillgänglighet med automatisk redundans om ett problem med infrastruktur eller programvara. Mer information om dessa distributioner finns:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av filresurs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

En liknande arkitektur som gäller för DBMS-lager av SAP NetWeaver, S/4HANA eller Hybris system. Du distribuerar DBMS-lager i ett aktivt/passivt läge med en lösning för redundanskluster att skydda mot infrastruktur- eller programvarufel. Lösningen för redundanskluster kan en DBMS-specifik redundans framework, Windows Server Failover Clustering, eller Pacemaker.

För att distribuera samma arkitektur med hjälp av Azure Availability Zones kan behöva du göra några ändringar på arkitekturen som beskrivs tidigare. Den här artikeln beskriver dessa ändringar.

## <a name="considerations-for-deploying-across-availability-zones"></a>Överväganden för distribution över Tillgänglighetszoner


Tänk på följande när du använder Tillgänglighetszoner:

- Det finns inga garantier avseende avståndet mellan olika Tillgänglighetszoner inom en Azure-region.
- Tillgänglighetszoner är inte en perfekt lösning för Haveriberedskap. Naturkatastrofer kan orsaka omfattande skador i världsregioner, inklusive tunga skador power-infrastrukturer. Avståndet mellan olika zoner kanske inte är tillräckligt stor för att utgöra en korrekt DR-lösning.
- Svarstiden i nätverk i olika Tillgänglighetszoner är inte samma i alla Azure-regioner. I vissa fall kan du distribuera och köra SAP-programnivån över olika zoner eftersom svarstiden i nätverk från en zon i aktivt DBMS VM är godkända. Men i vissa Azure-regioner, fördröjning mellan den aktiva DBMS VM och programinstansen SAP när de distribueras i olika zoner, kanske inte är godkänd för SAP affärsprocesser. Distributionsarkitektur för måste vara olika, med en aktiv/aktiv-arkitektur för programmet eller en aktiv/passiv arkitektur där Nätverksfördröjningen mellan zoner är för hög i dessa fall.
- När du bestämmer var du vill använda Tillgänglighetszoner basera din beslut om Nätverksfördröjningen mellan zonerna. Nätverksfördröjningen spelar en viktig roll i två områden:
    - Fördröjning mellan de två DBMS-instanser som måste ha synkron replikering. Ju högre Nätverksfördröjningen, desto mer sannolikt kommer att påverka skalbarheten i din arbetsbelastning.
    - Skillnaden i Nätverksfördröjningen mellan en virtuell dator med en SAP dialogrutan instans i zonen med den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. När den här skillnaden ökar inverkan på körningstiden för affärsprocesser och batch-jobb också ökar, beroende på om de körs i zonen med DBMS eller i en annan zon.

När du distribuerar virtuella Azure-datorer mellan Tillgänglighetszoner och upprätta redundans lösningar inom samma Azure-region, gäller vissa begränsningar:

- Du måste använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) när du distribuerar till Azure Availability Zones. 
- Mappningen av zonen uppräkningar till de fysiska zonerna är fast på grundval av Azure-prenumeration. Om du använder olika prenumerationer för att distribuera dina SAP-system som du behöver definiera perfekt zonerna för varje prenumeration.
- Du kan inte distribuera Azure tillgänglighetsuppsättningar i en Azure-Tillgänglighetszon. Välj något annat som ett distribution-ramverk för virtuella datorer.
- Du kan inte använda en [Azure belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) att skapa redundans baserat på Windows Server Failover Clustering- eller Linux-Pacemaker-klusterlösningar. I stället måste du använda den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Idealisk Tillgänglighetszoner kombination
Innan du bestämmer dig hur du använder Tillgänglighetszoner, måste du fastställa:

- Nätverksfördröjningen mellan de tre zonerna för en Azure-region. Detta kan du välja zonerna med den lägsta Nätverksfördröjningen mellan zoner nätverkstrafik.
- Skillnaden mellan VM-till-VM svarstiden inom någon av zonerna, valfritt, och Nätverksfördröjningen mellan två zoner valfritt.
- En bestämning av om vilka typer av virtuell dator som du behöver distribuera är tillgängliga i de två zoner som du har valt. Med vissa virtuella datorer, särskilt M-serien datorer, kan som uppstå situationer där vissa SKU: er är tillgängliga i endast två av de tre zonerna.

## <a name="network-latency-between-and-within-zones"></a>Nätverksfördröjningen mellan och inom zoner
För att fastställa fördröjning mellan olika zoner, måste du:

- Distribuera VM-SKU du vill använda för din DBMS-instans i alla tre zoner. Se till att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiveras när du tar denna mätning.
- När du har hittat två zoner med den lägsta Nätverksfördröjningen distribuera ytterligare tre virtuella datorer av VM-SKU som du vill använda som programnivån VM i tre Tillgänglighetszoner. Mäta svarstiden i nätverk mot två DBMS-datorer i de två DBMS-zoner som du har valt. 
- Använd **niping** som ett verktyg för mätning. Det här verktyget från SAP, beskrivs i SAP support notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på de kommandon som beskrivs i replikeringssvarstiderna. Eftersom **ping** fungerar inte via kodsökvägar Azure Accelerated Networking inte rekommenderar vi att du använder den.

Baserat på dina mått och tillgängligheten för din VM SKU: er i Availability Zones kan behöva du göra vissa beslut:

- Definiera perfekt zonerna för DBMS-lager.
- Avgör om du vill distribuera dina aktiva SAP-programnivån över en, två eller alla tre zoner, baserat på skillnader i nätverket svarstid i zon jämfört med i flera zoner.
- Avgör om du vill distribuera en aktiv/passiv konfiguration eller en aktiv/aktiv-konfiguration av ett program. (De här konfigurationerna beskrivs senare i den här artikeln.)

I de här besluten också beakta konto SAP nätverksrekommendationer till svarstid, enligt beskrivningen i SAP-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Mätning av faktisk användning och beslut du fattar är giltiga för den Azure-prenumeration du använde när du tog mätningar. Om du använder en annan Azure-prenumeration kan behöva du upprepa mätningar. Mappningen av uppräknade zoner kan vara olika för en annan Azure-prenumeration.


> [!IMPORTANT]
> Det förväntas att mätningar som beskrivs ovan ger olika resultat i varje Azure-region som har stöd för [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Även om dina nätverkskrav för svarstid är samma, kan du behöva använda olika distributionsstrategier i olika Azure-regioner eftersom Nätverksfördröjningen mellan zoner kan vara olika. Nätverksfördröjningen mellan de tre olika zonerna kan vara avsevärt olika i vissa Azure-regioner. Nätverksfördröjningen mellan de tre olika zonerna kan vara mer enhetlig i andra regioner. Anspråk som det finns alltid en Nätverksfördröjningen mellan 1 och 2 millisekunder är inte korrekt. Nätverksfördröjningen mellan Tillgänglighetszoner i Azure-regioner kan inte generaliseras.

## <a name="activeactive-deployment"></a>Aktiv/aktiv distribution
Den här distributionsarkitekturen kallas aktiv/aktiv eftersom du distribuera dina aktiva SAP dialogrutan instanser i två eller tre zoner. SAP Central Services-instans som använder sätta replikering ska distribueras mellan två zoner. Detsamma gäller för DBMS-lager, som ska distribueras till samma zoner som SAP Central tjänst.

När du överväger den här konfigurationen behöver du hitta två Tillgänglighetszoner i din region som erbjudandet mellan zoner Nätverksfördröjningen som är godtagbar för din arbetsbelastning och din synkron DBMS-replikering. Du bör också försäkra delta mellan Nätverksfördröjningen inom de områden som du har valt och Nätverksfördröjningen mellan zoner stöds inte för stor. Det beror på att du inte vill att stora variationer, beroende på om ett jobb körs i zonen med DBMS-servern eller i flera zoner, i körs tidsskillnad på affärsprocesserna eller batch-jobb. Vissa varianter accepteras, men inte faktorer med skillnaden.

En förenklad schemat för en aktiv/aktiv distribution över två zoner kan se ut så här:

![Aktiv/aktiv zon distribution](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Du behandla Azure Availability Zones som fel- och uppdateringsdomäner för alla virtuella datorer eftersom tillgänglighetsuppsättningar inte kan distribueras i Azure Availability Zones.
- För belastningsutjämnare för failover-kluster i SAP Central Services och DBMS-lagret, måste du använda den [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte i flera zoner.
- Azure-nätverket som du har distribuerat som värd för SAP-system, tillsammans med dess undernät sträcks i flera zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar, måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
    - För Windows, en lösning som använder SIOS DataKeeper, enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    För närvarande den lösning som använder Microsoft Scale-Out File Server, enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), är inte stöds i olika zoner.
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux Pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.
- För att uppnå konsekvens körningstid för affärskritiska processer som du kan försöka att dirigera vissa batch-jobb och instanser av programmet som är i zon användare med den aktiva DBMS-instansen med hjälp av SAP batch-servergrupper, inloggningsgrupper eller RFC grupper. Men när det gäller en zonindelad redundans, skulle du behöva flytta dessa grupper manuellt till instanser som körs på virtuella datorer som finns i zonen med den aktiva DB virtuella datorn.  
- Du kanske vill distribuera vilande dialogrutan instanser i alla zoner. Detta är att aktivera en omedelbar återgå till den tidigare versionen resurskapaciteten om en zon som används av en del av dina programinstanser är ur funktion.


## <a name="activepassive-deployment"></a>Aktiv/passiv-distribution
Om du inte hittar en godtagbar delta mellan svarstiden i nätverk inom en zon och svarstiden för trafik mellan zoner kan distribuera du en arkitektur som har en aktiv/passiv-tecken från SAP application layer synvinkel. Du definierar en *active* zon, vilket är zonen där du distribuerar fullständig programnivån och där du försöker köra både aktiva DBMS och SAP Central Services-instansen. Med en sådan konfiguration måste du kontrollera att du inte har extrem körningstid variationer, beroende på om ett jobb körs i zonen med den aktiva DBMS-instansen eller inte, i affärstransaktioner och batch-jobb.

Grundläggande layouten för arkitekturen ser ut så här:

![Aktiv/passiv zon distribution](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Tillgänglighetsuppsättningar kan inte distribueras i Azure Availability Zones. Så i detta fall använder har du en uppdaterings- och feldomäner domän för programlagret. Det beror på att det bara är distribuerat i en zon. Den här konfigurationen är något mindre önskvärda än referensarkitekturen, vilket tyder på att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- När du använder den här arkitekturen, måste du noga övervaka status och se till att de aktiva DBMS och SAP Central Services-instanserna i samma zon som programlagret distribuerade. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så snabbt som möjligt.
- För belastningsutjämnare för failover-kluster i SAP Central Services och DBMS-lagret, måste du använda den [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte i flera zoner.
- Azure-nätverket som du har distribuerat som värd för SAP-system, tillsammans med dess undernät sträcks i flera zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar, måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
    - För Windows, en lösning som använder SIOS DataKeeper, enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  För närvarande den lösning som använder Microsoft Scale-Out File Server, enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), är inte stöds i olika zoner.
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux Pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.
- Du bör distribuera vilande virtuella datorer i passivt zon (från en DBMS synvinkel) så att du kan börja programresurser vid fel zon.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) är för närvarande inte att replikera aktiva virtuella datorer till vilande virtuella datorer mellan zoner. 
- Du bör investera i automation som gör att du ska starta automatiskt SAP-programnivån i den andra zonen om zonen.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombineras hög tillgänglighet och haveriberedskapskonfiguration
Microsoft delar inte någon information om geografiska avstånd mellan funktionerna som värd olika Azure Availability Zones i en Azure-region. Vissa kunder använder fortfarande, zoner för en kombinerad HA och DR-konfiguration som ger ett mål för återställningspunkt (RPO) på noll. Det innebär att du inte får förlora alla allokerade databastransaktioner även vid katastrofåterställning. 

> [!NOTE]
> Vi rekommenderar att du använder en konfiguration som detta under vissa omständigheter. Du kan till exempel använda den när data inte tillåts lämna Azure-region för säkerhet eller kompatibilitetsskäl. 

Här är ett exempel på hur en sådan konfiguration kan se ut:

![Kombinerade DR för hög tillgänglighet i zoner](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande gäller för den här konfigurationen:

- Du är antingen om vi antar att det finns en betydande avståndet mellan anläggningar som är värd för en Tillgänglighetszon eller du måste hålla sig inom en viss Azure-region. Tillgänglighetsuppsättningar kan inte distribueras i Azure Availability Zones. Så i detta fall använder har du en uppdaterings- och feldomäner domän för programlagret. Det beror på att det bara är distribuerat i en zon. Den här konfigurationen är något mindre önskvärda än referensarkitekturen, vilket tyder på att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- När du använder den här arkitekturen, måste du noga övervaka status och se till att de aktiva DBMS och SAP Central Services-instanserna i samma zon som programlagret distribuerade. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så snabbt som möjligt.
- Du bör ha produktion programinstanser som är förinstallerade på de virtuella datorer som kör active QA programinstanserna.
- Om ett fel uppstår i zonen, Stäng av programinstanser QA och starta produktionsinstanser i stället. Observera att du behöver använda virtuella namn för instanserna av programmet för att göra detta arbete.
- För belastningsutjämnare för failover-kluster i SAP Central Services och DBMS-lagret, måste du använda den [Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Den grundläggande belastningsutjämnaren fungerar inte i flera zoner.
- Azure-nätverket som du har distribuerat som värd för SAP-system, tillsammans med dess undernät sträcks i flera zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar, måste du använda [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner.
- Azure Premium Storage och [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar dessa delade diskar eller resurser mellan zonerna. Dessa tekniker stöds:
    - För Windows, en lösning som använder SIOS DataKeeper, enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  För närvarande den lösning som använder Microsoft Scale-Out File Server, enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), är inte stöds i olika zoner.
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux Pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser.





## <a name="next-steps"></a>Nästa steg
Här följer nästa steg för att distribuera i olika Tillgänglighetszoner i Azure:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Förbereda Azure-infrastrukturen för hög tillgänglighet för SAP genom att använda en Windows failover-kluster och filresursen för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






