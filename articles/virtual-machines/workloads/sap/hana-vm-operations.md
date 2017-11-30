---
title: "SAP HANA-åtgärder på Azure | Microsoft Docs"
description: "Åtgärder för SAP HANA på interna virtuella Azure-datorer"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA på Azure-handboken
Den här guiden innehåller anvisningar för operativsystem SAP HANA som har distribuerats på Azure Virtual Machines. Det här dokumentet är inte avsedd att ersätta någon standard SAP-dokumentation. Kommentarer och SAP-guider finns på följande platser:

- [SAP Administrationsguide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guider för SAP-Installation](https://service.sap.com/instguides)
- [SAP-kommentar](https://sservice.sap.com/notes)

Förutsättning är att du har grundläggande kunskaper om de olika Azure komponenterna i:

- [Virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella nätverk](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Ytterligare dokumentation om SAP NetWeaver och andra SAP-komponenter på Azure finns i den [SAP på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) avsnitt i den [dokumentation för Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Överväganden för grundläggande konfiguration
### <a name="connecting-into-azure"></a>Ansluta till Azure
Enligt beskrivningen i [Azure Virtual Machines planering och implementering för SAP NetWeaver] [-Planeringsguiden], det finns två grundläggande metoder för att ansluta till virtuella datorer i Azure. 

- Ansluter via Internet och offentliga slutpunkter på en hoppa virtuell dator eller den virtuella datorn kör SAP HANA
- Ansluter via en [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

För produktion scenarier eller scenarier där icke-produktion scenarier som matas in produktion scenarier tillsammans med SAP-program, behöver du en plats-till-plats-anslutning via VPN eller ExpressRoute som visas i följande bild:

![Cross-plats-anslutning](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Valet av Virtuella Azure-typer
Azure VM-typer som kan användas för produktion scenarier kan slås upp [här](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Du kan välja en mängd olika interna virtuella Azure-datorer för icke-produktion scenarier. Du bör dock begränsa själv till VM-typer som [SAP Obs #1928533](https://launchpad.support.sap.com/#/notes/1928533). Distribution av dessa virtuella datorer i Azure kan utföras via:

- Azure Portal
- Azure Powershell-Cmdlets
- Azure CLI

Du kan också distribuera hela installerade SAP HANA-plattformen på Azure Virtual machine-tjänster genom [SAP molnplattform](https://cal.sap.com/) enligt [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Valet av Azure storage
Azure tillhandahåller två huvudsakliga lagringstyper som är lämplig för virtuella Azure-datorer som kör SAP HANA

- [Azure standardlagring](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure erbjuder två metoder för distribution för virtuella hårddiskar på Azure-Standard och Premium-lagring. Om det övergripande scenariot tillåter det rekommenderas att utnyttja [Azure hanteras Disk](https://azure.microsoft.com/services/managed-disks/) distributioner.

Exakt lagringstyper och servicenivåavtal runt de lagringstyper [denna dokumentation](https://azure.microsoft.com/pricing/details/managed-disks/)

Det rekommenderas att använda Azure Premiumdiskar för /hana/data och /hana/log volymer. Det går att skapa ett LVM RAID över flera diskar i Premium-lagring och använda dessa RAID-volymer som /hana/data och /hana/log volymer.

En möjlig konfiguration för olika vanliga VM-typer som kunder använder hittills som värd för SAP HANA på virtuella Azure-datorer kan se ut så:

| VM-SKU | RAM | / hana/data och loggfilen/hana /<br /> stripe-volymer med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap | Hana eller säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Azure-nätverk
Under förutsättning att du har en VPN eller ExpressRoute plats-till-plats-anslutning till Azure, har du åtminstone en [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som är anslutna via en virtuell Gateway till VPN eller ExpressRoute-krets. Den virtuella gatewayen bor i ett undernät i Azure Vnet. För att kunna installera HANA, skulle du skapa en annan två undernät i VNet. Ett undernät som värdar på virtuella datorer som kör SAP HANA-instanser och ett annat undernät som kör eventuell Jumpbox eller hantering av virtuella datorer som kan vara värd för SAP HANA Studio eller annan programvara för hantering.
När du installerar de virtuella datorer som ska köras HANA ska de virtuella datorerna ha:

- Två virtuella nätverkskort installerat som en ansluter till undernätet för hantering och ett nätverkskort används för att ansluta från antingen på lokala eller andra nätverk till SAP HANA-instansen i Azure-VM.
- Statisk privat IP-adresser som distribueras för båda vNICs

En översikt över de olika möjligheterna av IP-adresstilldelning hittar [här](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Trafikroutning till direkt till SAP HANA-instans eller till jumpbox dirigeras av [Azure Nätverkssäkerhetsgrupper](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) som är kopplade till HANA-undernät och Management-undernät.

Övergripande ser schemat grov distribution ut som:

![Grov distribution schemat för SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Om du bara distribuera SAP HANA i Azure utan en plats-till-plats (VPN eller ExpressRoute i Azure), skulle du åtkomst till SAP HANA-instans även om en offentlig IP-adress som tilldelats den virtuella Azure-datorn som kör Jumpbox-VM. I fallet med enkla måste du också förlitar sig på Azure inbyggda DNS-tjänster för att matcha värdnamn. Särskilt när du använder offentliga Internetriktade IP-adresser, som du vill använda säkerhetsgrupper för Azure-nätverk för att begränsa öppna portar eller IP-adressintervall som tillåts att ansluta till Azure-undernät med tillgångar med Internetriktade offentliga IP-adresser. Schemat för typen av distribution kan se ut så:
  
![Grov distribution schemat för SAP HANA utan plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Åtgärder
### <a name="backup-and-restore-operations-on-azure-vms"></a>Säkerhetskopiering och återställning på Azure Virtual Machines
Möjligheter för SAP HANA säkerhetskopiering och återställning finns dokumenterade i dessa dokument:

- [SAP HANA säkerhetskopiering: översikt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Säkerhetskopiering av SAP HANA-filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA lagring ögonblicksbild prestandamått](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Start- och omstart av virtuella datorer som innehåller SAP HANA
En av fördelarna med offentliga Azure-molnet är det faktum att endast debiteras du för beräknings-minuter du utgifter. Det innebär att om du stänger av en virtuell dator med SAP HANA körs i den debiteras endast kostnader för lagring under den tiden. När du startar den virtuella datorn med SAP HANA i den igen och den virtuella datorn ska börja igen och ska ha samma IP-adresser (om du har distribuerat med statiska IP-adresser). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter aktiverar SAP fjärrsupport
Om du har en plats-till-plats-anslutning mellan din lokala adress(er) och Azure och du kör SAP komponenter redan är det mycket troligt att du redan kör SAProuter redan. Då finns det inget måste du göra med SAP HANA-instanser som du distribuerar i Azure. Förutom för att underhålla privata och statiska IP-adressen för den virtuella datorn som är värd för HANA i SAPRouter konfigurationen och har NSG för undernätet värd anpassas HANA VM (trafik genom porten TCP/IP-port 3299 tillåtna).

Om du distribuerar SAP HANA och du ansluter till Azure via Internet och du inte har en installerad i virtuella nätverk som kör en virtuell dator med SAP HANA SAP-Router, bör du installera SAPRouter i en separat virtuell dator i Management-undernät som visas här :


![Grov distribution schemat för SAP HANA utan plats-till-plats-anslutning och SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Du bör installera SAPRouter i en separat virtuell dator och inte i din Jumpbox VM. Separata VM behöver en statisk IP-adress. För att ansluta SAPRouter till SAPRouter som är värd för SAP (motsvarighet för SAPRouter-instans som du installerar VM), måste du kontakta SAP för att få en IP-adress från SAP måste du konfigurera din SAPRouter-instans. Endast nödvändiga porten är TCP-port 3299.
Mer information om hur du skapar och underhåller fjärrsupport anslutningar via SAPRouter markerad [SAP källa](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på interna virtuella Azure-datorer
Kör SUSE Linux 12 SP1 och senare kan du skapa ett Pacemaker kluster med STONITH enheter att ställa in en SAP HANA-konfiguration som använder synkron replikering med HANA System replikering och automatisk redundans. Proceduren för installationen beskrivs i artikeln [hög tillgänglighet för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










