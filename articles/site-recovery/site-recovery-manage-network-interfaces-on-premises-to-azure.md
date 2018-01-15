---
title: "Hantera nätverksgränssnitt i Azure Site Recovery för lokalt till Azure scenarier | Microsoft Docs"
description: "Beskriver hur du hanterar nätverksgränssnitt för lokalt till Azure scenarier med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: manayar
ms.openlocfilehash: 036d5c2945bd6730d65f88f72c9377047fefcde6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Hantera virtuella nätverksgränssnitt för lokalt till Azure scenarier

En virtuell dator (VM) i Azure måste ha minst ett nätverksgränssnitt som är kopplade till den. Det kan ha flera nätverksgränssnitt som kopplats till den som stöds för VM-storlek. 

Som standard definieras första nätverksgränssnittet kopplad till en virtuell Azure-dator som det primära nätverksgränssnittet. Alla andra nätverksgränssnitt på den virtuella datorn är sekundära nätverksgränssnitt. Som standard skickas även all utgående trafik från den virtuella datorn ut IP-adressen som har tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.

I en lokal miljö har virtuella datorer eller servrar flera nätverksgränssnitt för olika nätverk i miljön. Olika nätverk används vanligtvis för att utföra specifika åtgärder som uppgradering, underhåll och tillgång till internet. När du migrerar eller redundansväxlar till Azure från en lokal miljö, Kom ihåg att nätverksgränssnitt i samma virtuella dator måste alla vara ansluten till samma virtuella nätverk.

Som standard skapar Azure Site Recovery som många nätverksgränssnitt på en virtuell Azure-dator som är anslutna till den lokala servern. Du kan undvika att skapa redundanta nätverksgränssnitt under migrering eller redundans genom att redigera gränssnitt för nätverksinställningar enligt inställningarna för den replikerade virtuella datorn.

## <a name="select-the-target-network"></a>Välj målnätverket

Du kan ange det virtuella målnätverket för enskilda virtuella datorer för VMware och fysiska datorer och för Hyper-V (utan System Center Virtual Machine Manager) virtuella datorer. Hyper-V virtuella datorer som hanteras med Virtual Machine Manager, använder [nätverksmappning](site-recovery-network-mapping.md) mappa Virtuella datornätverk på en källserver för Virtual Machine Manager och Azure-målnätverken.

1. Under **replikerade objekt** Markera replikerade objekt att komma åt inställningarna för det replikerade objektet i Recovery Services-valvet.

2. Välj den **beräknings- och nätverksinställningar** att komma åt nätverksinställningarna för det replikerade objektet.

3. Under **egenskaper**, Välj ett virtuellt nätverk i listan över tillgängliga nätverksgränssnitt.

    ![Nätverksinställningar](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Om du ändrar målnätverket påverkar alla nätverksgränssnitt för den specifika virtuella datorn.

För Virtual Machine Manager-moln påverkar ändra nätverksmappningen alla virtuella datorer och deras nätverksgränssnitt.

## <a name="select-the-target-interface-type"></a>Välj Målet gränssnittstyp

Under den **nätverksgränssnitt** avsnitt i den **beräknings- och nätverksinställningar** fönstret kan du visa och redigera inställningar för nätverksgränssnitt. Du kan också ange gränssnittstyp för mål-nätverk.

- En **primära** nätverksgränssnitt krävs för redundans.
- Alla andra valda nätverksgränssnitt, om någon, är **sekundära** nätverksgränssnitt.
- Välj **inte använder** för att utesluta ett nätverksgränssnitt från början under växling vid fel.

Som standard när du aktivera replikering, väljs Site Recovery alla identifierade nätverksgränssnitt på den lokala servern. En som markeras **primära** och alla andra som **sekundära**. Alla efterföljande gränssnitt som lagts till på den lokala servern är markerad **inte använder** som standard. När du lägger till flera nätverksgränssnitt, kontrollera att rätt virtuell Azure-dator Målstorlek har valts för alla obligatoriska nätverksgränssnitt.

## <a name="modify-network-interface-settings"></a>Ändra inställningar för nätverksgränssnitt

Du kan ändra undernätet och IP-adress för ett replikerade objekt nätverksgränssnitt. Om en IP-adress har angetts, tilldelas Site Recovery nästa tillgängliga IP-adress från undernätet till nätverksgränssnittet under växling vid fel.

1. Välj alla tillgängliga nätverksgränssnitt och öppna gränssnitt för nätverksinställningar.

2. Välj önskad undernätet i listan över tillgängliga undernät.

3. Ange önskad IP-adress (vid behov).

    ![Inställningar för nätverksgränssnitt](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Välj **OK** är klar och gå tillbaka till den **beräknings- och nätverksinställningar** fönstret.

5. Upprepa steg 1 – 4 för andra nätverksgränssnitt.

6. Välj **spara** spara alla ändringar.

## <a name="next-steps"></a>Nästa steg
  [Lär dig mer](../virtual-network/virtual-network-network-interface-vm.md) om nätverksgränssnitt för virtuella Azure-datorer.
