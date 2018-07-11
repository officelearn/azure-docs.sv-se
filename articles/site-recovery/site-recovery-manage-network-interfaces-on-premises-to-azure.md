---
title: Hantera nätverksgränssnitt i Azure Site Recovery för lokal till Azure replikering | Microsoft Docs
description: Beskriver hur du hanterar nätverksgränssnitt för en lokal plats till Azure-replikering med Azure Site Recovery
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 75220d964f3e1208c85d34c1da97fab32044e62b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917116"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-replication"></a>Hantera virtuella datorers nätverksgränssnitt för en lokal plats till Azure replikering

En virtuell dator (VM) i Azure måste ha minst ett nätverksgränssnitt som är kopplat till den. Det kan ha många nätverksgränssnitt kopplade till den som stöd för VM-storlek.

Som standard definieras det första nätverksgränssnittet som är kopplade till en Azure virtuell dator som det primära nätverksgränssnittet. Alla andra nätverksgränssnitt på den virtuella datorn är sekundära nätverksgränssnitt. Som standard skickas all utgående trafik från den virtuella datorn också ut IP-adressen som tilldelas till den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.

I en lokal miljö, kan virtuella datorer eller servrar innehålla flera nätverksgränssnitt för olika nätverk i miljön. Olika nätverk används vanligtvis för att utföra specifika åtgärder, till exempel uppgraderingar, underhåll och åtkomst till internet. När du migrerar eller redundansväxla till Azure från en lokal miljö, Kom ihåg att nätverksgränssnitt i samma virtuella dator måste alla vara ansluten till samma virtuella nätverk.

Som standard skapar Azure Site Recovery som många nätverksgränssnitt på en Azure-dator som är anslutna till den lokala servern. Du slipper skapa redundanta nätverksgränssnitt under migrering eller redundans genom att redigera gränssnitt för nätverksinställningar under inställningar för den replikerade virtuella datorn.

## <a name="select-the-target-network"></a>Välj målnätverket

Du kan ange det virtuella målnätverket för enskilda virtuella datorer för VMware och fysiska datorer och för Hyper-V (utan System Center Virtual Machine Manager) virtuella datorer. Hyper-V virtuella datorer som hanteras med Virtual Machine Manager, använda [nätverksmappning](site-recovery-network-mapping.md) att mappa Virtuella datornätverk på en källserver för Virtual Machine Manager och Azure-målnätverken.

1. Under **replikerade objekt** i ett Recovery Services-valv väljer du alla replikerade objekt som ska komma åt inställningarna för det replikerade objektet.

2. Välj den **beräkning och nätverk** flik för att komma åt nätverksinställningarna som det replikerade objektet.

3. Under **Nätverksegenskaper**, Välj ett virtuellt nätverk i listan över tillgängliga nätverksgränssnitt.

    ![Nätverksinställningar](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Ändra målnätverket påverkar alla nätverksgränssnitt för den specifika virtuella datorn.

För Virtual Machine Manager-moln påverkar ändra nätverksmappningen alla virtuella datorer och deras nätverksgränssnitt.

## <a name="select-the-target-interface-type"></a>Välj måltyp för gränssnittet

Under den **nätverksgränssnitt** delen av den **beräkning och nätverk** fönstret kan du visa och redigera inställningar för nätverksgränssnittet. Du kan också ange typ av målnätverkskort.

- En **primära** nätverksgränssnitt måste anges för redundans.
- Alla andra valda nätverksgränssnitt, om sådant finns, är **sekundära** nätverksgränssnitt.
- Välj **inte använder** att utesluta ett nätverksgränssnitt från skapandet vid en redundansväxling.

Som standard när du aktiverar replikering, väljer Site Recovery alla identifierade nätverksgränssnitt på den lokala servern. En som markeras **primära** och alla andra som **sekundära**. Alla efterföljande gränssnitt som har lagts till på den lokala servern markeras **inte använder** som standard. När du lägger till fler nätverksgränssnitt måste du kontrollera att Målstorlek rätt Azure-dator har valts för alla nödvändiga nätverksgränssnitt.

## <a name="modify-network-interface-settings"></a>Ändra inställningar för nätverksgränssnittet

Du kan ändra undernätet och IP-adress för en replikerade objektet nätverksgränssnitt. Om en IP-adress har angetts, tilldelar Site Recovery nästa tillgängliga IP-adress från undernätet till nätverksgränssnittet vid en redundansväxling.

1. Välj ett nätverksgränssnitt som är tillgängliga att öppna inställningarna för gränssnittet.

2. Välj önskad undernätet i listan över tillgängliga undernät.

3. Ange önskad IP-adress (vid behov).

    ![Inställningar för nätverksgränssnittet](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Välj **OK** är färdig med redigeringarna och gå tillbaka till den **beräkning och nätverk** fönstret.

5. Upprepa steg 1 – 4 för andra nätverksgränssnitt.

6. Välj **spara** att spara alla ändringar.

## <a name="next-steps"></a>Nästa steg
  [Läs mer](../virtual-network/virtual-network-network-interface-vm.md) om nätverksgränssnitt för virtuella Azure-datorer.
