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
ms.openlocfilehash: b60c746ae6c243d2b448056f8768df3baaed4cc1
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2017
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Hantera virtuella nätverksgränssnitt för lokalt till Azure scenarier

En virtuell dator i Azure måste ha minst ett nätverksgränssnitt som är kopplade till den och kan ha så många nätverksgränssnitt anslutna till den som stöd för VM-storlek. Som standard definieras första nätverksgränssnittet kopplad till en virtuell Azure-dator som det primära nätverksgränssnittet. Alla andra nätverksgränssnitt på den virtuella datorn är sekundära nätverksgränssnitt. Som standard skickas all utgående trafik från virtuella IP-adressen till den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.

I en lokal miljö har virtuella datorer /-servrar flera nätverksgränssnitt för olika nätverk i miljön. Olika nätverk används vanligtvis för att utföra specifika åtgärder, till exempel uppgraderingar, underhållet, Internetåtkomst och så vidare. När du migrerar eller redundansväxlar till Azure från en lokal miljö, Kom ihåg att nätverksgränssnitt i samma virtuella dator måste alla vara ansluten till samma virtuella nätverk.

Som standard skapar Site Recovery som många nätverksgränssnitt på en virtuell Azure-dator som är anslutna till den lokala servern. Du kan undvika att skapa redundanta nätverksgränssnitt under migrering eller redundans genom att redigera gränssnitt för nätverksinställningar under inställningar för den replikerade virtuella datorn.

## <a name="select-the-target-network"></a>Välj målnätverket

Du kan ange det virtuella målnätverket för enskilda virtuella datorer för VMware och fysiska datorer och för Hyper-V (utan VMM) virtuella datorer. För Hyper-V virtuella datorer som hanteras med VMM [nätverksmappning](site-recovery-network-mapping.md) används för att mappa Virtuella datornätverk på en VMM-källservern och Azure-målnätverken.

1. Under ”replikerade objekt” i ett Recovery Services-valv, klickar du på ett replikerade objekt att komma åt inställningarna för det replikerade objektet.

2. Klicka på fliken ”Beräkning och nätverk” för att få åtkomst till nätverksinställningar för det replikerade objektet.

3. Välj ett virtuellt nätverk under Nätverksegenskaper från listan över tillgängliga nätverksgränssnitt.

    ![Beräkning och nätverk](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Om du ändrar målnätverket påverkar alla nätverksgränssnitt för den specifika virtuella datorn.

För VMM-moln påverkar om du ändrar nätverksmappningen alla virtuella datorer och deras nätverksgränssnitt.

## <a name="select-the-target-interface-type"></a>Välj Målet gränssnittstyp

Under avsnittet 'Nätverksgränssnitt' i 'beräknings- och nätverksinställningarna-bladet, visa och redigera inställningar för nätverksgränssnitt och ange gränssnittstyp för mål-nätverk.

- En **primära** nätverksgränssnitt krävs för redundans.
- Alla andra valda nätverksgränssnitt, om någon, är **sekundära** nätverksgränssnitt.
- Välj **inte använder** för att utesluta ett nätverksgränssnitt från början under växling vid fel.

Som standard när du aktiverar replikering, Site Recovery väljer identifierade alla nätverkskort på den lokala servern, markera en som ”primär”, och alla andra som ”sekundär” nätverksgränssnitt. Alla efterföljande gränssnitt som lagts till på den lokala servern är markerad, Använd inte ”som standard. När du lägger till flera nätverksgränssnitt, kan du kontrollera att Målstorlek rätt virtuella Azure-datorn är markerad för alla obligatoriska nätverksgränssnitt.

## <a name="modifying-network-interface-settings"></a>Ändra inställningar för nätverksgränssnitt

Du kan ändra undernätet och IP för ett replikerade objekt nätverksgränssnitt. Om en IP-adress har angetts, tilldelas Site Recovery nästa tillgängliga IP-Adressen från undernätet till nätverksgränssnittet under växling vid fel.

1. Klicka på alla tillgängliga nätverksgränssnitt och öppna bladet network interface.

2. Välj önskad undernätet i listan över tillgängliga undernät.

3. Ange den önskade IP (vid behov).

    ![Inställningar för nätverksgränssnitt](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Klicka på OK om du är klar och återgå till bladet ”beräkning och nätverk”.

5. Upprepa steg 1 – 4 för andra nätverksgränssnitt.

6. Klicka på ”Spara som” för att spara alla ändringar.

## <a name="next-steps"></a>Nästa steg
  [Lär dig mer](../virtual-network/virtual-network-network-interface-vm.md) om nätverksgränssnitt för virtuella Azure-datorer.
