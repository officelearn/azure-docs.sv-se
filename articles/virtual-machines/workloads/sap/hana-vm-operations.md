---
title: "SAP HANA-åtgärder på Azure | Microsoft Docs"
description: "Handboken för SAP HANA-system som distribueras på virtuella Azure-datorer."
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
ms.openlocfilehash: e8ddfd5e2ee57d79fecacdc648af9264b6c95240
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA på Azure-handboken
Detta dokument ger vägledning för operativsystem SAP HANA som distribueras på Azure interna virtuella maskiner (VMs). Det här dokumentet är inte avsedd att ersätta standard SAP-dokumentationen som innehåller följande innehåll:

- [Guide för SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guider för SAP-installation](https://service.sap.com/instguides)
- [SAP anteckningar](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Krav
Om du vill använda den här guiden behöver du grundläggande kunskaper om Azure följande komponenter:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella nätverk](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Mer information om SAP NetWeaver och andra SAP-komponenter på Azure finns i [SAP på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) avsnitt i den [dokumentation för Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Överväganden för grundläggande konfiguration
I följande avsnitt beskrivs grundinställning överväganden för distribution av SAP HANA-system på virtuella Azure-datorer.

### <a name="connect-into-azure-virtual-machines"></a>Ansluta till virtuella Azure-datorer
Enligt beskrivningen i den [virtuella Azure-datorer Planeringshandbok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), det finns två grundläggande metoder för att ansluta till virtuella Azure-datorer:

- Anslut via internet och offentliga slutpunkter på en hoppa virtuell dator eller på den virtuella datorn som kör SAP HANA.
- Ansluta till en [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Plats-till-plats-anslutning via VPN eller ExpressRoute är nödvändigt för produktion scenarier. Den här typen av anslutning krävs också för icke-produktion scenarier som matas in produktion scenarier där SAP-program används. Följande bild visar ett exempel på cross-plats-anslutning:

![Cross-plats-anslutning](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Välj Virtuella Azure-typer
De Virtuella Azure-typer som kan användas för produktion scenarier visas i den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För icke-produktion scenarier finns en mängd olika interna Virtuella Azure-typer.

>[!NOTE]
>Icke-produktion scenarier använder VM-typer som anges i den [SAP-kommentar #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Distribuera virtuella datorer i Azure med hjälp av:

- Azure-portalen.
- Azure PowerShell-cmdlets.
- Azure CLI.

Du kan också distribuera en fullständig installerade SAP HANA-plattform på Virtuella Azure-tjänster genom den [SAP molnplattform](https://cal.sap.com/). Installationsprocessen beskrivs i [distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choose-azure-storage-type"></a>Välj typ av Azure Storage
Azure tillhandahåller två typer av lagring som är lämpliga för virtuella Azure-datorer som kör SAP HANA:

- [Azure standardlagring](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium-lagring](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure erbjuder två metoder för distribution för virtuella hårddiskar på Azure-Standard och Premium-lagring. Om det övergripande scenariot tillåter kan dra nytta av [Azure hanteras disk](https://azure.microsoft.com/services/managed-disks/) distributioner.

En lista över lagringstyper och deras serviceavtal, granska den [Azure-dokumentationen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

Azure Premium-diskar som rekommenderas för /hana/data och /hana/log volymer. Du kan skapa ett LVM RAID över flera diskar i Premium-lagring och använder RAID-volymer som /hana/data och /hana/log volymer.

I följande tabell visas en konfiguration av VM-typer som kunder använder ofta till värd SAP HANA på virtuella Azure-datorer:

| VM-SKU | RAM | / hana/data och loggfilen/hana /<br /> stripe-volymer med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap | Hana eller säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="set-up-azure-virtual-networks"></a>Konfigurera virtuella Azure-nätverk
När du har en plats-till-plats-anslutning till Azure via VPN eller ExpressRoute, måste du ha minst en [virtuella Azure-nätverket](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som är anslutna via en virtuell Gateway till VPN eller ExpressRoute-krets. Den virtuella gatewayen bor i ett undernät som virtuella Azure-nätverket. Om du vill installera SAP HANA, kan du skapa två ytterligare undernät i det virtuella nätverket. Ett undernät är värd för virtuella datorer om du vill köra SAP HANA-instanser. Det andra undernätet körs Jumpbox eller hantering av virtuella datorer som värd för SAP HANA Studio eller annan programvara för hantering.

När du installerar de virtuella datorerna till kör SAP HANA måste de virtuella datorerna:

- Två virtuella nätverkskort som är installerade: en nätverkskort för att ansluta till undernätet för hantering och ett nätverkskort för att ansluta från det lokala nätverket eller andra nätverk till SAP HANA-instansen i Azure-VM.
- Statisk privat IP-adresser som har distribuerats för både virtuella nätverkskort.

En översikt över de olika metoderna för att tilldela IP-adresser, se [IP-adressen typer och fördelningsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

[Azure Nätverkssäkerhetsgrupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) används för att dirigera trafik dirigeras till SAP HANA-instans eller Jumpbox. De NSG: er är kopplade till SAP HANA-undernät och Management-undernät.

Följande bild visar en översikt över en grov distribution schemat för SAP HANA:

![Grov distribution schemat för SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Åtkomst till SAP HANA-instans för att distribuera SAP HANA i Azure utan en plats-till-plats-anslutning, även om en offentlig IP-adress. IP-adressen måste tilldelas den virtuella Azure-datorn som kör din Jumpbox VM. I det här grundläggande scenariot distributionen förlitar sig på Azure inbyggda DNS-tjänster för att matcha värdnamn. Azure inbyggda DNS-tjänster är särskilt viktigt i en mer komplex distribution där offentliga IP-adresser används. Använd Azure NSG: er för att begränsa öppna portar eller IP-adressintervall som kan ansluta till Azure-undernät med tillgångar som har offentliga IP-adresser. Följande bild visar ett grov schema för att distribuera SAP HANA utan en plats-till-plats-anslutning:
  
![Grov distribution schemat för SAP HANA utan en plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Åtgärder för att distribuera SAP HANA på Azure Virtual Machines
I följande avsnitt beskrivs några av de åtgärder som rör distribution av SAP HANA-system på virtuella Azure-datorer.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Säkerhetskopiera och återställa åtgärder på virtuella Azure-datorer
Följande dokument beskriver hur du säkerhetskopierar och återställer SAP HANA-distribution:

- [Översikt över SAP HANA-säkerhetskopiering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA filnivå säkerhetskopiering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA lagring ögonblicksbild prestandamått](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Och starta om virtuella datorer som innehåller SAP HANA
Ett framträdande funktion i Azure offentligt moln är att du är debiteras endast för din databehandling minuter. Till exempel när du stänger en virtuell dator som kör SAP HANA är du debiteras endast för kostnader för lagring under den tiden. En annan funktion är tillgänglig när du anger statiska IP-adresser för dina virtuella datorer i din första distribution. När du startar om en virtuell dator som har SAP HANA startar om den virtuella datorn med dess tidigare IP-adresser. 


### <a name="use-saprouter-for-sap-remote-support"></a>Använd SAProuter för SAP fjärrsupport
Om du har en plats-till-plats-anslutning mellan lokala platser och Azure och du kör SAP-komponenter, du förmodligen redan köra SAProuter. I det här fallet utför du följande objekt för fjärrsupport:

- Underhålla privata och statiska IP-adressen för den virtuella datorn som är värd för SAP HANA i SAProuter-konfigurationen.
- Konfigurera NSG för undernätet som är värd för HANA VM för att tillåta trafik via TCP/IP-port 3299.

Om du vill ansluta till Azure via internet, och du inte har en SAP-router för den virtuella datorn med SAP HANA, måste du installera komponenten. Installera SAProuter i en separat virtuell dator i Management-undernät. Följande bild visar ett grov schema för att distribuera SAP HANA utan en plats-till-plats-anslutning och med SAProuter:

![Omild distribution schemat för SAP HANA utan en plats-till-plats-anslutning och SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Se till att installera SAProuter i en separat virtuell dator och inte i din Jumpbox VM. Separata VM måste ha en statisk IP-adress. Om du vill ansluta din SAProuter till SAProuter som finns i SAP, kontakta SAP för en IP-adress. (SAProuter som finns i SAP är motsvarigheten till SAProuter-instans som du installerar på den virtuella datorn.) Använd IP-adress från SAP för att konfigurera din SAProuter-instans. I konfigurationsinställningarna är endast nödvändiga porten TCP-port 3299.

Mer information om hur du ställer in och underhålla fjärrsupport anslutningar via SAProuter finns i [SAP dokumentationen](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på interna virtuella Azure-datorer
Om du kör SUSE Linux 12 SP1 eller senare, kan du upprätta ett Pacemaker kluster med STONITH enheter. Du kan använda enheterna som du ställer in en SAP HANA-konfiguration som använder synkron replikering med HANA System replikering och automatisk redundans. Mer information om installationen finns [hög tillgänglighet för SAP HANA på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).
