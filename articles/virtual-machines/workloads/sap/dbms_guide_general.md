---
title: Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar | Microsoft Docs
description: Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836130"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar
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

Den här guiden är en del av dokumentationen på hur du implementerar och distribuera SAP-program på Microsoft Azure. Innan du läser den här guiden kan du läsa den [planerings-och Implementeringsguiden][planning-guide]. Det här dokumentet beskriver allmänna distribution aspekter av SAP-relaterade DBMS system på Microsoft Azure-datorer (VM) med hjälp av Azure-infrastrukturen som en tjänst (IaaS)-funktioner.

Dokumentet kompletterar dokumentationen för installation av SAP och SAP Notes som motsvarar de viktigaste resurserna för installationer och distributioner av SAP-program på den angivna plattformar.

I det här dokumentet introduceras överväganden för att köra SAP-relaterade DBMS system i virtuella Azure-datorer. Det finns några referenser till specifika DBMS system i det här kapitlet. I stället hanteras specifika DBMS system i det här dokumentet, efter det här dokumentet.

## <a name="definitions"></a>Definitioner
Dessa villkor används för hela dokumentet:

* **IaaS**: Infrastruktur som en tjänst.
* **PaaS**: Plattform som en tjänst.
* **SaaS**: Programvara som en tjänst.
* **SAP-komponenten**: Ett enskilt SAP-program, till exempel ERP Central komponent (ECC), Business Warehouse (BW), lösningen Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller på ett icke-NetWeaver-baserade program, till exempel företag objekt.
* **SAP-miljön**: En eller flera SAP-komponenter som är logiskt grupperade för att utföra en funktion för business development, kvalitetssäkring, utbildning, haveriberedskap och produktionsmiljö.
* **SAP-landskap**: Den här termen avser hela SAP-tillgångar i en kunds IT-miljön. SAP-landskap innehåller alla miljöer för produktions- och icke-produktionsmiljöer.
* **SAP-system**: Kombinationen av en DBMS-lager och ett program lager, till exempel en SAP ERP utvecklingssystem, en SAP Business Warehouse testa system eller ett SAP CRM-system för produktion. I Azure-distributioner stöds mellan dessa två lager mellan lokala och Azure inte. Därför kan är ett SAP-system antingen distribueras lokalt eller dess distribueras i Azure. Du kan distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure men distribuera SAP CRM produktionsprogrammen system på plats.
* **Mellan lokala**: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, multisite eller Azure ExpressRoute-anslutning mellan lokala data centers och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. 

    Orsaken till att anslutningen är att utöka lokala domäner, en lokal Active Directory och lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar för prenumerationen. Med det här tillägget kan de virtuella datorerna inte ingå i den lokala domänen. Domänanvändare på den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer, liksom DBMS-tjänster. Kommunikation och namnmatchning mellan virtuella datorer distribueras på plats och virtuella datorer som distribueras i Azure är möjligt. Det här scenariot är det vanligaste scenariot används för att distribuera SAP-tillgångar i Azure. Mer information finns i [planering och design för VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Mellan lokala distributioner av SAP-system är om Azure virtuella datorer som kör SAP-system är medlemmar i en lokal domän och har stöd för SAP-system för produktion. Mellan lokala konfigurationer som stöds för att distribuera delar eller slutföra SAP-landskap i Azure. Även kör fullständig SAP-landskap i Azure måste de virtuella datorerna ska vara en del av en lokal domän och Active Directory/LDAP. 
>
> Hybrid-IT-scenarier har nämns i tidigare versioner av dokumentationen. Termen *hybrid* grundas på typsystemet i det faktum att det finns en anslutning mellan olika platser mellan lokala och Azure. Hybrid innebär i det här fallet också att de virtuella datorerna i Azure är en del av den lokala Active Directory.
>
>

Vissa Microsoft-dokumentationen beskriver mellan lokala scenarier lite annorlunda särskilt för DBMS konfigurationer med hög tillgänglighet. När det gäller SAP-relaterade-dokument mellan lokala scenariot som handlar till plats-till-plats eller privat [ExpressRoute](https://azure.microsoft.com/services/expressroute/) anslutning och ett SAP-landskap som distribueras mellan lokala och Azure.

## <a name="resources"></a>Resurser
Det finns andra artiklar på SAP-arbetsbelastningar på Azure. Börja med [SAP-arbetsbelastningar på Azure: Kom igång](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) och välj sedan din intresseområde.

Följande SAP-information är relaterade till SAP på Azure avseende området som beskrivs i det här dokumentet.

| Nummer | Rubrik |
| --- | --- |
| [1928533] |SAP-program i Azure: Produkter som stöds och Azure VM-typer |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Felsökning av förbättrad Azure övervakning för SAP |
| [2178632] |Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering på Windows: Förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: Förbättrad övervakning |
| [2039619] |SAP-program på Microsoft Azure med Oracle-databasen: Produkter som stöds och versioner |
| [2233094] |DB6: SAP-program i Azure med IBM DB2 för Linux, UNIX- och Windows: Ytterligare information |
| [2243692] |Linux på Microsoft Azure (IaaS) virtuell dator: Problem med SAP-licens |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP-installation och uppgradering |
| [1597355] |Växlingsutrymme rekommendation för Linux |
| [2171857] |Oracle Database 12c: Stöd för filer system på Linux |
| [1114181] |Oracle Database 11g: Stöd för filer system på Linux |


Information om SAP Notes för Linux finns i den [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Du behöver tidigare erfarenhet av Microsoft Azure-arkitekturen och hur Microsoft Azure-datorer distribueras och hanteras. Mer information finns i [dokumentation om Azure](https://docs.microsoft.com/azure/).

I allmänhet är Windows, Linux och DBMS installationen och konfigurationen i stort sett samma som valfri virtuell dator eller bare metal datorn som du installerar lokalt. Det finns vissa arkitektur och system management implementering beslut som skiljer sig när du använder Azure IaaS. Det här dokumentet beskriver den specifika arkitekturen och system management skillnader förberedas för när du använder Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Lagringsstrukturen för en virtuell dator för RDBMS-distributioner
Om du vill följa det här kapitlet har läst och förstått informationen på [i det här kapitlet] [ deployment-guide-3] av den [Distributionsguide][deployment-guide]. Du måste förstå och veta om de olika VM-serien, samt skillnaderna mellan standard och premium storage innan du läser det här kapitlet. 

Mer information om Azure Storage för virtuella Azure-datorer, se:

- [Introduktion till hanterade diskar för virtuella Azure Windows-datorer](../../windows/managed-disks-overview.md).
- [Introduktion till hanterade diskar för virtuella Linux-datorer](../../linux/managed-disks-overview.md).

En distributionsstruktur där operativsystemet, DBMS och eventuell SAP-binärfiler skiljer sig från databasfilerna rekommenderas vanligtvis i en grundläggande konfiguration. Vi rekommenderar att SAP-system som körs i Azure-datorer har den base VHD eller disk, installeras med operativsystemet, database management system körbara filer och SAP körbara filer. 

DBMS data och loggfiler filerna lagras i standardlagring eller premiumlagring. De lagras i separata diskar och ansluten som logiska diskar på den ursprungliga Azure operativsystemavbildningen VM. För Linux-distributioner dokumenteras olika rekommendationer särskilt för SAP HANA.

När du planerar din disklayouten kan hitta det bästa förhållandet mellan dessa objekt:

* Antalet datafiler.
* Antalet diskar som innehåller filerna.
* IOPS-kvoter för en enda disk.
* Data dataflöde per disk.
* Antal ytterligare datadiskar som är möjliga per VM-storlek.
* Det totala genomflödet som en virtuell dator kan ge.
* Svarstid som kan ge olika typer av Azure Storage.
* VM-serviceavtal.

Azure tillämpar en kvot för IOPS per datadisk. Dessa kvoter är olika för diskar som finns på standard-lagring och premiumlagring. I/o-svarstid sig också åt mellan två lagringstyper av. Premium storage tillhandahåller bättre i/o-svarstider. 

Var och en av de olika typerna av virtuell dator har ett begränsat antal datadiskar som kan bifogas. En annan begränsning är att endast vissa typer av virtuella datorer kan använda premium storage. Normalt kan väljer du att använda en viss VM-typ baserat på CPU och minne. Du kan också tänka på IOPS, svarstid och dataflöde diskkrav som vanligtvis skalas med antalet diskar eller vilken typ av premium storage-diskar. Antalet IOPS och dataflödet att uppnås genom att varje disk kan avgöra diskstorlek, särskilt med premium storage.

> [!NOTE]
> För DBMS-distributioner, vi rekommenderar användning av premium-lagring för data, oavsett transaktionsloggen, eller gör om filer. Det spelar ingen roll om du vill distribuera produktions- eller icke-produktionsmiljöer system.

> [!NOTE]
> Användarens unika kan dra nytta av Azure [enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), alla diskar som är anslutna måste vara lagringstypen premium, som innehåller en bas-VHD.

> [!NOTE]
> Värd för huvuddatabasen filer, stöds exempelvis data och loggfiler, SAP-databaser på lagringsmaskinvara som befinner sig i samplacerade tredje parts Datacenter vid Azure-Datacenter inte. För SAP-arbetsbelastningar stöds endast storage som representeras som interna Azure-tjänst för data och transaktionen loggfiler SAP-databaser.

Placeringen av databasfilerna och logg-och gör om och vilken typ av Azure-lagring som du använder definieras av krav på IOPS, latens och dataflöde. För att få tillräckligt med IOPS ska du tvingas att använda flera diskar eller använda en större premium storage disk. Om du använder flera diskar, kan du skapa en programvara stripe över diskar som innehåller datafiler eller loggen och gör om filer. I sådana fall, IOPS och diskdataflöde serviceavtal underliggande premiumlagring är diskar eller högsta möjliga IOPS för standardlagringsdiskar ackumulerande för den resulterande stripe-uppsättningen.

Balansera antalet IOPS som behövs för databasfilerna över ett antal virtuella hårddiskar som angivits, om dina krav på IOPS överskrider vad en enda virtuell Hårddisk kan ge. Det enklaste sättet att fördela belastningen IOPS över diskar är att skapa en programvara stripe över olika diskar. Placera sedan ett antal datafiler SAP DBMS på LUN högg utanför programvara stripe. antalet diskar i stripe drivs av IOPs krav, behov för disk och volym krav.


- - -
> ![Windows][Logo_Windows] Windows
>
> Vi rekommenderar att du använder Windows-lagringsutrymmen för att skapa stripe-uppsättningar på flera virtuella Azure-hårddiskar. Använd minst Windows Server 2012 R2 eller Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Endast MDADM och logiska Volume Manager (LVM) kan användas för att skapa en programvaru-RAID på Linux. Mer information finns i:
>
> - [Konfigurera programvaru-RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) med MDADM
> - [Konfigurera LVM på en Linux-VM i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) med LVM
>
>

- - -

> [!NOTE]
> Eftersom Azure Storage behåller tre avbildningar av virtuella hårddiskar, vara inte det klokt att konfigurera en redundans när du stripe. Du behöver bara konfigurera striping så att I/o är fördelade över de olika virtuella hårddiskarna.
>

### <a name="managed-or-nonmanaged-disks"></a>Hanterad eller icke-hanterade diskar
En administrativ konstruktion och Mottagarnamnet begränsningar är ett Azure storage-konto. Begränsningar skiljer sig åt mellan standard storage-konton och premium storage-konton. Information om funktioner och begränsningar finns i [skalbarhets- och prestandamål i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Kom ihåg att det finns en gräns på IOPS per lagringskonto för standard-lagring. Se den rad som innehåller **totala begära antalet** i artikeln [skalbarhets- och prestandamål i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Det finns också en inledande gränsen för antalet lagringskonton per Azure-prenumeration. Belastningsutjämna virtuella hårddiskar för större SAP-landskap över olika lagringskonton att undvika nått gränserna för dessa konton. Detta är tedious arbete när vi talar om några hundra virtuella datorer med mer än tusen virtuella hårddiskar.

Eftersom med standardlagring för DBMS distributioner tillsammans med en SAP-arbetsbelastning inte rekommenderas, referenser och rekommendationer till standardlagring är begränsade till det här korta [artikel](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

För att undvika det administrativa arbetet med att planera och distribuera virtuella hårddiskar mellan olika Azure storage-konton kan Microsoft introducerade [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) i 2017. Hanterade diskar är tillgängliga för standardlagring och premiumlagring. De stora fördelarna med hanterade diskar jämfört med icke-hanterade diskar är:

- För hanterade diskar distribuerar Azure de olika virtuella hårddiskarna mellan olika lagringskonton automatiskt vid tidpunkten för distribution. På så sätt kan lagringskonto begränsar för datavolym, i/o-dataflöde och IOPS når inte.
- Azure Storage godkänner använder hanterade diskar, konceptet med Azures tillgänglighetsuppsättningar. Om den virtuella datorn är en del av en Azure-tillgänglighetsuppsättning, distribueras bas-VHD och ansluten disk för en virtuell dator i olika fel- och uppdateringsdomäner.


> [!IMPORTANT]
> Fördelarna med Azure Managed Disks får rekommenderar vi att du använder Azure Managed Disks för DBMS-distributioner och SAP-distributioner i allmänhet.
>

Om du vill konvertera från ohanterade till hanterade diskar, se:

- [Konvertera en Windows-dator från ohanterade diskar till managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Konvertera en virtuell Linux-dator från ohanterade diskar till managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och datadiskar
När du monterar diskar till virtuella datorer kan välja du om i/o-trafiken mellan den virtuella datorn och diskarna i Azure storage cachelagras. Standard och premium storage använder två olika tekniker för den här typen av cache.

Följande rekommendationer förutsätter dessa i/o-egenskaper för standard DBMS:

- Det är främst en skrivskyddad arbetsbelastning mot datafiler för en databas. Dessa läsningar är prestanda som är viktiga för DBMS system.
- Skriva mot datafilerna som inträffar i toppar utifrån kontrollpunkter eller en konstant ström. Ett genomsnitt under en dag, finns det färre skrivningar än läsningar. Motsatt läsningar från datafiler, dessa skrivåtgärder är asynkrona och håll inte in någon användartransaktioner.
- Det är nästan alla läsningar från transaktionen logg eller gör om-filer. Undantagen är stora I/o när du utför säkerhetskopieringar av transaktionsloggen.
- Den största belastningen mot transaktion eller gör om loggfiler är skrivningar. Beroende på typen av arbetsbelastning, kan det ha I/o som är så litet som 4 KB eller i andra fall, i/o-storlekarna på minst 1 MB.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt.

För standardlagring är möjliga cache-typer:

* Ingen
* Läsa
* Läsa/Skriva

För att få konsekventa och deterministisk prestanda kan du ange cachelagring på standard-lagring för alla diskar som innehåller filer som DBMS-relaterad data, loggar och gör om filer och tabellutrymme för att **NONE**. Cachelagring av basen VHD kan förbli med standard.

För premium storage finns följande alternativ för cachelagring:

* Ingen
* Läsa
* Läs/skriv
* Ingen + Write Accelerator, som är endast för M-serien virtuella datorer i Azure
* Läs + Write Accelerator, som är endast för M-serien virtuella datorer i Azure

För premium-lagring, rekommenderar vi att du använder **läsa cachelagring för filer** av SAP-databasen och välj **ingen cachelagring för diskarna i befintlig(a) transaktionsloggfil(er)**.

För M-serien distributioner rekommenderar vi att du använder Azure Write Accelerator för DBMS-distribution. Information, begränsningar och distribution av Azure Write Accelerator finns i [aktivera Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Azure-ickebeständig diskar
Virtuella Azure-datorer erbjuder ickebeständig diskar när en virtuell dator har distribuerats. När det gäller en omstart av virtuell dator rensar allt innehåll på dessa enheter. Det är en tanke på att datafiler och logg- och gör om filer med databaser under inga omständigheter finns på dessa och ej beständig enheter. Det kan finnas undantag för vissa databaser, om enheterna och ej beständig skulle kunna vara lämplig för tempdb och temp registerutrymmen. Undvik att använda dessa enheter för virtuella datorer i A-serien eftersom dessa och ej beständig enheter är begränsade i dataflöde med den Virtuella datorserien. 

Mer information finns i [förstå den temporära enheten på Windows virtuella datorer i Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> Enhet D i en Azure VM är en och ej beständig enhet som backas upp av vissa lokala diskar på Azure compute-nod. Eftersom den är och ej beständig, ändringar av innehållet på enhet D går förlorade när den virtuella datorn startas om. Ändringar är filer som har lagrats, kataloger som har skapats och som har installerats.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuella Linux Azure-datorer montera automatiskt en enhet på /mnt/resource som är en och ej beständig enhet som backas upp av lokala diskar på Azure compute-nod. Eftersom den är och ej beständig, ändringar som görs till innehåll i/mnt/resurs går förlorade när den virtuella datorn startas om. Ändringar är filer som har lagrats, kataloger som har skapats och som har installerats.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage återhämtning
Microsoft Azure Storage lagrar en bas-VHD, med OS och anslutna diskar eller blobbar, på minst tre separata lagringsnoder. Den här typen av lagring kallas lokalt redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure.

Det finns andra metoder för redundans. Mer information finns i [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Premium storage är den rekommenderade typen av lagring för DBMS virtuella datorer och diskar som lagrar databasen och loggfiler och gör om filer. Enda tillgängliga redundans-metoden för premium storage är LRS. Därför måste du konfigurera databasen metoder för att aktivera databasreplikering för data i en annan Azure-region eller tillgänglighet zon. Databasen sätt är att SQL Server Always On Oracle Data Guard och HANA System Replication.


> [!NOTE]
> För DBMS-distributioner rekommenderas inte användning av geo-redundant lagring (GRS) för standardlagring. GRS påverkar prestanda kraftigt och följer inte skrivning ordningen mellan olika virtuella hårddiskar som är kopplade till en virtuell dator. Inte respekterar skrivning ordningen mellan olika virtuella hårddiskar potentiellt leder till inkonsekventa databaser på målsidan replikering. Den här situationen uppstår om databas och logg-och gör om är fördelade på flera virtuella hårddiskar, liksom Allmänt gäller på käll-VM på klientsidan.



## <a name="vm-node-resiliency"></a>VM-känslighet för noden
Azure erbjuder flera olika serviceavtal för virtuella datorer. Mer information finns i den senaste versionen av [serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Eftersom DBMS-lagret är vanligtvis kritiska tillgängliga ett SAP-system, måste du förstå tillgänglighetsuppsättningar, tillgänglighetszoner och underhåll. Mer information om de här koncepten finns i [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och [hantera tillgängligheten för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minsta rekommendationen för DBMS-produktionsscenarier med en SAP-arbetsbelastningar är att:

- Distribuera två virtuella datorer i en separat tillgänglighetsuppsättning i samma Azure-region.
- Kör dessa två virtuella datorer i samma Azure-nätverket och har nätverkskort kopplas från samma undernät.
- Använda databasen metoder för att hålla en hett vänteläge med den andra virtuella datorn. Metoder kan vara SQL Server Always On, Oracle Data Guard eller HANA System Replication.

Du kan också distribuera en tredje virtuell dator i en annan Azure-region och använda samma databas metoder för att ange en asynkron replikering i en annan Azure-region.

Information om hur du ställer in Azure tillgänglighetsuppsättningar finns i [den här självstudien](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Azure Nätverksöverväganden
I storskaliga SAP-distributioner du använder skissen av [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Använd det för dina virtuella nätverk konfiguration och behörigheter och rollen tilldelningar till olika delar av din organisation.

Dessa metodtips är resultatet av hundratals kunddistributioner:

- De virtuella nätverken SAP-programmet har distribuerats till har inte åtkomst till internet.
- Databasen virtuella datorer körs i samma virtuella nätverk som programnivån.
- De virtuella datorerna i det virtuella nätverket har en statisk allokering av privata IP-adress. Mer information finns i [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Routning begränsningar till och från de DBMS på virtuella datorer är *inte* med brandväggar som installeras på den lokala DBMS på virtuella datorer. Routning av nätverkstrafik i stället definieras med [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- För att avgränsa och isolera trafik för DBMS-VM, tilldela olika nätverkskort till den virtuella datorn. Varje nätverkskort som hämtar en annan IP-adress och varje nätverkskort tilldelas till ett annat virtuellt nätverksundernät. Varje undernät som har olika NSG-regler. Isolering eller separering av trafik är ett mått för routning. Den används inte för att ange kvoter för dataflöde i nätverket.

> [!NOTE]
> Tilldela statiska IP-adresser via Azure innebär att tilldela dem till enskilda virtuella nätverkskort. Inte tilldela statiska IP-adresser i gästoperativsystemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup är beroende av faktumet att vid minst primära virtuella nätverkskortet är inställd på DHCP- och inte på statiska IP-adresser. Mer information finns i [felsöka Azure VM backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Tilldela flera statiska IP-adresser till en virtuell dator genom att tilldela flera virtuella nätverkskort till en virtuell dator.
>


> [!IMPORTANT]
> Konfigurera [virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationsvägen mellan SAP-program och DBMS-lagret för en SAP NetWeaver - Hybris- eller SAP S/4HANA-baserade system stöds inte. Den här begränsningen är av funktioner och prestandaskäl. Kommunikationsvägen mellan SAP-programnivån och DBMS-lagret måste vara en direkt. Begränsningen inte innehåller [programsäkerhetsgruppen (ASG) och NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview) om dessa ASG och NSG-regler som tillåter en direktkommunikation sökväg. 
>
> Andra scenarier där virtuella nätverksinstallationer inte stöds finns med:
>
> * Kommunikationsvägar mellan virtuella Azure-datorer som representerar Linux Pacemaker klustrets noder och uppstår enheter enligt beskrivningen i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Kommunikationsvägar mellan virtuella Azure-datorer och Windows Server skalbar filserver (SOFS) Ange görs enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Virtuella nätverksinstallationer i kommunikationsvägar kan enkelt dubbla Nätverksfördröjningen mellan två kommunikation partner. De kan även begränsa genomflöde i kritiska vägar mellan SAP-programnivån och DBMS-lagret. I vissa kundscenarier orsaka virtuella nätverksinstallationer Pacemaker Linux-kluster att misslyckas. Dessa är fall där kommunikationen mellan noderna i Linux Pacemaker kommunicera med enheten uppstår genom ett nätverks virtuella installation.
>

> [!IMPORTANT]
> En annan utforma som *inte* stöds är uppdelning av SAP-programnivån och DBMS-lager i olika Azure-nätverk som inte är [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) med varandra. Vi rekommenderar att du särskilja SAP-programnivån och DBMS-lagret med hjälp av undernät i ett Azure-nätverk i stället för med hjälp av olika Azure-nätverk. 
>
> Om du inte väljer att följa rekommendationen och särskilja i stället de två lager i olika virtuella nätverk, de två virtuella nätverken måste vara [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tänk som nätverkstrafik mellan två [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuella Azure-nätverk kan komma att kostnaderna för dataöverföring. Enorm datavolym som består av många terabyte utväxlas mellan SAP-programnivån och DBMS-lagret. Du kan ackumuleras betydande kostnader om SAP-programnivån och DBMS-lager är åtskilda mellan två peer-kopplade Azure-nätverk.

Använd två virtuella datorer för din produktion DBMS distribution inom en Azure-tillgänglighetsuppsättning har angetts. Också använda separata routning för SAP-programnivån och hantering och åtgärder trafiken till de två DBMS på virtuella datorer. Se följande bild:

![Diagram över två virtuella datorer i två undernät](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Använda Azure Load Balancer för att omdirigera trafik
Användning av privata virtuella IP-adresser som används i funktioner som t.ex. SQL Server Always On eller HANA System Replication kräver konfiguration av en Azure load balancer. Belastningsutjämnaren använder avsökningen portar för att fastställa den aktiva noden för DBMS och dirigera trafiken endast till den aktiva databas-noden. 

Om det finns en redundansväxling av databasnoden, finns det inget behov av SAP-programmet att konfigurera om. I stället återansluta arkitekturer för de vanligaste SAP-program mot privata virtuella IP-adress. Under tiden kan reagerar belastningsutjämnaren på noden redundans genom att omdirigera trafik mot privata virtuella IP-adressen till den andra noden.

Azure erbjuder två olika [läsa in belastningsutjämnare SKU: er](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): grundläggande nivå och en standard-SKU. Om du vill distribuera i olika Azure tillgänglighetszoner gör med basic load balancer SKU bra.

Är trafiken mellan de DBMS på virtuella datorer och SAP-programnivån alltid dirigeras via belastningsutjämnaren hela tiden? Svaret beror på hur du konfigurerar belastningsutjämnaren. 

För tillfället är den inkommande trafiken för DBMS VM alltid dirigeras via belastningsutjämnaren. Utgående trafik vägen från DBMS VM till programnivån VM beror på konfigurationen av belastningsutjämnaren. 

Belastningsutjämnaren kan en DirectServerReturn. Om det alternativet är konfigurerad, är trafiken dirigeras från DBMS VM till SAP-programnivån *inte* dirigeras via belastningsutjämnaren. I stället går den direkt till programnivån. Om DirectServerReturn inte är konfigurerat kan dirigeras den returnera trafiken till SAP-programnivån via belastningsutjämnaren.

Vi rekommenderar att du konfigurerar DirectServerReturn i kombination med belastningsutjämnare som placerats mellan SAP-programnivån och DBMS-lagret. Den här konfigurationen minskar Nätverksfördröjningen mellan de två.

Ett exempel på hur du ställer in den här konfigurationen med SQL Server Always On finns [konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Om du använder publicerade GitHub JSON-mallar som referens för dina SAP-infrastrukturen distributioner i Azure kan du undersöka detta [mall för ett SAP-system för 3 nivåer](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). I den här mallen tar kan du även se de korrekta inställningarna för belastningsutjämnaren.

### <a name="azure-accelerated-networking"></a>Azure Accelererat nätverk
För att ytterligare minska Nätverksfördröjningen mellan virtuella Azure-datorer, rekommenderar vi att du väljer [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Använda den när du distribuerar virtuella Azure-datorer för en SAP-arbetsbelastning, särskilt för SAP-programnivån och SAP DBMS-lagret.

> [!NOTE]
> Inte alla typer av virtuella datorer stöder Accelererat nätverk. Föregående artikel visar en lista över de typer av virtuella datorer som har stöd för Accelererat nätverk.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Läs hur du distribuerar virtuella datorer med Accelererat nätverk för Windows i [skapa en Windows-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Mer information om Linux-distribution finns i [skapa en Linux-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> När det gäller SUSE, Red Hat och Oracle Linux stöds Accelererat nätverk med nya versioner. Äldre versioner som SLES 12 SP2 eller RHEL 7.2 stöder inte Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Distributionen av värden övervakning
SAP kräver möjlighet att få värden övervakningsdata från de fysiska värdar som kör de Azure-datorerna för produktion av SAP-program på Azure virtuella datorer. En specifik SAP Värdagenten korrigeringsnivå krävs som gör det möjligt för den här funktionen i SAPOSCOL och Värdagenten för SAP. Den exakta korrigeringsnivån dokumenteras i SAP-kommentar [1409604].

Mer information om distribution av komponenter som levererar värd för data att SAPOSCOL och SAP Värdagenten och Livscykelhantering för dessa komponenter finns i den [Distributionsguide][deployment-guide].


## <a name="next-steps"></a>Nästa steg
Mer information om ett visst DBMS finns:

- [DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines](dbms_guide_sqlserver.md)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](dbms_guide_oracle.md)
- [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](dbms_guide_ibm.md)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](dbms_guide_sapase.md)
- [SAP maxDB, Live-Cache och Content Server-distribution i Azure](dbms_guide_maxdb.md)
- [Användarguide för SAP HANA på Azure](hana-vm-operations.md)
- [SAP HANA, hög tillgänglighet för Azure-datorer](sap-hana-availability-overview.md)
- [Säkerhetskopieringsguide för SAP HANA på Azure virtual machines](sap-hana-backup-guide.md)

