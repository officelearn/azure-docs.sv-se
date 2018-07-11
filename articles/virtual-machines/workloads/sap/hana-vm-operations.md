---
title: SAP HANA-åtgärder på Azure | Microsoft Docs
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
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2480ad464f2fc716cf68672387a189aeb92f5737
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918840"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA på Azure handboken
Det här dokumentet innehåller information om operativsystem i SAP HANA-system som har distribuerats på Azures inbyggda virtuella datorer (VM). Det här dokumentet är inte avsedd att ersätta standard SAP-dokumentationen, som innehåller följande innehåll:

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
> För icke-produktionsscenarion, använder du VM-typer som anges i den [SAP-kommentar #1928533](https://launchpad.support.sap.com/#/notes/1928533). För användning av virtuella Azure-datorer för produktionsscenarier, kontrollera för SAP HANA-certifierade virtuella datorer i SAP publicerade [listan över certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

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

I den mån det du har köpt SAP HANA-enheterna för den lokala hade du aldrig bryr dig om i/o-undersystem och dess funktioner eftersom leverantören av enheten behöver se till att de minsta krav är uppfyllda för SAP HANA. När du skapar Azure-infrastrukturen själv, bör du också vara medveten om några av dessa krav att också förstå de konfigurationskrav som vi föreslår att du i följande avsnitt. Eller om du vill köra SAP HANA på i de fall där du konfigurerar de virtuella datorerna. Några av de egenskaper som uppmanas vilket resulterar i att behöva:

- Aktivera skrivning/volym på /hana/log en 250 MB/sek som minimum med 1 MB i/o-storlekar
- Aktivera Läs aktiviteten hos minst 400MB/sekund för /hana/data för 16 MB och 64 MB i/o-storlekar
- Aktivera skrivningsaktiviteter minst 250MB/sek för /hana/data med 16 MB och 64 MB i/o-storlekar

Svarstiden är viktigt för DBMS system, beroende på den låga storage även när de som SAP HANA, behålla data i minnet. Den kritiska vägen i storage är vanligtvis runt transaction log skrivningar av DBMS system. Men även operations skriva lagringspunkter eller läser in data i minnet när kraschåterställning kan vara avgörande. Därför är det obligatoriskt att utnyttja Azure Premium-diskar för /hana/data och /hana/log volymer. Få minsta dataflöde/hana/loggarna och/hana/data efter behov av SAP, du behöver för att skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar och använda RAID-volymer som/hana/data och loggvolymer/hana /. Eftersom stripe-storlekar för RAID 0 rekommendationen är att använda:

- 64 kB eller 128K för/hana/data
- 32K för/hana/logg

> [!NOTE]
> Du behöver inte konfigurera någon redundansnivå med RAID-volymer eftersom Azure Premium och Standard-lagring att tre avbildningar av en virtuell Hårddisk. Användningen av en RAID-volym är helt och hållet att konfigurera volymer som ger tillräcklig i/o-dataflöde.

Kan sparas ett antal virtuella Azure-hårddiskar under en RAID är ackumulerande från en sida för IOPS och lagring dataflöde. Så om du placerar en RAID 0 över 3 x P30 Azure Premium Storage-diskar, ger den dig tre gånger IOPS och tre gånger kapaciteten för lagring för en enskild Azure Premium Storage P30-disk.

Cachelagring rekommendationerna nedan förutsatt att i/o-egenskaper för SAP HANA listan som:

- Det finns nästan alla skrivskyddade arbetsbelastningar mot HANA datafiler. Undantagen är stora storlekar I/o efter omstart av HANA-instans eller starta om datorn för den virtuella Azure-datorn när data läses in i HANA. Ett annat fall av större läsa I/o mot datafiler kan vara HANA säkerhetskopior av databasen. Därmed måste Läs cachelagring huvudsakligen ingen visas meningsfull sedan i de flesta fall, alla datavolymer i filen läsas helt.
- Skriva mot datafilerna som uppstår i toppar baserat av HANA lagringspunkter och HANA kraschåterställning. Skriva lagringspunkter är asynkron och innehar inte upp alla användartransaktioner. Skrivdata under kraschåterställning är prestanda som är viktiga för att få systemet svarar snabbt igen. Kraschåterställning bör dock i stället undantagsfall
- Det är nästan alla läsningar från HANA gör om filerna. Undantagen är stora I/o när du genomför säkerhetskopieringar av transaktionsloggen kan kraschåterställning, eller i fasen omstart av en HANA-instans.  
- Största belastningen mot SAP HANA gör om loggfilen är skrivningar. Beroende på vilken typ av arbetsbelastning, kan det ha I/o som är så litet som 4 KB eller i andra fall i/o-storlekar på 1 MB eller mer. Skriva svarstid mot SAP HANA gör om loggen är prestanda som är viktiga.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

Till följd av dessa observerade i/o-mönster av SAP HANA anges cachelagring för olika volymer som använder Azure Premium Storage som:

- / hana/data – ingen cachelagring
- / hana/log - ingen cachelagring - undantag för M-serien (se senare i det här dokumentet)
- /Hana/Shared - läsa cachelagring


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


Diskarna rekommenderas för mindre VM-typer med 3 x P20 oversize volymer om utrymme rekommendationerna enligt den [SAP TDI Storage White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men gjordes valet som visas i tabellen för att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du behöver ändringar i den /hana/backup volymen, som har anpassat för att behålla säkerhetskopior som representerar två gånger minne volymen kan passa på att justera.   
Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan behöva du öka antalet Azure Premium Storage virtuella hårddiskar. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 

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


Diskarna rekommenderas för mindre VM-typer med 3 x P20 oversize volymer om utrymme rekommendationerna enligt den [SAP TDI Storage White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men gjordes valet som visas i tabellen för att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du behöver ändringar i den /hana/backup volymen, som har anpassat för att behålla säkerhetskopior som representerar två gånger minne volymen kan passa på att justera.  
Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan behöva du öka antalet Azure Premium Storage virtuella hårddiskar. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lagringslösning med Azure Write Accelerator för virtuella datorer i Azure M-serien
Azure Write Accelerator är en funktion som hämtar distribueras för virtuella datorer i M-serien exklusivt. Som namnet tillstånd, är syftet med funktionen för att förbättra i/o-svarstiden för skrivningar Azure Premium Storage. För SAP HANA ska Write Accelerator användas mot den /hana/log volymen. Därför måste de konfigurationer som visas så här långt ändras. Den huvudsakliga ändringen är bruten mellan /hana/data och /hana/log för att kunna använda Azure Write Accelerator mot den /hana/log volymen. 

> [!IMPORTANT]
> SAP HANA-certifiering för virtuella datorer i Azure M-serien är enbart med Azure Write Accelerator för /hana/log volymen. Därför förväntas Produktionsdistribution scenariot SAP HANA på virtuella datorer i Azure M-serien vara konfigurerad med Azure Write Accelerator för /hana/log volymen.  

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

Kontrollera om genomflödet för olika föreslagna volymer som passar den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver större volymer för /hana/data och /hana/log kan behöva du öka antalet Azure Premium Storage virtuella hårddiskar. Ändra storlek på en volym med flera virtuella hårddiskar än den ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator.

Azure Write Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör /hana/log volymen måste distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage virtuella hårddiskar per virtuell dator som stöds av Azure Write Accelerator. De aktuella gränserna är:

- 16 virtuella hårddiskar för ett M128xx VM
- 8 virtuella hårddiskar för ett M64xx VM
- 4 virtuella hårddiskar för ett M32xx VM

Mer detaljerad information om hur du aktiverar Azure Write Accelerator finns i artikeln [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Write Accelerator finns i samma-dokumentationen.


### <a name="set-up-azure-virtual-networks"></a>Konfigurera Azure-nätverk
När du har en plats-till-plats-anslutning till Azure via VPN eller ExpressRoute, måste du ha minst en [Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som är anslutna via en virtuell Gateway till VPN eller ExpressRoute-kretsen. Den virtuella gatewayen finns i ett undernät i virtuella Azure-nätverket. Om du vill installera SAP HANA kan du skapa två ytterligare undernät i det virtuella nätverket. Ett undernät är värd för de virtuella datorerna för att köra SAP HANA-instanser. Andra undernätet kör Jumpbox eller hantering av virtuella datorer som värd för SAP HANA-Studio eller annan programvara för hantering.

När du installerar de virtuella datorerna som kör SAP HANA, måste de virtuella datorerna:

- Två virtuella nätverkskort som är installerade: ett nätverkskort för att ansluta till undernätet för hantering och ett nätverkskort för att ansluta från det lokala nätverket eller andra nätverk, till SAP HANA-instans i Azure-VM.
- Statiska privata IP-adresser som har distribuerats för båda virtuella nätverkskort.

En översikt över de olika metoderna för att tilldela IP-adresser, se [IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

För virtuella datorer som kör SAP HANA, bör du arbeta med statiska IP-adresser som är tilldelade. Anledningen är att vissa configuration-attribut för HANA hänvisar till IP-adresser.

[Azure Nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) används för att dirigera trafik som dirigeras till SAP HANA-instans eller Jumpbox. Nätverkssäkerhetsgrupper är kopplade till SAP HANA-undernät och hanteringsundernätet.

Följande bild visar en översikt över en ungefärlig distribution-schemat för SAP HANA:

![Ungefärlig distribution schemat för SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Åtkomst till SAP HANA-instans för att distribuera SAP HANA i Azure utan en plats-till-plats-anslutning, även om en offentlig IP-adress. IP-adressen måste tilldelas Azure-datorn som kör din Jumpbox VM. I det här grundläggande scenariot distributionen förlitar sig på Azure inbyggd DNS-tjänster för att matcha värdnamn. Azure inbyggd DNS-tjänster är särskilt viktigt i en mer komplex distribution där offentliga IP-adresser används. Använd Azure NSG: er för att begränsa öppna portar eller IP-adressintervall som kan ansluta till Azure-undernät med tillgångar som har offentliga IP-adresser. Följande bild visar en ungefärlig schemat för att distribuera SAP HANA utan en plats-till-plats-anslutning:
  
![Ungefärlig distribution schemat för SAP HANA utan en plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking2.PNG)
 


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
