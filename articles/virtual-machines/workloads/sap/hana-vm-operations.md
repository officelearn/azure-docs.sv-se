---
title: Konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure | Microsoft Docs
description: Handbok för SAP HANA-system som distribueras på Azure virtual machines.
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
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699346"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer
Den här artikeln innehåller råd om hur du konfigurerar Azure-infrastrukturen och hanterar SAP HANA-system som har distribuerats på Azures inbyggda virtuella datorer (VM). Den här artikeln innehåller också konfigurationsinformation för SAP HANA skalbar för M128s VM SKU: N. Den här artikeln är inte avsedd att ersätta standard SAP-dokumentationen, som innehåller följande innehåll:

- [Administrationsguide för SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP-installationsguiderna](https://service.sap.com/instguides)
- [SAP-anteckningar](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenterna:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella nätverk](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Läs mer om SAP NetWeaver och andra SAP-komponenter på Azure i den [SAP på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) delen av den [dokumentation om Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Överväganden för grundläggande konfiguration
I följande avsnitt beskrivs grundinställning överväganden för distribution av SAP HANA-system på Azure Virtual Machines.

### <a name="connect-to-azure-virtual-machines"></a>Ansluta till Azure-datorer
Enligt beskrivningen i den [virtuella Azure-datorer Planeringshandbok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), två grundläggande metoder används för att ansluta till virtuella Azure-datorer:

- Anslut via internet och offentliga slutpunkter på en JumpBox-VM eller på den virtuella datorn som kör SAP HANA.
- Ansluta via en [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Plats-till-plats-anslutning via VPN eller ExpressRoute är nödvändigt för produktionsscenarier. Den här typen av anslutning krävs även för icke-produktionsmiljöer scenarier som går in i produktionsscenarier där SAP-program används. Följande bild visar ett exempel på cross-plats-anslutning:

![Cross-plats-anslutning](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Välj Azure VM-typer
Azure VM-typer för produktionsscenarier visas i den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Scenarier med icke-produktionsmiljöer finns en mängd olika interna Azure VM-typer.

>[!NOTE]
> Icke-produktionsmiljöer scenarier använder VM-typer som anges i den [SAP Obs! #1928533](https://launchpad.support.sap.com/#/notes/1928533). För användning av virtuella Azure-datorer för produktionsscenarier, kontrollera för SAP HANA-certifierade virtuella datorer i SAP publicerade [certifierade listan över plattformar som IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Om du vill distribuera de virtuella datorerna i Azure, använder du:

- Azure-portalen.
- Azure PowerShell-cmdlets.
- Azure CLI.

Du kan även distribuera en fullständig installerade SAP HANA-plattform på Virtuella Azure-tjänster via den [SAP-molnplattform](https://cal.sap.com/). Information om installationsprocessen finns i [distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Mer information om automation är finns i [i den här GitHub-artikeln](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Välj en typ av Azure Storage
Azure tillhandahåller två typer av lagring som är lämpliga för Azure virtuella datorer som kör SAP HANA:  

- Standard hårddiskar-(HDD)
- Premium SSD (solid-state drive)

Mer information om dessa disktyper, se [Välj en disktyp av](../../windows/disks-types.md).

Azure erbjuder två metoder för distribution för virtuella hårddiskar på Azure storage som standard och premium-lagring. Om det övergripande scenariot tillåter dra nytta av [Azure hanterade disk](https://azure.microsoft.com/services/managed-disks/) distributioner.

En lista över lagringstyper och deras serviceavtal i IOPS och lagring finns i [Azure-dokumentationen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Konfigurera lagring för virtuella Azure-datorer

Du skötas sannolikt aldrig om i/o-undersystem och deras funktioner eftersom leverantören av enheten sett till att de minsta krav har uppfyllts för SAP HANA. När du bygger Azure-infrastrukturen själv kan behöva du känna till några av dessa krav. Du bör också känna till de konfigurationskrav som föreslås i följande avsnitt. I de fall där du kan konfigurera de virtuella datorerna som du vill att SAP HANA ska köras på, kan du behöva:

- Aktivera skrivning/volym på /hana/log minst 250 MB per sekund för 1 MB i/o-storlekar.
- Aktivera Läs aktiviteten hos minst 400 MB/sekund för /hana/data för 16 MB och 64 MB i/o-storlekar.
- Aktivera skrivningsaktiviteter minst 250 MB/sek för /hana/data med 16 MB och 64 MB i/o-storlekar.

Låg lagringssvarstiden är viktigt för DBMS system, även om DBMS, t.ex. SAP HANA, behåller data i minnet. Den kritiska vägen i storage är vanligtvis runt transaction log skrivningar av DBMS system. Men operations skriva lagringspunkter eller läser in data i minnet när kraschåterställning också kan vara avgörande. 

Använda Azure premium-diskar för /hana/data och /hana/log volymer är obligatorisk. Skapa en RAID 0 med hjälp av mdadm eller en logisk Volume Manager (LVM) över flera Azure premium storage-diskar för att uppnå lägsta dataflödet för /hana/log och /hana/data efter behov av SAP. Använd också RAID-volymer som /hana/data och /hana/log volymer. Vi rekommenderar att du använder följande stripe-storlekar för RAID 0:

- 64 KB eller 128 KB för/hana/data
- 32 KB för/hana/logg

> [!NOTE]
> Du behöver inte konfigurera någon redundansnivå med hjälp av RAID-volymer eftersom Azure premium och standard-lagring att tre avbildningar av en virtuell Hårddisk. Användning av en RAID-volym är helt och hållet att konfigurera volymer som ger tillräcklig i/o-dataflöde.

Kan sparas ett antal virtuella Azure-hårddiskar under en RAID är ackumulerande från en sida för IOPS och lagring dataflöde. Om du placerar en RAID 0 över 3 x P30 Azure premium storage-diskar, får du tre gånger IOPS och tre gånger kapaciteten för lagring för en enda Azure premium storage P30-disk.

Följande cachelagring rekommendationer förutsätter att i/o-egenskaper för SAP HANA:

- Det är nästan alla skrivskyddade arbetsbelastningar mot HANA datafiler. Undantagen är stora I/o efter omstart av HANA-instans eller när data läses in i HANA. Ett annat fall av större läsa I/o mot datafiler kan vara HANA säkerhetskopior av databasen. Därför meningsfullt läscachelagring inte eftersom i de flesta fall alla datavolymer i filen måste läsas helt.
- Skriva mot datafilerna som uppstår i toppar baserat av HANA lagringspunkter och HANA kraschåterställning. Skriva lagringspunkter är asynkron och innehar inte upp alla användartransaktioner. Skrivdata under kraschåterställning är prestanda som är viktiga för att få systemet svarar snabbt igen. Kraschåterställning ska i stället undantagsfall.
- Det är nästan alla läsningar från HANA gör om filerna. Undantagen är stora I/o när du utför säkerhetskopieringar av transaktionsloggen eller kraschåterställning fasen omstart av en HANA-instans.  
- Den största belastningen mot loggfilen för SAP HANA gör om är skrivningar. Beroende på typ av arbetsbelastning, kan du ha I/o som är så litet som 4 KB eller i andra fall I/o storleken på minst 1 MB. Skriva svarstid mot SAP HANA gör om loggen är prestanda som är viktiga.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt.

Ange cachelagring för olika volymer som använder Azure premium storage till ett resultat av dessa observerade i/o-mönster av SAP HANA:

- **data/hana/**: Ingen cachelagring.
- **/ hana/log**: Ingen cachelagring, med ett undantag för virtuella datorer i M-serien (se fler senare i den här artikeln).
- **/ hana/delade**: Läsa cachelagring.


Dessutom bör du tänka på det totala VM-i/o-arbetsflödet när du väljer eller välja en virtuell dator. Den totala VM genomflödet dokumenteras i [storlekar för virtuella datorer för den minnesoptimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Linux-i/o-läge för scheduler
Linux har flera olika i/o-schemaläggning lägen. En vanlig rekommendation från Linux-leverantörer och SAP är att i/o-Schemaläggaren läge för diskvolymer bort från den **cfq** läge för att den **noop** läge. Mer information finns i [SAP Obs! #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Lagringslösning med Write Accelerator för Azure virtuella datorer i M-serien
 Skriva Accelerator är en funktion i Azure som är aktiv för Azure virtuella datorer i M-serien exklusivt. Syftet med funktionen är att förbättra i/o-svarstiden för skrivningar Azure premium storage. För SAP HANA ska Write Accelerator användas mot den /hana/log volymen. Därför måste du ändra de konfigurationer som visas så här långt. Den huvudsakliga ändringen är bruten mellan /hana/data och /hana/log för att kunna använda Write Accelerator mot den /hana/log volymen. 

> [!IMPORTANT]
> SAP HANA-certifiering för Azure virtuella datorer är exklusivt för Write Accelerator för loggvolymen/hana/M-serien. Resultatet blir produktionsscenario distribution av SAP HANA på Azure virtuella datorer som förväntas vara konfigurerad med Write Accelerator för/hana/M-serien logga volym.

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

I följande tabell visar rekommenderade konfigurationer.

| SKU FÖR VIRTUELL DATOR | RAM | Högsta antal i/o<br /> Dataflöde | / hana/data | / hana/log | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB per sekund | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GB | 500 MB per sekund | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1 000 MB per sekund | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 giB | 1 000 MB per sekund | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 giB | 1 000 MB per sekund | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 giB | 2 000 MB per sekund |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 giB | 2 000 MB per sekund | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Kontrollera om genomflödet för de olika föreslagna enheterna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan du öka antalet Azure premium storage virtuella hårddiskar. Ändrar storlek på en volym med flera virtuella hårddiskar än listade ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator.

Skriva Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Minst måste Azure premium storage-diskar som utgör /hana/log volymen distribueras som hanterade diskar.

Det finns gränser för Azure premium storage virtuella hårddiskar per virtuell dator som har stöd för Write Accelerator. De aktuella gränserna är:

- 16 virtuella hårddiskar för ett M128xx VM.
- 8 virtuella hårddiskar för ett M64xx VM.
- 4 virtuella hårddiskar för ett M32xx VM.

Läs mer om hur du aktiverar Write Accelerator [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Mer information om och begränsningar för Write Accelerator finns i samma dokumentationen.


#### <a name="cost-conscious-azure-storage-configuration"></a>Kostnadsmedvetna Azure Storage-konfiguration
I följande tabell visas en konfiguration av VM-typer som kunder använder ofta till SAP HANA på Azure Virtual Machines-värd. Det kan finnas vissa typer av virtuella datorer som inte uppfyller alla angivna krav för SAP HANA. Det kan också finnas vissa typer av virtuella datorer som officiellt inte stöds med SAP HANA av SAP. Så länge har de virtuella datorerna utfört bra för icke-produktionsmiljöer scenarier. 

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU FÖR VIRTUELL DATOR | RAM | Högsta antal i/o<br /> Dataflöde | / hana/data och/hana/log<br /> stripe används med LVM eller mdadm | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 giB | 768 MB/sek | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GB | 768 MB/sek | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 giB | 1 200 MB/sek | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 giB | 2 000 MB per sekund | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB per sekund | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GB | 500 MB per sekund | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1 000 MB per sekund | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1 000 giB | 1 000 MB per sekund | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1 750 giB | 1 000 MB per sekund | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 000 giB | 2 000 MB per sekund |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3 800 giB | 2 000 MB per sekund | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


De diskar som rekommenderas för mindre VM-typer med 3 x P20 oversize volymer avseende utrymme rekommendationerna som ges i den [SAP TDI storage white paper om](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Valet visas i tabellen har gjorts att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du vill ändra den **/hana/backup** volymen, som har anpassat till behåller säkerhetskopior som representerar två gånger minne volymen kan passa på att göra justeringar. 

Kontrollera om genomflödet för de olika föreslagna enheterna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan du öka antalet Azure premium storage virtuella hårddiskar. Ändrar storlek på en volym med flera virtuella hårddiskar än listade ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 

> [!NOTE]
> Tidigare konfigurationer inte dra nytta av [Azure-dator med enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) eftersom den använder en blandning av Azure premium storage och Azure standardlagring. Alternativet har valts för att optimera kostnaderna. Välj premium storage för alla diskar i tabellen som visas som standard Azure-lagring (Sxx) att VM-konfigurationen är kompatibla med Azure enkel VM-serviceavtal.


> [!NOTE]
> Disk-konfigurationsrekommendationer rikta minimikraven som SAP ger dess infrastruktur-providers. I faktiska kunddistributioner och arbetsbelastningsscenarier får inte de här rekommendationerna du tillräckligt med funktioner i vissa situationer. Till exempel en kund behövde en snabbare inläsning av data efter en omstart av HANA eller konfigurationer krävs högre bandbredd till lagringen. Andra objekt omfattar ofta /hana/log, där 5 000 IOPS inte tillräcklig för den specifika arbetsbelastningen. Använd de här rekommendationerna som utgångspunkt och anpassa dem efter behov av arbetsbelastning.
>  

### <a name="set-up-azure-virtual-networks"></a>Konfigurera Azure-nätverk
När du har plats-till-plats-anslutning till Azure via VPN eller Azure ExpressRoute, måste du ha minst en Azure-nätverk som är anslutna via en virtuell gateway till VPN eller ExpressRoute-kretsen. I enkla distributioner kan den virtuella gatewayen distribueras i ett undernät för virtuella Azure-nätverket som är värd för SAP HANA-instanser för. 

Skapa två ytterligare undernät i virtuella Azure-nätverket för att installera SAP HANA. Ett undernät är värd för de virtuella datorerna för att köra SAP HANA-instanser. Det andra undernätet körs JumpBox eller hantering av virtuella datorer till värden SAP HANA-Studio, andra hanteringsprogramvara eller ditt program.

> [!IMPORTANT]
> Konfigurera [Azure virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationsvägen mellan SAP-program och DBMS-lagret för en SAP NetWeaver - Hybris- eller SAP S/4HANA-baserade system stöds inte. Den här begränsningen är av funktioner och prestandaskäl. Kommunikationsvägen mellan SAP-programnivån och DBMS-lagret måste vara en direkt. Begränsningen inte innehåller [programsäkerhetsgruppen (ASG) och nätverkssäkerhet gruppen (NSG) regler](https://docs.microsoft.com/azure/virtual-network/security-overview) om dessa ASG och NSG-regler som tillåter en direktkommunikation sökväg. 
>
> Andra scenarier där virtuella nätverksinstallationer inte stöds finns med: 
>
> - Kommunikationsvägar mellan virtuella Azure-datorer som representerar Linux Pacemaker klustrets noder och uppstår enheter enligt beskrivningen i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Kommunikationsvägar mellan virtuella Azure-datorer och Windows Server Scale-Out filserver ange görs enligt beskrivningen i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Virtuella nätverksinstallationer i kommunikationsvägar kan enkelt dubbla Nätverksfördröjningen mellan två kommunikation partner. De kan även begränsa genomflöde i kritiska vägar mellan SAP-programnivån och DBMS-lagret. I vissa kundscenarier orsaka virtuella nätverksinstallationer Pacemaker Linux-kluster att misslyckas. Dessa är fall där kommunikationen mellan noderna i Linux Pacemaker kommunicera med enheten uppstår genom ett nätverks virtuella installation.  
> 

> [!IMPORTANT]
> En annan utforma som *inte* stöds är uppdelning av SAP-programnivån och DBMS-lager i olika Azure-nätverk som inte är [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) med varandra. Vi rekommenderar att du särskilja SAP-programnivån och DBMS-lagret med hjälp av undernät i ett Azure-nätverk i stället för med hjälp av olika Azure-nätverk. 
>
>Om du inte väljer att följa rekommendationen och särskilja i stället de två lager i olika virtuella nätverk, de två virtuella nätverken måste vara [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tänk som nätverkstrafik mellan två [peer-kopplade](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuella Azure-nätverk kan komma att kostnaderna för dataöverföring. Enorm datavolym som består av många terabyte utväxlas mellan SAP-programnivån och DBMS-lager. Du kan ackumuleras betydande kostnader om SAP-programnivån och DBMS-lager är åtskilda mellan två peer-kopplade Azure-nätverk. 

När du installerar de virtuella datorerna som kör SAP HANA, måste de virtuella datorerna:

- Två virtuella nätverkskort installerade. Ett nätverkskort som ansluter till hanteringsundernätet. Ett annat nätverkskort som ansluter från det lokala nätverket eller andra nätverk till SAP HANA-instans i Azure-VM.
- Statiska privata IP-adresser som har distribuerats för båda virtuella nätverkskort.

> [!NOTE]
> Tilldela statiska IP-adresser via Azure innebär att tilldela dem till enskilda virtuella nätverkskort. Inte tilldela statiska IP-adresser i gästoperativsystemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup är beroende av faktumet att vid minst primära virtuella nätverkskortet är inställd på DHCP- och inte på statiska IP-adresser. Mer information finns i [felsöka Azure VM backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Tilldela flera statiska IP-adresser till en virtuell dator genom att tilldela flera virtuella nätverkskort till en virtuell dator.
>
>

För distributioner som är bestående, skapa ett virtuellt datacenter nätverksarkitektur i Azure. Den här arkitekturen rekommenderar avgränsning av Azure vnet-gateway som ansluter till den lokala till en separat Azure-nätverk. Det här separata virtuella nätverket är värd för all trafik som lämnar till den lokala eller till internet. Med den här metoden kan distribuera du programvara för att granska och logg-trafik som anger virtuellt datacenter i Azure i den här separata virtuella navnätverket. På så sätt kan du ha ett virtuellt nätverk som är värd för alla program och konfigurationer som är kopplat till ingoing och utgående trafik till din Azure-distribution.


Mer information om virtuellt datacenter-metoden och relaterade Azure-nätverksdesign finns: 

- [Azure virtual datacenter: Ett nätverksperspektiv](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Azure virtual datacenter och företagskontrollplanen](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>Trafik som flödar mellan en hubbens virtuella nätverk och ett virtuellt eker-nätverk med hjälp av [Azure virtuell nätverkspeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kan komma att ytterligare [kostnader](https://azure.microsoft.com/pricing/details/virtual-network/). Baserat på dessa kostnader kan du behöva göra kompromisser mellan kör en strikt NAV och eker nätverksdesign och flera [Azure ExpressRoute-gatewayer](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) att du ansluter till ekrar för att kringgå det virtuella nätverkets peering. 
>
> Azure ExpressRoute-gatewayer införa ytterligare [kostnader](https://azure.microsoft.com/pricing/details/vpn-gateway/) för. Du kan även uppstå ytterligare kostnader för programvara från tredje part som du använder för att logga in, granska och övervaka nätverkstrafik. Tänk över kostnaderna för datautbyte via virtuell nätverkspeering kontra kostnader som skapats av ytterligare ExpressRoute-gatewayer och licenser. Du kan välja på mikro-segmentering i ett virtuellt nätverk med undernät som isolering enheter i stället för virtuella nätverk.


En översikt över olika metoder som du kan använda för att tilldela IP-adresser, se [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

För virtuella datorer som kör SAP HANA kan du arbeta med de statiska IP-adresser som är tilldelade. Anledningen är att vissa configuration-attribut för HANA hänvisar till IP-adresser.

[Azure NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) används för att dirigera trafik som dirigeras till SAP HANA-instans eller JumpBox. NSG: erna och så småningom [programsäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) är kopplade till SAP HANA-undernät och hanteringsundernätet.

Följande bild visar en översikt över en ungefärlig distribution-schemat för SAP HANA som följer en arkitektur med nav och eker virtuellt nätverk:

![Ungefärlig distribution schemat för SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Distribuera SAP HANA i Azure utan en plats-till-plats-anslutning, skärma av SAP HANA-instans från det offentliga internet och dölja det bakom en proxy för vidarebefordran. I det här grundläggande scenariot distributionen förlitar sig på Azure inbyggd DNS-tjänster matcha värdnamn. Azure inbyggd DNS-tjänster är särskilt viktigt i en mer komplex distribution där offentliga IP-adresser används. Använder Azure NSG: er och [Azure virtuella nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) att övervaka routningen från internet till din Azure-nätverksarkitektur i Azure. 

Följande bild visar en ungefärlig schemat för hur du distribuerar SAP HANA utan en plats-till-plats-anslutning i en arkitektur med nav och eker virtuellt nätverk:
  
![Ungefärlig distribution schemat för SAP HANA utan en plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking2.PNG)
 

En annan beskrivning av hur du använder Azure virtuella nätverksinstallationer att styra och övervaka åtkomst från internet utan arkitekturen NAV och eker virtuellt nätverk finns i [distribuera högtillgängliga virtuella nätverksinstallationer](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurera Azure-infrastrukturen för SAP HANA-utskalning
Microsoft har en M-serien VM SKU som är certifierade för en skalbar SAP HANA-konfiguration. Typ av virtuell dator M128s är certifierat för en skalbar upp till 16 noder. Ändringar i skalbar-certifieringar för SAP HANA på Azure Virtual Machines finns i den [certifierade listan över plattformar som IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

De lägsta OS-versioner som används för att distribuera skalbara konfigurationer i virtuella Azure-datorer är:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

För skalbar-certifiering med 16 noder:

- En nod är huvudnoden.
- Högst 15 noder är arbetsnoder.

>[!NOTE]
>I Azure VM skalbara distributioner är det inte går att använda en standby-nod.
>

Det finns två orsaker till varför du inte kan konfigurera en standby nod:

- Azure har nu ingen inbyggd NFS-tjänst. NFS-resurser måste därför konfigureras med hjälp av funktioner från tredje part.
- Ingen av NFS-konfigurationer från tredje part kan uppfyller kriterierna i storage-svarstid för SAP HANA med sina lösningar som distribueras på Azure.

Därför kan inte /hana/data och /hana/log volymer delas. Inte dela dessa volymer enstaka noder förhindrar användning av en standby SAP HANA-nod i en skalbar konfiguration.

Följande bild visar den grundläggande designen för en enskild nod i en skalbar konfiguration:

![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-basics.PNG)

Det ser ut som den grundläggande konfigurationen av en VM-nod för SAP HANA, skala ut:

- För /hana/shared bygga ut ett högtillgänglig NFS-kluster som är baserat på SUSE Linux 12 SP3. Det här klustret är värd för /hana/shared NFS-resurser för din skalbara konfiguration och SAP NetWeaver eller BW/4HANA Central Services. Information om hur du skapar en sådan konfiguration finns i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Alla andra volymer delas inte mellan de olika noderna och inte är baserade på NFS. Av installationskonfigurationer och steg för att skala ut HANA installationer med icke delad /hana/data och /hana/log finns senare i den här artikeln.

>[!NOTE]
>Det NFS-klustret med hög tillgänglighet stöds som visas på bilderna hittills med SUSE Linux endast. En högtillgänglig NFS-lösning baserad på Red Hat vi senare.

Ändra storlek på volymer för noderna är desamma som för skala upp, förutom /hana/shared. I följande tabell visar den föreslagna storlekar och -typer i M128s VM SKU: n.

| SKU FÖR VIRTUELL DATOR | RAM | Högsta antal i/o<br /> Dataflöde | / hana/data | / hana/log | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2 000 giB | 2 000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Kontrollera om genomflödet för de olika föreslagna enheterna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan du öka antalet Azure premium storage virtuella hårddiskar. Ändrar storlek på en volym med flera virtuella hårddiskar än listade ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. Gäller även Write Accelerator för de diskar som utgör /hana/log volymen.
 

En formel som definierar storleken på volymen/hana/delade för skalbar som minnesstorleken på en enda arbetsnod per fyra arbetsnoder, se [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Om vi antar att du skapar SAP HANA skalbar certifierade M128s Azure VM med ungefär 2 TB minne kan sammanfattas SAP-rekommendationer som:

- För en huvudnod och upp till fyra arbetsnoder är /hana/shared volymens storlek 2 TB.
- För en huvudnod och fem och åtta arbetsnoder är /hana/shared volymens storlek 4 TB.
- För en huvudnod och 9 och 12 arbetsnoder är storleken på volymen /hana/shared 6 TB.
- För en huvudnod och 12 – 15 arbetsnoder är /hana/shared volymens storlek 8 TB.

Andra viktiga design som visas i grafik för nod-konfigurationen för en skalbar SAP HANA virtuell dator är det virtuella nätverket med Undernätskonfigurationen. SAP rekommenderar starkt att en åtskillnad mellan klienten och programmet riktar sig mot trafik från kommunikationen mellan noderna HANA. 

Om du vill åstadkomma detta kan du koppla två olika virtuella nätverkskort till den virtuella datorn enligt bilderna. Båda virtuella nätverkskort finns i olika undernät och har två olika IP-adresser. Du kan sedan styra flödet av trafik med regler för routning med hjälp av NSG: er eller användardefinierade vägar.

Särskilt i Azure finns det inga innebär och metoder för att tillämpa servicekvalitet och kvoter för specifika virtuella nätverkskort. Uppdelning av klienten och programmet kund- och kommunikationen mellan noderna kommunikation öppnas därför inte alla möjligheter att prioritera en trafikström över den andra. Uppdelning förblir i stället ett mått på säkerhet i avskärmning kommunikationen mellan noderna kommunikation av skalbara konfigurationer.  

>[!IMPORTANT]
>SAP rekommenderar starkt att du avgränsa nätverkstrafik till klienten och programmet på klientsidan och kommunikationen mellan noderna trafiken enligt beskrivningen i den här artikeln. Vi rekommenderar att du placerar en arkitektur på plats som visas i föregående bilderna.
>

Följande bild visar den minsta nödvändiga nätverksarkitekturen ur ett nätverk:

![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-networking-overview.PNG)

De gränser som stöds hittills är 15 arbetsnoder förutom en huvudnoden.

Följande bild visar lagringsarkitektur från en storage synsätt:


![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-storage-overview.PNG)

/Hana/shared volymen finns på konfiguration för med hög tillgänglighet NFS-resursen. Alla andra enheter monteras lokalt till de enskilda virtuella datorerna. 

### <a name="highly-available-nfs-share"></a>Med hög tillgänglighet NFS-resurs
Hittills fungerar det NFS-klustret med hög tillgänglighet med SUSE Linux endast. Installationsprogrammet information finns i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Om du inte delar NFS-kluster med andra HANA konfigurationer utanför Azure-nätverket som kör SAP HANA-instanser kan du installera den i samma virtuella nätverk. Installera det i ett eget undernät och se till att inte all skadlig trafik kan komma åt undernätet. I stället begränsa trafik till det undernätet till IP-adresser för den virtuella datorn som kör trafiken till /hana/shared volym.

Relaterat till det virtuella nätverkskortet för en virtuell dator skalbar HANA som dirigerar /hana/shared-trafik, följande är rekommendationen:

- Eftersom trafiken till /hana/shared är begränsad, dirigera den via det virtuella nätverkskortet som är tilldelad till klientnätverk i den lägsta konfigurationen.
- Så småningom för trafik till/hana/delade distribuera ett tredje undernät i det virtuella nätverket där du distribuerar den skala ut SAP HANA-konfigurationen. Tilldela en tredje virtuell NIC som är värd för det undernätet. Använd tredje virtuella nätverkskortet och associerade IP-adress för trafiken till NFS-resurs. Du kan sedan använda separata åtkomst och regler för routning.

>[!IMPORTANT]
>Nätverkstrafiken mellan de virtuella datorerna som har SAP HANA i en skalbar sätt distribueras och högtillgänglig NFS under inga omständigheter kan vidarebefordras genom en [virtuell nätverksinstallation](https://azure.microsoft.com/solutions/network-appliances/) eller liknande virtuella installationer. Azure NSG: er finns inga sådana enheter. Kontrollera din routningsregler och se till att nätverkets virtuella installationer eller liknande virtuella installationer detoured när de använder NFS med hög tillgänglighet dela från de virtuella datorer som kör SAP HANA.
> 

Om du vill dela med hög tillgänglighet NFS klustret mellan konfigurationer på SAP HANA kan du flytta dessa HANA-konfigurationer till samma virtuella nätverk. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Installera SAP HANA-utskalning i Azure
Följ dessa allmänna steg för att installera en skala ut SAP-konfiguration:

- Distribuera nya eller anpassa den nya Azure-nätverksinfrastruktur.
- Distribuera nya virtuella datorer med hjälp av Azure-hanterade premium lagringsvolymer.
- Distribuera en ny eller anpassa en befintlig NFS-kluster med hög tillgänglighet.
- Anpassa nätverksroutning för att se till att, till exempel intra-nod-kommunikation mellan virtuella datorer inte dirigeras via en [virtuell nätverksinstallation](https://azure.microsoft.com/solutions/network-appliances/). Detsamma gäller för trafik mellan de virtuella datorerna och de NFS-klustret med hög tillgänglighet.
- Installera SAP HANA-huvudnoden.
- Anpassa konfigurationsparametrar för noden som SAP HANA.
- Fortsätt med installationen av SAP HANA-arbetsnoder.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Installera SAP HANA i en skalbar-konfiguration
Infrastrukturen för Azure virtuella datorer har distribuerats och alla andra förberedelser är klar. Nu, om du vill installera konfigurationer för SAP HANA-skala ut så här:

- Installera SAP HANA huvudnoden enligt SAP: s-dokumentationen.
- *Efter installationen kan du ändra filen global.ini och lägga till parametern ”basepath_shared = Nej” till global.ini*. Den här parametern gör det möjligt för SAP HANA att köra i skalbar utan delad /hana/data och /hana/log volymer mellan noderna. Mer information finns i [SAP Obs! #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- När du har ändrat parametern global.ini SAP HANA-instansen startas om.
- Lägg till ytterligare arbetsnoder. Mer information finns i [Administrationsguide för SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Ange det interna nätverket för kommunikation mellan noder i SAP HANA-kommunikation under installationen eller efteråt genom att använda, till exempel lokala hdblcm. Mer information finns i [SAP Obs! #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Efter den här installationen routine som, använder skalbara konfigurationen som du har installerat icke delad diskar för att köra /hana/data och /hana/log. / Hana/delade-volym placeras på den högtillgängliga NFS-resursens.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamisk lagringsnivåer 2.0 för Azure-datorer

Förutom certifieringar för SAP HANA på Azure virtuella datorer i M-serien stöds även SAP HANA dynamisk lagringsnivåer 2.0 (DT 2.0) på Microsoft Azure. Länkar till SAP HANA dynamisk lagringsnivåer dokumentation, finns i avsnittet ”länkar till DT 2.0 dokumentation” senare i den här artikeln. Det finns ingen skillnad i installerar produkten eller operativsystem, till exempel via SAP HANA Cockpit inuti en Azure-dator, men några viktiga saker som är obligatoriska för officiella support på Azure. Dessa nyckelpunkter beskrivs i följande avsnitt. 

SAP HANA DT 2.0 stöds inte av SAP BW eller S4HANA. De viktigaste användningsfallen är just nu interna HANA-program.


### <a name="overview"></a>Översikt

Följande bild ger en översikt över DT 2.0 stöd på Microsoft Azure. Följ dessa obligatoriska krav att följa officiell certifiering:

- DT 2.0 måste installeras på en dedikerad virtuell Azure-dator. Den kan inte köras på samma virtuella dator där SAP HANA körs.
- SAP HANA och DT 2.0 virtuella datorer måste distribueras inom samma Azure-nätverk.
- SAP HANA och DT 2.0 virtuella datorer måste distribueras med Azure Accelererat nätverk aktiverat.
- Lagringstypen för de virtuella datorerna 2.0 DT måste vara Azure premium storage.
- Flera Azure-diskar måste kopplas till den virtuella datorn 2.0 DT.
- Skapa en RAID- eller stripe-volym, antingen via LVM eller mdadm, krävs med hjälp av striping över Azure-diskar.

Följande avsnitt innehåller fler förklaringar.

![Översikt över arkitekturen för SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Reserverad Azure VM för SAP HANA DT 2.0

DT 2.0 stöds endast på en dedikerad virtuell dator på Azure IaaS. DT 2.0 är inte tillåtet att köra samma virtuella Azure-datorn där HANA-instansen körs. Först har kan två typer av virtuella datorer användas för att köra SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

VM-typbeskrivningar finns i [storlekar för virtuella datorer för den minnesoptimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Beroende Grundtanken med DT 2.0 som handlar om att bli av med frekventa data för att spara kostnader, bra det att använda motsvarande VM-storlekar. Det finns ingen strikt regel för möjliga kombinationer. Det beror på arbetsbelastningen viss kund.

I följande tabell visar rekommenderade konfigurationer.

| SAP HANA VM-typ | DT 2.0 VM-typ |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Alla kombinationer av SAP HANA-certifierade M-serien virtuella datorer med DT 2.0 virtuella datorer som stöds, till exempel M64 32ms och E32sv3, är möjliga.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-nätverk och SAP HANA DT 2.0

Installera DT 2.0 på en dedikerad virtuell dator kräver dataflöde i nätverket mellan den virtuella datorn 2.0 DT och SAP HANA VM med minst 10 GB. Därför är det nödvändigt att placera alla virtuella datorer inom samma Azure-nätverk och aktivera Azure Accelerated Networking.

Läs mer om Azure Accelerated Networking [skapa en Linux-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM-lagring för SAP HANA DT 2.0

Enligt DT 2.0 metodtips är minsta disk-i/o dataflödet 50 MB/sek per fysisk kärna. Titta på specifikationen för två Azure VM-typer som stöds för DT 2.0, är den maximala värdet för disken i/o-dataflödesgräns för den virtuella datorn:

- **E32sv3**:   768 MB/s, icke cachelagrat, vilket innebär att ett förhållande på 48 MB/sek per fysisk kärna
- **M64-32ms**:  1 000 MB/s, icke cachelagrat, vilket innebär att ett förhållande på 62,5 MB/sek per fysisk kärna

Koppla flera Azure-diskar till den virtuella datorn 2.0 DT och skapa en programvaru-RAID med striping på operativsystemsnivån för att uppnå den maximala gränsen på diskdataflöde per virtuell dator krävs. En enskild Azure disk tillhandahålla inte genomflöde och når maxgränsen för virtuell dator. Azure premium storage är obligatorisk för att köra DT 2.0. 

- Mer information om tillgängliga Azure disktyper finns i [väljer en disktyp för Windows Azure IaaS-VM](../../windows/disks-types.md).
- Läs mer om hur du skapar en programvaru-RAID via mdadm [konfigurera programvaru-RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Läs mer om hur du konfigurerar LVM för att skapa en stripe-volym för maximalt dataflöde, [konfigurera LVM på en Linux-VM i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Beroende på storleken krav finns det olika alternativ för att nå maximalt dataflöde för en virtuell dator. Här följer möjliga data volym diskkonfigurationer för varje DT 2.0 VM-typ att uppnå den övre gränsen för VM-dataflöde. E32sv3 VM anses vara en nivå för mindre arbetsbelastningar. Om det inte är tillräckligt fort, kan det vara nödvändigt att ändra storlek på den virtuella datorn till M64 32ms.

Eftersom den M64 32ms virtuella datorn har en stor mängd minne, kanske i/o-belastning inte når gränsen, särskilt för läsintensiva arbetsbelastningar. Färre diskar i stripe-uppsättning kan vara tillräcklig beroende på arbetsbelastningen kundspecifika. Följande diskkonfigurationer har valts för att vara på den säkra sidan, för att garantera maximalt dataflöde.


| SKU FÖR VIRTUELL DATOR | Disk-config 1 | Disk-config 2 | Disk-config 3 | Disk-config 4 | Disk-config 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Särskilt om arbetsbelastningen är läsintensiva, kan aktivera Azure-värd cache ”skrivskyddad”-inställningen som rekommenderas för datavolymer databasprogrammet öka i/o-prestanda. För att transaktionsloggen vara diskcache Azure-värd ”ingen”. 

Startpunkten som vi rekommenderar för storleken på loggvolymen är en tumregel på 15 procent av storleken på data. Du kan skapa loggvolymen med olika Azure disktyper beroende på kraven för kostnader och dataflöde. Hög i/o-genomströmning krävs för loggvolym. 

Om du använder den virtuella datorn skriver M64 32ms, rekommenderar vi att du aktiverar [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Write Accelerator är en funktion i Azure som ger optimala disk skrivfördröjningen för transaktionsloggen. Det är bara tillgängligt för M-serien. Det finns vissa saker att tänka på, t.ex det maximala antalet diskar per typ av virtuell dator. Läs mer på Write Accelerator [aktivera Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


I följande tabell visas några exempel för att du beräkna storleken på loggvolymen.

| skriver data volymens storlek och disk | loggvolymen och disk Skriv config 1 | loggvolymen och disk Skriv config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Liknar SAP HANA-utskalning, katalogen /hana/shared delas mellan SAP HANA VM och den virtuella datorn 2.0 DT. Vi rekommenderar att du använder samma arkitektur för SAP HANA, skala ut med hjälp av dedikerade virtuella datorer som fungerar som en NFS-server med hög tillgänglighet. För att ange en delad volym för säkerhetskopiering, använder du identiska designen. Men det är upp till dig att avgöra om hög tillgänglighet krävs eller om det räcker att använda en dedikerad virtuell dator med tillräckligt mycket lagringskapacitet för att fungera som en sekundär server.



### <a name="links-to-dt-20-documentation"></a>Länkar till DT 2.0-dokumentation 

- [Update guide och SAP HANA-installation av dynamisk lagringsnivåer](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamisk lagringsnivåer självstudier och resurser](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dynamisk lagringsnivåer konceptbevis](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Förbättringar av SAP HANA 2.0 Service Pack 02 dynamisk lagringsnivåer](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Åtgärder för att distribuera SAP HANA på Azure Virtual Machines
I följande avsnitt beskrivs några av de åtgärder som rör distribution av SAP HANA-system på Azure Virtual Machines.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Säkerhetskopiera och återställa åtgärder på virtuella Azure-datorer
Följande dokument beskriver hur du säkerhetskopierar och återställer din SAP HANA-distribution:

- [Översikt över SAP HANA-säkerhetskopiering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Säkerhetskopiering för SAP HANA-filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA-lagring ögonblicksbild prestandamått](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Starta och starta om virtuella datorer som innehåller SAP HANA
En framstående funktion i Azure offentligt moln är att du debiteras endast för din databehandling minuter. Till exempel när du stänger en virtuell dator som kör SAP HANA kan faktureras du bara för kostnader för lagring under den tiden. En annan funktion är tillgänglig när du anger statiska IP-adresser för dina virtuella datorer i den första distributionen. När du startar om en virtuell dator som har SAP HANA, startar om den virtuella datorn med dess tidigare IP-adresser. 


### <a name="use-saprouter-for-sap-remote-support"></a>Använd SAProuter för fjärrsupport för SAP
Om du har en plats-till-plats-anslutning mellan ditt lokala platser och Azure och du kör SAP-komponenter, kör du förmodligen redan SAProuter. I det här fallet, Följ dessa steg för fjärrsupport:

- Underhålla privata och statiska IP-adressen för den virtuella datorn som är värd för SAP HANA i SAProuter-konfigurationen.
- Konfigurera NSG för undernätet som är värd för HANA VM för att tillåta trafik via TCP/IP-port 3299.

Om du ansluter till Azure via internet och du inte har en SAP-router för den virtuella datorn med SAP HANA, installera komponenten. Installera SAProuter i en separat virtuell dator i hanteringsundernätet. 

Följande bild visar en ungefärlig schemat för att distribuera SAP HANA utan en plats-till-plats-anslutning och med SAProuter:

![Omild distribution schemat för SAP HANA utan en plats-till-plats-anslutning och SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Glöm inte att installera SAProuter i en separat virtuell dator och inte i din JumpBox VM. Den separata virtuella datorn måste ha en statisk IP-adress. Om du vill ansluta din SAProuter till SAProuter som är värd för SAP, kontakta SAP för en IP-adress. SAProuter som är värd för SAP är motsvarigheten till SAProuter-instans som du installerar på den virtuella datorn. Använd IP-adressen från SAP för att konfigurera din SAProuter-instans. I inställningarna för konfiguration är endast nödvändiga porten TCP-port 3299.

Läs mer om hur du konfigurerar och underhåller fjärrsupport anslutningar via SAProuter [SAP-dokumentationen](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på interna virtuella Azure-datorer
Om du kör SUSE Linux Enterprise Server för SAP-program 12 SP1 eller senare, kan du upprätta ett Pacemaker-kluster med STONITH enheter. Använda enheterna som du ställer in en SAP HANA-konfiguration som använder synkron replikering med HANA System Replication och automatisk redundans. Mer information om installationsprocessen finns i [SAP HANA guide för hög tillgänglighet för Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
