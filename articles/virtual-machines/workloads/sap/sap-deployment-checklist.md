---
title: SAP arbetsbelastning planerings- och Checklista | Microsoft Docs
description: Checklista för planering och distribution av arbetsbelastningar som SAP på Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67083a8214724659765922047c1f0ccd6da87b9d
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884936"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>SAP-arbetsbelastningar på Azure checklista för planering och distribution 

Den här checklistan har utformats för kunder som flyttar sina SAP NetWeaver och S/4HANA Hybris program till Azure-infrastruktur som en tjänst.  Den här checklistan bör granskas av en kund och/eller SAP-partner under hela projektet. Det är viktigt att många av kontrollerna som utförs i början av projektet och i planeringsfasen. När distributionen är klar, kan det bli komplexa elementär ändringar på distribuerade Azure-infrastruktur eller SAP programversioner. Gå igenom checklistan på viktigaste milstolparna under ett projekt.  Mindre problem kan identifieras innan de hunnit bli stora problem och tillräckligt med tid finns för att utveckla igen och testa nödvändiga ändringar. Checklista på Ingen sätt påstår sig vara klar. Beroende på enskilda situationen, det kan finnas många fler kontroller som behöver utföras. 

Checklistan monteras innehåller inte uppgifter som är oberoende av Azure.  Exempel: SAP-program gränssnitt ändras under en flytt till Azures offentliga moln eller till en värdleverantör.    

Den här checklistan kan också användas för redan distribuerade system. Nya funktioner som Write Accelerator, Tillgänglighetszoner och nya typer av virtuella datorer kan ha lagts eftersom du har distribuerat.  Därför är det bra att granska checklista med jämna mellanrum för att säkerställa att du är medveten om nya funktioner i Azure-plattformen. 

## <a name="project-preparation-and-planning-phase"></a>Projektet jobbförberedelse- och planeringsfasen
I den här fasen planeras en migrering av SAP-arbetsbelastningar till offentliga Azure-molnet. Den minsta uppsättningen med entiteter och objekt beskrivs och definierad lista som:

1. Övergripande Designdokument – det här dokumentet ska innehålla:
    1. Aktuella inventeringen för SAP-komponenter och program och mål-webbprograminventering på Azure
    2. Skapa och arbeta via en ansvar tilldelning matris (RACI) som definierar ansvaret och tilldelningar av berörda parterna. Starta på hög nivå och arbetar för att fler och fler detaljerade nivåer dataflöde första och du planerar distributioner
    2. En övergripande lösningsarkitektur
    3. Beslut att distribuera till på Azure-regioner. En lista över Azure-regioner, kontrollera den [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Tjänster som är tillgängliga i varje Azure-regioner finns i artikeln [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/)
    4. Nätverksarkitekturen att ansluta från en lokal till Azure. Börja Visa vem bekant med den [Virtual Datacenter skissen till Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Säkerhetsprinciper för att köra viktiga för verksamheten påverka data i Azure. För att läsa in material börjar med [dokumentation om Azure-säkerhet](https://docs.microsoft.com/azure/security/)
2.  Teknisk Design dokumentet – som innehåller:
    1.  Ett blockdiagram för lösningen 
    2.  Storleksändring av beräkning, lagring och nätverkskomponenter i Azure. SAP storleksändringar av virtuella Azure-datorer, finns stöd för SAP-kommentar [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Arkitektur för affärskontinuitet och Haveriberedskap
    4.  Detaljerad OS, DB, Kernel och SAP stöd för pack-versioner. Det är inte en tanke på att någon OS-version som stöds av SAP NetWeaver och S/4HANA stöds i Azure virtuella datorer. Detsamma gäller för DBMS-versioner. Det är obligatoriskt att checkas följande källor för att justera och om det behövs uppgradera SAP-versioner, utgåvor av DBMS och versioner för att vara i ett SAP och fönstret stöds av Azure. Det är obligatoriskt att du befinner dig i SAP och Azure versionen kombinationer för att få fullständig support av SAP och Microsoft som stöds. Om det behövs, måste du planera för uppgradering av några av programvarukomponenter. Mer information om vilka program som stöds SAP-, OS- och DBMS dokumenteras på följande platser:
        1.  Stöd för SAP-kommentar [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Den här anteckningen definierar de lägsta OS-versioner som stöds i Azure virtuella datorer. Den definierar även minsta databas-versioner som krävs för de flesta icke HANA-databas. Anmärkning presenterar även SAP-storlek SAP stöds Azure VM-typer.
        2.  Stöd för SAP-kommentar [#2039619](https://launchpad.support.sap.com/#/notes/2039619). Anmärkning definierar stödmatris Oracle på Azure. Tänk på att Oracle stöder endast Windows- och Oracle Linux som gästoperativsystem i Azure för SAP-arbetsbelastningar. Den här kompatibilitetsinformationen gäller för SAP-programnivån kör SAP-instanser. Oracle stöder dock inte hög tillgänglighet för SAP Central Services i Oracle Linux via Pacemaker. Om du behöver hög tillgänglighet för ASCS på Oracle Linux kan behöva du utnyttja SIOS säkerhetspaket för Linux. För detaljerade data för SAP-certifiering, kontrollera SAP support-kommentar [#1662610 - information om Support för SIOS säkerhetspaket för Linux](https://launchpad.support.sap.com/#/notes/1662610). För Windows, SAP som stöds Windows Failover-kluster redundanslösning för SAP Central Services stöds tillsammans med Oracle som DBMS-lager. 
        3.  Stöd för SAP-kommentar [#2235581](https://launchpad.support.sap.com/#/notes/2235581) hämta av stödmatrisen för SAP HANA på olika OS-versioner
        4.  SAP HANA stöds virtuella Azure-datorer och [HANA stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) visas [här](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [SAP produkten tillgänglighet matris](https://support.sap.com/en/)
        6.  Andra SAP-information för andra specifika SAP-produkter  
    5.  Vi rekommenderar strikt 3 nivåer utformning för SAP produktionssystem. Kombinera ASCS + APP servrar på samma VM rekommenderas inte.  Med multi-SID klusterkonfigurationer för SAP Central Services stöds med Windows som gäst-OS på Azure. Medan SAP Central Services – flera SÄKERHETSIDENTIFIERARE klusterkonfigurationer inte stöds med Linux-operativsystem på Azure. Dokumentation för Windows guest OS finns i:
        1.  [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet med Windows Server Failover Clustering och delad disk på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet med Windows Server Failover Clustering och filresursen på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Hög tillgänglighet och disaster recovery-arkitekturen
        1.  Definiera utifrån RTO och RPO vad som behöver hög tillgänglighet och katastrofåterställning recovery arkitekturen ska se ut som
        2.  Kontrollera vad önskad DBMS har att erbjuda i Azure för hög tillgänglighet i samma zon. De flesta DBMS erbjuder synkrona metoder för en synkron hett vänteläge, vilket rekommenderas för produktionssystem. Även kontrollera SAP relaterad dokumentation för olika databaser från och med [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) och relaterade dokument
            1.  Med hjälp av Windows-tjänsten för redundanskluster med delad diskkonfiguration för DBMS-lager som exempelvis beskrivningen för SQL Server [här](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) är **inte** stöds. I stället som lösningar:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA-Systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Kontrollera vilka möjligheter som erbjuds av olika DBMS-leverantörer för katastrofåterställning i olika Azure-regioner. De flesta av dem stöder asynkron replikering eller loggöverföring
        4.  Definiera om du skulle köras business-regression testsystem som helst är repliker av dina produktionsdistributioner, i samma Azure-region eller DR-regionen för SAP-programnivån. I det senare fallet kan rikta du företag regression systemet som DR-mål för din produktion
        5.  Om du inte väljer att placera de icke-produktionssystem i DR-plats kan du se över Azure Site Recovery som lönsamma metod för att replikera SAP-programnivån i Azure-DR-regionen. Se även [konfigurera haveriberedskap för en app-distribution för flera nivåer av SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Om du vill använda en kombinerad hr/DR-konfiguration utnyttjar [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) bli bekant med Azure-regioner där tillgänglighetszoner och begränsningar som kan uppstå med ökad nätverksfördröjningar mellan två Tillgänglighetszoner  
3.  Kunden Partner bör skapa en förteckning över alla SAP-gränssnitt (SAP och icke-SAP). 
4.  Design av Foundation Services Design - den här designen omfattar exempelvis
    1.  Utforma Active Directory och DNS
    2.  Nätverkets topologi inom Azure och tilldelning av olika SAP-system
    3.  [Rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview) strukturen för dina olika team som hanterar infrastrukturen och SAP-program i Azure
    3.  Topologi för resursgrupp 
    4.  [Tagga strategi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Naming Convention för virtuella datorer och andra infrastrukturkomponenter och/eller logiska namn
5.  Microsoft Premier Support-kontrakt – identifiera MS Technical Account Manager (TAM). Stöd för krav av SAP finns stöd för SAP-kommentar [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Definiera antalet Azure-prenumerationer och kärnkvoten för olika prenumerationer. [Öppna supportärenden att öka kvoter för Azure-prenumerationer](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) efter behov 
7.  Dataminskning och migrering av data kan du planera för migrering av SAP-data till Azure. För SAP NetWeaver-system har SAP riktlinjer om hur du håller volymen på en stor mängd data som är begränsad. SAP publicerade [handboken djupgående](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) om datahantering av i SAP ERP-system. Men vissa innehållet gäller för NetWeaver och S/4HANA-system i allmänhet.
8.  Definiera och välja metod för automatisk distribution. Målet med automation bakom distribution av infrastruktur på Azure är att distribuera på ett deterministiskt sätt och få deterministiska resultat. Många kunder använder PowerShell eller CLI baserat skript. Men det finns olika tekniker med öppen källkod som kan användas för att distribuera Azure-infrastrukturen för SAP och även installera SAP-program. Exempel finns i github:
    1.  [Automatiserad SAP-distributioner i Azure-molnet](https://github.com/Azure/sap-hana)
    2.  [SAP HANA-Installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definiera en vanlig design och distribution granska takt mellan dig som kund systemintegratör, Microsoft och andra inblandade parter

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotfasen (rekommenderas starkt)
 
Piloten kan köras före eller parallellt till projektet planering och förberedelser. Fasen kan också användas för att testa metoder och design som gjorts i fasen av planering och förberedelser. Pilotfasen kan stretchas till en verklig bevis på koncept. Vi rekommenderar att konfigurera och verifiera en fullständig hr/DR-lösning samt säkerhetsdesign under en pilotdistribution. I vissa fall kunden kan skalbarhet tester också utföras i det här steget. Andra kunder använder distribution av SAP sandbox-system som pilotfasen. Så vi förutsätter att du har identifierat ett system som du vill migrera till Azure, i syfte att köra ett pilotprojekt.

1.  Optimera dataöverföring i Azure. Mycket beroende på kunden fall överföring via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) från en lokal var snabbaste om Express-kretsen har tillräckligt med bandbredd. Med andra kunder kommit gå via internet fram till att vara snabbare
2.  Vid en SAP heterogena migreringen som inbegriper en export och import av databasdata, testa, och optimera exportera och importera faser. För stora migreringar som rör SQL-Server som målplattform, rekommendationer finns [här](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/08/sap-osdb-migration-to-sql-server-faq-v6-2-april-2017/). Du kan vidta metod för migrering Övervakare/SWPM om du inte behöver en uppgradering av kombinerade versionen eller [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) när du kombinerar migrering med en uppgradering för SAP-versionen och uppfyller vissa käll- och DBMS-plattform kombinationer som beskrivs i exempelvis [databasen migrering alternativet DMO () av SUMMAN 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
    1.  Exportera till källa, Export filuppladdning till Azure och importera prestanda.  Maximera överlapp mellan export och import
    2.  Utvärdera mängden databas mellan mål- och mål-plattformen för att avspegla i infrastruktur-storlek    
    3.  Validera och optimera Tidsinställning 
3.  Teknisk verifiering 
    1.  VM-typer
        1.  Verifiera resurserna på SAP support anteckningar, SAP HANA maskinvara directory och SAP PAM igen för att se till att det fanns inga ändringar i virtuella datorer som stöds för Azure, OS-versioner som stöds för dessa typer av virtuella datorer och SAP och DBMS-versioner som stöds
        2.  Validera igen storleksändringar av ditt program och infrastruktur som du distribuerar på Azure. När det gäller flytta befintliga program har du ofta kan härleda nödvändiga SAP från infrastrukturen som du använder och [SAP benchmark webbsidan](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) och jämför den med SAP-siffror som visas i SAP support-kommentar [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Håll också [i den här artikeln](https://blogs.msdn.microsoft.com/saponsqlserver/2018/11/04/saps-ratings-on-azure-vms-where-to-look-and-where-you-can-get-confused/) i åtanke
        3.  Utvärdera och testa storleksändring av virtuella datorer i Azure avseende största genomflödet och nätverkets dataflöde av olika VM-typer som du valde i planeringsfasen. Data kan hittas i:
            1.  [Storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på de **maximalt icke cachelagrat diskgenomflöde** för storleksändring
            2.  [Storlekar för Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) är det viktigt att tänka på de **maximalt icke cachelagrat diskgenomflöde** för storleksändring
    2.  Storage
        1.  Använd Azure Premium Storage för virtuella datorer i databasen
        2.  Använd [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/)
        3.  Använd Azure Write Accelerator för DBMS loggenheter med M-serien. Tänk på Write accelerator gränser och användning enligt beskrivningen i [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
        4.  De olika DBMS-typerna, kontrollera den [allmän SAP DBMS dokumentationen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) och specifika dokumentationen DBMS allmän dokumentet pekar du
        5.  För SAP HANA, mer information finns dokumenterade i [konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
        6.  Aldrig montera Azure datadiskar till en virtuell Linux-dator med hjälp av enhets-ID. Använd istället universell unik identifierare (UUID). Var försiktig när du använder grafiska verktyg för att montera en Azure-datadiskar, till exempel. Kontrollera posterna i/etc/fstab att se till att diskarna är monterade med UUID
            1.  Mer information finns [här](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
    3.  Nätverk
        1.  Testa och utvärdera din VNet-infrastruktur och distribution av dina SAP-program på en eller i olika Azure-nätverk
            1.  Utvärdera metoden med nav och eker-arkitektur för virtuellt nätverk eller microsegmentation inom en enda Azure-nätverk som baseras på
                1.  Kostnaderna på grund av datautbyte mellan [peer-kopplade virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Kostnaderna Kontrollera [prisinformation för Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/)
                2.  Utnyttja fast koppla för peering mellan Azure-nätverk för jämförelse med ändringen NSG för att isolera ett undernät i ett virtuellt nätverk i de fall där program eller virtuella datorer som finns i ett undernät för det virtuella nätverket blev en säkerhetsrisk
                3.  Central loggning och granskning av nätverkstrafik mellan en lokal och världen utanför virtuellt datacenter som du har byggt upp i Azure
            2.  Utvärdera och testa datasökväg mellan SAP-programnivån och SAP DBMS-lager. 
                1.  Alla placeringen av [Azure virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationsvägen mellan SAP-program och DBMS-lager med en SAP NetWeaver, Hybris eller S/4HANA SAP system stöds inte alls
                2.  Placera SAP-programnivån och SAP DBMS i olika Azure-nätverk inte peer-kopplade stöds inte
                3.  [Azure ASG och NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview) stöds för att definiera vägar mellan SAP-programnivån och SAP DBMS-lager
            3.  Se till att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiveras på virtuella datorer som används på SAP-programnivån och SAP DBMS-lagret. Tänk på att olika nivåer för OS behövs för att stödja Accelererat nätverk i Azure:
                1.  Windows Server 2012 R2 eller senare versioner
                2.  SUSE Linux 12 SP3 eller senare versioner
                3.  RHEL 7.4 eller senare versioner
                4.  Oracle Linux 7.5. Använder RHCKL-kernel kan måste versionen vara 3.10.0-862.13.1.el7. Med hjälp av Oracle-UEK är kernel-version 5 obligatoriskt
            4.   Testa och utvärdera Nätverksfördröjningen mellan SAP-programnivån VM och DBMS VM enligt SAP support-kommentar [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten med nätverket svarstid vägledning för SAP support anteckning [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Svarstiden i nätverk ska vara i intervallet måttliga och bra. Undantag gäller för trafik mellan virtuella datorer och stora HANA-instansen enheter enligt beskrivningen [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
            5.   Se till att ILB-distributioner är konfigurerat att använda direkt Serverreturnering. Den här inställningen kommer att minska svarstiden i fall där Azure Ilb används för konfigurationer med hög tillgänglighet i DBMS-lager
    4.   Hög tillgänglighet och disaster recovery-distributioner. 
        1.   Om du har distribuerat SAP-programnivån utan att definiera en viss Azure-Tillgänglighetszon kan du se till att alla virtuella datorer som kör SAP dialogrutan instans eller mellanprogram instanser av en enda SAP-system har distribuerats i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
            1.   Om du inte behöver hög tillgänglighet för SAP Central Services och DBMS kan kan dessa virtuella datorer distribueras till samma Tillgänglighetsuppsättning som SAP-programnivån
        2.   Om du skyddar SAP Central Services och DBMS-lagret för hög tillgänglighet med passiva repliker, har de två noderna för SAP Central Services i en separat Tillgänglighetsuppsättning och noden två DBMS i en annan Tillgänglighetsuppsättning
        3.   Om du distribuerar till Azure Availability Zones kan du utnyttja Tillgänglighetsuppsättningar. Men du behöver se till att du distribuerar de aktiva och passiva Central Services-noderna i två olika Tillgänglighetszoner, som visar den minsta svarstiden mellan zoner.
            1.   Tänk på att du behöver använda [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) för fall av upprätta Windows eller Pacemaker redundanskluster för DBMS och SAP Central Services lagret i olika Tillgänglighetszoner. [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kan inte användas för zonindelade distributioner 
    5.   Timeout-inställningar
        1.   Kontrollera SAP NetWeaver developer spåren för de olika SAP-instanserna och se till att inga anslutning radbrytningar mellan sätta server och SAP arbetsprocesser anges. Dessa anslutning radbrytningar kan undvikas genom att ange dessa två registerparametrar:
            1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – Se även [i den här artikeln](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
            2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – Se även [i den här artikeln](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
        2.   För att undvika GUI timeout mellan en lokal distribuerat SAP-GUI-gränssnitt och SAP programskikt distribueras i Azure, kontrollera om följande parametrar är inställda i default.pfl eller profilen för instans:
            1.   rdisp/keepalive_timeout = 3600
            2.   rdisp/keepalive = 20
        3.   Om du använder en konfiguration för Windows-redundanskluster, se till att tid att reagera på inte svarar noder är korrekt för Azure. Microsoft-artikeln [justering tröskelvärden för Failover-kluster nätverk](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) visar en lista över parametrar och hur de påverkar sensitivities för redundans. Dessa två parametrar ska anges med värden för parametrarna:
            1.   SameSubNetDelay = 2
            2.   SameSubNetThreshold = 15
4.   Testa din hög tillgänglighet och katastrofåterställning återställningsproceduren
    1.   Simulera felväxling genom att stänga av virtuella datorer (Windows gästoperativsystem) eller placera operativsystem i panik läge (Linux gästoperativsystem) för att ta reda på om din redundanskonfigurationer som fungerar som avsett. 
    2.   Mät de tid det tar för att köra en redundans. Om tiderna tar för lång tid bör:
        1.   För SUSE Linux, använda uppstår enheter i stället för Azure-agenten hägna in snabbare redundans
        2.   För SAP HANA om inläsning av data tar för lång tid du distribuera mer bandbredd för lagring
    3.   Testa backup/restore-sekvensen och val av tidpunkt och justera vid behov. Se till att inte bara säkerhetskopieringstider är tillräckliga. Också testa återställning och utför tidsinställningen på återställning aktiviteter. Se till att återställningen går är inom dina RTO-serviceavtal om din RTO är beroende av en databas eller VM återställningen
    4.   Testa i regionen DR-funktioner och arkitektur
5.  Säkerhetskontroller
    1.  Testa giltigheten hos Azure-roll baserad åtkomst (RBAC)-arkitektur som du har implementerat. Målet är att avgränsa och begränsa åtkomst och behörighet för olika team. Till exempel ska SAP-Basis teammedlemmar kunna distribuera virtuella datorer och tilldela diskar från Azure storage till en viss Azure-nätverk. Men SAP bas-teamet inte ska kunna skapa egna virtuella nätverk eller ändra inställningarna för befintliga virtuella nätverk. Å andra sidan bör medlemmar i teamet nätverk inte kunna distribuera virtuella datorer i virtuella nätverk där SAP-programmet och DBMS virtuella datorer körs. Eller medlemmar i teamet nätverk ska kunna ändra attribut för virtuella datorer eller ta bort virtuella datorer eller diskar.  
    2.  Kontrollera [NSG och ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) regler fungerar som förväntat och skärma av den skyddade resurser
    3.  Se till att alla resurser som ska krypteras är krypterad. Definiera och köra processer för att säkerhetskopiera certifikat, lagra, och kommer åt certifikaten och återställa de krypterade entiteterna. 
    4.  Använd [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) och/eller för OS-diskar där möjligt från ett operativsystem stöder synvinkel
    5.  Kontrollera att inte för många nivåer av kryptering har använts. Gör den begränsade klokt att använda Azure Disk encryption och viktigaste sedan någon av metoderna DBMS Transparent databaskryptering på
6.  Testning av prestanda
    1.  Jämför översta 10 online-rapporter för aktuella implementeringen i SAP baserat på SAP-spårning och mätning av faktisk användning, om tillämpligt 
    2.  Jämför översta 10 batch-jobb för aktuella implementeringen i SAP baserat på SAP-spårning och mätning av faktisk användning, om tillämpligt 
    3.  I SAP baserat på SAP-spårning och mätning av faktisk användning, jämför dataöverföringar via gränssnitt i SAP-system. Fokusera på gränssnitt där du vet att överföringen kommer nu mellan olika platser, som kommer från lokalt till Azure 


## <a name="non-production-phase"></a>Icke-produktion fas 
I det här steget förutsätter vi att när du har en lyckad pilot eller PoC kan du börjar distribuera SAP-system för icke-produktion i Azure. Alla erfarenheter och upplevelser utanför bevis på koncept ska anpassas till typen av distribution. Alla villkor och stegen i PoC gäller i den här typen av samt distributioner. I det här steget du distribuerar normalt utvecklingssystem jednotek system och företag regression testar system till Azure. Vi rekommenderar minst en icke-produktion systemet i ett SAP-program raden innehåller konfiguration för fullständig hög tillgänglighet eftersom framtida produktionssystemet ska. Ytterligare steg som du behöver tänka på under den här fasen är:  

1.  Innan du flyttar system från den gamla plattformen Azure samla in resursdata förbrukning som CPU-användning, genomflödet och IOPS-data. Särskilt från de DBMS layer enheterna, utan även från application layer-enheter. Mät också nätverks- och svarstid.
2.  Registrera tillgänglighet tid användningsmönster på system. Målet är att ta reda på om icke-produktionssystem måste vara 7 x 24 tillgängligt eller om det icke-produktionssystem som kan stängas av under vissa faser av en vecka eller månad
3.  Testa och definiera om du vill skapa egna OS-avbildningar för virtuella datorer i Azure eller om du vill använda en avbildning av avbildningar av virtuella Azure-galleriet. Om du använder en avbildning från Azure-galleriet, se till att du vidtar rätt bild som visar supportavtal med leverantören av OS. För vissa OS-leverantörer erbjuder Azure gallerier att använda dina egna avbildningar för licens. För andra OS-avbildningar, support ingår i priset som anges av Azure. Om du vill skapa dina egna OS-avbildningar, hittar du dokumentation i de här artiklarna:
    1.  Du kan skapa en generaliserad avbildning av en Windows VM som distribueras i Azure baserat på [den här dokumentationen](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Du kan skapa en generaliserad avbildning av en virtuell Linux-dator som distribueras i Azure baserat på [den här dokumentationen](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Om du använder SUSE och Red Hat Linux-avbildningar från Virtuella Azure-galleriet kan behöva du använda avbildningar för SAP tillhandahålls av Linux-leverantörer i Virtuella Azure-galleriet
4.  Kontrollera att du uppfyller kraven för stöd SAP har om Microsoft support-avtal. Information finns i SAP support-kommentar [#2015553](https://launchpad.support.sap.com/#/notes/2015553). HANA stora instanser, finns i dokumentet [integrationskrav](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Kontrollera att rätt personer får [meddelanden planerat underhåll](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), så att du kan välja driftstoppet och en omstart av virtuella datorer i tid
5.  Kontrollera stadigt dokumentation om Microsoft presentationer om kanaler, till exempel Azure [Channel9](https://channel9.msdn.com/) för nya funktioner som kan användas för dina distributioner
6.  Kontrollera SAP notes relaterade till Azure, som stöd Obs [#1928533](https://launchpad.support.sap.com/#/notes/1928533) för nya VM SKU: er eller nyligen stöds OS och DBMS-versionen. Jämför nya typer av virtuella datorer mot äldre VM matar i priser, så, att du kan distribuera virtuella datorer med förhållandet bästa pris/prestanda
7.  Verifiera resurserna på SAP support anteckningar, SAP HANA maskinvara directory och SAP PAM igen för att se till att det fanns inga ändringar i virtuella datorer som stöds för Azure, stöds OS-versionerna på dessa virtuella datorer och stöds SAP och DBMS versioner
8.  Kontrollera [här](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för nya HANA-certifierade SKU: er i Azure och jämför priser med de som du har planerat och slutligen ändra för att få enheterna med bättre pris prestanda ransonen 
9.  Anpassa din distributionsskript för att utnyttja nya typer av virtuella datorer och lägga till nya funktioner i Azure som du vill använda
10. Efter distributionen av infrastrukturen, testa och utvärdera Nätverksfördröjningen mellan SAP-programnivån VM och DBMS VM enligt SAP support-kommentar [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten med nätverket svarstid vägledning för SAP support anteckning [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Svarstiden i nätverk ska vara i intervallet måttliga och bra. Undantag gäller för trafik mellan virtuella datorer och stora HANA-instansen enheter enligt beskrivningen [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Kontrollera att ingen av begränsningarna som nämns i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) och [konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) gäller för din distribution
11. Utföra alla andra kontroller som anges i fasen Proof of concept innan du tillämpar arbetsbelastning
12. När det gäller arbetsbelastning, registrera de resurser som används för dessa system i Azure och jämför med de poster som du fick från din gamla plattform. Justera VM-storlek för framtida distributioner om du ser att du har större skillnader. Tänk på att vid downsizing, lagring och nätverksbandbredd för en virtuell dator kommer att minska samt:
    1.  [Storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Storlekar för Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Arbeta i kopieringsfunktionalitet system och processer. Målet är att göra det lättare för dig att kopiera en utvecklingssystem eller ett testsystem, så som projektet team kan få nya system snabbt. Överväg att [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) som ett verktyg som utför sådana uppgifter.
14. Optimera och få fram din grupps Azure rollbaserad åtkomst, behörigheter och processer för att se till att du har en åtskillnad mellan använts på ena sidan. Å andra sidan som du vill ha alla team som är aktiverade för att utföra sina uppgifter i Azure-infrastrukturen.
15. Övning, testa och dokumentet hög tillgänglighet och katastrofåterställning återställningsproceduren för att aktivera din personal att utföra dessa uppgifter. Identifiera brister och anpassa nya Azure-funktioner som du integrerar i dina distributioner

 
## <a name="production-preparation-phase"></a>Förberedelsefasen för produktion 
I det här steget som du vill samla in alla upplevelser och erfarenheter av dina produktionsdistributioner och använda dem i framtiden Produktionsdistribution. Dessutom till de olika faserna innan du också måste du förbereda arbetet med att överföra data mellan ditt aktuella värdplats och Azure. 

1.  Gå igenom nödvändiga SAP versionen uppgraderingar av din produktionssystem innan du flyttar till Azure
2.  Acceptera företagsägare på den funktionella och business-tester som ska utföras efter migreringen av produktionssystemet
    1.  Se till att alla dessa tester utförs med källsystem i den aktuella värdplats. Du vill undvika tester utförs för första gången när systemet har flyttats till Azure
2.  Testa migreringsprocessen för produktion i Azure. När det gäller inte flytta alla produktionssystem till Azure på samma tidsram, skapa grupper av produktionssystem som måste finnas på samma plats som värd. Övning och testa migrering av data. Lista med vanliga metoder som:
    1.  Använder DBMS metoder som säkerhetskopiering/återställning i kombination med SQL Server AlwaysOn, HANA System Replication eller Log leverans för att dirigera och synkronisera databasinnehållet i Azure
    2.  Använd säkerhetskopiering/återställning för mindre databaser
    3.  Använd SAP migrering övervakaren implementerats till SAP SWPM verktyg för heterogena migreringar
    4.  Använd den [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) bearbeta om du vill kombinera med en uppgradering för SAP-versionen. Tänk på att inte alla kombinationer av mellan källa och mål-DBMS stöds. Mer information finns i den specifika informationen för SAP för de olika versionerna av DMO. Till exempel [databasen migrering alternativet DMO () av SUMMAN 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Testa om dataöverföring via internet eller via ExpressRoute passar bättre i dataflödet om du behöver flytta säkerhetskopior eller SAP exportera filer. För till exempel flyttar data via internet, kan du behöva ändra några av dina NSG/ASG säkerhetsregler som du behöver för framtida produktionssystem
3.  Innan du flyttar system från den gamla plattformen Azure samla in resursdata förbrukning som CPU-användning, genomflödet och IOPS-data. Särskilt från de DBMS layer enheterna, utan även från application layer-enheter. Mät också nätverks- och svarstid.
4.  Verifiera resurserna på SAP support anteckningar, SAP HANA maskinvara directory och SAP PAM igen för att se till att det fanns inga ändringar i virtuella datorer som stöds för Azure, stöds OS-versionerna på dessa virtuella datorer och stöds SAP och DBMS versioner 
4.  Anpassa distributionsskript som de senaste ändringarna som du valt VM-typer och Azure-funktioner
5.  När distributionen av infrastruktur och gå igenom en rad kontroller i för att verifiera:
    1.  Rätt VM-typer distribuerades med rätt attribut och lagringsstorlekar
    2.  Kontrollera att de virtuella datorerna är på rätt och önskad OS-versioner och korrigeringar och är enhetliga
    3.  Kontrollera att Härdning av de virtuella datorerna är som krävs och enhetlig
    4.  Kontrollera att rätt program versioner och korrigeringar har installerat och distribueras
    5.  De virtuella datorerna har distribuerats i Azure-Tillgänglighetsuppsättningar som planerat
    6.  Azure Premium Storage har använts för svarstid känsliga diskarna eller där den [enkel VM SLA för 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) krävs
    7.  Kontrollera rätt distributionen av Azure Write Accelerator
        1.  Se till att den virtuella datorn, lagringsutrymmen, eller stripe-uppsättningar har skapats korrekt över diskar som behöver support för Azure Write Accelerator
            1.  Kontrollera [konfigurera programvaru-RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Kontrollera [konfigurera LVM på en Linux-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/) användes exklusivt
    9.  Virtuella datorer distribuerades till rätt Tillgänglighetsuppsättningar och Tillgänglighetszoner
    10. Se till att [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiveras på virtuella datorer som används på SAP-programnivån och SAP DBMS-lager
    11. Ingen placering av [Azure virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) kommunikationsvägen mellan SAP-program och DBMS-lager med en SAP NetWeaver Hybris eller S/4HANA baserad SAP-system
    12. ASG och NSG-reglerna tillåter kommunikation som önskad och planerade och blockera kommunikation om det behövs
    13. Timeout-inställningar har enligt beskrivningen ovan ställts in korrekt
    14. Testa och utvärdera Nätverksfördröjningen mellan SAP-programnivån VM och DBMS VM enligt SAP support-kommentar [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support-kommentar [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultaten med nätverket svarstid vägledning för SAP support anteckning [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Svarstiden i nätverk ska vara i intervallet måttliga och bra. Undantag gäller för trafik mellan virtuella datorer och stora HANA-instansen enheter enligt beskrivningen [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Kontrollera om kryptering distribuerades vid behov och med krypteringsmetoden som behövs
    16. Kontrollera om gränssnitt och andra program kan ansluta den nyligen distribuerade infrastrukturen
6.  Skapa en spelbok för att reagera på Azure planerat underhåll. Definiera ordningen på system och virtuella datorer startas om vid planerat underhåll
    

## <a name="go-live-phase"></a>Go Live Phase
Du måste se till att följa dina spelböcker som du har utvecklat i tidigare faser för Go-Live-fasen. Kör de steg som du har testat och tränas. Inte acceptera sista ändringar i konfigurationer och processen. Förutom som gäller följande åtgärder:

1. Kontrollera att Azure-portalen övervakning och andra övervakningsverktyg fungerar.  Rekommenderade verktyg är Perfmon (Windows) eller SAR (Linux): 
    1.  CPU-räknare 
        1.  Genomsnittlig CPU-tid – totalt (alla CPU)
        2.  Genomsnittlig CPU-tid – varje enskild processor (så 128 processorer på m128 virtuell dator)
        3.  Processor tid kernel – varje enskild processor
        4.  Processor tid användare – varje enskild processor
    5.  Minne 
        1.  Ledigt minne
        2.  Minnessidan i/sek
        3.  Minnessidan ut/s
    4.  Disk 
        1.  Disk-lästa kb/SEK – per enskild disk 
        2.  Diskläsningar/sek – per enskild disk
        3.  Disk-lästa ms/diskläsning – per enskild disk
        4.  Disk-skrivna kb/s – per enskild disk 
        5.  Disk-skrivna/s – per enskild disk
        6.  Skriv ms/diskläsning – per enskild disk
    5.  Nätverk 
        1.  Paket/sek
        2.  Nätverkspaket ut/s
        3.  Nätverket kb/sek
        4.  Nätverket kb ut/s 
2.  Efter migreringen av data, utför alla verifieringstester som du har kommit överens med företagets ägare. Godkänn endast test valideringsresultat där du har resultat för de ursprungliga källsystem
3.  Kontrollera om gränssnitt fungerar och om andra program kan kommunicera med de nyligen distribuerade produktionssystem
4.  Kontrollera om systemet via SAP transaktion STM transport och korrigering
5.  Säkerhetskopiera databasen när systemet släpps för produktion
6.  Säkerhetskopiera virtuella datorer för SAP-programnivån virtuella datorer när systemet släpps för produktion
7.  För SAP system som inte ingick i de aktuella go live fas men kommunicera med SAP-system som du flyttade till Azure i den här fasen för go live, måste du återställa Formatnamnsbufferten som värden i SM51. Det här steget ska ta bort de gamla cachelagrade IP-adresser som är associerade med namnen på de instanser av programmet som du flyttade till Azure  


## <a name="post-production"></a>Publicera produktion
I den här fasen handlar det om övervakning, operativsystem och administrerar systemet. Från en SAP synsätt gäller de vanliga uppgifter som du skulle behöva utföra med din gamla värdplats. Azure specifika uppgifter som du vill göra är:

1. Analysera Azure fakturor för hög avgifter som
2. Optimera effektivitet pris/prestanda på virtuella datorer och lagring serversidan
3. Optimera tid system kan stängas av  


## <a name="next-steps"></a>Nästa steg
I dokumentationen:

- [Azure virtuella datorer, planering och implementering av SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

