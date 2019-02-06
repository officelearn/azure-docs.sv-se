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
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746225"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurationer för SAP-arbetsbelastning med Tillgänglighetszoner i Azure

En av funktionerna för hög tillgänglighet som Azure erbjuder att förbättra den övergripande tillgängligheten för SAP-arbetsbelastningar på Azure, är [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Tillgänglighetszoner är tillgängliga i olika [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/) redan. Fler regioner följer. 

Grundläggande arkitektur för hög tillgänglighet för SAP kan beskrivas som visas i den här bilden:

![Standardkonfiguration för hög tillgänglighet](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP-programnivån distribueras i en Azure [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). För hög tillgänglighet i SAP Central Services kan du distribuera två virtuella datorer i en separat tillgänglighetsuppsättning och använda Windows Failover Cluster Services eller Pacemaker (Linux) för att distribuera ett ramverk för hög tillgänglighet som gör automatisk redundans vid en infrastruktur eller programfel. Dokumentation med information om sådana distributioner lista som:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av filresurs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

En liknande arkitektur som gäller för DBMS-lager av SAP NetWeaver, S/4HANA eller Hybris system. Du distribuerar DBMS-lager i ett aktivt/passivt läge med en lösning för redundanskluster, som använder antingen DBMS specifika redundans ramverk Windows Failover Cluster Services eller Pacemaker för att initiera redundans-aktiviteter när det gäller infrastruktur eller programvara fel. 

Vissa ändringar ska den arkitektur som beskrivs måste göras för att distribuera samma arkitektur med hjälp av Azure tillgänglighetszoner. Dessa beskrivs i de olika delarna i det här dokumentet.

## <a name="considerations-deploying-across-availability-zones"></a>Överväganden för distribution över Tillgänglighetszoner

Med Availability Zones finns vissa saker att tänka på. Att tänka på listan som:

- Azure tillgänglighetszoner ger inte några garantier för vissa avståndet mellan olika zoner i en Azure-region
- Azure tillgänglighetszoner är inte en perfekt lösning för Haveriberedskap. I tider där enorm naturkatastrofer orsakar wide kanske sprids skador i vissa områden i världen, inklusive tunga skador power infrastruktur, avståndet mellan olika zoner inte tillräckligt stor för att utgöra en korrekt DR-lösning
- Nätverkssvarstid i flera olika Azure availability zones i olika Azure-regioner skiljer sig från Azure-region till region. Det finns fall där du kan köra SAP-programnivån som distribueras över olika zoner eftersom svarstiden i nätverk från en zon i aktivt DBMS VM är fortfarande acceptabla inverkan på processen. Men det finns scenarier i vissa Azure-regioner där det kan vara fördröjning mellan den aktiva DBMS VM i en zon och en SAP-programinstans i en annan zon för påträngande och inte godkänd för SAP-affärsprocesser. Därför måste distributionsarkitekturen vara olika med en aktiv/aktiv-arkitektur för programmet eller aktiv/passiv arkitektur där flera zonen svarstiden är för högt.
- Fatta ett beslut som du kan använda Azure tillgänglighetszoner för i vilken konfiguration. Baserat på svarstiden i nätverk som du mäter mellan olika zoner. Svarstiden i nätverk spelar en viktig roll i två olika områden:
    - Fördröjning mellan de två DBMS-instanser som måste ha en synkron replikering upprättats. Ju högre Nätverksfördröjningen, desto högre kan påverkan skalbarhet för din arbetsbelastning
    - Skillnaden i Nätverksfördröjningen mellan en virtuell dator som kör en instans för SAP-dialogrutan i samma zon som den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. Ju högre denna skillnad, desto högre påverkan på körtid affärsprocesser och batchjobb, beroende på om de körs i samma zon med DBMS eller i en annan zon


Från och med användning av Azure-funktioner finns det vissa begränsningar av funktioner som kan användas när du distribuerar virtuella Azure-datorer i zoner och upprätta redundans lösningar i olika tillgänglighetszoner inom samma Azure-region. Dessa begränsningar visas som:

- Med hjälp av [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/) är obligatoriskt för att distribuera i Azure tillgänglighetszoner 
- Mappningen av zonen uppräkningar till de fysiska zonerna är fast på grundval av Azure-prenumeration. Om du använder olika prenumerationer för att distribuera dina SAP-system, måste du definiera perfekt zonerna för varje prenumeration separat
- Du kan inte distribuera Azure tillgänglighetsuppsättningar i en Azure-tillgänglighetszon. Välj en Azure-tillgänglighetszon eller en Azure-tillgänglighetsuppsättning som distribution ramverk för virtuella datorer.
- Du kan inte använda en [Azure belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) att skapa redundans baserat på Windows Failover Cluster Services eller Linux Pacemaker-klusterlösningar. I stället måste du använda den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Perfekt zon kombination
För att kunna bestämma i hur du kan utnyttja tillgänglighetszoner, måste du utföra en undersökning som ger dig:

- Nätverksfördröjningen mellan tre olika zoner i en Azure-region. Så att du kan välja de zoner som har den lägsta Nätverksfördröjningen mellan zon nätverkstrafik
- Skillnaden mellan VM-till-VM svarstiden inom någon av de zoner som du har valt och Nätverksfördröjningen mellan två zoner som du har valt
- En instruktion om den virtuella datorn matar när måste du har distribuerat över tillgänglighetszoner med finns i två zoner valfri. Särskilt i virtuella datorer i M-serien kommer du att uppstå situationer där olika M-serien VM SKU: er ska vara tillgängliga i två av de tre zonerna

### <a name="network-latency-between-zones-and-within-zone"></a>Nätverksfördröjningen mellan zoner och inom zonen
Om du vill ta reda på vad som är fördröjningen mellan olika zoner, måste du:

- Distribuera VM-SKU du vill använda för din DBMS-instans i alla tre zoner. Se till att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiveras när du utför denna mätning
- När du har hittat två zoner med den lägsta Nätverksfördröjningen kan du distribuera ytterligare tre virtuella datorer av VM-SKU som du vill använda som programnivån VM i tre tillgänglighetszoner. Mäta svarstiden i nätverk mot de två 'DBMS virtuella datorerna ”i två olika” DBMS' zoner valfri. 
- Som ett verktyg för att mäta, använda **niping**. Ett verktyg från SAP, vilket fungerar enligt beskrivningen i SAP support notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på att kommandona SAP som beskrivs i replikeringssvarstiderna. Med hjälp av **ping** är inte ett rekommenderat verktyg sedan **ping** fungerar inte via Azure accelererat nätverk kodsökvägar.

Du behöver fatta beslut beroende på mätningar och tillgängligheten för din VM SKU: er i olika zoner:

- Definiera perfekt zonerna för DBMS-lager
- Besvara frågan om, baserat på skillnader Nätverksfördröjningen inom en zon eller i olika zoner, kan du distribuera dina aktiva SAP-programnivån över en, två eller alla tre olika zoner
- Besvara frågan om du vill distribuera en aktiv/passiv eller en aktiv/aktiv-konfiguration av ett program (se senare)

De här besluten även återkalla SAP: s nätverksrekommendationer svarstid enligt beskrivningen i SAP-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Tänk på

> [!IMPORTANT]
> Mätning av faktisk användning och beslut du fattar är giltiga för den Azure-prenumeration du använde för att göra dessa mätningar. Med en annan Azure-prenumeration måste du upprepa mätningar eftersom mappningen av din prenumeration beroende uppräkning av zoner kan ändras med en annan prenumeration


> [!IMPORTANT]
> Det förväntas att mätningar som de genomförs ovan visas olika resultat i varje Azure-region som har stöd för [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan behöva anpassa olika distributionsstrategier i olika Azure-regioner eftersom Nätverksfördröjningen mellan zoner kan vara olika för även med dina krav på Nätverksfördröjningen ändras inte. I vissa Azure-regioner Nätverksfördröjningen mellan tre olika zoner att avsevärt olika förväntas. Nätverksfördröjningen mellan tre olika zoner är mer enhetlig i andra regioner. Anspråket det **alltid** är en nätverkssvarstid i flera zoner av 1 millisekund till 2 millisekunder är **fel**. Nätverksfördröjningen mellan tillgänglighetszoner i Azure-regioner kan inte generaliseras.


## <a name="activeactive-deployment"></a>Aktiv/aktiv distribution
Den här distributionsarkitekturen kallas aktiv/aktiv, eftersom du distribuera dina aktiva SAP dialogrutan instanser i två eller tre zoner. SAP Central Services med hjälp av sätta replikering ska distribueras mellan två zoner. Detsamma gäller för DBMS-lager, som kommer att distribueras till samma zoner som SAP Central tjänst.

Om du vill överväga en sådan konfiguration måste du hitta de två tillgänglighetszonerna i din region som erbjuder mellan zon Nätverksfördröjningen som är godtagbar för din arbetsbelastning och för din synkron DBMS-replikering. Dessutom du vill delta mellan Nätverksfördröjningen inom de områden som du har valt och över zon nätverksfördröjning inte var för stor. Skäl för det senare är att du inte vill för stora variationer i för dina affärsprocesser eller batch bearbetar beroende på om ett jobb körs i zonen med DBMS-servern eller i zonen. Vissa varianter accepteras, men inte faktorer med skillnaden.

En förenklad schemat för en aktiv/aktiv distribution över två zoner kan se ut:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Du behandla Azure tillgänglighetszoner som fel- och uppdateringsdomäner för alla virtuella datorer eftersom tillgänglighetsuppsättningar inte kan distribueras i Azure tillgänglighetszoner
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services samt DBMS-lagret, måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- Använd för alla virtuella datorer som du distribuerar, [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux bygger en NFS-resurs enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - Vid denna tidpunkt, lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser
- Att uppnå körningstid konsekvens för vissa viktiga business-transaktioner och/eller jobb. Du kan försöka att dirigera vissa batch-jobb och användare direkt till specifika programinstanser som är i zonen med den aktiva DBMS-instansen med hjälp av SAP Batch servergrupper, inloggningsgrupper eller RFC grupper. Men vid zonindelad redundans som du behöver flytta dessa grupper manuellt till dialogrutan instanser, som i återstående zonerna 
- Bestäm om du vill distribuera vissa vilande dialogrutan instanser i alla zoner som ska kunna gå direkt till den tidigare versionen resurskapaciteten om en zon som du har distribuerat en del av dina programinstanser, är ur funktion


## <a name="activepassive-deployment"></a>Aktiv/passiv-distribution
I fall där du inte hittar en godtagbar delta mellan svarstiden i nätverk inom en zon och mellan zon nätverkstrafik, har den arkitektur som du kan distribuera en aktiv/passiv tecken från SAP application layer synvinkel. Du definierar en ”aktiv” zonen, vilket är den zon som du distribuerar det hela appen lagret och där du försöker köra den aktiva DBMS-instansen och den aktiva SAP Central Services-instansen. Med konfigurationen kan se du till att du inte behöver extrem körningstid skillnaden i affärstransaktioner och batchjobb, beroende på om ett jobb körs i samma zon med den aktiva DBMS-instansen eller inte.

Grundläggande layouten för sådana arkitekturen ser ut som:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande gäller för den här konfigurationen:

- Tillgänglighetsuppsättningar kan inte distribueras i Azure tillgänglighetszoner. Därför i detta fall använder avslutar du med en uppdaterings- och feldomäner domän för programlagret. Anledningen är att det bara är distribuerat i en zon. Den här konfigurationen är en liten bakslag jämfört med referensarkitektur som förutser att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- Använda sådana en arkitektur som du behöver övervakas noggrant och se till att de aktiva DBMS och SAP Central services-instanserna i samma zon som distribuerade programlagret. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så tidigt som möjligt
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services samt DBMS-lagret, måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- För alla virtuella datorer som du distribuerar, måste du använda [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux bygger en NFS-resurs enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - Vid denna tidpunkt, lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser
- Distribuera vilande virtuella datorer i den passiva zon (från en DBMS synvinkel) för att kunna starta programresurser vid fel zon
    - Du kan inte använda [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) att replikera aktiva virtuella datorer till vilande virtuella datorer mellan zoner. Vid denna tidpunkt, kan Azure Site Recovery inte slutföra en sådan funktion
- Investera i automation som gör att du ska starta automatiskt SAP-programnivån i den andra zonen om zonen

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombineras hög tillgänglighet och haveriberedskapskonfiguration
Trots att Microsoft inte ger någon information på geografiska avståndet mellan anläggningar som är värdar för olika Azure availability zones i en viss Azure-region, vissa kunder använder sig av zoner för en kombinerad HA och DR-konfiguration som lovar ett **R**redundansstatusen **P**lats **O**bjective (RPO) på noll. Det här innebär att bör du inte förlorar alla allokerade databastransaktioner även i disaster recovery fallet. 

> [!NOTE]
> En konfiguration som detta rekommenderas för särskilda omständigheter. Till exempel fall där data inte kan lämna Azure-region på grund av orsaker för säkerhet och efterlevnad. 

Den här bilden visar ett exempel hur en sådan konfiguration kan se ut:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande gäller för den här konfigurationen:

- Antingen är förutsatt att det finns en betydande avståndet mellan anläggningar som är värd för en tillgänglighetszon. Eller tvingas du Fortsätt att arbeta med en viss Azure-region. Tillgänglighetsuppsättningar kan inte distribueras i Azure tillgänglighetszoner. Därför i detta fall använder avslutar du med en uppdaterings- och feldomäner domän för programlagret. Anledningen är att det bara är distribuerat i en zon. Det här är ett bakslag jämfört med referensarkitektur som förutser att du distribuerar programnivån i en Azure-tillgänglighetsuppsättning.
- Använda sådana en arkitektur som du behöver övervakas noggrant och se till att de aktiva DBMS och SAP Central services-instanserna i samma zon som distribuerade programlagret. Vid redundans för SAP Central tjänst eller DBMS-instans som du vill se till att du manuellt växla tillbaka till zonen med SAP-programnivån distribueras så tidigt som möjligt
- Du har produktion programinstanser som är förinstallerade på de virtuella datorer som kör active QA programinstanserna.
- Om zonindelad att Stäng programinstanser QA och starta produktionsinstanser i stället. Tänk på att du behöver arbeta med virtuella namn för instanserna av programmet till att det ska fungera
- Azure-belastningsutjämnare som du använder för failover-kluster av SAP Central Services samt DBMS-lagret, måste vara den [Standard-SKU belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Med basic load balancer fungerar inte mellan zoner
- Azure-nätverket och dess undernät som du har distribuerat som värd för SAP-system sträcks ut i flera zoner. Du **behöver inte** separata virtuella nätverk för varje zon
- För alla virtuella datorer som du distribuerar, måste du använda [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade distributioner
- Azure Premium storage eller [Ultra SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) stöder inte någon typ av storage-replikering i flera zoner. Därmed ansvarar för programmet (DBMS eller SAP Central Services) för att replikera viktiga data
- Samma sak gäller för den dela sapmnt-katalogen, vilket är en delad disk (Windows), en CIFS-resurs (Windows) eller NFS-resursens (Linux). Du måste använda en teknik som replikerar sådana en delad disk eller resurs mellan zonerna. För tillfället stöds följande tekniker:
    - För Windows, en lösning som använder SIOS Datakeeper enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) stöds i olika zoner
    - För SUSE Linux bygger en NFS-resurs enligt beskrivningen i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) stöds
    - Vid denna tidpunkt, lösningen med hjälp av tjänster för Windows-klustrad uppskalningsfilserver (SOFS) enligt beskrivningen i [förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för SAP ASCS/SCS instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **stöds inte i flera zoner**
- Den tredje zonen används som värd för enheten uppstår om du skapar en [SUSE Linux pacemaker kluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) eller ytterligare programinstanser





## <a name="next-steps"></a>Nästa steg
Kontrollera nästa steg för att distribuera i olika tillgänglighetszoner i Azure:

- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Förbereda Azure-infrastrukturen för hög tillgänglighet för SAP genom att använda en Windows failover-kluster och filresursen för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






