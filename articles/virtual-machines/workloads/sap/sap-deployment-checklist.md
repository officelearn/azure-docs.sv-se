---
title: Checklista för planering och distribution av SAP-arbetsbelastning | Microsoft-dokument
description: Checklista för planering av SAP-arbetsbelastningsdistributioner till Azure och distribution av arbetsbelastningar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060070"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-arbetsbelastningar på Azure: checklista för planering och distribution

Den här checklistan är utformad för kunder som flyttar SAP NetWeaver-, S/4HANA- och Hybris-program till Azure-infrastruktur som en tjänst. Under projektets löptid bör en kund och/eller SAP-partner granska checklistan. Det är viktigt att notera att många av kontrollerna är slutförda i början av projektet och under planeringsfasen. När distributionen är klar kan enkla ändringar på distribuerad Azure-infrastruktur eller SAP-programvaruversioner bli komplexa.

Granska checklistan vid viktiga milstolpar under projektet. Om du gör det kan du upptäcka små problem innan de blir stora problem. Du har också tillräckligt med tid för att omforma och testa nödvändiga ändringar. Tänk inte på den här checklistan som den här checklistan är fullständig. Beroende på din situation kan du behöva utföra många fler kontroller.

Checklistan innehåller inte uppgifter som är oberoende av Azure. Sap-programgränssnitt ändras till exempel under en flytt till Azure-plattformen eller till en värdleverantör.

Den här checklistan kan också användas för system som redan har distribuerats. Nya funktioner, till exempel Skrivaccelerator och tillgänglighetszoner, och nya vm-typer kan ha lagts till sedan du distribuerade. Så det är användbart att granska checklistan med jämna mellanrum för att säkerställa att du är medveten om nya funktioner i Azure-plattformen.

## <a name="project-preparation-and-planning-phase"></a>Projektförberedelse- och planeringsfas
Under den här fasen planerar du migreringen av din SAP-arbetsbelastning till Azure-plattformen. Under den här fasen måste du åtminstone skapa följande dokument och definiera och diskutera följande element i migreringen:

1. Designdokument på hög nivå. Detta dokument bör innehålla:
    - Den aktuella inventeringen av SAP-komponenter och -program och ett målprograminventering för Azure.
    - En ansvarsfördelningsmatris (RACI) som definierar de berörda parternas ansvar och uppdrag. Börja på en hög nivå och arbeta till mer detaljerade nivåer under hela planeringen och de första distributionerna.
    - En lösningsarkitektur på hög nivå.
    - Ett beslut om vilka Azure-regioner som ska distribueras till. Se [listan över Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). Information om vilka tjänster som är tillgängliga i varje region finns i Produkter som är [tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/).
    - En nätverksarkitektur för att ansluta från lokalt till Azure. Börja bekanta dig med virtual [datacenter-skissen för Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    - Säkerhetsprinciper för att köra affärsdata med hög påverkan i Azure. Om du vill veta mer om datasäkerhet börjar du med [Azure-säkerhetsdokumentationen](https://docs.microsoft.com/azure/security/).
2.  Tekniskt designdokument. Detta dokument bör innehålla:
    - Ett blockdiagram för lösningen.
    - Storleksändring av beräknings-, lagrings- och nätverkskomponenter i Azure. Information om SAP-storlek för virtuella Azure-datorer finns i [SAP-supportanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Arkitektur för kontinuitet och katastrofåterställning.
    - Detaljerad information om supportpaketversioner för OPERATIVSYSTEM, DB, kernel och SAP. Det är inte nödvändigtvis sant att varje OS-version som stöds av SAP NetWeaver eller S/4HANA stöds på virtuella Azure-datorer. Detsamma gäller för DBMS-versioner. Kontrollera följande källor för att justera och vid behov uppgradera SAP-versioner, DBMS-versioner och OS-versioner för att säkerställa SAP- och Azure-support. Du måste ha versionskombinationer som stöds av SAP och Azure för att få fullt stöd från SAP och Microsoft. Om det behövs måste du planera för att uppgradera vissa programvarukomponenter. Mer information om SAP-, OS- och DBMS-programvara som stöds dokumenteras här:
        - [SAP-stödanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533). Den här anteckningen definierar de minsta OS-versioner som stöds på virtuella Azure-datorer. Den definierar också de minsta databasversioner som krävs för de flesta icke-HANA-databaser. Slutligen ger det SAP-storlek för SAP-stödda Azure VM-typer.
        - [SAP-stödanteckning #2015553](https://launchpad.support.sap.com/#/notes/2015553). Den här anteckningen definierar supportprinciper kring Azure-lagring och supportrelation som behövs med Microsoft.
        - [SAP-stödanteckning #2039619](https://launchpad.support.sap.com/#/notes/2039619). Den här anteckningen definierar Oracle-supportmatrisen för Azure. Oracle stöder endast Windows och Oracle Linux som gästoperativsystem på Azure för SAP-arbetsbelastningar. Den här supportsatsen gäller även för SAP-programlagret som kör SAP-instanser. Oracle stöder dock inte hög tillgänglighet för SAP Central Services i Oracle Linux via Pacemaker. Om du behöver hög tillgänglighet för ASCS på Oracle Linux måste du använda SIOS Protection Suite för Linux. Detaljerad SAP-certifieringsdata finns i [SAP-supportanteckning #1662610 - Supportinformation för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610). För Windows stöds den SAP-stödda Windows Server Failover-klusterlösningen för SAP Central Services tillsammans med Oracle som DBMS-lager.
        - [SAP-stödanteckning #2235581](https://launchpad.support.sap.com/#/notes/2235581). Den här anteckningen ger supportmatrisen för SAP HANA på olika OS-versioner.
        - SAP HANA-stödda virtuella Azure-datorer och [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) finns listade på [SAP-webbplatsen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [SAP-produkttillgänglighetsmatris](https://support.sap.com/en/).
        - [SAP-support #2555629 – SAP HANA 2.0 Dynamisk nivåindelning – Hypervisor- och molnsupport](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP-supportanteckning #1662610 - Supportinformation för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-anteckningar för andra SAP-specifika produkter.     
    - Vi rekommenderar strikta trestegskonstruktioner för SAP-produktionssystem. Vi rekommenderar inte att du kombinerar ASCS- och/eller DBMS- och/eller appservrar på en virtuell dator. Använda klusterkonfigurationer med flera SID för SAP Central Services stöds på Windows gästoperativsystem på Azure. Men den här konfigurationen stöds inte för SAP Central Services på Linux-operativsystem på Azure. Du hittar dokumentation för Windows gäst-OS-scenariot i följande artiklar:
        - [SAP ASCS/SCS-instans med hög SID-sida med Windows Server Redundanskluster och delad disk på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [SAP ASCS/SCS-instans med hög SID-sida med Windows Server Redundanskluster och filresurs på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Arkitektur med hög tillgänglighet och haveriberedskap.
        - Baserat på RTO och RPO definierar du hur arkitekturen för hög tillgänglighet och katastrofåterställning behöver se ut.
        - För hög tillgänglighet inom en zon, kontrollera vad den önskade DBMS har att erbjuda i Azure. De flesta DBMS-paket erbjuder synkrona metoder för en synkron hot standby, som vi rekommenderar för produktionssystem. Kontrollera också SAP-relaterad dokumentation för olika databaser, som börjar med [överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) och relaterade dokument.
           Det går inte att använda Windows Server Failover-kluster med en delad diskkonfiguration för DBMS-lagret som till exempel [beskrivs för SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017). Använd istället lösningar som:
           - [Ständig aktivering av SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - För haveriberedskap i Azure-regioner kan du granska de lösningar som erbjuds av olika DBMS-leverantörer. De flesta av dem har stöd för asynkron replikering eller loggfrakt.
        - För SAP-programlagret, ta reda på om du ska köra dina affärsregressionstestsystem, som helst är repliker av dina produktionsdistributioner, i samma Azure-region eller i din DR-region. I det andra fallet kan du rikta in dig på affärsregressionssystemet som DR-mål för dina produktionsdistributioner.
        - Om du bestämmer dig för att inte placera icke-produktionssystemen på DR-platsen, titta på Azure Site Recovery som en metod för att replikera SAP-programlagret till Azure DR-regionen. Mer information finns i en [Konfigurera haveriberedskap för en distribution av SAP NetWeaver-appar på flera nivåer](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap).
        - Om du bestämmer dig för att använda en kombinerad HADR-konfiguration med hjälp av [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview)kan du bekanta dig med De Azure-regioner där tillgänglighetszoner är tillgängliga. Ta också hänsyn till begränsningar som kan införas genom ökade nätverksfördydningar mellan två tillgänglighetszoner.  
3.  En inventering av alla SAP-gränssnitt (SAP och icke-SAP).
4.  Utformning av grundläggande tjänster. Denna design bör innehålla följande punkter:
    - Active Directory- och DNS-design.
    - Nätverkstopologi inom Azure och tilldelning av olika SAP-system.
    - [Rollbaserad åtkomststruktur](https://docs.microsoft.com/azure/role-based-access-control/overview) för team som hanterar infrastruktur- och SAP-program i Azure.
    - Resursgruppstopologi.
    - [Taggning strategi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Namngivningskonventioner för virtuella datorer och andra infrastrukturkomponenter och/eller logiska namn.
5.  Microsoft Premier Support-kontrakt. Identifiera din Microsoft Technical Account Manager (TAM). För SAP-supportkrav finns i [SAP-supportanteckning #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Antalet Azure-prenumerationer och kärnkvot för prenumerationerna. [Öppna supportbegäranden för att öka kvoterna för Azure-prenumerationer](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) efter behov.
7.  Datareducering och datamigreringsplan för att migrera SAP-data till Azure. För SAP NetWeaver-system har SAP riktlinjer för hur du begränsar volymen av stora mängder data. Se [den här SAP-guiden](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) om datahantering i SAP-affärssystem. En del av innehållet gäller även NetWeaver- och S/4HANA-system i allmänhet.
8.  En automatiserad distributionsmetod. Målet med automatisering av infrastrukturdistributioner på Azure är att distribuera på ett deterministiskt sätt och få deterministiska resultat. Många kunder använder PowerShell- eller CLI-baserade skript. Men det finns olika tekniker med öppen källkod som du kan använda för att distribuera Azure-infrastruktur för SAP och till och med installera SAP-programvara. Du hittar exempel på GitHub:
    - [Automatiserade SAP-distributioner i Azure Cloud](https://github.com/Azure/sap-hana)
    - [SAP HANA-installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definiera en regelbunden design- och distributionsgranskningskadens mellan dig som kund, systemintegratör, Microsoft och andra berörda parter.

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotfas (rekommenderas starkt)
 
Du kan köra en pilot före eller under projektplanering och förberedelse. Du kan också använda pilotfasen för att testa metoder och konstruktioner som gjorts under planerings- och förberedelsefasen. Och du kan utöka pilotfasen för att göra det till ett verkligt konceptbevis.

Vi rekommenderar att du ställer in och validerar en fullständig HADR-lösning och säkerhetsdesign under en pilotdistribution. Vissa kunder utför skalbarhetstester under den här fasen. Andra kunder använder distributioner av SAP-sandlådesystem som en pilotfas. Vi antar att du redan har identifierat ett system som du vill migrera till Azure för piloten.

1. Optimera dataöverföring till Azure. Det optimala valet är mycket beroende av det specifika scenariot. Överföring från lokalt via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) är snabbast om ExpressRoute-kretsen har tillräckligt med bandbredd. I andra scenarier är det snabbare att överföra via Internet.
2. För en heterogen SAP-plattformsmigrering som innebär export och import av data testar och optimerar du export- och importfaserna. För stora migreringar där SQL Server är målplattformen kan du hitta [rekommendationer](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Du kan använda Migreringsövervakaren/SWPM om du inte behöver en kombinerad versionsuppgradering. Du kan använda [SAP DMO-processen](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) när du kombinerar migreringen med en SAP-versionsuppgradering. För att göra det måste du uppfylla vissa krav för kombinationen av källa och mål-DBMS-plattform. Den här processen dokumenteras i [Alternativ för databasmigrering (DMO) på SUMMA 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportera till källa, exportera filöverföring till Azure och importera prestanda. Maximera överlappningen mellan export och import.
   2.  Utvärdera databasens volym på mål- och målplattformarna för infrastrukturstorlek.
   3.  Validera och optimera timingen.
1. Teknisk validering.
   1. Typer av virtuella datorer.
        - Granska resurserna i SAP-supportanteckningar, i SAP HANA-maskinvarukatalogen och i SAP PAM igen. Kontrollera att det inte finns några ändringar i virtuella datorer som stöds för Azure, os-versioner som stöds för dessa VM-typer och att SAP- och DBMS-versioner stöds.
        - Verifiera igen storleken på ditt program och den infrastruktur som du distribuerar på Azure. Om du flyttar befintliga program kan du ofta härleda de nödvändiga SAPS-programmen från den infrastruktur du använder och webbsidan för [SAP-benchmark](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) och jämföra den med SAPS-numren i [SAP-supportanteckningen #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tänk också [på den här artikeln om SAPS-klassificeringar.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - Utvärdera och testa storleken på dina virtuella Azure-datorer med avseende på maximalt lagringsdataflöde och nätverksdataflöde för de VM-typer som du valde under planeringsfasen. Du hittar data här:
           -  [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på *max uncached diskdataflöde* för storlek.
           -  [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på *max uncached diskdataflöde* för storlek.
   2. Lagring.
        - Använd åtminstone [Azure Standard SSD-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) för virtuella datorer som representerar SAP-programlager och för distribution av DBMS som inte är prestandakänsliga.
        - I allmänhet rekommenderar vi inte användning av [Azure Standard HDD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Använd [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) för alla virtuella DBMS-datorer som är prestandakänsliga på distans.
        - Använd [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/).
        - Använd Azure Write Accelerator för DBMS-loggenheter med M-serien. Var medveten om skrivacceleratorgränser och användning, vilket dokumenteras i [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - För de olika DBMS-typerna, kontrollera den [generiska SAP-relaterade DBMS-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) och den DBMS-specifika dokumentation som det allmänna dokumentet pekar på.
        - Mer information om SAP HANA finns i [SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Montera aldrig Azure-datadiskar på en Virtuell Azure Linux med hjälp av enhets-ID. Använd i stället den universellt unika identifieraren (UUID). Var försiktig när du använder grafiska verktyg för att montera Azure-datadiskar, till exempel. Dubbelkolla posterna i /etc/fstab för att se till att UUID används för att montera diskarna. Du hittar mer information i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Nätverk.
        - Testa och utvärdera din virtuella nätverksinfrastruktur och distributionen av dina SAP-program över eller inom de olika virtuella Azure-nätverken.
        -  Utvärdera hub-and-spoke virtuell nätverksarkitektur metod eller mikrosegmentering metod inom en enda Azure virtuellt nätverk. Basera denna utvärdering på:
               1. Kostnader för datautbyte mellan [peered Azure virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Information om kostnader finns i [Prissättning för virtuellt nätverk](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Fördelar med en snabb frånkoppling av peering mellan Virtuella Azure-nätverk i stället för att ändra nätverkssäkerhetsgruppen för att isolera ett undernät i ett virtuellt nätverk. Den här utvärderingen är för fall där program eller virtuella datorer som finns i ett undernät till det virtuella nätverket blev en säkerhetsrisk.
                3. Central loggning och granskning av nätverkstrafik mellan lokala, omvärlden och det virtuella datacenter som du har skapat i Azure.
        - Utvärdera och testa datasökvägen mellan SAP-programlagret och SAP DBMS-lagret.
            -  Placering av [virtuella Azure-nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationssökvägen mellan SAP-programmet och DBMS-lagret av SAP-system baserat på SAP NetWeaver, Hybris eller S/4HANA stöds inte.
            -  Placering av SAP-programlagret och SAP DBMS i olika virtuella Azure-nätverk som inte är peer-intämda stöds inte.
            -  Du kan använda [programsäkerhetsgrupp och nätverkssäkerhetsgruppsregler](https://docs.microsoft.com/azure/virtual-network/security-overview) för att definiera vägar mellan SAP-programlagret och SAP DBMS-lagret.
        - Kontrollera att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-programlagret och SAP DBMS-lagret. Tänk på att olika os-nivåer behövs för att stödja accelererade nätverk i Azure:
            - Windows Server 2012 R2 eller senare.
            - SUSE Linux 12 SP3 eller senare.
            - RHEL 7.4 eller senare.
            - Oracle Linux 7.5. Om du använder RHCKL-kärnan krävs 3.10.0-862.13.1.el7. Om du använder Oracle UEK-kärnan krävs version 5.
        - Testa och utvärdera nätverksfördröjningen mellan virtuella datorer för SAP-programlager och virtuella DBMS-datorer enligt SAP-stödanteckningar [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten mot riktlinjerna för nätverksfördröjning i [SAP-stödanteckning #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverksfördröjningen bör vara i det måttliga eller goda intervallet. Undantag gäller för trafik mellan virtuella datorer och STORA HANA-instansenheter, vilket dokumenteras i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Kontrollera att ILB-distributioner är konfigurerade för att använda Direkt serverretur. Den här inställningen minskar svarstiden när Azure ILBs används för konfigurationer med hög tillgänglighet på DBMS-lagret.
        - Om du använder Azure Load Balancer tillsammans med Linux gästoperativsystem kontrollerar du att Linux-nätverksparametern **net.ipv4.tcp_timestamps** är inställd på **0**. Den här rekommendationen står i konflikt med rekommendationer i äldre versioner av [SAP-#2382421](https://launchpad.support.sap.com/#/notes/2382421). SAP-anteckningen uppdateras nu för att ange att den här parametern måste ställas in på **0** för att fungera med Azure-belastningsutjämnare.
        - Överväg att använda [Azure-närhetsplaceringsgrupper](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) för att få optimal nätverksfördröjning. Mer information finns i [Azure proximity placement groups for optimal network lateency with SAP applications](sap-proximity-placement-scenarios.md).
   4. Distributioner av hög tillgänglighet och haveriberedskap.
        - Om du distribuerar SAP-programlagret utan att definiera en specifik Azure-tillgänglighetszon kontrollerar du att alla virtuella datorer som kör SAP-dialoginstanser eller middleware-instanser av ett enda SAP-system distribueras i en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Om du inte behöver hög tillgänglighet för SAP Central Services och DBMS kan du distribuera dessa virtuella datorer till samma tillgänglighetsuppsättning som SAP-programlagret.
        - Om du skyddar SAP Central Services och DBMS-lagret för hög tillgänglighet med hjälp av passiv replikering placerar du de två noderna för SAP Central Services i en separat tillgänglighetsuppsättning och de två DBMS-noderna i en annan tillgänglighetsuppsättning.
        - Om du distribuerar till Azure-tillgänglighetszoner kan du inte använda tillgänglighetsuppsättningar. Men du måste se till att du distribuerar de aktiva och passiva centraltjänstnoderna i två olika tillgänglighetszoner. Använd tillgänglighetszoner som har den lägsta svarstiden mellan sig.
          Tänk på att du måste använda [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) för användningsfallet för att upprätta Windows- eller Pacemaker-redundanskluster för DBMS- och SAP Central Services-lagret över tillgänglighetszoner. Du kan inte använda [Grundläggande belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) för zondistributioner.
   5. Timeout-inställningar.
        - Kontrollera SAP NetWeaver-utvecklarspårningarna av SAP-instanserna för att se till att det inte finns några anslutningsavbrott mellan enqueue-servern och SAP-arbetsprocesserna. Du kan undvika dessa anslutningsavbrott genom att ange dessa två registerparametrar:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Mer information finns i [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Mer information finns i [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Om du vill undvika GUI-timeout mellan lokala SAP GUI-gränssnitt och SAP-programlager som distribueras i Azure kontrollerar du om dessa parametrar anges i standard-pfl- eller instansprofilen:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - För att förhindra avbrott i etablerade anslutningar mellan SAP-enqueue-processen och SAP-arbetsprocesserna måste du ange parametern **enque/encni/set_so_keepalive** till **true**. Se även [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Om du använder en Windows redundansklusterkonfiguration kontrollerar du att tiden för att reagera på icke-responsiva noder är korrekt inställd för Azure. I artikeln [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) visas parametrar och hur de påverkar redundanskänsligheterna. Förutsatt att klusternoderna finns i samma undernät bör du ändra dessa parametrar:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Os-inställningar eller korrigeringar
        - För att köra HANA på SAP, läs dessa anteckningar och dokumentationer:
            -   [SAP-supportanteckning #2814271 - SAP HANA Backup misslyckas på Azure med Checksum-fel](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP-stödanteckning #2753418 - Potentiell prestandaförsämring på grund av timerfallback](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP-supportanteckning #2791572 - Prestandaförsämring på grund av att VDSO-stöd saknas för Hyper-V i Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP-supportanteckning #2382421 - Optimera nätverkskonfigurationen på HANA- och OS-nivå](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP-supportanteckning #2694118 - Red Hat Enterprise Linux HA-tillägg på Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP-supportanteckning #1984787 - SUSE LINUX Enterprise Server 12: Installationsanteckningar](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP-supportanteckning #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP-supportanteckning #2292690 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP-supportanteckning #2772999 - Red Hat Enterprise Linux 8.x: Installation och konfiguration](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP-supportanteckning #2777782 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP-supportanteckning #2578899 - SUSE Linux Enterprise Server 15: Installationsanteckning](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP-stödanteckning #2455582 - Linux: Köra SAP-program som sammanställts med GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP-supportanteckning #2729475 - HWCCT misslyckades med fel "Hypervisor stöds inte" på Azure-virtuella datorer certifierade för SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Testa dina rutiner för hög tillgänglighet och haveriberedskap.
   1. Simulera redundanssituationer genom att stänga av virtuella datorer (Windows gästoperativsystem) eller sätta operativsystem i panikläge (Linux gästoperativsystem). Det här steget hjälper dig att ta reda på om dina redundanskonfigurationer fungerar som de är utformade.
   1. Mät hur lång tid det tar att köra en redundans. Om tiderna är för långa, tänk på:
        - För SUSE Linux använder du SBD-enheter i stället för Azure Fence-agenten för att snabba upp redundansen.
        - För SAP HANA, om omladdningen av data tar för lång tid, överväg att etablera mer lagringsbandbredd.
   3. Testa din säkerhetskopiering / återställning sekvens och timing och göra korrigeringar om du behöver. Se till att säkerhetskopieringstiderna är tillräckliga. Du måste också testa återställnings- och tidsåterställningsaktiviteterna. Kontrollera att återställningstider finns i dina RTO-sla där din RTO förlitar sig på en återställningsprocess för en databas eller virtuell dator.
   4. Testa dr-funktioner och arkitektur över flera regioner.
1. Säkerhetskontroller.
   1. Testa giltigheten för din RBAC-arkitektur (Azure Role-based Access Control). Målet är att separera och begränsa åtkomst och behörighet för olika team. Sap Basis-gruppmedlemmar bör till exempel kunna distribuera virtuella datorer och tilldela diskar från Azure Storage till ett visst virtuellt Azure-nätverk. Men SAP Basis-teamet bör inte kunna skapa sina egna virtuella nätverk eller ändra inställningarna för befintliga virtuella nätverk. Medlemmar i nätverksteamet bör inte kunna distribuera virtuella datorer till virtuella nätverk där SAP-program och DBMS-datorer körs. Medlemmar i det här teamet bör inte heller kunna ändra attribut för virtuella datorer eller ens ta bort virtuella datorer eller diskar.  
   1.  Kontrollera att [nätverkssäkerhetsgruppen och ASC-reglerna](https://docs.microsoft.com/azure/virtual-network/security-overview) fungerar som förväntat och skydda de skyddade resurserna.
   1.  Se till att alla resurser som behöver krypteras är krypterade. Definiera och implementera processer för att säkerhetskopiera certifikat, lagra och komma åt dessa certifikat och återställa de krypterade entiteterna.
   1.  Använd [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) för OS-diskar där det är möjligt från en OS-stöd synvinkel.
   1.  Se till att du inte använder för många lager av kryptering. I vissa fall är det klokt att använda Azure Disk Encryption tillsammans med en av DBMS Transparent Data Encryption metoder för att skydda olika diskar eller komponenter på samma server.  På en SAP DBMS-server kan till exempel Azure Disk Encryption (ADE) aktiveras på startdisken för operativsystemet (om operativsystemet stöder ADE) och de datadiskar som inte används av DBMS-databeständighetsfilerna.  Ett exempel är att använda ADE på disken som innehåller DBMS TDE-krypteringsnycklar.
1. Prestandatestning. I SAP, baserat på SAP-spårning och mätningar, gör du följande jämförelser:
   - I förekommande fall kan du jämföra de 10 bästa onlinerapporterna med din nuvarande implementering.
   - I förekommande fall, jämför de 10 bästa batchjobben med din nuvarande implementering.
   - Jämför dataöverföringar via gränssnitt i SAP-systemet. Fokusera på gränssnitt där du vet att överföringen går mellan olika platser, till exempel från lokalt till Azure.


## <a name="non-production-phase"></a>Icke-produktionsfas 
I den här fasen antar vi att du efter en lyckad pilot eller proof of concept (POC) börjar distribuera SAP-system som inte är produktion till Azure. Införliva allt du lärt dig och upplevt under POC till den här distributionen. Alla kriterier och steg som anges för POC gäller även för den här distributionen.

Under den här fasen distribuerar du vanligtvis utvecklingssystem, enhetstestsystem och affärsregressionstestningssystem till Azure. Vi rekommenderar att minst ett icke-produktionssystem i en SAP-programrad har den fullständiga konfiguration med hög tillgänglighet som det framtida produktionssystemet kommer att ha. Här är några ytterligare steg som du behöver slutföra under den här fasen:  

1.  Innan du flyttar system från den gamla plattformen till Azure samlar du in resursförbrukningsdata, till exempel CPU-användning, lagringsdataflöde och IOPS-data. Samla särskilt in dessa data från DBMS-lagerenheterna, men också samla in dem från programlagerenheterna. Mät även nätverks- och lagringsfördröjning.
2.  Registrera tillgänglighetsanvändningstidmönstren för dina system. Målet är att ta reda på om icke-produktionssystem måste vara tillgängliga hela dagen, varje dag eller om det finns icke-produktionssystem som kan stängas under vissa faser av en vecka eller månad.
3.  Testa och ta reda på om du vill skapa egna OS-avbildningar för dina virtuella datorer i Azure eller om du vill använda en avbildning från Azure Shared Image Gallery. Om du använder en bild från det delade bildgalleriet måste du använda en avbildning som återspeglar supportavtalet med os-leverantören. För vissa os-leverantörer kan du med Shared Image Gallery ta med egna licensavbildningar. För andra OS-avbildningar ingår support i priset som anges av Azure. Om du bestämmer dig för att skapa egna OS-bilder kan du hitta dokumentation i följande artiklar:
    -   [Skapa en allmän avbildning av en Windows-virtuell dator som distribueras i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Skapa en generaliserad avbildning av en virtuell Linux-dator som distribueras i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Om du använder SUSE- och Red Hat Linux-avbildningar från det delade bildgalleriet måste du använda avbildningarna för SAP som tillhandahålls av Linux-leverantörerna i det delade bildgalleriet.
4.  Se till att uppfylla SAP-supportkraven för Microsofts supportavtal. Se [SAP-supportanteckning #2015553](https://launchpad.support.sap.com/#/notes/2015553). För stora HANA-instanser finns i [Onboarding-krav](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Se till att rätt personer får [planerade underhållsmeddelanden](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) så att du kan välja de bästa driftstoppen.
5.  Kontrollera ofta om det finns Azure-presentationer på kanaler som [Kanal 9](https://channel9.msdn.com/) efter nya funktioner som kan gälla för dina distributioner.
6.  Kontrollera SAP-anteckningar relaterade till Azure, till [exempel supportanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533), för nya virtuella SKU:er och nyligen stödda OS- och DBMS-versioner. Jämför prissättningen för nya vm-typer med äldre vm-typer, så att du kan distribuera virtuella datorer med det bästa förhållandet mellan pris och prestanda.
7.  Kontrollera SAP-supportanteckningar igen, SAP HANA-maskinvarukatalogen och SAP PAM. Kontrollera att det inte fanns några ändringar i virtuella datorer som stöds för Azure, os-versioner som stöds på dessa virtuella datorer och att SAP- och DBMS-versioner stöds.
8.  Kontrollera [sap-webbplatsen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för nya HANA-certifierade SKU:er i Azure. Jämför prissättningen för nya SKU:er med de som du planerade att använda. Så småningom, göra nödvändiga ändringar för att använda de som har det bästa förhållandet mellan pris och prestanda.
9.  Anpassa distributionsskripten så att de använder nya vm-typer och införliva nya Azure-funktioner som du vill använda.
10. Efter distribution av infrastrukturen, testa och utvärdera nätverksfördröjningen mellan SAP-programlager virtuella datorer och DBMS virtuella datorer, enligt SAP-stödanteckningar [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten mot riktlinjerna för nätverksfördröjning i [SAP-stödanteckning #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverksfördröjningen bör vara i det måttliga eller goda intervallet. Undantag gäller för trafik mellan virtuella datorer och STORA HANA-instansenheter, vilket dokumenteras i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Se till att ingen av de begränsningar som nämns i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) och SAP [HANA-infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) gäller för distributionen.
11. Kontrollera att dina virtuella datorer distribueras till rätt [Azure-närhetsplaceringsgrupp,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)enligt beskrivningen i [Azure-närhetsplaceringsgrupper för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).
11. Utför alla andra kontroller som anges för proof of concept-fasen innan du använder arbetsbelastningen.
12. När arbetsbelastningen gäller registrerar du resursförbrukningen för systemen i Azure. Jämför den här förbrukningen med poster från din gamla plattform. Justera vm-storlek för framtida distributioner om du ser att du har stora skillnader. Tänk på att även när du minskar, lagring och nätverksbandbredder för virtuella datorer.
    - [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimentera med systemkopieringsfunktioner och processer. Målet är att göra det enkelt för dig att kopiera ett utvecklingssystem eller ett testsystem, så att projektteam kan få nya system snabbt. Överväg att använda [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) för dessa uppgifter.
14. Optimera och finslipa teamets Azure-rollbaserade åtkomst, behörigheter och processer för att se till att du har åtskillnad mellan uppgifter. Samtidigt, se till att alla team kan utföra sina uppgifter i Azure-infrastrukturen.
15. Öva, testa och dokumentera rutiner för hög tillgänglighet och haveriberedskap så att personalen kan utföra dessa uppgifter. Identifiera brister och anpassa nya Azure-funktioner som du integrerar i dina distributioner.

 
## <a name="production-preparation-phase"></a>Produktionsberedningsfas 
I den här fasen samlar du in det du upplevde och lärde dig under distributionerna som inte är produktion och tillämpar det på framtida produktionsdistributioner. Du måste också förbereda arbetet med dataöverföringen mellan din aktuella värdplats och Azure.

1.  Slutför nödvändiga SAP-utgivningsuppgraderingar av dina produktionssystem innan du flyttar till Azure.
1.  Håller med företagare om funktionella och affärstester som måste genomföras efter migrering av produktionssystemet.
1.  Kontrollera att dessa tester är slutförda med källsystemen på den aktuella värdplatsen. Undvik att utföra tester för första gången efter att systemet har flyttats till Azure.
1.  Testa processen för att migrera produktionssystem till Azure. Om du inte flyttar alla produktionssystem till Azure under samma tidsperiod kan du skapa grupper av produktionssystem som måste finnas på samma värdplats. Testa datamigrering. Här är några vanliga metoder:
    - Använd DBMS-metoder som säkerhetskopiering/återställning i kombination med SQL Server Always On, HANA System Replication eller Logga leverans till dirigera och synkronisera databasinnehåll i Azure.
    - Använd säkerhetskopiering/återställning för mindre databaser.
    - Använd SAP Migration Monitor, som är integrerad i SAP SWPM, för att utföra heterogena migreringar.
    - Använd [SAP DMO-processen](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) om du behöver kombinera migreringen med en SAP-utgivningsuppgradering. Tänk på att inte alla kombinationer av källa DBMS och mål DBMS stöds. Du hittar mer information i de specifika SAP-supportanteckningarna för de olika versionerna av DMO. Till exempel [alternativ för databasmigrering (DMO) på SUMMA 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Testa om dataöverföringsdataflödet är bättre via internet eller via ExpressRoute, om du behöver flytta säkerhetskopior eller SAP-exportfiler. Om du flyttar data via Internet kan du behöva ändra några av säkerhetsgrupps-/programsäkerhetsgruppsreglerna för nätverk som du måste ha för framtida produktionssystem.
1.  Innan du flyttar system från din gamla plattform till Azure samlar du in resursförbrukningsdata. Användbara data inkluderar CPU-användning, lagringsdataflöde och IOPS-data. Samla särskilt in dessa data från DBMS-lagerenheterna, men också samla in dem från programlagerenheterna. Mät även nätverks- och lagringsfördröjning.
1.  Kontrollera SAP-supportanteckningar och nödvändiga OS-inställningar, SAP HANA-maskinvarukatalogen och SAP PAM. Kontrollera att det inte fanns några ändringar i virtuella datorer som stöds för Azure, os-versioner som stöds i dessa virtuella datorer och att SAP- och DBMS-versioner stöds.
1.  Uppdatera distributionsskript för att ta hänsyn till de senaste besluten du har fattat om vm-typer och Azure-funktioner.
1.  När du har distribuerat infrastruktur och program, verifiera att:
    - Rätt VM-typer har distribuerats med rätt attribut och lagringsstorlekar.
    - De virtuella datorerna är på rätt och önskade OS-utgåvor och patchar och är enhetliga.
    - Virtuella datorer är härdade efter behov och på ett enhetligt sätt.
    - Rätt programversioner och korrigeringsfiler installerades och distribuerades.
    - De virtuella datorerna har distribuerats till Azure-tillgänglighetsuppsättningar som planerat.
    - Azure Premium Storage används för svarskänsliga diskar eller där [SLA för en virtuell dator på 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) krävs.
    - Azure Write Accelerator distribueras korrekt.
        - Kontrollera att inom de virtuella datorerna har lagringsutrymmen eller stripe-uppsättningar byggts korrekt över diskar som behöver Write Accelerator.
        - Kontrollera [konfigurationen av programvara RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Kontrollera [konfigurationen av LVM på virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/) används uteslutande.
    - Virtuella datorer har distribuerats till rätt tillgänglighetsuppsättningar och tillgänglighetszoner.
    - [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-programlagret och SAP DBMS-lagret.
    - Inga [virtuella Azure-nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) finns i kommunikationssökvägen mellan SAP-programmet och DBMS-lagret av SAP-system baserat på SAP NetWeaver, Hybris eller S/4HANA.
    - Programsäkerhetsgrupp och nätverkssäkerhetsgruppsregler tillåter kommunikation som önskas och planeras och blockerar kommunikation där det behövs.
    - Tidsgränsen är korrekt inställda, enligt beskrivningen ovan.
    - Virtuella datorer distribueras till rätt [Azure proximity placement group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), enligt beskrivningen i Azure [närhetsplaceringsgrupper för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).
    - Nätverksfördröjning mellan virtuella datorer i SAP-programlager och virtuella DBMS-datorer testas och valideras enligt beskrivningen i SAP-stödanteckningar [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten mot riktlinjerna för nätverksfördröjning i [SAP-stödanteckning #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverksfördröjningen bör vara i det måttliga eller goda intervallet. Undantag gäller för trafik mellan virtuella datorer och STORA HANA-instansenheter, vilket dokumenteras i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - Kryptering implementerades vid behov och med lämplig krypteringsmetod.
    - Gränssnitt och andra program kan ansluta den nyligen distribuerade infrastrukturen.
1.  Skapa en spelbok för att reagera på planerat Azure-underhåll. Bestäm i vilken ordning system och virtuella datorer ska startas om för planerat underhåll.
    

## <a name="go-live-phase"></a>Go-live-fas
Under go-live-fasen, se till att följa de spelböcker du utvecklat under tidigare faser. Kör de steg som du har testat och övat på. Acceptera inte ändringar i sista minuten i konfigurationer och processer. Slutför även dessa steg:

1. Kontrollera att Azure Portal övervakning och andra övervakningsverktyg fungerar. Vi rekommenderar Windows Performance Monitor (perfmon) för Windows och SAR för Linux.
    - CPU-räknare.
        - Genomsnittlig CPU-tid, totalt (alla processorer)
        - Genomsnittlig CPU-tid, varje enskild processor (128 processorer på M128 virtuella datorer)
        - CPU-kärntid, varje enskild processor
        - CPU-användartid, varje enskild processor
    - Memory.
        - Ledigt minne
        - Minnessida i/sekund
        - Minnessida ut/sekund
    - Disk.
        - Disken lästes i KBps, per enskild disk
        - Diskläsningar/sekund, per enskild disk
        - Disk läsas i mikroseconds/read, per enskild disk
        - Diskskrivning i KBps, per enskild disk
        - Diskskrivning/sekund, per enskild disk
        - Diskskrivning i mikroseconds/read, per enskild disk
    - Nätverk.
        - Nätverkspaket i/sekund
        - Nätverkspaket ut/sekund
        - Kb i/sekund nätverk
        - Nätverk KB ut/sekund
1.  Efter datamigrering utför du alla valideringstester som du kom överens om med företagsägarna. Acceptera endast valideringstestresultat när du har resultat för de ursprungliga källsystemen.
1.  Kontrollera om gränssnitten fungerar och om andra program kan kommunicera med de nyligen distribuerade produktionssystemen.
1.  Kontrollera transport- och korrigeringssystemet via SAP-transaktionen STMS.
1.  Utför säkerhetskopiering av databaser när systemet har släppts för produktion.
1.  Utför VM-säkerhetskopior för virtuella sap-programlager virtuella datorer när systemet har släppts för produktion.
1.  För SAP-system som inte ingick i den aktuella go-live-fasen men som kommunicerar med SAP-system som du flyttade till Azure under den här go-live-fasen måste du återställa värdnamnsbufferten i SM51. Om du gör det tas de gamla cachelagrade IP-adresserna som är associerade med namnen på de programinstanser som du flyttade till Azure bort.  


## <a name="post-production"></a>Efter produktion
Denna fas handlar om övervakning, drift och administrering av systemet. Ur SAP-synvinkel gäller de vanliga uppgifter som du var tvungen att slutföra på din gamla värdplats. Slutför även dessa Azure-specifika uppgifter:

1. Granska Azure-fakturor för system med hög debitering.
2. Optimera pris-/prestandaeffektiviteten på vm-sidan och lagringssidan.
3. Optimera de tider då du kan stänga av system.  


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [Planering och implementering av virtuella Azure-datorer för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

