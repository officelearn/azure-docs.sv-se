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
ms.openlocfilehash: 5e514f35567f4be0932c7bcc591cbd0f05cd9814
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606766"
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

Den här guiden är en del av dokumentationen för implementering och distribution av SAP-programvara på Microsoft Azure. Innan du läser den här guiden kan du läsa den [planerings- och Implementeringsguide för][planning-guide]. Det här dokumentet beskriver allmänna distribution aspekter av SAP-relaterade DBMS system på Microsoft Azure Virtual Machines (VM) med Azure-infrastruktur som en tjänst (IaaS)-funktioner.

Dokumentet kompletterar dokumentationen för Installation av SAP och SAP Notes som motsvarar de viktigaste resurserna för installationer och distributioner av SAP-program på den angivna plattformar.

I det här dokumentet introduceras överväganden för att köra SAP-relaterade DBMS system i virtuella Azure-datorer. Det finns några referenser till specifika DBMS system i det här kapitlet. I stället hanteras specifika DBMS system i det här dokumentet, efter det här dokumentet.

## <a name="definitions-upfront"></a>Definitioner förskott
Hela dokumentet används följande termer:

* IaaS: Infrastruktur som en tjänst.
* PaaS: Plattform som en tjänst.
* SaaS: Programvara som en tjänst.
* SAP-komponent: Ett enskilt SAP-program, till exempel ECC, BW, lösningen Manager eller EP. SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller ett icke-NetWeaver-baserade program, till exempel företag objekt.
* SAP-miljön: en eller flera SAP-komponenter logiskt grupperade för att utföra en företagsfunktion, till exempel utveckling, QAS, utbildning, DR eller produktion.
* SAP-landskap: Den här termen avser hela SAP-tillgångar i en kunds IT-miljön. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
* SAP-System: Kombinationen av DBMS-lagret och programnivån av, till exempel en utvecklingssystem för SAP ERP, SAP BW testsystem, SAP CRM produktionssystemet osv. I Azure-distributioner, är det inte stöd för att dela upp de här två lager mellan lokala och Azure. Därför kan ett SAP-system är antingen distribueras lokalt eller det distribueras i Azure. Du kan dock distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure men SAP CRM produktionsprogrammen system på plats.
* Mellan olika platser: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, flera platser eller ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. Orsaken till att anslutningen är att utöka lokala domäner, en lokal Active Directory och lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar för prenumerationen. Med det här tillägget, kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare på den lokala domänen har åtkomst till servrarna och kan köra services på de virtuella datorerna (till exempel DBMS tjänster). Kommunikation och namnmatchning mellan virtuella datorer distribueras på plats och virtuella datorer som distribueras i Azure är möjligt. Det här scenariot är det vanligaste scenariot för att distribuera SAP-tillgångar i Azure. Mer information finns i [planering och design för VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Mellan lokala distributioner av SAP-system om Azure virtuella datorer som kör SAP-system är medlemmar i en lokal domän har stöd för SAP-system för produktion. Mellan lokala konfigurationer som stöds för att distribuera delar eller slutföra SAP-landskap i Azure. Även köra fullständig SAP-landskap i Azure kräver att dessa virtuella datorer är en del av den lokala domänen och AD/LDAP. I tidigare versioner av dokumentationen Hybrid-IT-scenarier har nämns, där termen *Hybrid* grundas på typsystemet i det faktum att det finns en anslutning mellan olika platser mellan lokala och Azure. I det här fallet *Hybrid* innebär också att de virtuella datorerna i Azure är en del av den lokala Active Directory.
>
>

Vissa Microsoft-dokumentationen beskriver mellan lokala scenarier lite annorlunda särskilt för DBMS HA konfigurationer. När det gäller SAP-relaterade-dokument mellan lokala scenariot som handlar att ha en plats-till-plats eller privat [ExpressRoute](https://azure.microsoft.com/services/expressroute/) anslutningen och det faktum att SAP-landskap fördelas mellan lokala och Azure.

## <a name="resources"></a>Resurser
Finns olika artiklar om SAP-arbetsbelastningar på Azure släppte. Rekommendation är att starta i [SAP-arbetsbelastningar på Azure – Kom igång](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) och välj intresseområden

Följande SAP-information är relaterade till SAP på Azure om området som beskrivs i det här dokumentet:

| Nummer | Titel |
| --- | --- |
| [1928533] |SAP-program i Azure: Produkter som stöds och Azure VM-typer |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Felsökning av utökad Azure övervakning för SAP |
| [2178632] |Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering på Windows: Förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: Förbättrad övervakning |
| [2039619] |SAP-program på Microsoft Azure med Oracle-databasen: Produkter som stöds och versioner |
| [2233094] |DB6: SAP-program i Azure med IBM DB2 för Linux, UNIX- och Windows - ytterligare Information |
| [2243692] |Linux på Microsoft Azure (IaaS) virtuell dator: Problem med SAP-licens |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP-Installation och uppgradering |
| [1597355] |Växlingsutrymme rekommendation för Linux |
| [2171857] |Oracle Database 12c - stöd för filer system på Linux |
| [1114181] |Oracle Database 11g - stöd för filer system på Linux |


Läs även den [Tillståndsändringsavisering Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP Notes för Linux.

Du bör ha kunskaper om arkitektur för Microsoft Azure och hur Microsoft Azure Virtual Machines distribueras och hanteras. Du hittar mer information på [dokumentation om Azure](https://docs.microsoft.com/azure/).

Även om diskutera IaaS, är i allmänhet Windows, Linux och DBMS installationen och konfigurationen i stort sett samma som valfri virtuell dator eller bare metal datorn som du skulle installera en lokal. Det finns dock vissa arkitektur och system management implementering beslut, som skiljer sig när du använder Azure IaaS. Syftet med det här dokumentet är att förklara den specifika arkitekturen och system management skillnader som du måste förberedas för när du använder Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Lagringsstrukturen för en virtuell dator för RDBMS-distributioner
För att kunna följa det här kapitlet, det är nödvändigt att förstå vad som angavs i [detta] [ deployment-guide-3] kapitel i den [Distributionsguide][deployment-guide]. Information om de olika VM-serien och deras skillnader och skillnaderna för Azure Standard och [Premiumlagring](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) ska tolkas och kända innan du läser det här kapitlet.

När det gäller Azure Storage för virtuella Azure-datorer, bör du känna till artiklarna:

- [Om disklagring för virtuella Azure Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [Om disklagring för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds)

En struktur för distribution där operativsystemet, DBMS och eventuell SAP-binärfiler skiljer sig från databasfilerna rekommenderas vanligtvis i en grundläggande konfiguration. Därför rekommenderar vi SAP-system som körs i Azure virtuella datorer har den base VHD (eller disk) som installeras med operativsystemet, database management system körbara filer och SAP körbara filer. DBMS data och loggfiler filerna lagras i Azure Storage (Standard eller Premium Storage) i separata diskar och ansluten som logiska diskar på den ursprungliga Azure operativsystemavbildningen VM. Särskilt i Linux-distributioner kan det finnas olika rekommendationer dokumenterade. Särskilt när det gäller SAP HANA.

När du planerar din disklayouten, måste du hitta en bra balans mellan följande objekt:

* Antalet datafiler.
* Antalet diskar som innehåller filerna.
* IOPS-kvoter för en enda disk.
* Data dataflöde per disk.
* Antal ytterligare datadiskar som är möjliga per VM-storlek.
* Det totala genomflödet som en virtuell dator kan ge.
* Svarstid som kan ge olika typer av Azure Storage.
* VM-serviceavtal

Azure tillämpar en kvot för IOPS per datadisk. Dessa kvoter är olika för diskar som finns på Azure Standard Storage och Premium Storage. I/o-svarstid sig också åt mellan två lagringstyper av. Med Premium Storage levererar faktorer bättre i/o-svarstider. Var och en av de olika typerna av virtuell dator har ett begränsat antal datadiskar som du kan ansluta. En annan begränsning är att endast vissa typer av virtuella datorer kan använda Azure Premium Storage. Därför kan beslutet för en viss typ av virtuell dator inte bara styras av processor och minne krav, utan även av IOPS, svarstid och dataflödeskrav som vanligtvis skalas med antalet diskar eller vilken typ av Premium Storage-diskar. Särskilt med Premium Storage kan storleken på en disk också avgöras av antalet IOPS och dataflöde som måste uppnås genom att varje disk.

> [!NOTE]
> För DBMS distributioner rekommenderas användning av Premium Storage för alla data, transaktionsloggen och gör om filer. Därmed spelar det ingen roll om du vill distribuera produktions- eller icke-produktionssystem.

> [!NOTE]
> För att kunna dra nytta av Azure är unika [enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) alla diskar som är anslutna måste vara av typen Azure Premium Storage, inklusive en bas-VHD.
>

Placeringen av databasfilerna och log/gör om filer och vilken typ av Azure Storage används, måste definieras av krav på IOPS, latens och dataflöde. För att få tillräckligt med IOPS, kan du tvingas att utnyttja flera diskar eller använda en större Premium Storage-disk. Vid att använda flera diskar, skapar du en programvara stripe över diskar som innehåller datafiler eller log/gör om filer. I sådana fall kan är IOPS och diskdataflöde serviceavtal för de underliggande Premium Storage-diskarna eller högsta möjliga IOPS för Azure Standard Storage-diskar ackumulerande för den resulterande stripe-uppsättningen.

Om dina krav på IOPS överskrider vad en enda virtuell Hårddisk kan ge som angivits måste du balansera antalet IOPS som behövs för databasfilerna över ett antal virtuella hårddiskar. Enklaste sättet att fördela belastningen IOPS över diskar är att skapa en programvara stripe över olika diskar. Placera sedan ett antal datafiler SAP DBMS på LUN högg utanför programvara stripe. antalet diskar i stripe drivs av IOPs krav, behov för disk och volym krav.


- - -
> ![Windows][Logo_Windows] Windows
>
> Vi rekommenderar att du använder lagringsutrymmen för Windows för att skapa sådana stripe-uppsättningar på flera virtuella Azure-hårddiskar. Det rekommenderas att använda minst Windows Server 2012 R2 eller Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Endast MDADM och LVM (Logical Volume Manager) kan användas för att skapa en programvaru-RAID på Linux. Mer information finns i följande artiklar:
>
> - [Konfigurera programvaru-RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) med MDADM
> - [Konfigurera LVM på en Linux-VM i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) med LVM
>
>

- - -

> [!NOTE]
> Eftersom Azure Storage är att ha tre avbildningar av virtuella hårddiskar, det inte vara klokt att konfigurera en redundans när Strimling. Du behöver bara konfigurera Strimling så, att I/o ska komma distribueras över de olika virtuella hårddiskarna.
>

### <a name="managed-or-non-managed-disks"></a>Hanterad eller icke-hanterade diskar
Azure Storage-kontot är inte bara en administrativ konstruktion, utan också ett ämne med begränsningar. Begränsningarna skiljer sig mellan Azure Standard Storage-konto och Azure Premium Storage-konton. Exakta funktioner och begränsningar finns i artikeln [mål för prestanda och skalbarhet för lagring i Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Azure standardlagring, är det viktigt att komma ihåg att det finns en gräns på IOPS per lagringskonto (rad som innehåller **totala begära antalet** i artikeln [skalbarhet för lagring av Azure- och prestandamål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). Det finns dessutom en inledande gräns för antalet Lagringskonton per Azure-prenumeration. Du måste därför fördela virtuella hårddiskar för större SAP-landskap mellan olika lagringskonton för att undvika nått gränserna för dessa konton. En tedious fungerar när du pratar om några hundra virtuella datorer med mer än tusen virtuella hårddiskar.

Eftersom det inte är rekommenderat att du använder Azure standardlagring för DBMS distributioner tillsammans med SAP-arbetsbelastningar, referenser och rekommendationer för Azure Standard-lagring är begränsade till det här korta [artikel](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

För att undvika det administrativa arbetet med att planera och distribuera virtuella hårddiskar mellan olika Azure-lagringskonton, Microsoft introducerade något som kallas [Managed Disks](https://azure.microsoft.com/services/managed-disks/) i 2017. Hanterade diskar är tillgängliga för Azure standardlagring samt Azure Premium Storage. De stora fördelarna med Managed Disks jämfört med icke-hanterade diskar lista som:

- För Managed Disks Azure distribuerar de olika virtuella hårddiskarna mellan olika lagringskonton automatiskt vid tidpunkten för distribution och undviker därigenom nått gränserna för ett Azure Storage-konto när det gäller datavolym, i/o-dataflöde och IOPS.
- Använda hanterade diskar, Azure Storage respektera koncepten för Azure-Tillgänglighetsuppsättningar och med som distribuerar Virtuella bashårddisken och ansluten disk för en virtuell dator i olika fel- och uppdateringsdomäner om den virtuella datorn är en del av en Azure-Tillgänglighetsuppsättning.


> [!IMPORTANT]
> Angivna fördelarna med Azure Managed Disks, rekommenderas att använda Azure Managed Disks för DBMS-distributioner och SAP-distributioner i allmänhet.
>

Om du vill konvertera från ohanterade till hanterade diskar, läser du artiklarna:

- [Konvertera en virtuell dator i Windows från ohanterade diskar till Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Konvertera en virtuell Linux-dator från ohanterade diskar till managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och datadiskar
När du monterar diskar till virtuella datorer kan välja du om i/o-trafiken mellan den virtuella datorn och diskarna i Azure storage cachelagras. Azure Standard och Premium Storage kan du använda två olika tekniker för den här typen av cache.

Rekommendationerna nedan förutsatt att följande i/o-egenskaper för Standard DBMS:

- Den läses huvudsakligen arbetsbelastning mot datafiler för en databas. Dessa läsningar är prestanda som är viktiga för DBMS system
- Skriva mot datafilerna som uppstår i toppar utifrån kontrollpunkter eller en konstant ström. Dock är utgöra ett genomsnitt under dagen skrivningar mindre än läsningar. I motsatt läsningar från datafiler, dessa skrivåtgärder är asynkrona och innehar inte upp alla användartransaktioner.
- Det är nästan alla läsningar från transaktionen logg eller gör om-filer. Undantagen är stora I/o när du genomför säkerhetskopieringar av transaktionsloggen.
- Viktigaste belastning transaktion eller gör om loggfiler är skrivningar. Beroende på vilken typ av arbetsbelastning, kan det ha I/o som är så litet som 4 KB eller i andra fall i/o-storlekar på 1 MB eller mer.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

För Azure Standard Storage är möjliga cache-typer:

* Ingen
* Läsa
* Läsa/Skriva

För att få konsekventa och deterministisk prestanda, bör du ange cachelagring på Azure standardlagring för alla diskar som innehåller **DBMS-relaterade datafiler, log/gör om filer och tabellen utrymme för att ”NONE”**. Cachelagring av basen VHD kan förbli med standard.

För Azure Premium Storage finns följande alternativ för cachelagring:

* Ingen
* Läsa
* Läs/Skriv
* Ingen + Write Accelerator (endast för virtuella datorer Azure M-serien)
* Läsa + Write Accelerator (endast för virtuella datorer Azure M-serien)

Rekommendation för Azure Premium Storage är att använda **läsa cachelagring för filer** för SAP-databasen och välja **ingen cachelagring för diskarna i befintlig(a) transaktionsloggfil(er)**.

För distributioner för M-serien rekommenderas att använda Azure Write Accelerator för DBMS-distributionen. Mer information finns begränsningar och distribution av Azure Write Accelerator dokumentet [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-non-persistent-disks"></a>Azure-icke-beständiga diskar
Virtuella Azure-datorer erbjuder icke-beständiga diskar när en virtuell dator har distribuerats. Allt innehåll på dessa enheter kommer att rensas vid omstart av en virtuell dator. Det är därför en tanke på att data och log/gör om-filer med databaser under inga omständigheter finns på dessa icke bestående enheter. Det kan finnas undantag för några av databaserna, om enheterna icke bestående skulle kunna vara lämplig för tempdb och temp registerutrymmen. Men Undvik att använda dessa enheter för virtuella datorer i A-serien eftersom dessa icke bestående enheter är begränsade i dataflöde med den Virtuella datorserien. Mer information finns i artikeln [förstå den temporära enheten på Windows-datorer i Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
>
> Enhet D:\ i en Azure VM är en icke bestående enhet som backas upp av vissa lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehållet på D:\ enheten går förlorad när den virtuella datorn startas om eftersom den är icke-beständig. Genom att ”ändringar”, till exempel filer lagras, kataloger som skapas, program som har installerats, osv.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuella Linux Azure-datorer montera automatiskt en enhet på /mnt/resource som är en icke bestående enhet som backas upp av lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehåll i /mnt/resource går förlorade när den virtuella datorn startas om eftersom den är icke-beständig. Av alla ändringar, till exempel filer lagras, kataloger som skapas, program som har installerats, osv.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage återhämtning
Microsoft Azure Storage lagrar bas-VHD (med OS) och anslutna diskar eller Blobar på minst tre separata lagringsnoder. Detta kallas för lokalt Redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure.

Det finns flera mer redundans metoder, som beskrivs i artikeln [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Från och med Azure Premium Storage, vilket är den rekommenderade typen av lagring för DBMS virtuella datorer och diskar som lagrar databasen och log/gör om, är den enda tillgängliga metoden LRS. Därför måste du konfigurera databasen metoder, som SQL Server Always On, Oracle Data Guard eller HANA System Replication att aktivera databasreplikering för data i en annan Azure-Region eller en annan Azure-Tillgänglighetszon.


> [!NOTE]
> Användning av Geo-Redundant lagring som är tillgänglig med Azure standardlagring rekommenderas inte för DBMS-distributioner, eftersom det mycket hög prestanda och respekterar inte skriva ordningen mellan olika virtuella hårddiskar som är kopplade till en virtuell dator. Fakta för inte respekterar skrivning ordningen mellan olika virtuella hårddiskar har en hög potentiell till hamnar i inkonsekventa databaser på målsidan replikering om databas och logg/gör om filer är fördelade på flera virtuella hårddiskar (som huvudsakligen fallet) allt på käll-VM på klientsidan.



## <a name="vm-node-resiliency"></a>VM-känslighet för noden
Azure-plattformen erbjuder flera olika serviceavtal för virtuella datorer. Mer information finns i den senaste versionen av [serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Eftersom DBMS-lagret är vanligtvis en viktig del tillgänglighet i ett SAP-system, behöver du bli bekant med principerna för Tillgänglighetsuppsättningar, Tillgänglighetszoner och underhåll. Artiklar som beskriver de här koncepten är [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och [hantera tillgängligheten för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minsta rekommendation för DBMS-produktionsscenarier med SAP-arbetsbelastningar är att:

- Distribuera två virtuella datorer i en separat Tillgänglighetsuppsättning i samma Azure-Region.
- Dessa två virtuella datorer körs i samma Azure VNet och skulle ha nätverkskort kopplas från samma undernät.
- Använda databasen metoder för att hålla en hett vänteläge med den andra virtuella datorn. Metoder kan vara SQL Server Always On, Oracle Data Guard eller HANA System Replication.

Dessutom kan du distribuera en tredje virtuell dator i en annan Azure-Region och använda samma databas metoder för att ange en asynkron replikering i en annan Azure-region.

Sätt att konfigurera Azure-Tillgänglighetsuppsättningar visas i den här [självstudien](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Azure Nätverksöverväganden
I storskaliga SAP-distributioner, rekommendation är att du använder skissen av [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) för VNet konfiguration och behörigheter och rollen tilldelningarna till olika delar av organisationen.

Det finns flera bästa praxis, vilket resulterade från hundratals kunddistributioner:

- Virtuella nätverk SAP-programmet har distribuerats till, har inte åtkomst till Internet.
- Databasen virtuella datorer körs i samma VNet som programnivån.
- De virtuella datorerna i det virtuella nätverket har en statisk allokering av privata IP-adress. Finns i artikeln [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) som referens.
- Routning begränsningar till och från de DBMS på virtuella datorer är **inte** med brandväggar som installeras på den lokala DBMS på virtuella datorer. Routning av nätverkstrafik i stället definieras med [Azure Nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
- I syfte att avgränsa och isolera trafik för DBMS VM kan tilldela du olika nätverkskort till den virtuella datorn. Där varje nätverkskort som har en annan IP-adress och varje nätverkskort som är tilldelade till ett annat VNet-undernät som igen har olika NSG-regler. Tänk på att isolering eller separering av trafik är bara ett mått för Routning och tillåter inte att ange kvoter för dataflöde i nätverket.

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure innebär att enskilda virtuella nätverkskort. Du bör inte tilldela statiska IP-adresser i gästoperativsystemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup-tjänsten är beroende av faktumet att vid minst det primära virtuella nätverkskortet är inställd på DHCP- och inte på statiska IP-adresser. Se även dokumentet [felsöka Azure VM backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Om du vill tilldela flera statiska IP-adresser till en virtuell dator måste du tilldela flera virtuella nätverkskort till en virtuell dator.
>


> [!IMPORTANT]
> Utanför funktioner, men mer viktiga av prestandaskäl, det finns inte stöd för att konfigurera [Azure virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationsvägen mellan SAP-program och DBMS-lager med en SAP NetWeaver Hybris eller S/4HANA-baserade SAP-system. Kommunikationen mellan SAP-programnivån och DBMS-lagret måste vara en direkt. Begränsningen omfattar inte [Azure ASG och NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview) så länge dessa ASG och NSG-reglerna tillåter en direkt kommunikation. Ytterligare scenarier där nva: er inte stöds finns i kommunikationsvägar mellan virtuella Azure-datorer som representerar Linux Pacemaker klusternoder och uppstår enheter enligt beskrivningen i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Eller i kommunikationen ange sökvägar mellan virtuella Azure-datorer och Windows Server SOFS görs enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Nva: er i kommunikationen sökvägar kan enkelt dubbelklicka Nätverksfördröjningen mellan två kommunikation partner, kan begränsa genomflöde i kritiska vägar mellan SAP-programnivån och DBMS-lagret. I vissa scenarier som observerats med kunder, kan nva: er orsaka Pacemaker Linux-kluster misslyckas i fall där kommunikationen mellan noderna i Linux Pacemaker behöver kommunicera med enheten uppstår via NVA.
>

> [!IMPORTANT]
> En annan design som är **inte** stöds är uppdelning av SAP-programnivån och DBMS-lager i olika Azure-nätverk som inte är [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) med varandra. Vi rekommenderar att särskilja SAP-programnivån och DBMS-lagret med hjälp av undernät i ett Azure-nätverk istället för att använda olika Azure-nätverk. Om du inte väljer att följa rekommendationen och särskilja i stället de två lager till olika virtuella nätverk, de två virtuella nätverken måste vara [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tänk som nätverkstrafik mellan två [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuella Azure-nätverk är föremål för kostnaderna för dataöverföring. Med enorm datavolym i många terabyte som utbyts mellan SAP-programnivån och DBMS layer betydande kostnader kan visas om SAP-programnivån och DBMS lager är indelad mellan två peer-kopplade Azure-nätverk.

Med hjälp av två virtuella datorer för din produktion DBMS distribution inom ett Azure-Tillgänglighetsuppsättning plus en separat routning för SAP-programnivån och hantering och åtgärder trafiken till de två DBMS på virtuella datorer, ser grov diagram ut som:

![Diagram över två virtuella datorer i två undernät](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Azure-belastningsutjämnare för att omdirigera trafik
Användningen av privata virtuella IP-adresser som används i funktioner som t.ex. SQL Server Always On eller HANA System replication kräver konfiguration av en Azure Load Balancer. Azure Load Balancer kan via avsökningen portar för att avgöra den aktiva noden för DBMS och dirigera trafiken endast till den aktiva databas-noden. Vid redundans för databasnoden finns det inget behov av SAP-programmet att konfigurera om. Arkitekturer för de vanligaste SAP-program kommer i stället att återansluta mot privata virtuella IP-adress. Azure load balancer uppvisat under tiden på den nod som redundansväxlingen genom att omdirigera trafik mot privata virtuella IP-adressen till den andra noden.

Azure erbjuder två olika [läsa in belastningsutjämnare SKU: er](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). En grundläggande SKU och standard-SKU. Om du inte vill distribuera i olika Tillgänglighetszoner i Azure, har bra med basic load balancer SKU.

Är trafiken mellan de DBMS på virtuella datorer och SAP-programnivån alltid dirigeras via Azure load balancer hela tiden? Svaret beror på hur du konfigurerar belastningsutjämnaren. Vid denna tidpunkt, kommer den inkommande trafiken för DBMS VM alltid att dirigeras via Azure-belastningsutjämnaren. Utgående trafik vägen från DBMS VM till programnivån VM beror på konfigurationen av Azure-belastningsutjämnaren. Belastningsutjämnaren kan en DirectServerReturn. Om det alternativet är konfigurerad, kommer den trafik som skickas från DBMS VM till SAP-programnivån **inte** dirigeras via Azure-belastningsutjämnaren. Det hamnar i stället direkt programnivån. Om DirectServerReturn inte är konfigurerad dirigeras returtrafik till SAP-programnivån via Azure load balancer

Det rekommenderas att konfigurera DirectServerReturn i kombination med Azure belastningsutjämnare som placerats mellan SAP-programnivån och DBMS-lagret för att minska Nätverksfördröjningen mellan de två lager

Ett exempel på hur du konfigurerar en sådan konfiguration är publicerad runt SQLServer alltid på i [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Om du vill använda publicerade GitHub JSON-mallar som referens för dina SAP-infrastrukturen distributioner i Azure bör du undersöka detta [mall för ett SAP-system för 3 nivåer](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). I den här mallen tar kan du även studera de korrekta inställningarna för Azure load balancer.

### <a name="azure-accelerated-networking"></a>Azure Accelererat nätverk
För att ytterligare minska Nätverksfördröjningen mellan virtuella Azure-datorer, bör du välja alternativet för [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) när du distribuerar virtuella Azure-datorer för SAP-arbetsbelastningar. Särskilt för SAP-programnivån och SAP DBMS-lagret.

> [!NOTE]
> Inte alla typer av virtuella datorer stöder Accelererat nätverk. Artikeln som refereras är en lista med VM-typer som har stöd för Accelererat nätverk.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> För Windows finns i artikeln [skapa en Windows-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) att lära dig begrepp och hur sätt att distribuera virtuella datorer med Accelererat nätverk
>
> ![Linux][Logo_Linux] Linux
>
> För Linux finns i artikeln [skapa en Linux-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) för att få information om Linux-distribution.
>
>

- - -

> [!NOTE]
> När det gäller SUSE, Red Hat och Oracle Linux stöds Accelererat nätverk med nya versioner. Äldre versioner som SLES 12 SP2 eller RHEL 7.2 stöder inte Azure Accelererat nätverk
>


## <a name="deployment-of-host-monitoring"></a>Distributionen av värden övervakning
För användning i produktion av SAP-program i Azure Virtual Machines kräver SAP möjligheten att hämta övervakningsdata från de fysiska värdar som kör Azure Virtual Machines-värd. En specifik SAP Värdagenten korrigeringsnivå krävs som gör det möjligt för den här funktionen i SAPOSCOL och Värdagenten för SAP. Den exakta korrigeringsnivån dokumenteras i SAP-kommentar [1409604].

Information om distribution av komponenter som levererar värd för data att SAPOSCOL och SAP Värdagenten och Livscykelhantering för dessa komponenter i den [Distributionsguide][deployment-guide]


## <a name="next-steps"></a>Nästa steg
Dokumentation om visst DBMS finns i följande artiklar:

- [DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines](dbms_guide_sqlserver.md)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](dbms_guide_oracle.md)
- [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](dbms_guide_ibm.md)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](dbms_guide_sapase.md)
- [SAP maxDB, Live-Cache och Content Server-distribution i Azure](dbms_guide_maxdb.md)
- [Användarguide för SAP HANA på Azure](hana-vm-operations.md)
- [SAP HANA, hög tillgänglighet för Azure-datorer](sap-hana-availability-overview.md)
- [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md)

