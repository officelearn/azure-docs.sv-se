---
title: Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft-dokument
description: Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101368"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den här guiden är en del av dokumentationen om hur du implementerar och distribuerar SAP-programvara på Microsoft Azure. Innan du läser den här guiden kan du läsa [planerings- och implementeringsguiden][planning-guide]. Det här dokumentet täcker de allmänna distributionsaspekterna av SAP-relaterade DBMS-system på Microsoft Azure-virtuella datorer (VMs) med hjälp av Azure-infrastrukturen som tjänstfunktioner (IaaS).

Papperet kompletterar SAP-installationsdokumentationen och SAP Notes, som representerar de primära resurserna för installationer och distributioner av SAP-programvara på givna plattformar.

I det här dokumentet introduceras överväganden om att köra SAP-relaterade DBMS-system i virtuella Azure-datorer. Det finns få referenser till specifika DBMS-system i det här kapitlet. I stället hanteras de specifika DBMS-systemen i det här dokumentet, efter det här dokumentet.

## <a name="definitions"></a>Definitioner
I hela dokumentet används dessa termer:

* **IaaS**: Infrastruktur som en tjänst.
* **PaaS**: Plattform som en tjänst.
* **SaaS**: Programvara som en tjänst.
* **SAP-komponent**: Ett enskilt SAP-program som ERP Central Component (ECC), Business Warehouse (BW), Solution Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionell ABAP- eller Java-teknik eller på ett icke-NetWeaver-baserat program som affärsobjekt.
* **SAP-miljö**: En eller flera SAP-komponenter grupperade logiskt för att utföra en affärsfunktion som utveckling, kvalitetssäkring, utbildning, haveriberedskap eller produktion.
* **SAP-landskap**: Den här termen refererar till hela SAP-tillgångarna i en kunds IT-landskap. SAP-landskapet omfattar alla produktions- och icke-produktionsmiljöer.
* **SAP-system**: Kombinationen av ett DBMS-lager och ett programlager av till exempel ett SAP ERP-utvecklingssystem, ett SAP Business Warehouse-testsystem eller ett SAP CRM-produktionssystem. I Azure-distributioner stöds inte uppdelning av dessa två lager mellan lokala och Azure. Därför distribueras ett SAP-system antingen lokalt eller distribueras i Azure. Du kan distribuera de olika systemen i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings- och testsystemen i Azure men distribuera SAP CRM-produktionssystemet lokalt.
* **Korslokaler:** Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats, multiwebbplats eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanlig Azure-dokumentation beskrivs även dessa typer av distributioner som scenarier över flera lokala. 

    Anledningen till anslutningen är att utöka lokala domäner, lokala Active Directory och lokal DNS till Azure. Det lokala landskapet utökas till Azure-tillgångarna för prenumerationen. Med det här tillägget kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare av den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer, till exempel DBMS-tjänster. Kommunikation och namnmatchning mellan virtuella datorer som distribueras lokalt och virtuella datorer som distribueras i Azure är möjlig. Det här scenariot är det vanligaste scenariot som används för att distribuera SAP-resurser på Azure. Mer information finns i [Planera och designa VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Korslokal distributioner av SAP-system är där virtuella Azure-datorer som kör SAP-system är medlemmar i en lokal domän och stöds för produktion SAP-system. Korslokala konfigurationer stöds för distribution av delar eller fullständiga SAP-landskap till Azure. Även kör hela SAP-landskapet i Azure kräver dessa virtuella datorer att vara en del av en lokal domän och Active Directory / LDAP. 
>
> I tidigare versioner av dokumentationen nämndes hybrid-IT-scenarier. Termen *hybrid* har sin grund i det faktum att det finns en anslutning mellan lokala och Azure. I det här fallet innebär hybrid också att de virtuella datorerna i Azure är en del av den lokala Active Directory.
>
>

I en viss Microsoft-dokumentation beskrivs scenarier över flera lokala scenarier lite annorlunda, särskilt för DBMS-konfigurationer med hög tillgänglighet. När det gäller SAP-relaterade dokument kokar det lokala scenariot ner till plats-till-plats- eller privat [ExpressRoute-anslutning](https://azure.microsoft.com/services/expressroute/) och ett SAP-landskap som distribueras mellan lokala och Azure.

## <a name="resources"></a>Resurser
Det finns andra artiklar om SAP-arbetsbelastning på Azure. Börja med [SAP-arbetsbelastning på Azure: Kom igång](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) och välj sedan ditt intresseområde.

Följande SAP Notes är relaterade till SAP på Azure med avseende på det område som omfattas av det här dokumentet.

| Antecknar numrerar | Titel |
| --- | --- |
| [1928533] |SAP-program på Azure: Produkter som stöds och Azure VM-typer |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Felsöka förbättrad Azure-övervakning för SAP |
| [2178632] |Viktiga övervakningsmått för SAP på Microsoft Azure |
| [1409604] |Virtualisering i Windows: Förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: Förbättrad övervakning |
| [2039619] |SAP-program på Microsoft Azure med Oracle-databasen: Produkter och versioner som stöds |
| [2233094] |DB6: SAP-program på Azure med IBM DB2 för Linux, UNIX och Windows: Ytterligare information |
| [2243692] |Linux på Microsoft Azure (IaaS) VM: SAP-licensproblem |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsanteckningar |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP installation och uppgradering |
| [1597355] |Rekommendation om växlingsutrymme för Linux |
| [2171857] |Oracle Database 12c: Stöd för filsystem på Linux |
| [1114181] |Oracle Database 11g: Stöd för filsystem på Linux |


Information om alla SAP Notes för Linux finns i [SAP-communitywikin](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Du behöver en fungerande kunskap om Microsoft Azure-arkitektur och hur microsoft Azure virtuella datorer distribueras och drivs. Mer information finns i [Azure-dokumentation](https://docs.microsoft.com/azure/).

I allmänhet är installation och konfiguration av Windows, Linux och DBMS i stort sett desamma som alla virtuella datorer eller bare metal-datorer som du installerar lokalt. Det finns vissa beslut om implementering av arkitektur och systemhantering som är olika när du använder Azure IaaS. I det här dokumentet beskrivs de specifika skillnader i arkitektur- och systemhantering som ska förberedas för när du använder Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Lagringsstruktur för en virtuell dator för RDBMS-distributioner
Om du vill följa det här kapitlet läser och förstår du informationen som presenteras i [det här kapitlet][deployment-guide-3] i [distributionsguiden][deployment-guide]. Du måste förstå och känna till de olika VM-serien och skillnaderna mellan standard- och premiumlagring innan du läser det här kapitlet. 

Mer information om virtuella Azure-lagring för virtuella Azure-datorer finns i:

- [Introduktion till hanterade diskar för virtuella Azure Windows-datorer](../../windows/managed-disks-overview.md).
- [Introduktion till hanterade diskar för virtuella Azure Linux-datorer](../../linux/managed-disks-overview.md).

I en grundläggande konfiguration rekommenderar vi vanligtvis en distributionsstruktur där operativsystemet, DBMS och eventuella SAP-binärfiler är åtskilda från databasfilerna. Vi rekommenderar att SAP-system som körs i virtuella Azure-datorer har bas-VHD, eller disk, installerat med operativsystemet, databashanteringssystem körbara filer och SAP-körbara filer. 

DBMS-data och loggfiler lagras i standardlagring eller premiumlagring. De lagras i separata diskar och bifogas som logiska diskar till den ursprungliga virtuella datorn för Azure-operativsystem. För Linux-distributioner dokumenteras olika rekommendationer, särskilt för SAP HANA.

När du planerar disklayouten hittar du det bästa saldot mellan dessa objekt:

* Antalet datafiler.
* Antalet diskar som innehåller filerna.
* IOPS-kvoterna för en enda disk.
* Dataflödet per disk.
* Antalet ytterligare datadiskar som är möjliga per vm-storlek.
* Det övergripande lagringsdataflödet som en virtuell dator kan tillhandahålla.
* Svarstiden olika Azure Storage-typer kan tillhandahålla.
* VM-sla.

Azure tillämpar en IOPS-kvot per datadisk. Dessa kvoter är olika för diskar som finns på standardlagring och premiumlagring. I/O-svarstid skiljer sig också mellan de två lagringstyperna. Premiumlagring ger bättre I/O-latens. 

Var och en av de olika vm-typerna har ett begränsat antal datadiskar som du kan koppla. En annan begränsning är att endast vissa vm-typer kan använda premiumlagring. Vanligtvis bestämmer du dig för att använda en viss vm-typ baserat på CPU- och minneskrav. Du kan också överväga kraven för IOPS-, svarstid och diskdataflöde som vanligtvis skalas med antalet diskar eller typen av premiumlagringsdiskar. Antalet IOPS och dataflödet som ska uppnås av varje disk kan diktera diskstorlek, särskilt med premiumlagring.

> [!NOTE]
> För DBMS-distributioner rekommenderar vi användning av premiumlagring för data, transaktionslogg eller gör om filer. Det spelar ingen roll om du vill distribuera produktions- eller icke-produktionssystem.

> [!NOTE]
> För att dra nytta av Azures unika [single VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)måste alla diskar som är kopplade vara premiumlagringstypen, vilket inkluderar bas-VHD.

> [!NOTE]
> Att vara värd för huvuddatabasfiler, till exempel data och loggfiler, av SAP-databaser på lagringsmaskinvara som finns i datacenter från tredje part som ligger intill Azure-datacenter stöds inte. För SAP-arbetsbelastningar stöds endast lagring som representeras som inbyggd Azure-tjänst för data- och transaktionsloggfiler i SAP-databaser.

Placeringen av databasfilerna och loggen och gör om filer och vilken typ av Azure Storage du använder definieras av IOPS- och svarstiderskrav. Om du vill ha tillräckligt med IOPS kan du behöva använda flera diskar eller använda en större premiumlagringsdisk. Om du använder flera diskar skapar du en programvaruremsa över diskarna som innehåller datafilerna eller loggen och gör om filer. I sådana fall ackumuleras IOPS- och diskdataflödesslalabilerna på de underliggande premiumlagringsdiskarna eller den högsta möjliga IOPS-standarden för standardlagringsdiskar för den resulterande stripe-uppsättningen.

Som redan nämnts, om ditt IOPS-krav överskrider vad en enda virtuell hårddisk kan ge, balansera antalet IOPS som behövs för databasfilerna över ett antal virtuella hårddiskar. Det enklaste sättet att distribuera IOPS-belastningen över diskar är att bygga en programvaruredr över de olika diskarna. Placera sedan ett antal datafiler i SAP DBMS på LUN huggen ur programvaran randen. Antalet diskar i randen drivs av IOPs-krav, diskgenomströmningskrav och volymkrav.


---
> ![Windows][Logo_Windows] Windows
>
> Vi rekommenderar att du använder Windows Lagringsutrymmen för att skapa stripe-uppsättningar över flera Virtuella Azure-hårddiskar. Använd minst Windows Server 2012 R2 eller Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Endast MDADM och Logical Volume Manager (LVM) stöds för att bygga en programvara RAID på Linux. Mer information finns i:
>
> - [Konfigurera programvara RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) med MDADM
> - [Konfigurera LVM på en Virtuell Linux-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) med LVM
>
>

---

> [!NOTE]
> Eftersom Azure Storage behåller tre avbildningar av hårddiskarna är det inte meningsfullt att konfigurera en redundans när du stripe. Du behöver bara konfigurera striping så att I/Os fördelas över de olika virtuella hårddiskarna.
>

### <a name="managed-or-nonmanaged-disks"></a>Hanterade eller ickehanterade diskar
Ett Azure-lagringskonto är en administrativ konstruktion och även ett ämne för begränsningar. Begränsningarna skiljer sig åt mellan standardlagringskonton och premiumlagringskonton. Information om funktioner och begränsningar finns i [Azure Storage skalbarhet och prestandamål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

För standardlagring, kom ihåg att det finns en gräns för IOPS per lagringskonto. Se raden som innehåller **total begärandehastighet** i artikeln [Azure Storage skalbarhet och prestandamål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Det finns också en första gräns för antalet lagringskonton per Azure-prenumeration. Balanserade virtuella hårddiskar för större SAP-ligganden över olika lagringskonton för att undvika att träffa gränserna för dessa lagringskonton. Detta är tråkigt arbete när du pratar om några hundra virtuella maskiner med mer än tusen virtuella hårddiskar.

Eftersom det inte rekommenderas att använda standardlagring för DBMS-distributioner tillsammans med en SAP-arbetsbelastning, är referenser och rekommendationer till standardlagring begränsade till den här korta [artikeln](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

För att undvika det administrativa arbetet med att planera och distribuera virtuella hårddiskar över olika Azure-lagringskonton introducerade Microsoft [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017. Hanterade diskar är tillgängliga för standardlagring och premiumlagring. De största fördelarna med hanterade diskar jämfört med ickehanterade diskar är:

- För hanterade diskar distribuerar Azure de olika virtuella hårddiskarna över olika lagringskonton automatiskt vid distributionen. På så sätt träffas inte lagringskontogränser för datavolym, I/O-dataflöde och IOPS.
- Med hjälp av hanterade diskar hedrar Azure Storage begreppen Azure-tillgänglighetsuppsättningar. Om den virtuella datorn är en del av en Azure-tillgänglighetsuppsättning distribueras den grundläggande virtuella hårddisken och den anslutna disken för en virtuell dator till olika fel- och uppdateringsdomäner.


> [!IMPORTANT]
> Med tanke på fördelarna med Azure Managed Disks rekommenderar vi att du använder Azure Managed Disks för dina DBMS-distributioner och SAP-distributioner i allmänhet.
>

Om du vill konvertera från ohanterade till hanterade diskar finns i:

- [Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och datadiskar
När du monterar diskar på virtuella datorer kan du välja om I/O-trafiken mellan den virtuella datorn och de diskar som finns i Azure-lagring cachelagras. Standard- och premiumlagring använder två olika tekniker för den här typen av cache.

Följande rekommendationer förutsätter dessa I/O-egenskaper för standard-DBMS:

- Det är mest en läsarbetsbelastning mot datafiler i en databas. Dessa läsningar är prestandakritiska för DBMS-systemet.
- Skriva mot datafilerna sker i skurar baserat på kontrollpunkter eller en konstant ström. Genomsnitt över en dag, det finns färre skrivningar än läser. Motsatsen till läsningar från datafiler, dessa skrivningar är asynkrona och inte hålla upp några användartransaktioner.
- Det finns knappast några läsningar från transaktionsloggen eller gör om filer. Undantag är stora I/Os när du utför säkerhetskopior av transaktionsloggen.
- Huvudbelastningen mot transaktion eller gör om loggfiler är skrivningar. Beroende på vilken typ av arbetsbelastning, kan du ha I / Os så liten som 4 KB eller, i andra fall, I / O-storlekar på 1 MB eller mer.
- Alla skrivningar måste finnas kvar på disken på ett tillförlitligt sätt.

För standardlagring är de möjliga cachetyperna:

* Inget
* Läsa
* Läsa/Skriva

Om du vill få konsekvent och deterministisk prestanda ställer du in cachelagringen på standardlagring för alla diskar som innehåller DBMS-relaterade datafiler, loggar och gör om filer och tabellutrymme till **NONE**. Cachelagringen av bas-VHD kan förbli med standardvärdet.

För premiumlagring finns följande cachelagringsalternativ:

* Inget
* Läsa
* Läsa/skriva
* Ingen + Skrivaccelerator, som endast är för virtuella Azure M-serien
* Läs + skrivaccelerator, som endast är för virtuella Azure M-serien

För premiumlagring rekommenderar vi att du använder **Läsa cachelagring för datafiler** i SAP-databasen och väljer **Ingen cachelagring för diskarna i loggfilerna.**

För M-seriedistributioner rekommenderar vi att du använder Azure Write Accelerator för din DBMS-distribution. Mer information, begränsningar och distribution av Azure Write Accelerator finns i [Aktivera skrivaccelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Icke-utspänd diskar i Azure
Virtuella Azure-datorer erbjuder icke-utspända diskar när en virtuell dator har distribuerats. När det gäller en omstart av den virtuella datorn raderas allt innehåll på dessa enheter. Det är en självklarhet att datafiler och logga och göra om filer av databaser bör under inga omständigheter finnas på dessa nonpersisted enheter. Det kan finnas undantag för vissa databaser, där dessa icke-stödda enheter kan vara lämpliga för tempdb och temp tablespaces. Undvik att använda dessa enheter för virtuella datorer i A-serien eftersom dessa icke-stödda enheter är begränsade i dataflödet med den virtuella datorn. 

Mer information finns [i Förstå den tillfälliga enheten på virtuella Windows-datorer i Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> Enhet D i en Azure VM är en icke-styrd enhet, som backas upp av vissa lokala diskar på Azure-beräkningsnoden. Eftersom det inte är ouppröjt går alla ändringar som görs i innehållet på enhet D förlorade när den virtuella datorn startas om. Ändringarna omfattar filer som har lagrats, kataloger som har skapats och program som installerades.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuella linux Azure-datorer monterar automatiskt en enhet på /mnt/resource som är en icke-stödd enhet som backas upp av lokala diskar på Azure-beräkningsnoden. Eftersom det inte är ouppröjt går alla ändringar som görs i innehållet i /mnt/resource förlorade när den virtuella datorn startas om. Ändringarna omfattar filer som har lagrats, kataloger som har skapats och program som installerades.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage återhämtning
Microsoft Azure Storage lagrar bas-VHD, med OS och anslutna diskar eller blobbar, på minst tre separata lagringsnoder. Den här typen av lagring kallas lokalt redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure.

Det finns andra redundansmetoder. Mer information finns i [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Premiumlagring är den rekommenderade typen av lagring för DBMS virtuella datorer och diskar som lagrar databas och loggar och gör om filer. Den enda tillgängliga redundansmetoden för premiumlagring är LRS. Därför måste du konfigurera databasmetoder för att aktivera databasdatareplikering till en annan Azure-region eller tillgänglighetszon. Databasmetoderna inkluderar SQL Server Always On, Oracle Data Guard och HANA System Replication.


> [!NOTE]
> För DBMS-distributioner rekommenderas inte användning av geouppsagbar lagring (GRS) för standardlagring. GRS påverkar prestanda och respekterar inte skrivordningen för olika virtuella hårddiskar som är kopplade till en virtuell dator. Att inte uppfylla skrivordningen för olika virtuella hårddiskar leder potentiellt till inkonsekventa databaser på replikeringsmålsidan. Den här situationen uppstår om databas- och logg- och gör om-filer sprids över flera virtuella hårddiskar, vilket vanligtvis är fallet, på käll-VM-sidan.



## <a name="vm-node-resiliency"></a>Återhämtning för VM-nod
Azure erbjuder flera olika SLA:er för virtuella datorer. Mer information finns i den senaste versionen av [SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Eftersom DBMS-lagret vanligtvis är avgörande för tillgängligheten i ett SAP-system måste du förstå tillgänglighetsuppsättningar, tillgänglighetszoner och underhållshändelser. Mer information om dessa begrepp finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och Hantera [tillgängligheten för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minsta rekommendation för produktion DBMS scenarier med en SAP-arbetsbelastning är att:

- Distribuera två virtuella datorer i en separat tillgänglighetsuppsättning i samma Azure-region.
- Kör dessa två virtuella datorer i samma virtuella Azure-nätverk och har nätverkskort anslutna från samma undernät.
- Använd databasmetoder för att hålla ett vänteläge med den andra virtuella datorn. Metoder kan vara SQL Server Always On, Oracle Data Guard eller HANA System Replication.

Du kan också distribuera en tredje virtuell dator i en annan Azure-region och använda samma databasmetoder för att tillhandahålla en asynkron replik i en annan Azure-region.

Information om hur du konfigurerar Azure-tillgänglighetsuppsättningar finns i [den här självstudien](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Överväganden för Azure-nätverk
I storskaliga SAP-distributioner använder du skissen för [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Använd den för din virtuella nätverkskonfiguration och behörigheter och rolltilldelningar till olika delar av organisationen.

De här metodtipsen är resultatet av hundratals kunddistributioner:

- De virtuella nätverk som SAP-programmet distribueras till har inte tillgång till Internet.
- Databas-virtuella datorer körs i samma virtuella nätverk som programlagret.
- De virtuella datorerna i det virtuella nätverket har en statisk allokering av den privata IP-adressen. Mer information finns i [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Routningsbegränsningar till och från DBMS-datorerna anges *inte* med brandväggar installerade på lokala DBMS-virtuella datorer. I stället definieras trafikroutning med [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Om du vill separera och isolera trafik till DBMS-datorn tilldelar du olika nätverkskort till den virtuella datorn. Varje nätverkskort får en annan IP-adress och varje nätverkskort tilldelas ett annat virtuellt nätverksundernät. Varje undernät har olika NSG-regler. Isolering eller separation av nätverkstrafik är ett mått för routning. Det används inte för att ange kvoter för nätverksdataflöde.

> [!NOTE]
> Tilldela statiska IP-adresser via Azure innebär att tilldela dem till enskilda virtuella nätverkskort. Tilldela inte statiska IP-adresser i gästoperativsystemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup förlitar sig på det faktum att åtminstone det primära virtuella nätverkskortet är inställt på DHCP och inte statiska IP-adresser. Mer information finns i [Felsöka säkerhetskopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Om du vill tilldela flera statiska IP-adresser till en virtuell dator tilldelar du flera virtuella nätverkskort till en virtuell dator.
>


> [!IMPORTANT]
> Det går inte att konfigurera [virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationssökvägen mellan SAP-programmet och DBMS-lagret i ett SAP NetWeaver-, Hybris- eller S/4HANA-baserat SAP-system. Den här begränsningen är av funktions- och prestandaskäl. Kommunikationssökvägen mellan SAP-programlagret och DBMS-lagret måste vara direkt. Begränsningen inkluderar inte [ASG-regler (Application Security Group) och NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) om dessa ASG- och NSG-regler tillåter en direkt kommunikationsväg. 
>
> Andra scenarier där virtuella nätverksinstallationer inte stöds finns i:
>
> * Kommunikationssökvägar mellan virtuella Azure-datorer som representerar Linux Pacemaker-klusternoder och SBD-enheter enligt beskrivningen i [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Kommunikationssökvägar mellan virtuella Azure-datorer och SOFS (Windows Server Scale-Out File Server) som har konfigurerats enligt beskrivningen i [Cluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Virtuella nätverksinstallationer i kommunikationsvägar kan enkelt fördubbla nätverksfördröjningen mellan två kommunikationspartner. De kan också begränsa dataflödet i kritiska linjer mellan SAP-programlagret och DBMS-lagret. I vissa kundscenarier kan virtuella nätverksinstallationer orsaka att Pacemaker Linux-kluster misslyckas. Det här är fall där kommunikation mellan Linux Pacemaker-klusternoderna kommunicerar till sin SBD-enhet via en virtuell nätverksinstallation.
>

> [!IMPORTANT]
> En annan design som *inte* stöds är segregeringen av SAP-programlagret och DBMS-lagret i olika virtuella Azure-nätverk som inte är [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) med varandra. Vi rekommenderar att du segregerar SAP-programlagret och DBMS-lagret med hjälp av undernät i ett virtuellt Azure-nätverk i stället för genom att använda olika virtuella Azure-nätverk. 
>
> Om du bestämmer dig för att inte följa rekommendationen och i stället segregera de två lagren i olika virtuella nätverk, måste de två virtuella nätverken [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tänk på att nätverkstrafik mellan två [peer-peer-azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuella nätverk är föremål för överföringskostnader. Enorm datavolym som består av många terabyte utbyts mellan SAP-programskiktet och DBMS-lagret. Du kan ackumulera betydande kostnader om SAP-programskiktet och DBMS-lagret är segregerade mellan två peered Azure virtuella nätverk.

Använd två virtuella datorer för din produktion DBMS-distribution inom en Azure-tillgänglighetsuppsättning. Använd också separat routning för SAP-programskiktet och hanterings- och driftstrafiken till de två DBMS-virtuella datorerna. Se följande bild:

![Diagram över två virtuella datorer i två undernät](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Använda Azure Load Balancer för att omdirigera trafik
Användningen av privata virtuella IP-adresser som används i funktioner som SQL Server Always On eller HANA System Replication kräver konfiguration av en Azure-belastningsutjämnare. Belastningsutjämnaren använder avsökningsportar för att bestämma den aktiva DBMS-noden och dirigera trafiken uteslutande till den aktiva databasnoden. 

Om det finns en redundans av databasnoden behöver SAP-programmet inte konfigureras om. I stället återansluter de vanligaste SAP-programarkitekturerna mot den privata virtuella IP-adressen. Under tiden reagerar belastningsutjämnaren på noden redundans genom att omdirigera trafiken mot den privata virtuella IP-adressen till den andra noden.

Azure erbjuder två olika [belastningsutjämna sku:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)en grundläggande SKU och en standard SKU. Om du inte vill distribuera över Azure-tillgänglighetszoner går det bra för den grundläggande belastningsutjämnaren SKU.

Dirigeras trafiken mellan DBMS-datorerna och SAP-programlagret alltid genom belastningsutjämnaren hela tiden? Svaret beror på hur du konfigurerar belastningsutjämnaren. 

För närvarande dirigeras den inkommande trafiken till DBMS-datorn alltid genom belastningsutjämnaren. Den utgående trafikvägen från DBMS-datorn till den virtuella programlagret beror på konfigurationen av belastningsutjämnaren. 

Belastningsutjämnaren erbjuder en möjlighet till DirectServerReturn. Om det alternativet är konfigurerat dirigeras *inte* trafiken som dirigeras från DBMS-datorn till SAP-programlagret via belastningsutjämnaren. I stället går den direkt till programlagret. Om DirectServerReturn inte är konfigurerad dirigeras returtrafiken till SAP-programlagret genom belastningsutjämnaren.

Vi rekommenderar att du konfigurerar DirectServerReturn i kombination med belastningsutjämnare som är placerade mellan SAP-programlagret och DBMS-lagret. Den här konfigurationen minskar nätverksfördröjningen mellan de två lagren.

Ett exempel på hur du konfigurerar den här konfigurationen med SQL Server Always On finns i [Konfigurera en ILB-lyssnare för alltid på tillgänglighetsgrupper i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Om du använder publicerade GitHub JSON-mallar som referens för dina SAP-infrastrukturdistributioner i Azure kan du studera den här [mallen för ett SAP 3-nivåsystem](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). I den här mallen kan du också se rätt inställningar för belastningsutjämnaren.

### <a name="azure-accelerated-networking"></a>Azure accelererade nätverk
Om du vill minska nätverksfördröjningen ytterligare mellan virtuella Azure-datorer rekommenderar vi att du väljer [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Använd den när du distribuerar virtuella Azure-datorer för en SAP-arbetsbelastning, särskilt för SAP-programlagret och SAP DBMS-lagret.

> [!NOTE]
> Alla vm-typer stöder inte accelererat nätverk. I föregående artikel visas de vm-typer som stöder accelererat nätverkande.
>

---
> ![Windows][Logo_Windows] Windows
>
> Mer information om hur du distribuerar virtuella datorer med accelererade nätverk för Windows finns i [Skapa en virtuell Windows-dator med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Mer information om Linux-distribution finns i [Skapa en virtuell Linux-dator med Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> När det gäller SUSE, Red Hat och Oracle Linux stöds Accelerated Networking med de senaste utgåvorna. Äldre versioner som SLES 12 SP2 eller RHEL 7.2 stöder inte Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Införande av värdövervakning
För produktionsanvändning av SAP-program i virtuella Azure-datorer kräver SAP möjligheten att hämta värdövervakningsdata från de fysiska värdar som kör virtuella Azure-datorer. En specifik SAP Host Agent-korrigeringsnivå krävs som aktiverar den här funktionen i SAPOSCOL och SAP Host Agent. Den exakta korrigeringsnivån dokumenteras i SAP Note [1409604].

Mer information om distribution av komponenter som levererar värddata till SAPOSCOL- och SAP-värdagenten och livscykelhanteringen av dessa komponenter finns i [distributionsguiden][deployment-guide].


## <a name="next-steps"></a>Nästa steg
Mer information om en viss DBMS finns i:

- [DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines](dbms_guide_sqlserver.md)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_ibm.md)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](dbms_guide_sapase.md)
- [SAP maxDB-, Live-cache- och Content Server-distribution på Azure](dbms_guide_maxdb.md)
- [Användarguide för SAP HANA på Azure](hana-vm-operations.md)
- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](sap-hana-availability-overview.md)
- [Reservguide för SAP HANA på virtuella Azure-datorer](sap-hana-backup-guide.md)

