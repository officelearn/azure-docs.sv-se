---
title: Accelererat nätverk med Azure VM-katastrofåterställning | Microsoft Docs
description: Beskriver hur du aktiverar Accelererat nätverk med Azure Site Recovery för katastrofåterställning i Azure-dator
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: bb4a3f433f213984c4c351439760ddd3327cda73
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353144"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Accelererat nätverk med haveriberedskap i Azure-dator

Accelererat nätverk gör det möjligt för i/o-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden nätverkets prestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande arbetsbelastningarna på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery kan du utnyttja fördelarna med Accelererat nätverk för Azure-datorer som har redundansväxlats till en annan Azure-region. Den här artikeln beskrivs hur du kan aktivera Accelerated Networking för för Azure virtuella datorer som replikeras med Azure Site Recovery.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du kontrollera att du förstår:
-   Azure-dator [replikeringsarkitektur](azure-to-azure-architecture.md)
-   [Hur du konfigurerar replikering](azure-to-azure-tutorial-enable-replication.md) för Azure-datorer
-   [Redundansväxla](azure-to-azure-tutorial-failover-failback.md) Azure-datorer

## <a name="accelerated-networking-with-windows-vms"></a>Accelererat nätverk med virtuella Windows-datorer

Azure Site Recovery stöder aktivering av Accelererat nätverk för den replikerade virtuella datorer endast om den virtuella källdatorn har Accelererat nätverk aktiverat. Om din virtuella källdator inte har Accelererat nätverk aktiverat kan du lära dig hur du aktiverar Accelererat nätverk för Windows-datorer [här](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure-galleriet:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererat nätverk stöds i de flesta generella och beräkningsoptimerade instansstorlekar med minst 2 virtuella processorer.  Dessa alternativ som stöds är: D/DSv2 och F/Fs

På-instanser som har stöd för hypertrådning stöds Accelererat nätverk för VM-instanser med 4 eller fler virtuella processorer. Stöds-serier är: D/DSv3, E/ESv3, Fsv2 och Ms-/ Mms

Läs mer på VM-instanser, [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Accelererat nätverk med virtuella Linux-datorer

Azure Site Recovery stöder aktivering av Accelererat nätverk för den replikerade virtuella datorer endast om den virtuella källdatorn har Accelererat nätverk aktiverat. Om din virtuella källdator inte har Accelererat nätverk aktiverat kan du lära dig hur du aktiverar Accelererat nätverk för virtuella Linux-datorer [här](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure-galleriet:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian ”utvidga” med backportar kernel**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererat nätverk stöds i de flesta generella och beräkningsoptimerade instansstorlekar med minst 2 virtuella processorer.  Dessa alternativ som stöds är: D/DSv2 och F/Fs

På-instanser som har stöd för hypertrådning stöds Accelererat nätverk för VM-instanser med 4 eller fler virtuella processorer. Stöds-serier är: D/DSv3, E/ESv3, Fsv2 och Ms-/ Mms.

Läs mer på VM-instanser, [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Aktivering av Accelererat nätverk för replikerade virtuella datorer

När du [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md) för Azure-datorer, Site Recovery identifierar automatiskt om virtuella datorers nätverksgränssnitt har Accelererat nätverk aktiverat. Om Accelererat nätverk redan är aktiverad, konfigurerar Site Recovery automatiskt Accelererat nätverk i nätverksgränssnitt för den replikerade virtuella datorn.

Status för Accelererat nätverk kan verifieras den **nätverksgränssnitt** delen av den **beräkning och nätverk** inställningar för den replikerade virtuella datorn.

![Inställningen för accelererat nätverk](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Om du har aktiverat Accelererat nätverk på den virtuella källdatorn för när du har aktiverat replikering kan aktivera du Accelererat nätverk för den replikerade virtuella datorns nätverksgränssnitt av följande process:
1. Öppna **beräkning och nätverk** inställningar för den replikerade virtuella datorn
2. Klicka på namnet på nätverksgränssnittet under den **nätverksgränssnitt** avsnittet
3. Välj **aktiverad** i listrutan för Accelererat nätverk under den **Target** kolumn

![Aktivera Accelererat nätverk](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Ovanstående procedur bör också följas för befintliga replikerade virtuella datorer, som inte tidigare har Accelererat nätverk aktiverat automatiskt av Site Recovery.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [fördelarna med Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Mer information om begränsningar och begränsningarna för Accelererat nätverk för [Windows virtuella datorer](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) och [Linux-datorer](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans i programmet.
