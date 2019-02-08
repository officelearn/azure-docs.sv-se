---
title: Konfigurationer för SAP-arbetsbelastning med Azure Availability Zones | Microsoft Docs
description: Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver med hjälp av Azure tillgänglighetszoner
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
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858813"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurationer för SAP-arbetsbelastning med Tillgänglighetszoner i Azure

En av de funktioner för hög tillgänglighet som Azure erbjuder är [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Med hjälp av tillgänglighetszoner förbättrar den övergripande tillgängligheten för SAP-arbetsbelastningar på Azure. Tillgänglighetszoner är tillgängliga på flera [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/) redan. Fler regioner följer. 

Grundläggande arkitektur för hög tillgänglighet för SAP kan beskrivas som visas i den här bilden:

![Standardkonfiguration för hög tillgänglighet](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP-programnivån distribueras i en Azure [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). För hög tillgänglighet i SAP Central Services kan distribuera du två virtuella datorer i en separat tillgänglighetsuppsättning. Använda Windows Failover Cluster Services eller Pacemaker (Linux) som ett ramverk för hög tillgänglighet med automatisk redundans vid problem med infrastruktur eller programvara. Dokument med information om distributioner finns här:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av filresurs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

En liknande arkitektur som gäller för DBMS-lager av SAP NetWeaver, S/4HANA eller Hybris system. Du distribuerar DBMS-lager i ett aktivt/passivt läge med en lösning för redundanskluster att skydda mot infrastruktur- eller programvarufel. Lösningen för redundanskluster kan vara antingen DBMS specifika redundans framework, Windows Failover Cluster Services eller Pacemaker. 

För att distribuera samma arkitektur med hjälp av Azure tillgänglighetszoner krävs några ändringar i arkitekturen som beskrivs ovan. Dessa beskrivs i det här dokumentet.

## <a name="considerations-deploying-across-availability-zones"></a>Överväganden för distribution över Tillgänglighetszoner

När du använder Tillgänglighetszoner, finns det några saker att tänka på:

- Azure tillgänglighetszoner garanterar inte vissa avståndet mellan olika zoner i en Azure-region.
- Azure tillgänglighetszoner är inte en perfekt lösning för Haveriberedskap. Naturkatastrofer kan orsaka wide sprids skador i vissa världsregioner, inklusive tunga skador power infrastruktur. Avståndet mellan olika zoner kan inte vara tillräckligt stor för att utgöra en korrekt DR-lösning.
- Svarstiden i nätverk i olika tillgänglighetszoner varierar i olika Azure-regioner. Det finns fall där du kan distribuera och köra SAP-programnivån över olika zoner som svarstiden i nätverk från en zon för aktiva DBMS VM är acceptabelt från en process inverkan på företaget. Men i vissa Azure-regioner fördröjning mellan den aktiva DBMS VM och programinstansen SAP distribueras i olika zoner kan vara för påträngande och oacceptabla för SAP-affärsprocesser. Därför måste distributionsarkitekturen vara olika med en aktiv/aktiv-arkitektur för programmet eller aktiv/passiv arkitektur där flera zonen svarstiden är för högt.
- Bestämma i vilken konfiguration som du kan använda Azure tillgänglighetszoner, baserat på svarstiden i nätverk som du mäter mellan olika zoner. Svarstiden i nätverk spelar en viktig roll i två områden:
    - Fördröjning mellan de två DBMS-instanser som måste ha en synkron replikering upprättats. Ju högre Nätverksfördröjningen, desto högre kan påverkan skalbarhet för din arbetsbelastning
    - Skillnaden i Nätverksfördröjningen mellan en virtuell dator med en SAP dialogrutan instans i zonen med den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. Ju högre detta skillnad, desto högre påverkan på körning av affärsprocesser och batchjobb, beroende på om de körs i zonen med DBMS eller i en annan zon.


Det finns vissa begränsningar när du distribuerar virtuella Azure-datorer mellan tillgänglighetszoner och upprätta redundans lösningar inom samma Azure-region. Dessa begränsningar beskrivs i följande lista:

- Med hjälp av [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/) är obligatoriskt för att distribuera i Azure tillgänglighetszoner 
- Mappningen av zonen uppräkningar till de fysiska zonerna är fast på grundval av Azure-prenumeration. Om du använder olika prenumerationer för att distribuera dina SAP-system som du behöver definiera perfekt zonerna för varje prenumeration.
- Du kan inte distribuera Azure tillgänglighetsuppsättningar i en Azure-tillgänglighetszon. Välj en Azure-tillgänglighetszon eller en Azure-tillgänglighetsuppsättning som distribution ramverk för virtuella datorer.
- Du kan inte använda en [Azure belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) att skapa redundans baserat på Windows Failover Cluster Services eller Linux Pacemaker-klusterlösningar. I stället måste du använda den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-availability-zones-combination"></a>Perfekt Availability Zones kombination
Innan du bestämmer dig hur du kan använda tillgänglighetszoner måste du utföra en undersökning som ger dig:

- Nätverksfördröjningen mellan tre olika zoner i en Azure-region. Så att du kan välja zonerna med den lägsta Nätverksfördröjningen i mellan zon nätverkstrafik
- Skillnaden mellan VM-till-VM svarstiden inom någon av de zoner som du har valt och Nätverksfördröjningen mellan två zoner som du har valt
- En instruktion om att du behöver distribuera skriver i den virtuella datorn är tillgängliga i två zoner valfri. Särskilt i virtuella datorer i M-serien kommer du att uppstå situationer där olika M-serien VM SKU: er ska vara tillgängliga i två av de tre zonerna

### <a name="network-latency-between-zones-and-within-zone"></a>Nätverksfördröjningen mellan zoner och inom zonen
Om du vill ta reda på vad som är fördröjningen mellan olika zoner, måste du:

- Distribuera VM-SKU du vill använda för din DBMS-instans i alla tre zoner. Se till att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiveras när du utför det här måttet.
- När du har hittat två zoner med den lägsta Nätverksfördröjningen kan du distribuera ytterligare tre virtuella datorer av VM-SKU som du vill använda som programnivån VM i tre tillgänglighetszoner. Mäta svarstiden i nätverk mot de två 'DBMS virtuella datorerna ”i två olika” DBMS' zoner valfri. 
- Som ett verktyg för att mäta, använda **niping**. Ett verktyg från SAP, vilket fungerar enligt beskrivningen i SAP support notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på att kommandona SAP som beskrivs i replikeringssvarstiderna. Med hjälp av **ping** rekommenderas inte eftersom **ping** fungerar inte via Azure accelererat nätverk kodsökvägar.

Baserat på mätningar och tillgängligheten för din VM SKU: er i tillgänglighetszoner, måste du fatta beslut:

- Definiera perfekt zonerna för DBMS-lager
- Svar på frågan baserat om du vill distribuera dina aktiva SAP-programnivån över en, två eller alla tre zoner, på skillnader i nätverket svarstid i zon jämfört med i flera zoner
- Besvara frågan om du vill distribuera en aktiv/passiv eller en aktiv/aktiv-konfiguration av ett program (se senare)

De här besluten även återkalla SAP: s nätverksrekommendationer svarstid enligt beskrivningen i SAP-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Tänk på

> [!IMPORTANT]
> Mätning av faktisk användning och beslut du fattar är giltiga för den Azure-prenumeration du använde för att göra dessa mätningar. Om med en annan Azure-prenumeration, måste du upprepa mätningar, eftersom mappningen av uppräknade zoner kan skilja sig åt för en annan Azure-prenumeration.


> [!IMPORTANT]
> Det förväntas att mätningar som de genomförs ovan visas olika resultat i varje Azure-region som har stöd för [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Även om dina nätverkskrav för svarstid är samma, kan du behöva använda olika distributionsstrategier i olika Azure-regioner eftersom Nätverksfördröjningen mellan zoner kan vara olika. I vissa Azure-regioner Nätverksfördröjningen mellan tre olika zoner att avsevärt olika förväntas. Nätverksfördröjningen mellan tre olika zoner i andra regioner är mer enhetlig. Anspråket det **alltid** är en nätverkssvarstid i flera zoner av 1 millisekund till 2 millisekunder är **fel**. Nätverksfördröjningen mellan tillgänglighetszoner i Azure-regioner kan inte generaliseras.


## <a name="activeactive-deployment"></a>Aktiv/aktiv distribution
Den här distributionsarkitekturen kallas aktiv/aktiv, eftersom du distribuera dina aktiva SAP dialogrutan instanser i två eller tre zoner. SAP Central Services med hjälp av sätta replikering ska distribueras mellan två zoner. Detsamma gäller för DBMS-lager, som kommer att distribueras till samma zoner som SAP Central tjänst.

Om du vill överväga en sådan konfiguration måste du hitta de två tillgänglighetszonerna i din region som erbjuder mellan zon Nätverksfördröjningen som är godtagbar för din arbetsbelastning och för din synkron DBMS-replikering. Dessutom du vill delta mellan Nätverksfördröjningen inom de områden som du har valt och över zon nätverksfördröjning inte var för stor. Skäl för det senare, är att du inte vill ha för stora variationer i för dina affärsprocesser eller batch-jobb, beroende på om ett jobb körs i zonen med DBMS-servern eller i zonen. Vissa varianter accepteras, men inte faktorer med skillnaden.

En förenklad schemat för en aktiv/aktiv distribution över två zoner kan se ut:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Du behandla Azure tillgänglighetszoner som fel- och uppdateringsdomäner för alla virtuella datorer eftersom tillgänglighetsuppsättningar inte kan distribueras i Azure tillgänglighetszoner
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services och DBMS-lagret måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- Använd för alla virtuella datorer som du distribuerar, [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - För närvarande lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser
- För att uppnå konsekvens körningstid för affärskritiska processer som du kan försöka att dirigera vissa batch-jobb och instanser av programmet som är i zon användare med den aktiva DBMS-instansen med hjälp av SAP Batch servergrupper, inloggningsgrupper eller RFC grupper. Men vid zonindelad redundans måste flytta dessa grupper manuellt till instanser som körs på virtuella datorer i zoner med den aktiva DB virtuella datorn.  
- Bestäm om du vill distribuera vissa vilande dialogrutan instanser i alla zoner för att kunna direkt komma till den tidigare versionen resurskapaciteten om en zon som används av en del av dina programinstanser är ur funktion


## <a name="activepassive-deployment"></a>Aktiv/passiv-distribution
Om du inte hittar en godtagbar delta mellan svarstiden i nätverk inom en zon och mellan zon nätverkstrafik, har den arkitektur som du kan distribuera en aktiv/passiv tecken från SAP application layer synvinkel. Du definierar en ”aktiv” zon, vilket är zonen där du distribuerar fullständig programnivån och där du försöker köra både den aktiva DBMS och SAP Central Services instansen. Med en sådan konfiguration du att du inte har extrem körningstid variationer i affärstransaktioner och batchjobb, beroende på om ett jobb körs i zonen med den aktiva DBMS-instansen eller inte.

Grundläggande layouten för sådana arkitekturen ser ut som:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Tillgänglighetsuppsättningar kan inte distribueras i Azure tillgänglighetszoner. Därför kan i så fall har du en uppdaterings- och feldomäner domän för programlagret. Anledningen är att det bara är distribuerat i en zon. Den här konfigurationen är en liten bakslag, jämfört med referensarkitektur som förutser att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- Använda sådana en arkitektur som du behöver övervakas noggrant och se till att de aktiva DBMS och SAP Central services-instanserna i samma zon som distribuerade programlagret. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så tidigt som möjligt
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services och DBMS-lagret måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- För alla virtuella datorer som du distribuerar, måste du använda [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - För närvarande lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser
- Distribuera vilande virtuella datorer i den passiva zon (från en DBMS synvinkel) för att kunna starta programresurser vid fel zon
    - Du kan inte använda [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) att replikera aktiva virtuella datorer till vilande virtuella datorer mellan zoner. Vid denna tidpunkt, kan Azure Site Recovery inte slutföra en sådan funktion
- Investera i automation som gör att du ska starta automatiskt SAP-programnivån i den andra zonen om zonen

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombineras hög tillgänglighet och haveriberedskapskonfiguration
Microsoft delar inte någon information om geografiska avståndet mellan verksamhet att hantera olika Azure-tillgänglighetsuppsättning zoner i Azure-region. Den här trots vissa kunder använder sig av zoner för en kombinerad HA och DR-konfiguration som ger en **R**redundansstatusen **P**lats **O**bjective (RPO) på noll. Det här innebär att bör du inte förlorar alla allokerade databastransaktioner även i disaster recovery fallet. 

> [!NOTE]
> En konfiguration som detta rekommenderas för särskilda omständigheter. Till exempel fall där data inte kan lämna Azure-region på grund av orsaker för säkerhet och efterlevnad. 

Den här bilden visar ett exempel hur en sådan konfiguration kan se ut:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande gäller för den här konfigurationen:

- Antingen är förutsatt att det finns en betydande avståndet mellan anläggningar som är värd för en tillgänglighetszon. Eller tvingas du Fortsätt att arbeta med en viss Azure-region. Tillgänglighetsuppsättningar kan inte distribueras i Azure tillgänglighetszoner. Därför i detta fall använder avslutar du med en uppdaterings- och feldomäner domän för programlagret. Anledningen är att det bara är distribuerat i en zon. Det här är ett bakslag jämfört med referensarkitektur som förutser att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- Använda sådana en arkitektur som du behöver övervakas noggrant och se till att de aktiva DBMS och SAP Central services-instanserna i samma zon som distribuerade programlagret. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så tidigt som möjligt
- Du har produktion programinstanser som är förinstallerade på de virtuella datorer som kör active QA programinstanserna.
- Om zonindelad att Stäng programinstanser QA och starta produktionsinstanser i stället. Tänk på att du behöver arbeta med virtuella namn för instanserna av programmet till att det ska fungera
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services och DBMS-lagret måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- För alla virtuella datorer som du distribuerar, måste du använda [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux, en NFS-resursens som skapats enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - Vid denna tidpunkt, lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser





## <a name="next-steps"></a>Nästa steg
Kontrollera nästa steg för att distribuera i olika tillgänglighetszoner i Azure:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Förbereda Azure-infrastrukturen för hög tillgänglighet för SAP genom att använda en Windows failover-kluster och filresursen för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






