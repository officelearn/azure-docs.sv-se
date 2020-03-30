---
title: 'SAP på Azure: Scenarier som stöds med virtuella Azure-datorer'
description: Azure Virtual Machines stöds scenarier med SAP-arbetsbelastning
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137636"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-arbetsbelastning på Azure-virtuell dator som stöds scenarier
Designa SAP NetWeaver- `Hybris` eller Business one- eller S/4HANA-systemarkitektur i Azure öppnar många olika möjligheter för olika arkitekturer och verktyg att använda för att komma till en skalbar, effektiv och högtillgänglig distribution. Även beroende av operativsystemet eller DBMS används, det finns begränsningar. Inte heller alla scenarier som stöds lokalt stöds på samma sätt i Azure. Det här dokumentet kommer att leda genom de konfigurationer som inte är hög tillgänglighet och konfigurationer och arkitekturer med hög tillgänglighet med azure-virtuella datorer. För scenarier som stöds med [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)finns i artikeln [Scenarier som stöds för STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>Konfiguration på 2 nivåer
En SAP 2-nivå konfiguration anses vara uppbyggd av ett kombinerat lager av SAP DBMS och programlager som körs på samma server eller VM-enhet. Den andra nivån anses vara användargränssnittslagret. När det gäller en konfiguration på två nivåer delar DBMS- och SAP-programskiktet resurserna för Azure VM. Därför måste du konfigurera de olika komponenterna på ett sätt som de inte konkurrerar om resurser. Du måste också vara noga med att inte överteckna resurserna för den virtuella datorn. En sådan konfiguration ger inte någon hög tillgänglighet, utöver [Azure Service Level-avtalen](https://azure.microsoft.com/support/legal/sla/) för de olika Azure-komponenterna som ingår.

En grafisk representation av en sådan konfiguration kan se ut som:

![Enkel konfiguration på 2 nivåer](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Sådana konfigurationer stöds med Windows, Red Hat, SUSE och Oracle Linux för DBMS-systemen för SQL Server, Oracle, Db2, maxDB och SAP ASE för produktions- och icke-produktionsfall. För SAP HANA som DBMS stöds endast sådan typ av konfigurationer för fall som inte är produktionsfall. Detta inkluderar distributionsfallet för [Azure HANA Stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) också.
För alla OS/DBMS-kombinationer som stöds på Azure stöds den här typen av konfiguration. Det är dock obligatoriskt att du ställer in konfigurationen av DBMS- och SAP-komponenterna på ett sätt som DBMS- och SAP-komponenter inte konkurrerar om minnes- och CPU-resurser och därmed överskrider de fysiska tillgängliga resurserna. Detta måste göras genom att begränsa minnet dbms tillåts allokera. Du måste också begränsa SAP Extended Memory på programinstanser. Du måste också övervaka CPU-förbrukningen av den virtuella datorn totalt för att se till att komponenterna inte maximerar CPU-resurserna. 

> [!NOTE]
> För sap-system för produktion rekommenderar vi ytterligare hög tillgänglighet och eventuella konfigurationer för haveriberedskap enligt beskrivningen senare i det här dokumentet


## <a name="3-tier-configuration"></a>Konfiguration på 3 nivåer
I sådana konfigurationer separerar du SAP-programlagret och DBMS-lagret i olika virtuella datorer. Du brukar göra det för större system och av skäl att vara mer flexibel på resurserna i SAP-programlagret. I den enklaste installationen finns det ingen hög tillgänglighet utöver [Azure Service Level-avtalen](https://azure.microsoft.com/support/legal/sla/) för de olika Azure-komponenterna som ingår. 

Den grafiska representationen ser ut som:

![Enkel konfiguration på 2 nivåer](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Den här typen av konfiguration stöds på Windows, Red Hat, SUSE och Oracle Linux för DBMS-systemen för SQL Server, Oracle, Db2, SAP HANA, maxDB och SAP ASE för produktions- och icke-produktionsfall. Det här är standarddistributionskonfigurationen för [Azure HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). För förenkling har vi inte skilja mellan SAP Central Services och SAP-dialoginstanser i SAP-programlagret. I den här enkla konfigurationen på 3 nivåer skulle det inte finnas något skydd för hög tillgänglighet för SAP Central Services.

> [!NOTE]
> För sap-system för produktion rekommenderar vi ytterligare hög tillgänglighet och eventuella konfigurationer för haveriberedskap enligt beskrivningen senare i det här dokumentet


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Flera DBMS-instanser per virtuell dator eller STOR HANA-instansenhet
I den här konfigurationstypen är du värd för flera DBMS-instanser per Azure VM- eller HANA Large Instance-enhet. Motivationen kan vara att ha mindre operativsystem att underhålla och med det minskade kostnader. Andra motiv kan vara att ha mer flexibilitet och mer effektivitet genom att dela resurser för en större VIRTUELL eller HANA Stor instans enhet mellan flera DBMS-instanser. Hittills har dessa konfigurationer dök upp mest för icke-produktionssystem.

En sådan konfiguration kan se ut:

![Flera DBMS-instanser i en enhet](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Den här typen av DBMS-distribution stöds för:

- SQL Server i Windows
- IBM Db2. Hitta information i artikeln [Flera instanser (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- För Oracle. Mer information finns i [SAP-supportanteckning #1778431](https://launchpad.support.sap.com/#/notes/1778431) och relaterade SAP-anteckningar
- För SAP HANA stöds flera instanser på en virtuell dator, SAP-anlöpningsmetoden MCOS. Mer information finns i SAP-artikeln [Multiple SAP HANA Systems on One Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Köra flera databasinstanser på en värd, måste du se till att de olika instanserna inte konkurrerar om resurser och därmed överskrider de fysiska resursgränserna för den virtuella datorn. Detta gäller särskilt för minne där du behöver för att begränsa minnet någon av de instanser som delar den virtuella datorn kan allokera. Det kan också vara sant för CPU-resurser som de olika databasinstanserna kan utnyttja. Alla DBMS som nämns har konfigurationer som gör det möjligt att begränsa minnesallokering och CPU-resurser på en instansnivå.
För att ha stöd för en sådan konfiguration för virtuella Azure-datorer förväntas diskarna eller volymerna som används för data och logg/gör om loggfiler i de databaser som hanteras av de olika instanserna separat. Eller med andra ord ska data eller logg-/göra om loggfiler för databaser som hanteras av olika DBMS-instans inte dela samma diskar eller volymer. 

Diskkonfigurationen för STORA HANA-instanser levereras konfigurerad och beskrivs i [scenarier som stöds för STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> För produktion SAP-system rekommenderar vi ytterligare hög tillgänglighet och eventuella konfigurationer för haveriberedskap enligt beskrivningen senare i det här dokumentet. Virtuella datorer med flera DBMS-instanser stöds inte med de konfigurationer med hög tillgänglighet som beskrivs senare i det här dokumentet.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Flera SAP-dialoginstanser i en virtuell dator
I många fall har flera dialoginstanser distribuerats på bare metal-servrar eller till och med i virtuella datorer som körs i privata moln. Orsaken till sådana konfigurationer var att anpassa vissa SAP-dialoginstanser till vissa arbetsbelastnings-, affärsfunktioner eller arbetsbelastningstyper. Orsaken till att inte isolera dessa instanser i separata virtuella datorer var arbetet med underhåll och drift av operativsystemet. Eller i många fall kostnaderna om värdföretaget eller operatören av den virtuella datorn ber om en månadsavgift per vm drivs och administreras. I Azure, ett scenario med att vara värd för flera SAP-dialoginstanser inom en enda virtuell dator som vi stöds för produktion och icke-produktionsändamål på operativsystemen i Windows, Red Hat, SUSE och Oracle Linux. SAP-kärnparametern PHYS_MEMSIZE, som är tillgänglig i Windows och moderna Linux-kärnor, bör ställas in om flera SAP Application Server-instanser körs på en enda virtuell dator. Det rekommenderas också att begränsa utbyggnaden av SAP Extended Memory på operativsystem, som Windows där automatisk tillväxt av SAP utökat minne implementeras. Detta kan göras med parametern `em/max_size_MB`SAP-profil .

På 3-nivå konfiguration där flera SAP-dialoginstanser körs inom Azure virtuella datorer kan se ut:

![Flera DBMS-instanser i en enhet](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

För förenkling har vi inte skilja mellan SAP Central Services och SAP-dialoginstanser i SAP-programlagret. I den här enkla konfigurationen på 3 nivåer skulle det inte finnas något skydd för hög tillgänglighet för SAP Central Services. För produktionssystem rekommenderas det inte att lämna SAP Central Services oskyddat. Mer information om så kallade multi-SID-konfigurationer runt SAP Central Instances och hög tillgänglighet för sådana multi-SID-konfigurationer finns i senare avsnitt i det här dokumentet.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Skydd mot hög tillgänglighet för SAP DBMS-lagret
När du ser till att distribuera SAP-produktionssystem måste du överväga att använda konfigurationer med hög tillgänglighet. Särskilt med SAP HANA, där data måste läsas in i minnet innan de kan få tillbaka full prestanda och skalbarhet, är Azure-tjänstläkning inte ett idealiskt mått för hög tillgänglighet. 

I allmänhet stöder Microsoft endast konfigurationer och programvarupaket med hög tillgänglighet som beskrivs under avsnittet SAP-arbetsbelastning i docs.microsoft.com. Du kan läsa samma uttryck i [SAP-#1928533](https://launchpad.support.sap.com/#/notes/1928533). Microsoft tillhandahåller inte support för andra programvaruramverk från tredje part med hög tillgänglighet som inte dokumenteras av Microsoft tillsammans med SAP-arbetsbelastning. I sådana fall är tredjepartsleverantören av ramen för hög tillgänglighet den stödjande parten för konfigurationen med hög tillgänglighet som måste anlitas av dig som kund i supportprocessen. Undantag kommer att nämnas i denna artikel. 

I allmänhet stöder Microsoft en begränsad uppsättning konfigurationer med hög tillgänglighet på virtuella Azure-datorer eller HANA-enheter för stora instanser. För scenarier som stöds av HANA-stora instanser läser du [scenarierna för dokumentstödda för stora HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

För virtuella Azure-datorer stöds följande konfigurationer med hög tillgänglighet på DBMS-nivå:

- SAP HANA System Replication baserad på Linux Pacemaker på SUSE och Red Hat. Se de detaljerade artiklarna:
    - [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA-utskalning av n+m-konfigurationer med [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/) på SUSE och Red Hat. Detaljer finns i dessa artiklar:
    - [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- SQL Server Redundanskluster baserat på Windows Scale-Out File Services. Även rekommendation för produktionssystem är att använda SQL Server Alltid på istället för klustring. SQL Server Always On ger bättre tillgänglighet med separat lagring. Information beskrivs i den här artikeln: 
    - [Konfigurera en SQL Server-redundansklusterininstans på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- SQL Server Always On stöds med Windows-operativsystemet för SQL Server på Azure. Det här är standardrekommendationen för SQL Server-instanser för produktion på Azure. Närmare uppgifter beskrivs i dessa artiklar:
    - [Introduktion till SQL Server Always On Availability Groups på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
    - [Konfigurera en alltid på tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Konfigurera en belastningsutjämnare för en alltid på tillgänglighetsgrupp i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Oracle Data Guard för Windows och Oracle Linux. Detaljer för Oracle Linux finns i den här artikeln:
    - [Implementera Oracle Data Guard på en virtuell Azure Linux-dator](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR på SUSE och RHEL Detaljerad dokumentation för SUSE och RHEL med Pacemaker finns här:
    - [Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- SAP ASE och SAP maxDB konfiguration som beskrivs i dessa dokument:
    - [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [SAP MaxDB-, liveCache- och Content Server-distribution på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Hana Stora instanser med hög tillgänglighet beskrivs i:
    - [Scenarier som stöds för HANA Large Instances- HSR med STONITH för hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Scenarier som stöds för STORA HANA-instanser – Automatisk redundans för värd (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> För inget av de scenarier som beskrivs ovan stöder vi konfigurationer av flera DBMS-instanser i en virtuell dator. Medel i vart och ett av fallen kan endast en databasinstans distribueras per virtuell dator och skyddas med de beskrivna metoderna med hög tillgänglighet. Det går **inte** att skydda flera DBMS-instanser under samma Windows- eller Pacemaker-redundanskluster vid denna tidpunkt. Även Oracle Data Guard stöds endast för enskilda instanser per vm-distributionsfall. 

Olika databassystem gör det möjligt att vara värd för flera databaser under en DBMS-instans. Precis som i fallet med SAP HANA kan flera databaser finnas i flera databasbehållare (MDC). För fall där dessa konfigurationer med flera databaser fungerar inom en redundansklusterresurs stöds dessa konfigurationer. Konfigurationer som inte stöds är fall där flera klusterresurser skulle krävas. När det gäller konfigurationer där du skulle definiera flera SQL Server-tillgänglighetsgrupper, under en SQL Server-instans.


![DBMS HA-konfiguration](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Beroende på DBMS och operativsystem kan komponenter som Azure load balancer krävas eller inte kan krävas som en del av lösningsarkitekturen. 

Specifikt för maxDB måste lagringskonfigurationen vara annorlunda. I maxDB måste data och loggfiler finnas på delad lagring för konfigurationer med hög tillgänglighet. Endast när det gäller maxDB stöds delad lagring för hög tillgänglighet. För alla andra DBMS separat lagring stackar per nod är de enda diskkonfigurationer som stöds.

Andra ramverk för hög tillgänglighet är kända för att finnas och är kända för att köras på Microsoft Azure också. Microsoft testade dock inte dessa ramverk. Om du vill bygga konfigurationen med hög tillgänglighet med dessa ramverk måste du arbeta med leverantören av programvaran för att:

- Utveckla en distributionsarkitektur
- Utbyggnad av arkitekturen
- Stöd för arkitekturen

> [!IMPORTANT]
> Microsoft Azure Marketplace erbjuder en mängd olika mjuka enheter som tillhandahåller lagringslösningar utöver azure-inbyggt lagringsutrymme. Dessa mjuka enheter kan användas för att skapa NFS-resurser samt som teoretiskt kan användas i SAP HANA-utskalningsdistributioner där en standby-nod krävs. Av olika skäl stöds ingen av dessa mjuka lagringsinstallationer för någon av DBMS-distributionerna av Microsoft och SAP på Azure. Distributioner av DBMS på SMB-resurser stöds inte alls vid denna tidpunkt. Distributioner av DBMS på NFS-resurser är begränsade till NFS 4.1-resurser på [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Hög tillgänglighet för SAP Central Service
SAP Central Services är en andra enda felpunkt för din SAP-konfiguration. Som ett resultat skulle du behöva skydda dessa centraltjänstprocesser också. Erbjudandet som stöds och dokumenteras för SAP-arbetsbelastningen lyder som:

- Windows Redundansklusterserver med Windows Scale-out File Services för sapmnt och global transportkatalog. Detaljer beskrivs i artikeln:
    - [Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Förbereda Azure-infrastruktur för SAP-hög tillgänglighet med hjälp av ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Windows Failover Cluster Server med SMB-resurs baserat på [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/) för sapmnt och global transportkatalog. Detaljer visas i artikeln:
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files(SMB) för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows Redundansklusterserver `Datakeeper`baserad på SIOS . Även dokumenteras av Microsoft, behöver du en supportrelation med SIOS, så att du kan samarbeta med SIOS-stöd när du använder den här lösningen. Detaljer beskrivs i artikeln:
    - [Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Förbereda Azure-infrastrukturen för SAP HA med hjälp av ett Windows redundanskluster och delad disk för SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Pacemaker på SUSE operativsystem med att skapa en högtillgängliga `drdb` NFS-resurs med hjälp av två virtuella SUSE-datorer och för filreplikering. Detaljer dokumenteras i artikeln
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Pacemaker SUSE operativsystem med utnyttja NFS-resurser som tillhandahålls av [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Detaljer dokumenteras i
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker på Red Hat operativsystem med NFS-resurs värd på ett `glusterfs` kluster. Detaljer finns i artiklarna
    - [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`på Virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker på Red Hat operativsystem med NFS-resurs värd på [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Detaljer beskrivs i artikeln
    - [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Av de listade lösningarna behöver du en supportrelation `Datakeeper` med SIOS för att stödja produkten och för att samarbeta med SIOS direkt i händelse av problem. Beroende på hur du licensierade Windows, Red Hat och/eller SUSE OS kan du också behöva ha ett supportavtal med os-leverantören för att ha fullt stöd för de listade konfigurationerna med hög tillgänglighet.

Konfigurationen kan också visas som:

![DBMS- och ASCS HA-konfiguration](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

På höger sida av grafiken visas de mycket tillgängliga SAP Central Services. Förutom att ha SAP Central-tjänsterna skyddade med ett redundansklusterramverk som kan växla över i händelse av ett problem, finns det en nödvändighet för en högtillgänglig NFS- eller SMB-resurs, eller en Windows-delad disk för att se till att sapmnt och den globala transportkatalogen är oberoende av förekomsten av en enda virtuell dator. Ytterligare några av lösningarna, till exempel Windows Redunda Cluster Server och Pacemaker, kommer att kräva en Azure-belastningsutjämnare för att dirigera eller dirigera om trafik till en felfri nod.

I listan som visas finns det inget omnämnande av Oracle Linux-operativsystemet. Oracle Linux stöder inte Pacemaker som klusterramverk. Om du vill distribuera ditt SAP-system på Oracle Linux och du behöver ett ramverk med hög tillgänglighet för Oracle Linux måste du arbeta med tredjepartsleverantörer. En av leverantörerna är SIOS med sin Protection Suite för Linux som stöds av SAP på Azure. För mer information läs SAP note [#1662610 - Supportinformation för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610) för mer information.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Lagring som stöds med SAP Central Services-scenarierna ovan
Eftersom endast en delmängd av Azure-lagringstyper tillhandahåller högtillgängbara NFS- eller SMB-resurser som är kvalitet för användningen i våra SAP Central Services-klusterscenarier, en lista över lagringstyper som stöds

- Windows Failover Cluster Server med Windows Scale-out File Server kan distribueras på alla inbyggda Azure-lagringstyper, utom Azure NetApp-filer. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna servicenivåavtal i dataflöde och IOPS.
- Windows Failover Cluster Server med SMB på Azure NetApp-filer stöds på Azure NetApp-filer. SMB-resurser på Azure File-tjänster stöds **INTE** vid denna tidpunkt.
- Windows Failover Cluster Server med windows `Datakeeper` delad disk baserad på SIOS kan distribueras på alla inbyggda Azure-lagringstyper, utom Azure NetApp-filer. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna servicenivåavtal i dataflöde och IOPS.
- SUSE eller Red Hat Pacemaker med NFS-resurser på Azure NetApp-filer stöds på Azure NetApp-filer. 
- SUSE Pacemaker `drdb` med hjälp av en konfiguration mellan två virtuella datorer stöds med hjälp av inbyggda Azure-lagringstyper, förutom Azure NetApp-filer. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna servicenivåavtal i dataflöde och IOPS.
- Red Hat `glusterfs` Pacemaker som använder för att tillhandahålla NFS-resurs stöds med hjälp av inbyggda Azure-lagringstyper, förutom Azure NetApp-filer. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna servicenivåavtal i dataflöde och IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace erbjuder en mängd olika mjuka enheter som tillhandahåller lagringslösningar utöver azure-inbyggt lagringsutrymme. Dessa mjuka apparater kan användas för att skapa NFS eller SMB-aktier samt som teoretiskt kan användas i redundanskluster klustrade SAP Central Services också. Dessa lösningar stöds inte direkt för SAP-arbetsbelastning av Microsoft. Om du bestämmer dig för att använda en sådan lösning för att skapa din NFS- eller SMB-resurs måste stöd för SAP Central Service-konfigurationen tillhandahållas av den tredje part som äger programvaran i den mjuka lagringsverktyget.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Redundanskluster för multi-SID SAP-tjänster
För att minska antalet virtuella datorer som behövs i stora SAP-landskap gör SAP det möjligt att köra SAP Central Services-instanser av flera olika SAP-system i redundansklusterkonfiguration. Föreställ dig fall där du har 30 eller fler NetWeaver eller S/4HANA produktionssystem. Utan multi-SID-kluster kräver dessa konfigurationer 60 eller fler virtuella datorer i 30 eller fler klusterkonfigurationer för Windows eller Pacemaker. Förutom DBMS redundanskluster som behövs. Distribuera flera SAP centrala tjänster över två noder i en redundanskluster konfiguration kan minska antalet virtuella datorer avsevärt. Men distribuera flera SAP Central-tjänster instanser på en enda två nod kluster konfiguration har också vissa nackdelar. Problem runt en enda virtuell dator i klusterkonfigurationen gäller för flera SAP-system. Underhåll på gästoperativsystemet som körs i klusterkonfigurationen kräver mer samordning eftersom flera SAP-produktions-SAP-system påverkas. Verktyg som SAP LaMa stöder inte multi-SID klustring i deras system kloningsprocessen.

På Azure stöds en klusterkonfiguration med flera SID för Windows-operativsystemet med ENSA1 och ENSA2. Rekommendationen är inte att kombinera den äldre enqueue Replication Service-arkitekturen (ENSA1) med den nya arkitekturen (ENSA2) på ett multi-SID-kluster. Detaljer om en sådan arkitektur dokumenteras i artiklarna

- [SAP ASCS/SCS-instans med hög SID-sida med Windows Server Redundanskluster och delad disk på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [SAP ASCS/SCS-instans med hög SID-sida med Windows Server Redundanskluster och filresurs på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

För SUSE stöds även ett multi-SID-kluster baserat på Pacemaker. Hittills stöds konfigurationen för:

- Högst fem SAP ASCS/SCS-instanser
- Den gamla enqueue replikeringsserver isarkitektur (ENSA1)
- Pacemaker-klusterkonfigurationer för två noder

Konfigurationen dokumenteras i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program med flera SID-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Ett multi-SID-kluster med Enqueue Replication-server ser schematiskt ut

![DBMS- och ASCS HA-konfiguration](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA-utskalningsscenarier
SAP HANA-utskalningsscenarier stöds för en delmängd av de HANA-certifierade virtuella Azure-datorerna som anges i [SAP HANA-maskinvarukatalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Alla virtuella datorer som är markerade med "Ja" i kolumnen "Klustring" kan användas för antingen OLAP- eller S/4HANA-utskalning. Konfigurationer utan vänteläge stöds med Azure Storage-typerna av: 

- Azure Premium Storage, inklusive Azure Write accelerator för /hana/log-volymen
- [Ultradisk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA-utskalningskonfigurationer för OLAP eller S/4HANA med standby-nod stöds exklusivt med NFS-delad värdbaserad på Azure NetApp-filer.

Mer information om exakta lagringskonfigurationer med eller utan standby-nod finns i artiklarna:

- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [SAP-supportanteckning #2080991](https://launchpad.support.sap.com/#/notes/2080991)

För information om HANA Stora instanser som stöds HANA utskalning konfigurationer, följande dokumentation gäller:

- [Scenarier som stöds för HANA Large Instances scale-out med vänteläge](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Scenarier som stöds för HANA Large Instances scale-out utan vänteläge](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenario för katastrofåterställning
Det finns en mängd olika scenarier för haveriberedskap som stöds. Vi definierar katastrofarkitekturer som arkitekturer som bör kompensera för en komplett Azure-regioner som går utanför nätet. Det innebär att vi behöver att katastrofåterställningsmålet ska vara en annan Azure-region som mål för att köra ditt SAP-landskap. Vi separerar metoder och konfigurationer i DBMS-skikt och icke-DBMS-lager. 

### <a name="dbms-layer"></a>DBMS-lager
För DBMS-lagret stöds konfigurationer med dbms-inbyggda replikeringsmekanismer, som Alltid på, Oracle Data Guard, Db2 HADR, SAP ASE Always-On eller HANA System Replication. Det är obligatoriskt att replikeringsströmmen i sådana fall är asynkron, i stället för synkron som i typiska scenarier med hög tillgänglighet som distribueras inom en enda Azure-region. Ett typiskt exempel på en sådan dbms-haveriberedskapskonfiguration som stöds beskrivs i artikeln [SAP HANA-tillgänglighet i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions). Den andra bilden i det avsnittet beskriver ett scenario med HANA som ett exempel. De viktigaste databaserna som stöds för SAP-program kan alla distribueras i ett sådant scenario.

Det stöds för att använda en mindre virtuell dator som målinstans i regionen för haveriberedskap eftersom den virtuella datorn inte upplever fullständig arbetsbelastningstrafik. Om du gör det måste du tänka på följande:

- Mindre vm-typer tillåter inte att många diskar är anslutna än mindre virtuella datorer
- Mindre virtuella datorer har mindre nätverks- och lagringsdataflöde
- Storleksändring mellan virtuella vm-familjer kan vara ett problem när de olika virtuella datorerna samlas in i en Azure-tillgänglighetsuppsättning eller när storleksändringen ska ske mellan M-serien och Mv2-familjen med virtuella datorer
- CPU och minnesförbrukning för databasinstansen att kunna ta emot strömmen av ändringar med minimal fördröjning och tillräckligt med CPU och minnesresurser för att tillämpa dessa ändringar med minimal fördröjning på data  

Mer information om begränsningar av olika VM-storlekar finns [här](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

En annan metod som stöds för att distribuera ett DR-mål är att ha en andra DBMS-instans installerad på en virtuell dator som kör en DBMS-instans som inte är produktion av en ICKE-produktions-SAP-instans. Detta kan vara lite mer utmanande eftersom du måste ta reda på vad på minne, CPU-resurser, nätverksbandbredd och lagringsbandbredd behövs för de specifika målinstanserna som ska fungera som huvudinstans i DR-scenariot. Speciellt i HANA rekommenderar vi starkt att du konfigurerar den instans som fungerar som DR-mål på en delad värd så att data inte läses in i DR-målinstansen.

För HANA Large Instance DR-scenarier kontrollera dessa dokument:

- [Enkel nod med DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Enkel nod med DR (multifunktion) med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Enkel nod med DR (multifunktion) med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Hög tillgänglighet med HSR och DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Skala ut med DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Enkel nod med DR med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Enkel nod HSR till DR (kostnadsoptimerad)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Hög tillgänglighet och haveriberedskap med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Hög tillgänglighet och haveriberedskap med HSR (kostnadsoptimerad)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Skala ut med DR med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Användning av [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) har inte testats för DBMS-distributioner under SAP-arbetsbelastning. Därför stöds det inte för DBMS-lagret av SAP-system vid denna tidpunkt. Andra replikeringsmetoder från Microsoft och SAP som inte finns med i listan stöds inte. Använda programvara från tredje part för att replikera DBMS-lagret av SAP-system mellan olika Azure-regioner, måste stödjas av leverantören av programvaran och kommer inte att stödjas via Microsoft- och SAP-supportkanaler. 
 
## <a name="non-dbms-layer"></a>Icke-DBMS-lager
För SAP-programlagret och eventuella resurser eller lagringsplatser som behövs utnyttjas de två huvudscenarierna av kunder:

- Katastrofåterställningsmålen i den andra Azure-regionen används inte för någon produktion eller icke-produktionsändamål. I det här fallet replikeras de virtuella datorerna som fungerar som katastrofåterställningsmål helst inte och avbildningen och ändringarna av avbildningarna av produktions-SAP-programlagret replikeras till katastrofåterställningsregionen. En funktion som kan utföra en sådan uppgift är [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). Azure Site Recovery stöder ett Azure-to-Azure-replikeringsscenarier som detta. 
- Katastrofåterställningsmålen är virtuella datorer som faktiskt används av icke-produktionssystem. Hela SAP-landskapet är fördelat på två olika Azure-regioner med produktionssystem vanligtvis i en region och icke-produktionssystem i en annan region. I många kunddistributioner har kunden ett icke-produktionssystem som motsvarar ett produktionssystem. Kunden har produktionsprograminstanser förinstallerade på programskiktet icke-produktionssystem. Vid en redundans skulle icke-produktionsinstanserna stängas av, de virtuella namnen på de virtuella produktions-datorerna flyttas till de virtuella datorerna för icke-produktion (efter att ha tilldelat nya IP-adresser i DNS) och de förinstallerade produktionsinstanserna har startats

### <a name="sap-central-services-clusters"></a>Kluster för SAP Central Services
SAP Central Services-kluster som använder delade diskar (Windows), SMB-resurser (Windows) eller NFS-resurser är lite svårare att replikera. På Windows-sidan är Windows Storage Replication en möjlig lösning. På Linux rsync är en hållbar lösning.



## <a name="non-supported-scenario"></a>Scenario som inte stöds
Det finns en lista över scenario, som inte stöds för SAP-arbetsbelastning på Azure-arkitekturer. **Stöds inte** innebär att SAP och Microsoft inte kommer att kunna stödja dessa konfigurationer och måste skjuta upp till en eventuell inblandad tredje part som tillhandahåller programvara för att upprätta sådana arkitekturer. Två av kategorierna är:

- Mjuka lagringsenheter: Det finns ett antal mjuka lagringsinstallationer som erbjuds på Azure Marketplace. Några av leverantörerna erbjuder egen dokumentation om hur du använder dessa lagrings mjuka enheter på Azure som är relaterade till SAP-programvara. Stöd för konfigurationer eller distributioner som involverar sådana mjuka lagringsenheter måste tillhandahållas av leverantören av dessa mjuka lagringsapparater. Detta faktum manifesteras också i [SAP-stödanteckning #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Ramverk med hög tillgänglighet: Endast Pacemaker och Windows Server Redundanskluster stöds med hög tillgänglighet för SAP-arbetsbelastning på Azure. Som tidigare nämnts beskrivs `Datakeeper` och dokumenteras lösningen av SIOS av Microsoft. Ändå måste komponenterna `Datakeeper` i SIOS stödjas genom SIOS som leverantör som tillhandahåller dessa komponenter. SAP listade även andra certifierade ramverk med hög tillgänglighet i olika SAP-anteckningar. Några av dem certifierades av tredjepartsleverantören för Azure också. Support för konfigurationer som använder dessa produkter måste dock tillhandahållas av produktleverantören. Olika leverantörer har olika integrering i SAP-supportprocesserna. Du bör klargöra vilken supportprocess som fungerar bäst för den specifika leverantören innan du bestämmer dig för att använda produkten i SAP-konfigurationer som distribueras på Azure.
- Delade diskkluster där databasfiler finns på de delade diskarna stöds inte med undantag för maxDB. För alla andra databaser är den lösning som stöds att ha separata lagringsplatser i stället för en SMB- eller NFS-resurs eller delad disk för att konfigurera scenarier med hög tillgänglighet

Andra scenarier som inte stöds är scenarier som:

- Distributionsscenarier som introducerar en större nätverksfördröjning mellan SAP-programnivån och SAP DBMS-nivån i SAP:s gemensamma `Hybris`arkitektur som visas i NetWeaver, S/4HANA och t.ex. Det här omfattar:
    - Distribuera en av nivåerna lokalt medan den andra nivån distribueras i Azure
    - Distribuera SAP-programnivån för ett system i en annan Azure-region än DBMS-nivån
    - Distribuera en nivå i datacenter som finns samtidigt till Azure och den andra nivån i Azure, utom när ett sådant arkitekturmönster tillhandahålls av en inbyggd Azure-tjänst
    - Distribuera virtuella nätverksinstallationer mellan SAP-programnivån och DBMS-lagret
    - Utnyttja lagring som finns i datacenter som finns i Azure-datacenter för SAP DBMS-nivån eller SAP:s globala transportkatalog
    - Distribuera de två lagren med två olika molnleverantörer. Till exempel distribuera DBMS-nivån i Oracles molninfrastruktur och programnivån i Azure
- Fler instanser av HANA Pacemaker-klusterkonfigurationer
- Windows-klusterkonfigurationer med delade diskar via SOFS eller SMB i ANF för SAP-databaser som stöds i Windows. Istället rekommenderar vi användning av inbyggd hög tillgänglighet replikering av de särskilda databaserna och använda separata lagringsstackar
- Distribution av SAP-databaser som stöds på Linux med databasfiler som finns i NFS-resurser ovanpå ANF med undantag för SAP HANA
- Distribution av Oracle DBMS på något annat gäst-OS än Windows och Oracle Linux. Se även [SAP-support #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenario(er) att vi inte testa och därför har ingen erfarenhet av listan som:

- Azure Site Recovery replikera DBMS lager virtuella datorer. Därför rekommenderar vi att du använder databasens inbyggda asynkrona replikeringsfunktioner för potentiell konfiguration för haveriberedskap
 

## <a name="next-steps"></a>Efterföljande moment
Läs nästa steg i [azure virtual machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



