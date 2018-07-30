---
title: Konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure | Microsoft Docs
description: Handbok för SAP HANA-system som distribueras på Azure virtual machines.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/27/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba24fc1d75e2a2d2e50bfaba89de9404fc5e6c75
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325378"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure
Det här dokumentet innehåller anvisningar för att konfigurera Azure-infrastrukturen och använda SAP HANA-system som har distribuerats på Azures inbyggda virtuella datorer (VM). Dokumentet innehåller också konfigurationsinformation för SAP HANA skalbar för M128s VM SKU: N. Det här dokumentet är inte avsedd att ersätta standard SAP-dokumentationen, som innehåller följande innehåll:

- [Administrationsguide för SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP-installationsguiderna](https://service.sap.com/instguides)
- [SAP-anteckningar](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenterna:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella nätverk](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Läs mer om SAP NetWeaver och andra SAP-komponenter på Azure i den [SAP på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) delen av den [dokumentation om Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Överväganden för grundläggande konfiguration
I följande avsnitt beskrivs grundinställning överväganden för distribution av SAP HANA-system på Azure Virtual Machines.

### <a name="connect-into-azure-virtual-machines"></a>Ansluta till Azure-datorer
Enligt beskrivningen i den [virtuella Azure-datorer Planeringshandbok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), det finns två grundläggande metoder för att ansluta till virtuella Azure-datorer:

- Anslut via internet och offentliga slutpunkter på en hoppa virtuell dator eller på den virtuella datorn som kör SAP HANA.
- Ansluta via en [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Plats-till-plats-anslutning via VPN eller ExpressRoute är nödvändigt för produktionsscenarier. Den här typen av anslutning krävs även för icke-produktionsscenarion som går in i produktionsscenarier där SAP-program används. Följande bild visar ett exempel på cross-plats-anslutning:

![Cross-plats-anslutning](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Välj Azure VM-typer
Vilka typer av virtuella Azure-datorer som kan användas för produktionsscenarier visas i den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För icke-produktionsscenarion finns en mängd olika interna Azure VM-typer.

>[!NOTE]
> För icke-produktionsscenarion, använder du VM-typer som anges i den [SAP-kommentar #1928533](https://launchpad.support.sap.com/#/notes/1928533). För användning av virtuella Azure-datorer för produktionsscenarier, kontrollera för SAP HANA-certifierade virtuella datorer i SAP publicerade [listan över certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Distribuera virtuella datorer i Azure med hjälp av:

- Azure-portalen.
- Azure PowerShell-cmdlets.
- Azure CLI.

Du kan även distribuera en fullständig installerade SAP HANA-plattform på Virtuella Azure-tjänster via den [SAP-molnplattform](https://cal.sap.com/). Installationsprocessen beskrivs i [distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) eller med automation är [här](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Välj typ av Azure Storage
Azure tillhandahåller två typer av lagring som är lämpliga för virtuella Azure-datorer som kör SAP HANA:

- [Azure Standard-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure erbjuder två metoder för distribution för virtuella hårddiskar på Azure Standard och Premium Storage. Om det övergripande scenariot tillåter dra nytta av [Azure hanterade disk](https://azure.microsoft.com/services/managed-disks/) distributioner.

En lista över lagringstyper och deras serviceavtal i IOPS och lagring dataflöde, granska de [Azure-dokumentationen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurera lagring för virtuella Azure-datorer

I den mån det du har köpt SAP HANA-enheterna för den lokala haft du aldrig om i/o-undersystem och dess funktioner. Eftersom leverantören av enheten som behövs för att se till att de minsta krav är uppfyllda för SAP HANA. När du bygger Azure-infrastrukturen själv kan bör du också känna till några av dessa krav. Och förstår även de konfigurationskrav som föreslås i följande avsnitt. Eller om du vill köra SAP HANA på i de fall där du konfigurerar de virtuella datorerna. Några av de egenskaper som uppmanas vilket resulterar i att behöva:

- Aktivera skrivning/volym på **/hana/log** en 250 MB/sek som minimum med 1 MB i/o-storlekar
- Aktivera Läs aktiviteten hos minst 400MB/sekund för **/hana/data** för 16 MB och 64 MB i/o-storlekar
- Aktivera skrivningsaktiviteter minst 250MB/sek för **/hana/data** med 16 MB och 64 MB i/o-storlekar

Svarstiden är viktigt för DBMS system, beroende på den låga storage även när de olika DBMS som SAP HANA, behålla data i minnet. Den kritiska vägen i storage är vanligtvis runt transaction log skrivningar av DBMS system. Men även operations skriva lagringspunkter eller läser in data i minnet när kraschåterställning kan vara avgörande. Därför är det obligatoriskt att utnyttja Azure Premium-diskar för **/hana/data** och **/hana/log** volymer. För att uppnå lägsta dataflödet för **/hana/log** och **/hana/data** efter behov av SAP, du behöver för att skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar. Och Använd RAID-volymer som **/hana/data** och **/hana/log** volymer. Eftersom stripe-storlekar för RAID 0 rekommendationen är att använda:

- 64KB eller 128KB för   **/hana/data**
- 32KB för   **/hana/log**

> [!NOTE]
> Du behöver inte konfigurera någon redundansnivå med RAID-volymer eftersom Azure Premium och Standard-lagring att tre avbildningar av en virtuell Hårddisk. Användningen av en RAID-volym är helt och hållet att konfigurera volymer som ger tillräcklig i/o-dataflöde.

Kan sparas ett antal virtuella Azure-hårddiskar under en RAID är ackumulerande från en sida för IOPS och lagring dataflöde. Så om du placerar en RAID 0 över 3 x P30 Azure Premium Storage-diskar, ger den dig tre gånger IOPS och tre gånger kapaciteten för lagring för en enskild Azure Premium Storage P30-disk.

Cachelagring rekommendationerna nedan förutsatt att i/o-egenskaper för SAP HANA listan som:

- Det finns nästan alla skrivskyddade arbetsbelastningar mot HANA datafiler. Undantagen är stora storlekar I/o efter omstart av HANA-instans eller när data läses in i HANA. Ett annat fall av större läsa I/o mot datafiler kan vara HANA säkerhetskopior av databasen. Därmed måste Läs cachelagring huvudsakligen ingen visas meningsfull sedan i de flesta fall, alla datavolymer i filen läsas helt.
- Skriva mot datafilerna som uppstår i toppar baserat av HANA lagringspunkter och HANA kraschåterställning. Skriva lagringspunkter är asynkron och innehar inte upp alla användartransaktioner. Skrivdata under kraschåterställning är prestanda som är viktiga för att få systemet svarar snabbt igen. Kraschåterställning bör dock i stället undantagsfall
- Det är nästan alla läsningar från HANA gör om filerna. Undantagen är stora I/o när du genomför säkerhetskopieringar av transaktionsloggen kan kraschåterställning, eller i fasen omstart av en HANA-instans.  
- Största belastningen mot SAP HANA gör om loggfilen är skrivningar. Beroende på vilken typ av arbetsbelastning, kan det ha I/o som är så litet som 4 KB eller i andra fall i/o-storlekar på 1 MB eller mer. Skriva svarstid mot SAP HANA gör om loggen är prestanda som är viktiga.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

Till följd av dessa observerade i/o-mönster av SAP HANA anges cachelagring för olika volymer som använder Azure Premium Storage som:

- **data/hana/** -ingen cachelagring
- **/ hana/log** – ingen cachelagring - undantag för M-serien (se senare i det här dokumentet)
- **/ hana/delade** – Läs cachelagring


Tänk också övergripande VM-i/o-dataflöde när du ändrar storlek på eller bestämmer dig för en virtuell dator. Den totala VM genomflödet dokumenteras i artikeln [storlekar för virtuella datorer för den minnesoptimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Kostnad medvetna Azure Storage-konfiguration
I följande tabell visas en konfiguration av VM-typer som kunder använder ofta till SAP HANA på Azure Virtual Machines-värd. Det kan finnas vissa typer av virtuella datorer som inte uppfyller alla angivna krav för SAP HANA. Men än så länge de virtuella datorerna visat sig att utföra bra för icke-produktionsscenarion. 

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU FÖR VIRTUELL DATOR | RAM | Max. VM-I/O<br /> Dataflöde | / hana/data och/hana/log<br /> stripe används med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 giB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 giB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1 750 giB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 giB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Diskarna rekommenderas för mindre VM-typer med 3 x P20 oversize volymer om utrymme rekommendationerna enligt den [SAP TDI Storage White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men gjordes valet som visas i tabellen för att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du behöver ändringar av den **/hana/säkerhetskopiering**p volymen, som har anpassat för att behålla säkerhetskopior som representerar två gånger minne volymen kan passa på att justera.   
Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 

> [!NOTE]
> Konfigurationer som angetts ovan inte skulle ha nytta av [Azure-dator med enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) eftersom den använder en blandning av Azure Premium Storage och Azure standardlagring. Dock har markeringen valts för att optimera kostnaderna.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Azure Storage-konfiguration kan dra i syfte att uppfylla SLA för enskild virtuell dator
Om du vill dra nytta av [Azure-dator med enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), måste du använda Azure Premium Storage virtuella hårddiskar exklusivt.

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| SKU FÖR VIRTUELL DATOR | RAM | Max. VM-I/O<br /> Dataflöde | / hana/data och/hana/log<br /> stripe används med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 giB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 giB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 giB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 giB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Diskarna rekommenderas för mindre VM-typer med 3 x P20 oversize volymer om utrymme rekommendationerna enligt den [SAP TDI Storage White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men gjordes valet som visas i tabellen för att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du behöver ändringar av den **/hana/backup** volymen, som har anpassat för att behålla säkerhetskopior som representerar två gånger minne volymen kan passa på att justera.  
Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lagringslösning med Azure Write Accelerator för virtuella datorer i Azure M-serien
Azure Write Accelerator är en funktion som hämtar distribueras för virtuella datorer i M-serien exklusivt. Som namnet tillstånd, är syftet med funktionen för att förbättra i/o-svarstiden för skrivningar Azure Premium Storage. För SAP HANA, Write Accelerator ska användas mot den **/hana/log** endast volymen. Därför måste de konfigurationer som visas så här långt ändras. Den huvudsakliga ändringen är bruten mellan den **/hana/data** och **/hana/log** för att kunna använda Azure Write Accelerator mot den **/hana/log** endast volymen. 

> [!IMPORTANT]
> SAP HANA-certifiering för virtuella datorer i Azure M-serien är enbart med Azure Write Accelerator för den **/hana/log** volym. Därför Produktionsdistribution scenariot SAP HANA på virtuella datorer i Azure M-serien förväntas vara konfigurerad med Azure Write Accelerator för den **/hana/log** volym.  

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

De rekommenderade konfigurationerna se ut:

| SKU FÖR VIRTUELL DATOR | RAM | Max. VM-I/O<br /> Dataflöde | / hana/data | / hana/log | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 giB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 giB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator.

Azure Write Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör den **/hana/log** volymen behöver distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage virtuella hårddiskar per virtuell dator som stöds av Azure Write Accelerator. De aktuella gränserna är:

- 16 virtuella hårddiskar för ett M128xx VM
- 8 virtuella hårddiskar för ett M64xx VM
- 4 virtuella hårddiskar för ett M32xx VM

Mer detaljerad information om hur du aktiverar Azure Write Accelerator finns i artikeln [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Write Accelerator finns i samma-dokumentationen.



### <a name="set-up-azure-virtual-networks"></a>Konfigurera Azure-nätverk
När du har en plats-till-plats-anslutning till Azure via VPN eller ExpressRoute, måste du ha minst en Azure-nätverk som är anslutna via en virtuell Gateway till VPN eller ExpressRoute-kretsen. I enkla distributioner kan den virtuella gatewayen distribueras i ett undernät för den Azure-nätverk (VNet) som är värd för SAP HANA-instanser. Om du vill installera SAP HANA kan du skapa två ytterligare undernät i virtuella Azure-nätverket. Ett undernät är värd för de virtuella datorerna för att köra SAP HANA-instanser. Andra undernätet kör Jumpbox eller hantering av virtuella datorer som värd för SAP HANA-Studio eller annan hanteringsprogramvara av din programvara.

När du installerar de virtuella datorerna som kör SAP HANA, måste de virtuella datorerna:

- Två virtuella nätverkskort som är installerade: ett nätverkskort för att ansluta till undernätet för hantering och ett nätverkskort för att ansluta från det lokala nätverket eller andra nätverk, till SAP HANA-instans i Azure-VM.
- Statiska privata IP-adresser som har distribuerats för båda virtuella nätverkskort.

Men för distributioner som är bestående, måste du skapa ett virtuellt datacenter nätverksarkitektur i Azure. Den här arkitekturen rekommenderar avgränsning av Azure VNet-Gateway som ansluter till lokala i ett separat virtuellt Azure-nätverk. Det här separata virtuella nätverket bör vara värd för all trafik som lämnar antingen till en lokal eller till internet. Den här metoden kan du distribuera programvara för granskning och loggning trafik som anger virtuellt datacenter i Azure i den här separata virtuella hubbnätverket. Du måste ett virtuellt nätverk som är värd för programvara och konfigurationer som är kopplat till in- och utgående trafik till din Azure-distribution.

Artiklarna [Azure Virtual Datacenter: ett Nätverksperspektiv](https://docs.microsoft.com/azure/networking/networking-virtual-datacenter) och [Azure Virtual Datacenter och Företagskontrollplanen](https://docs.microsoft.com/azure/architecture/vdc/) ger mer information om virtuellt datacenter-metoden och relaterade Azure VNet-design.


>[!NOTE]
>Trafik som flödar mellan virtuella hubbnätverket och eker virtuellt nätverk med hjälp av [Azure VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) är föremål för ytterligare [kostnader](https://azure.microsoft.com/pricing/details/virtual-network/). Baserat på dessa kostnader kan du behöva tänka på att göra kompromisser mellan kör en strikt NAV och ekrar nätverksdesign och flera [Azure ExpressRoute-gatewayer](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) att du ansluter till 'ekrar ”för att kringgå VNet-peering. Men Azure ExpressRoute-gatewayer införa ytterligare [kostnader](https://azure.microsoft.com/pricing/details/vpn-gateway/) samt. Du kan även uppstå ytterligare kostnader för programvara från tredje part som du använder för nätverkstrafik loggning, granskning och övervakning. Beroende på kostnaderna för datautbyte via VNet-peering på ena sidan och kostnader som skapats av ytterligare Azure ExpressRoute-gatewayer och ytterligare licenser, kan du välja för mikro-segmentering i ett virtuellt nätverk med undernät som isolering enhet i stället för virtuella nätverk.


En översikt över de olika metoderna för att tilldela IP-adresser, se [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

För virtuella datorer som kör SAP HANA, bör du arbeta med statiska IP-adresser som är tilldelade. Anledningen är att vissa configuration-attribut för HANA hänvisar till IP-adresser.

[Azure Nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) används för att dirigera trafik som dirigeras till SAP HANA-instans eller jumpbox. NSG: erna och så småningom [Programsäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) är kopplade till SAP HANA-undernät och hanteringsundernätet.

Följande bild visar en översikt över en ungefärlig distribution-schemat för SAP HANA följa en nav och ekrar VNet-arkitektur:

![Ungefärlig distribution schemat för SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

För att distribuera SAP HANA i Azure utan en plats-till-plats-anslutning kan vill du fortfarande skärma av SAP HANA-instans från det offentliga internet och dölja det bakom en proxy för vidarebefordran. I det här grundläggande scenariot distributionen förlitar sig på Azure inbyggd DNS-tjänster för att matcha värdnamn. Azure inbyggd DNS-tjänster är särskilt viktigt i en mer komplex distribution där offentliga IP-adresser används. Använder Azure NSG: er och [nva: er för Azure](https://azure.microsoft.com/solutions/network-appliances/) att styra, övervaka routningen från internet till din Azure VNet-arkitektur i Azure. Följande bild visar en ungefärlig schemat för att distribuera SAP HANA utan en plats-till-plats-anslutning i en nav och ekrar VNet-arkitektur:
  
![Ungefärlig distribution schemat för SAP HANA utan en plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking2.PNG)
 

En annan beskrivning om hur du använder Azure nva: er att styra och övervaka åtkomst från Internet utan hubben och ekrar VNet-arkitekturen finns i artikeln [distribuerar virtuella nätverksinstallationer med hög tillgänglighet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurera Azure-infrastrukturen för SAP HANA-utskalning
Microsoft har en M-serien SKU för virtuell dator som är certifierade för en skalbar SAP HANA-konfiguration. Typ av virtuell dator M128s har certifierat för en skalbar upp till 16 noder. Ändringar i skalbar-certifieringar för SAP HANA på Azure Virtual Machines, kontrollera [listan över certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Lägsta OS-versioner för att distribuera skalbara konfigurationer i virtuella Azure-datorer är:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

Skala ut-certifieringen 16 noder

- En nod är huvudnoden
- Högst 15 noder är arbetsnoder

>[!NOTE]
>Det finns ingen risk att använda en standby nod i Azure VM skalbara distributioner
>

Orsaken till inte att kunna konfigurera en standby-noden har två syften:

- Azure har nu ingen inbyggd NFS-tjänst. Därmed måste NFS-resurser konfigureras med hjälp av funktioner från tredje part.
- Ingen av NFS-konfigurationer från tredje part kan uppfyller kriterierna för svarstid i lagringen för SAP HANA med sina lösningar som distribueras på Azure.

Därför **/hana/data** och **/hana/log** volymer kan inte delas. Inte dela dessa volymer enstaka noder, förhindrar att en standby SAP HANA-nod i en skalbar konfiguration.

Därmed kommer grundläggande design för en enskild nod i en skalbar konfiguration att se ut:

![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-basics.PNG)

Det ser ut som den grundläggande konfigurationen av en VM-nod för SAP HANA, skala ut:

- För **/hana/delade**, du bygga ut ett högtillgänglig NFS-kluster som är baserat på SUSE Linux 12 SP3. Det här klustret ska vara värd för den **/hana/delade** NFS-resurs/er av din skalbara konfigurations- och SAP NetWeaver- eller BW/4HANA Central Services. Dokumentation för att skapa en sådan konfiguration finns i artikeln [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Alla andra diskvolymer är **inte** delas mellan olika noder och är **inte** baserat på NFS. Av installationskonfigurationer och steg för att skala ut HANA installationer med icke-delade **/hana/data** och **/hana/log** tillhandahålls längre ned i det här dokumentet.

>[!NOTE]
>Det NFS-klustret med hög tillgänglighet stöds som visas på bilderna hittills med SUSE Linux endast. En högtillgänglig NFS-lösning baserad på Red Hat vi senare.

Ändra storlek på volymer för noderna är samma som för skala upp, utom **/hana/delade**. För VM-SKU M128s ut föreslagna storlekar och typer:

| SKU FÖR VIRTUELL DATOR | RAM | Max. VM-I/O<br /> Dataflöde | / hana/data | / hana/log | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. Gäller även Azure Write Accelerator för diskarna som utgör den **/hana/log** volym.
 
I dokumentet [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), en formel med namnet som definierar storleken på den **/hana/delade** volymen för skalbar som minnesstorleken på en enda arbetsnod per 4 arbetsnoder.

Om vi antar att du vidta SAP HANA skalbar certifierade M128s Azure VM med ungefär 2 TB minne, kan SAP-rekommendationer sammanfattas som:

- En huvudnod och upp till fyra arbetsnod den **/hana/delade** volymen måste vara 2 TB stora. 
- En huvudnod och fem och åtta arbetsnoder, storleken på **/hana/delade** ska vara 4 TB. 
- En huvudnod och 9 och 12 arbetsnoder, en storlek på 6 TB för **/hana/delade** krävs. 
- En huvudnod och använder mellan 12 och 15 arbetsnoder, du måste tillhandahålla en **/hana/delade** volym som är 8 TB i storlek.

Andra viktiga design som visas i grafik för nod-konfigurationen för en skalbar SAP HANA virtuell dator är det virtuella nätverket eller bättre undernätskonfiguration. SAP rekommenderar starkt att en åtskillnad mellan klienter/program riktade trafiken från kommunikationen mellan noderna HANA. I bilderna visas det här målet uppnås genom att ha två olika virtuella nätverkskort kopplat till den virtuella datorn. Båda virtuella nätverkskort finns i olika undernät, har två olika IP-adresser. Du kan sedan styra trafikflödet med regler för routning med NSG: er eller användardefinierade vägar.

Särskilt i Azure finns det inga innebär och metoder för att tillämpa servicekvalitet och kvoter för specifika virtuella nätverkskort. Därmed öppnas uppdelning av klientprogram/kund- och kommunikationen mellan noderna kommunikation inte alla möjligheter att prioritera en trafikström över den andra. Uppdelning förblir i stället ett mått på säkerhet i avskärmning kommunikationen mellan noderna kommunikation av skalbara konfigurationer.  

>[!IMPORTANT]
>SAP rekommenderar starkt att separera nätverkstrafik till klienter/program-sida och kommunikationen mellan noderna trafik som beskrivs i det här dokumentet. Vi rekommenderar starkt att du därför sätta en arkitektur enligt senaste bilderna.
>

Från ett nätverk synsätt ser minsta nödvändiga nätverksarkitekturen ut som:

![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-networking-overview.PNG)

De gränser som stöds hittills är 15 worker ytterligare till en huvudnoden.

Från en storage synsätt ser lagringsarkitekturen ut som:


![Skala ut grunderna inom en enskild nod](media/hana-vm-operations/scale-out-storage-overview.PNG)

Den **/hana/delade** volymen finns på konfiguration för med hög tillgänglighet NFS-resursen. Medan alla andra enheter än systemenheter monteras lokalt ”till enskilda virtuella datorer. 

### <a name="highly-available-nfs-share"></a>Med hög tillgänglighet NFS-resurs
Det NFS-klustret med hög tillgänglighet samarbetar hittills med SUSE Linux endast. Dokumentet [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) beskriver hur du ställer in. Om du inte delar NFS-kluster med andra HANA konfigurationer utanför azure VNet som kör SAP HANA-instanser kan du installera den i samma virtuella nätverk. Installera den i ett eget undernät och se till att inte all skadlig trafik kan komma åt undernätet. I stället du vill begränsa trafik till det undernätet till IP-adresserna för den virtuella datorn som kör trafiken till **/hana/delade** volym.

Relaterade till det virtuella nätverkskortet för en virtuell dator skalbar HANA som ska vidarebefordra den **/hana/delade** trafik, är rekommendationen följande:

- Sedan trafiken till **/hana/delade** är begränsad, dirigerar den via det virtuella nätverkskortet som är tilldelad till klientnätverk i den lägsta konfigurationen
- Så småningom för trafik till **/hana/delade**, distribuera ett tredje undernät i det virtuella nätverket som du har distribuerat SAP HANA skalbar konfiguration och tilldela en tredje virtuellt nätverkskort som finns i det undernätet. Använd tredje virtuella nätverkskortet och associerade IP-adress för trafiken till NFS-resurs. Du kan sedan använda separata åtkomst och regler för routning.

>[!IMPORTANT]
>Nätverkstrafiken mellan de virtuella datorerna som har SAP HANA i en skalbar sätt distribueras och högtillgänglig NFS kan under inga omständigheter dirigeras via en [NVA](https://azure.microsoft.com/solutions/network-appliances/) eller liknande virtuella installationer. Medan Azure NSG: er finns inga sådana enheter. Kontrollera din routningsregler för att se till att nva: er eller liknande virtuella installationer detoured när åtkomst till med hög tillgänglighet NFS-resursen från de virtuella datorerna med SAP HANA.
> 

Om du vill dela med hög tillgänglighet NFS klustret mellan konfigurationer på SAP HANA kan du flytta dessa HANA-konfigurationer till samma virtuella nätverk. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Installera SAP HANA skalbara n Azure
Installera en skala ut SAP-konfiguration som du behöver utföra grov stegen för att:

- Distribuerar nya eller anpassa den nya virtuella Azure-nätverket infrastruktur
- Distribuera den nya hanterade virtuella datorer med Azure Premium Storage-volymer
- Distribuera en ny eller anpassa en befintlig NFS-kluster med hög tillgänglighet
- Anpassa nätverksroutning för att se till att, t.ex. kommunikationen mellan noderna kommunikation mellan virtuella datorer inte dirigeras via en [NVA](https://azure.microsoft.com/solutions/network-appliances/). Detsamma gäller för trafik mellan de virtuella datorerna och de NFS-klustret med hög tillgänglighet.
- Installera SAP HANA-huvudnoden.
- Anpassa konfigurationsparametrar för noden som SAP HANA
- Fortsätt med installationen av SAP HANA-arbetsnoder

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installationen av SAP HANA i skalbar konfiguration
När infrastrukturen för Azure virtuella datorer har distribuerats och alla andra förberedelser är klar kan behöver du installera konfigurationer för SAP HANA-skalbarhet i dessa steg:

- Installera SAP HANA huvudnoden enligt SAP: s dokumentationen
- **Efter installationen kan du behöva ändra filen global.ini och lägga till parametern ”basepath_shared = Nej” till global.ini**. Den här parametern gör att SAP HANA att köra i skalbar utan ”delade” **/hana/data** och **/hana/log** volymer mellan noderna. Information som finns dokumenterade i [SAP Obs! #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- När du har ändrat parametern global.ini SAP HANA-instansen startas om
- Lägg till ytterligare arbetsnoder. Se även <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Ange det interna nätverket för SAP HANA kommunikation mellan noder kommunikation under installationen eller efteråt använder, t.ex. lokala hdblcm. Mer dokumentation finns också [SAP Obs! #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Efter den här installationsrutinen skalbar konfigurationen som du har installerat kommer att använda icke-delade diskar för att köra **/hana/data** och **/hana/log**. Medan den **/hana/delade** volym placeras på den högtillgängliga NFS-resursens.
  


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
Om du har en plats-till-plats-anslutning mellan ditt lokala platser och Azure och du kör SAP-komponenter, sedan kör du förmodligen redan SAProuter. I det här fallet, Slutför följande objekt för fjärrsupport:

- Underhålla privata och statiska IP-adressen för den virtuella datorn som är värd för SAP HANA i SAProuter-konfigurationen.
- Konfigurera NSG för undernätet som är värd för HANA VM för att tillåta trafik via TCP/IP-port 3299.

Om du ansluter till Azure via internet och du inte har en SAP-router för den virtuella datorn med SAP HANA, måste du installera komponenten. Installera SAProuter i en separat virtuell dator i hanteringsundernätet. Följande bild visar en ungefärlig schemat för att distribuera SAP HANA utan en plats-till-plats-anslutning och med SAProuter:

![Omild distribution schemat för SAP HANA utan en plats-till-plats-anslutning och SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Glöm inte att installera SAProuter i en separat virtuell dator och inte i din Jumpbox VM. Den separata virtuella datorn måste ha en statisk IP-adress. Om du vill ansluta din SAProuter till SAProuter som är värd för SAP, kontakta SAP för en IP-adress. (SAProuter som är värd för SAP är motsvarigheten till SAProuter-instans som du installerar på den virtuella datorn.) Använd IP-adressen från SAP för att konfigurera din SAProuter-instans. I inställningarna för konfiguration är endast nödvändiga porten TCP-port 3299.

Mer information om hur du konfigurerar och underhåller fjärrsupport anslutningar via SAProuter finns i den [SAP-dokumentationen](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på interna virtuella Azure-datorer
Om du kör SUSE Linux 12 SP1 eller senare, kan du upprätta ett Pacemaker-kluster med STONITH enheter. Du kan använda enheterna som du ställer in en SAP HANA-konfiguration som använder synkron replikering med HANA System Replication och automatisk redundans. Mer information om installationsprocessen finns i [guide för hög tillgänglighet för SAP HANA för Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
