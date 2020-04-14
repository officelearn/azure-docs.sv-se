---
title: SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft-dokument
description: DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines
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
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273213"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines

I det här dokumentet täcker flera olika områden att tänka på när du distribuerar SAP ASE i Azure IaaS. Som en förutsättning för det här dokumentet bör du ha läst dokumentet [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md) och andra guider i [SAP-arbetsbelastningen i Azure-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Det här dokumentet täcker SAP ASE som körs på Linux och Windows operativsystem. Minsta version som stöds på Azure är SAP ASE 16.0.02 (Version 16 Support Pack 2). Vi rekommenderar att du distribuerar den senaste versionen av SAP och den senaste korrigeringsnivån.  Som ett minimum SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7) rekommenderas.  Den senaste versionen av SAP finns i [Targeted ASE 16.0 Release Schedule och CR list Information](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Ytterligare information om utgivningsstöd med SAP-program eller installationsmediaplats finns, förutom i SAP-produkttillgänglighetsmatrisen på dessa platser:

- [SAP-supportanteckning #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP-supportanteckning #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP-supportanteckning #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP-supportanteckning #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Anmärkning: Under hela dokumentationen inom och utanför SAP-världen refereras namnet på produkten som Sybase ASE eller SAP ASE eller i vissa fall båda. För att vara konsekvent använder vi namnet **SAP ASE** i den här dokumentationen.

## <a name="operating-system-support"></a>Stöd för operativsystem
SAP-produkttillgänglighetsmatrisen innehåller kombinationerna operativsystem och SAP-kärna som stöds för varje SAP-program.  Linux-distributioner SUSE 12.x, SUSE 15.x, Red Hat 7.x stöds fullt ut.  Oracle Linux som operativsystem för SAP ASE stöds inte.  Vi rekommenderar att du använder de senaste Linux-versionerna som finns tillgängliga. Windows-kunder bör använda Windows Server 2016- eller Windows Server 2019-versioner.  Äldre versioner av Windows som Windows 2012 stöds tekniskt, men den senaste Windows-versionen rekommenderas alltid.


## <a name="specifics-to-sap-ase-on-windows"></a>Detaljer till SAP ASE i Windows
Från och med Microsoft Azure kan du migrera dina befintliga SAP ASE-program till Virtuella Azure-datorer. SAP ASE i en Virtuell Azure-dator gör att du kan minska den totala ägandekostnaden för distribution, hantering och underhåll av företagsbreddsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en virtuell Azure-dator kan administratörer och utvecklare fortfarande använda samma utvecklings- och administrationsverktyg som finns tillgängliga lokalt.

Microsoft Azure erbjuder många olika typer av virtuella datorer som gör att du kan köra minsta SAP-system och landskap upp till stora SAP-system och landskap med tusentals användare. SAP-storlek på SAPS-nummer för de olika SAP-certifierade VM-SKU:erna finns i [SAP-supportmeddelandet #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentation för att installera SAP ASE i Windows finns i [SAP ASE Installationsguide för Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Lås sidor i minnet är en inställning som förhindrar att SAP ASE-databasbufferten återställs.  Den här inställningen är användbar för stora upptagna system med mycket minne. Kontakta BC-DB-SYB för mer information. 


## <a name="linux-operating-system-specific-settings"></a>Specifika inställningar för Linux-operativsystem
På Virtuella Linux-datorer bör köras `saptune` med profilen SAP-ASE Linux Huge Pages aktiveras som standard och kan verifieras med kommando  

`cat /proc/meminfo` 

Sidstorleken är vanligtvis 2048 KB. För mer information se artikeln [Huge Pages på Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Rekommendationer om VM och diskstruktur för SAP ASE-distributioner

SAP ASE för SAP NetWeaver-program stöds på alla VM-typer som anges i [SAP-stödanteckningen #1928533](https://launchpad.support.sap.com/#/notes/1928533) typiska VM-typer som används för medelstora SAP ASE-databasservrar inkluderar Esv3.  Stora multi-terabyte databaser kan utnyttja M-serien VM-typer. SAP ASE-transaktionsloggens skrivprestanda kan förbättras genom att aktivera Skrivaccelerator i M-serien. Write Accelerator bör testas noggrant med SAP ASE på grund av det sätt som SAP ASE utför Loggskrivningar.  Granska [SAP-supportanteckning #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) och överväg att köra ett prestandatest.  
Write Accelerator är endast avsedd för transaktionsloggdisk. Disknivåcachen ska anges till NONE. Bli inte förvånad om Azure Write Accelerator inte visar liknande förbättringar som med andra DBMS. Baserat på hur SAP ASE skriver i transaktionsloggen kan det vara så att det finns liten eller ingen acceleration av Azure Write Accelerator.
Separata diskar rekommenderas för dataenheter och loggenheter.  Systemet databaser sybsecurity `saptools` och kräver inte dedikerade diskar och kan placeras på diskar som innehåller SAP databasdata och loggenheter 

![Lagringskonfiguration för SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Filsystem, randstorlek & IO-balansering 
SAP ASE skriver data sekventiellt i disklagringsenheter om inte annat konfigureras. Detta innebär att en tom SAP ASE-databas med fyra enheter endast skriver data till den första enheten.  De andra diskenheterna skrivs bara till när den första enheten är full.  Mängden LÄS- och SKRIV-IO till varje SAP ASE-enhet kommer sannolikt att vara annorlunda. För att balansera disk-I/O på alla tillgängliga Azure-diskar måste antingen Windows Storage Spaces eller Linux LVM2 användas. På Linux rekommenderas att använda XFS filsystem för att formatera diskarna. LVM-randstorleken bör testas med ett prestandatest. 128 KB randstorlek är en bra utgångspunkt. I Windows bör NTFS Allocation Unit Size (AUS) testas. 64 KB kan användas som startvärde. 

Vi rekommenderar att du konfigurerar automatisk databasexpansion enligt beskrivningen i artikeln [Konfigurera automatisk databasrymdsexpansion i SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) och [SAP-stödanteckning #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Exempel på SAP ASE på Azure-konfigurationer för virtuella datorer, disk- och filsystem 
Mallarna nedan visar exempelkonfigurationer för både Linux och Windows. Innan du bekräftar den virtuella datorn och diskkonfigurationen se till att nätverks- och lagringsbandbreddskvoterna för den enskilda virtuella datorn är tillräckliga för att uppfylla affärskravet. Tänk också på att olika Azure VM-typer har olika maximala antal diskar som kan kopplas till den virtuella datorn. En E4s_v3 vm har till exempel ett i/delflöde för lagring på 48 MB/sek. Om lagringsdataflödet som krävs av säkerhetskopieringsaktivitet för databaser kräver mer än 48 MB/sek är en större vm-typ med mer dataflöde för lagringsbandbredd oundviklig. När du konfigurerar Azure-lagring måste du också tänka på att dataflödet och IOPS per GB kapacitet ändras särskilt med [Azure Premium-lagring.](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) Läs mer om det här avsnittet i artikeln [Vilka disktyper finns i Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). Kvoterna för specifika Azure VM-typer dokumenteras i artikeln [Minnesoptimerade storlekar](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) och artiklar som är länkade till den. 

> [!NOTE]
>  Om ett DBMS-system flyttas från lokalt till Azure, rekommenderas att utföra övervakning på den virtuella datorn och bedöma CPU, minne, IOPS och lagringsdataflöde. Jämför toppvärdena som observerats med de kvotgränser för virtuella datorer som dokumenteras i de artiklar som nämns ovan

Exemplen nedan är för belysande ändamål och kan ändras utifrån individuella behov. På grund av utformningen av SAP ASE är antalet dataenheter inte lika kritiskt som med andra databaser. Antalet dataenheter som beskrivs i det här dokumentet är endast en guide. 

Ett exempel på en konfiguration för en liten SAP ASE DB Server med en databasstorlek på mellan 50 GB – 250 GB, till exempel SAP-lösningshanteraren, kan se ut

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| Antal dataenheter | 4 | 4 | ---|
| Antal loggenheter | 1 | 1 | --- |
| Antal temp-enheter | 1 | 1 | mer för SAP BW-arbetsbelastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 eller RHEL 7.6 | --- |
| Aggregering av disk | Lagringsutrymmen | LVM2 (LVM2) | --- |
| Filsystem | NTFS | Xfs |
| Formatblockstorlek | behöver arbetsbelastningstestning | behöver arbetsbelastningstestning | --- |
| # och typ av datadiskar | Premium-lagring: 2 x P10 (RAID0) | Premium-lagring: 2 x P10 (RAID0)| Cache = Skrivskyddad |
| # och typ av loggdiskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = INGEN |
| Ase MaxMemory-parameter | 90% av fysisk RAM | 90% av fysisk RAM | förutsatt att en instans |
| Antal säkerhetskopieringsenheter | 4 | 4| --- |
| # och typ av säkerhetskopieringsdiskar | 1 | 1 | --- |


Ett exempel på en konfiguration för en medelstor SAP ASE DB Server med en databasstorlek på mellan 250 GB – 750 GB, till exempel ett mindre SAP Business Suite-system, kan se ut så här

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| Antal dataenheter | 8 | 8 | ---|
| Antal loggenheter | 1 | 1 | --- |
| Antal temp-enheter | 1 | 1 | mer för SAP BW-arbetsbelastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 eller RHEL 7.6 | --- |
| Aggregering av disk | Lagringsutrymmen | LVM2 (LVM2) | --- |
| Filsystem | NTFS | Xfs |
| Formatblockstorlek | behöver arbetsbelastningstestning | behöver arbetsbelastningstestning | --- |
| # och typ av datadiskar | Premium-lagring: 4 x P20 (RAID0) | Premium-lagring: 4 x P20 (RAID0)| Cache = Skrivskyddad |
| # och typ av loggdiskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = INGEN |
| Ase MaxMemory-parameter | 90% av fysisk RAM | 90% av fysisk RAM | förutsatt att en instans |
| Antal säkerhetskopieringsenheter | 4 | 4| --- |
| # och typ av säkerhetskopieringsdiskar | 1 | 1 | --- |

Ett exempel på en konfiguration för en liten SAP ASE DB Server med en databasstorlek mellan 750 GB – 2000 GB, till exempel ett större SAP Business Suite-system, kan se ut

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| Antal dataenheter | 16 | 16 | ---|
| Antal loggenheter | 1 | 1 | --- |
| Antal temp-enheter | 1 | 1 | mer för SAP BW-arbetsbelastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 eller RHEL 7.6 | --- |
| Aggregering av disk | Lagringsutrymmen | LVM2 (LVM2) | --- |
| Filsystem | NTFS | Xfs |
| Formatblockstorlek | behöver arbetsbelastningstestning | behöver arbetsbelastningstestning | --- |
| # och typ av datadiskar | Premium-lagring: 4 x P30 (RAID0) | Premium-lagring: 4 x P30 (RAID0)| Cache = Skrivskyddad |
| # och typ av loggdiskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = INGEN |
| Ase MaxMemory-parameter | 90% av fysisk RAM | 90% av fysisk RAM | förutsatt att en instans |
| Antal säkerhetskopieringsenheter | 4 | 4| --- |
| # och typ av säkerhetskopieringsdiskar | 1 | 1 | --- |


Ett exempel på en konfiguration för en liten SAP ASE DB Server med en databasstorlek på 2 TB+, till exempel ett större globalt använt SAP Business Suite-system, kan se ut så här

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | M-serien (1,0 till 4,0 TB RAM)  | M-serien (1,0 till 4,0 TB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| Antal dataenheter | 32 | 32 | ---|
| Antal loggenheter | 1 | 1 | --- |
| Antal temp-enheter | 1 | 1 | mer för SAP BW-arbetsbelastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 eller RHEL 7.6 | --- |
| Aggregering av disk | Lagringsutrymmen | LVM2 (LVM2) | --- |
| Filsystem | NTFS | Xfs |
| Formatblockstorlek | behöver arbetsbelastningstestning | behöver arbetsbelastningstestning | --- |
| # och typ av datadiskar | Premium-lagring: 4+ x P30 (RAID0) | Premium-lagring: 4+ x P30 (RAID0)| Cache = Skrivskyddad, tänk på Azure Ultra-disk |
| # och typ av loggdiskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = NONE, Tänk på Azure Ultra-disk |
| Ase MaxMemory-parameter | 90% av fysisk RAM | 90% av fysisk RAM | förutsatt att en instans |
| Antal säkerhetskopieringsenheter | 16 | 16 | --- |
| # och typ av säkerhetskopieringsdiskar | 4 | 4 | Använd LVM2/lagringsutrymmen |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Säkerhetskopiering & återställa överväganden för SAP ASE på Azure
Om du ökar antalet data- och säkerhetskopieringsenheter ökar säkerhetskopierings- och återställningsprestanda. Vi rekommenderar att du striperar Azure-diskarna som är värdar för SAP ASE-säkerhetskopieringsenheten som visas i tabellerna som visas tidigare. Försiktighet bör iakttas för att balansera antalet enheter och diskar för säkerhetskopiering och se till att dataflödet för säkerhetskopiering inte bör överstiga 40-50 % av den totala kvoten för datorflöde. Vi rekommenderar att du använder SAP Backup Compression som standard. Mer information finns i artiklarna:

- [SAP-supportanteckning #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP-supportanteckning #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP-supportanteckning #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Använd inte enhet D:\ eller /temp space som databas- eller loggdumpmål.

### <a name="impact-of-database-compression"></a>Inverkan av databaskomprimering
I konfigurationer där I/O-bandbredd kan bli en begränsande faktor kan åtgärder som minskar IOPS bidra till att sträcka ut arbetsbelastningen som man kan köra i ett IaaS-scenario som Azure. Därför rekommenderas att se till att SAP ASE-komprimering används innan du överför en befintlig SAP-databas till Azure.

Rekommendationen att tillämpa komprimering innan du överför till Azure ges av flera orsaker:

* Mängden data som ska överföras till Azure är lägre
* Varaktigheten av komprimeringskörningen är kortare förutsatt att man kan använda starkare maskinvara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt
* Mindre databasstorlekar kan leda till lägre kostnader för diskallokering

Data- och LOB-komprimering fungerar i en virtuell dator som finns i Virtuella Azure-datorer som den gör lokalt. Mer information om hur du kontrollerar om komprimering redan används i en befintlig SAP ASE-databas finns i [SAP-stödanteckningen 1750510](https://launchpad.support.sap.com/#/notes/1750510). Mer information om SAP ASE-databaskomprimeringskontroll finns [i SAP-stödanteckningen #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Hög tillgänglighet för SAP ASE på Azure 
Hadr-användarguiden beskriver installationen och konfigurationen av en 2-nod SAP ASE-lösning "Alltid på".  Dessutom stöds även en tredje nod för haveriberedskap. SAP ASE stöder många konfigurationer med hög tillgänglighet, inklusive delad disk och inbyggd OS-kluster (flytande IP). Den enda konfiguration som stöds på Azure använder Fault Manager utan flytande IP.  Metoden Flytande IP-adress fungerar inte på Azure.  SAP Kernel är ett "HA Aware"-program och känner till de primära och sekundära SAP ASE-servrarna. Det finns inga nära integreringar mellan SAP ASE och Azure, Azure Internal load balancer används inte. Därför bör standard-SAP ASE-dokumentationen följas med början i [SAP ASE HADR-användarhandboken](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Den enda konfiguration som stöds på Azure använder Fault Manager utan flytande IP.  Metoden Flytande IP-adress fungerar inte på Azure. 

### <a name="third-node-for-disaster-recovery"></a>Tredje noden för haveriberedskap
Förutom att använda SAP ASE Always-On för lokal hög tillgänglighet, kanske du vill utöka konfigurationen till en asynkront replikerad nod i en annan Azure-region. Dokumentation för ett sådant scenario finns [här](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE-databaskryptering & SSL 
SAP Software provisioning Manager (SWPM) ger en möjlighet att kryptera databasen under installationen.  Om du vill använda kryptering rekommenderar vi att du använder SAP-fullständig databaskryptering.  Se detaljer dokumenterade i:

- [SAP-supportanteckning #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP-supportanteckning #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP-supportanteckning #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP-supportanteckning #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Om en SAP ASE-databas är krypterad kommer säkerhetskopieringsdumpkomprimering inte att fungera. Se även [SAP-support #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE på checklista för Azure-distribution
 
- Distribuera SAP ASE 16.0.03.07 eller senare
- Uppdatera till senaste versionen och patchar av FaultManager och SAPHostAgent
- Distribuera på senast certifierade operativsystem som är tillgängligt, till exempel Windows 2019, Suse 15.1 eller Redhat 7.6 eller senare
- Använd SAP-certifierade virtuella datorer – azure VM-sku:er med högt minne, till exempel Es_v3 eller för x-stora system M-serien VM-SKU: er, rekommenderas
- Matcha diskenS IOPS och den totala kvoten för samlat dataflöde för den virtuella datorn med diskdesignen.  Distribuera tillräckligt många diskar
- Aggregerade diskar med Windows Storage Spaces eller Linux LVM2 med rätt randstorlek och filsystem
- Skapa tillräckligt många enheter för data-, logg-, temp- och säkerhetskopieringsändamål
- Överväg att använda UltraDisk för x-stora system 
- Kör `saptune` SAP-ASE på Linux OS 
- Skydda databasen med DB-kryptering – lagra nycklar manuellt i Azure Key Vault 
- Slutför [SAP-checklistan för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Konfigurera säkerhetskopiering av loggar och fullständig säkerhetskopiering 
- Testa HA/DR, säkerhetskopiera och återställa och utföra stress & volymtest 
- Bekräfta att det automatiska databastillägget fungerar 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka databasinstanser
För SAP-system, som använder SAP ASE som databasplattform, är DBACockpit tillgänglig som inbäddade webbläsarfönster i transaktion DBACockpit eller som Webdynpro. Den fullständiga funktionaliteten för övervakning och administrering av databasen är dock endast tillgänglig i Webdynpro-implementeringen av DBACockpit.

Som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro-implementeringen av DBACockpit. Följ [SAP-supportanteckningen #1245200](https://launchpad.support.sap.com/#/notes/1245200) för att aktivera användning av webdynpros och generera de nödvändiga. När du följer instruktionerna i ovanstående anteckningar konfigurerar du`ICM`även Internet Communication Manager ( ) tillsammans med de portar som ska användas för http- och https-anslutningar. Standardinställningen för http ser ut som:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

och länkarna som genereras i transaktionen DBACockpit liknar:

> https:\//\<fullt kvalificerathostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullt kvalificeradehostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på hur Azure Virtual Machine som är värd för SAP-systemet är anslutet till din AD och DNS måste du se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du öppnar DBACockpit från. Se [SAP-supportanteckning #773830](https://launchpad.support.sap.com/#/notes/773830) för att förstå hur ICM bestämmer det fullständigt kvalificerade värdnamnet baserat på profilparametrar och ange parameter icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett cloud-only-scenario utan anslutning mellan lokala och `domainlabel`Azure måste du definiera en offentlig IP-adress och en . Formatet för det offentliga DNS-namnet på den virtuella datorn ser ut som:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Mer information om DNS-namnet finns [här][virtual-machines-azurerm-versus-azuresm].

Om du ställer in ICM/host_name_full för DNS-namnet på den virtuella Azure-datorn kan länken se ut ungefär som:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

I det här fallet måste du se till att:

* Lägga till inkommande regler i nätverkssäkerhetsgruppen i Azure-portalen för TCP/IP-portarna som används för att kommunicera med ICM
* Lägga till inkommande regler i Windows-brandväggen för TCP/IP-portar som används för att kommunicera med ICM

För en automatiserad importerad av alla tillgängliga korrigeringar rekommenderar vi att du regelbundet tillämpar den korrigeringsinsamling SOM gäller för din SAP-version:

* [SAP-supportanteckning #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP-supportanteckning #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP-supportanteckning #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Mer information om DBA Cockpit för SAP ASE finns i följande SAP Notes:

* [SAP-supportanteckning #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP-supportanteckning #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP-supportanteckning #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP-supportanteckning #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP-supportanteckning #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP-supportanteckning #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP-supportanteckning #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP-supportanteckning #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Användbara länkar, anteckningar & whitepapers för SAP ASE
Startsidan för [SAP ASE 16.0.03.07 Dokumentation](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) ger länkar till olika dokument där dokumenten för:

- SAP ASE Learning Journey - Administration & Övervakning
- SAP ASE Learning Journey - Uppgradering & installation

är till hjälp. Ett annat användbart dokument är [SAP-program på SAP Adaptive Server Enterprise Best Practices for Migration and Runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Andra användbara SAP-supportanteckningar är:

- [SAP-supportanteckning #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP-supportanteckning #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP-supportanteckning #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP-supportanteckning #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP-supportanteckning #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP-supportanteckning #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP-supportanteckning #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-supportanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP-supportanteckning #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP-supportanteckning #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP-supportanteckning #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP-supportanteckning #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-supportanteckning #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Övrig information publiceras på 

- [SAP-program på SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE infocenter](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always-on med installation av tredje DR-nod](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Ett månatligt nyhetsbrev publiceras via [SAP-supportmeddelande #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Nästa steg
Kontrollera artikeln [SAP-arbetsbelastningar på Azure: checklista för planering och distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

