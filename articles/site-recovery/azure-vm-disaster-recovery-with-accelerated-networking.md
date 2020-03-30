---
title: Aktivera snabbare nätverk för azure vm-haveriberedskap med Azure Site Recovery
description: Beskriver hur du aktiverar snabbare nätverk med Azure Site Recovery för azure virtuell dator katastrofåterställning
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73622435"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Snabbare nätverk med azure virtuell dator katastrofåterställning

Accelererad nätverk möjliggör enkel rot-I/O-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt förbättrar dess nätverksprestanda. Den här banan med hög prestanda kringgår värden från datasökvägen, vilket minskar svarstiden, jitter- och CPU-användningen, för användning med de mest krävande nätverksarbetsbelastningarna på vm-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverkande:

![Jämförelse](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery kan du använda fördelarna med accelerated networking, för virtuella Azure-datorer som inte har överst till en annan Azure-region. I den här artikeln beskrivs hur du kan aktivera accelererat nätverk för virtuella Azure-datorer som replikeras med Azure Site Recovery.

## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du förstår:
-   Azure arkitektur för [virtuell datorreplikering](azure-to-azure-architecture.md)
-   [Konfigurera replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer
-   [Misslyckas över](azure-to-azure-tutorial-failover-failback.md) Virtuella Azure-datorer

## <a name="accelerated-networking-with-windows-vms"></a>Accelererade nätverk med virtuella Windows-datorer

Azure Site Recovery stöder aktivera accelererat nätverk för replikerade virtuella datorer endast om källan virtuell dator har accelererat nätverk aktiverat. Om den virtuella datorn för källan inte har aktiverat Accelerated Networking kan du läsa om hur du aktiverar Virtuella datorer med accelererade nätverk för Windows [här](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure Gallery:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererad nätverk stöds på de flesta allmänna ändamål och beräkningsoptimerade instansstorlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/Fs

På instanser som stöder hypertrådning stöds accelererad nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien stöds är: D/DSv3, E/ESv3, Fsv2 och Ms/Mms

Mer information om VM-instanser finns i [Storlekarna för Virtuella datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i Windows.

## <a name="accelerated-networking-with-linux-vms"></a>Accelererade nätverk med virtuella Linux-datorer

Azure Site Recovery stöder aktivera accelererat nätverk för replikerade virtuella datorer endast om källan virtuell dator har accelererat nätverk aktiverat. Om din virtuella dator inte har aktiverat Accelerated Networking kan du läsa om hur du aktiverar Accelerated Networking för virtuella Linux-datorer [här](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure Gallery:
* **Ubuntu 16,04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" med backports kärna**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererad nätverk stöds på de flesta allmänna ändamål och beräkningsoptimerade instansstorlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/Fs

På instanser som stöder hypertrådning stöds accelererad nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien stöds är: D/DSv3, E/ESv3, Fsv2 och Ms/Mms.

Mer information om VM-instanser finns i [Storlekar på virtuella linux-datorer](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Aktivera accelererat nätverk för replikerade virtuella datorer

När du [aktiverar replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer identifierar Site Recovery automatiskt om nätverksgränssnitten för den virtuella datorn har aktiverat Accelererat nätverk. Om Accelererat nätverk redan är aktiverat konfigureras Accelererat nätverk automatiskt i nätverksgränssnitten för den replikerade virtuella datorn.

Statusen för Accelererat nätverk kan **verifieras** under avsnittet Nätverksgränssnitt i **beräknings- och nätverksinställningarna** för den replikerade virtuella datorn.

![Snabbare nätverksinställning](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Om du har aktiverat Accelererat nätverk på den virtuella källdatorn efter att replikering aktiverat kan du aktivera Accelererat nätverk för den replikerade virtuella datorns nätverksgränssnitt genom följande process:
1. Öppna **beräknings- och nätverksinställningar** för den replikerade virtuella datorn
2. Klicka på namnet på nätverksgränssnittet under avsnittet **Nätverksgränssnitt**
3. Välj **Aktiverad** i listrutan för accelererat nätverk under kolumnen **Mål**

![Aktivera accelererat nätverk](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Ovanstående process bör också följas för befintliga replikerade virtuella datorer, som inte tidigare har accelererat nätverk aktiverat automatiskt av Site Recovery.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [fördelarna med accelererat nätverkande](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Läs mer om begränsningar och begränsningar för accelererade nätverk för [virtuella Windows-datorer](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) och [virtuella Linux-datorer](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) för att automatisera programundans.
