---
title: Hantera nätverkskort för lokal haveriberedskap med Azure Site Recovery
description: Beskriver hur du hanterar nätverksgränssnitt för lokal haveriberedskap till Azure med Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954588"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Hantera VM-nätverksgränssnitt för lokal haveriberedskap till Azure

En virtuell dator (VM) i Azure måste ha minst ett nätverksgränssnitt kopplat till den. Den kan ha så många nätverksgränssnitt som är anslutna till den som den virtuella datorns storlek stöder.

Som standard definieras det första nätverksgränssnittet som är kopplat till en virtuell Azure-dator som det primära nätverksgränssnittet. Alla andra nätverksgränssnitt i den virtuella datorn är sekundära nätverksgränssnitt. Som standard skickas också all utgående trafik från den virtuella datorn ut den IP-adress som har tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet.

I en lokal miljö kan virtuella datorer eller servrar ha flera nätverksgränssnitt för olika nätverk i miljön. Olika nätverk används vanligtvis för att utföra specifika åtgärder som uppgraderingar, underhåll och internetåtkomst. När du migrerar eller misslyckas med Azure från en lokal miljö bör du tänka på att nätverksgränssnitt på samma virtuella dator måste vara anslutna till samma virtuella nätverk.

Som standard skapar Azure Site Recovery så många nätverksgränssnitt på en virtuell Azure-dator som är anslutna till den lokala servern. Du kan undvika att skapa redundanta nätverksgränssnitt under migrering eller redundans genom att redigera nätverksgränssnittsinställningarna under inställningarna för den replikerade virtuella datorn.

## <a name="select-the-target-network"></a>Välj målnätverk

För virtuella datorer med VMware och fysiska datorer och för virtuella Hyper-V-datorer (utan System Center Virtual Machine Manager) kan du ange målvirkavnätverket för enskilda virtuella datorer. För virtuella Hyper-V-datorer som hanteras med Virtual Machine Manager använder du [nätverksmappning](site-recovery-network-mapping.md) för att mappa VM-nätverk på en källa för Virtual Machine Manager-server och rikta in sig på Azure-nätverk.

1. Under **Replikerade objekt** i ett Recovery Services-valv väljer du alla replikerade objekt för att komma åt inställningarna för det replikerade objektet.

2. Välj fliken **Beräkna och Nätverk** om du vill komma åt nätverksinställningarna för det replikerade objektet.

3. Under **Nätverksegenskaper**väljer du ett virtuellt nätverk i listan över tillgängliga nätverksgränssnitt.

    ![Nätverksinställningar](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Om du ändrar målnätverket påverkas alla nätverksgränssnitt för den specifika virtuella datorn.

För moln för Virtual Machine Manager påverkar ändring av nätverksmappning alla virtuella datorer och deras nätverksgränssnitt.

## <a name="select-the-target-interface-type"></a>Välj målgränssnittstyp

Under avsnittet **Nätverksgränssnitt** i fönstret **Beräkna och Nätverk** kan du visa och redigera inställningar för nätverksgränssnitt. Du kan också ange typen av målnätverksgränssnitt.

- Ett **primärt** nätverksgränssnitt krävs för redundans.
- Alla andra valda nätverksgränssnitt, om sådana finns, är **sekundära** nätverksgränssnitt.
- Välj **Använd inte** för att utesluta ett nätverksgränssnitt från att skapas vid redundans.

När du aktiverar replikering väljer site recovery som standard alla identifierade nätverksgränssnitt på den lokala servern. Det markerar en som **Primär** och alla andra som **Sekundär**. Alla efterföljande gränssnitt som läggs till på den lokala servern markeras **Använd inte** som standard. När du lägger till fler nätverksgränssnitt kontrollerar du att rätt azure-målstorlek för virtuella azure-datorer har valts för att tillgodose alla nödvändiga nätverksgränssnitt.

## <a name="modify-network-interface-settings"></a>Ändra inställningar för nätverksgränssnitt

Du kan ändra undernätet och IP-adressen för ett replikerat objekts nätverksgränssnitt. Om en IP-adress inte har angetts kommer Site Recovery att tilldela nästa tillgängliga IP-adress från undernätet till nätverksgränssnittet vid redundans.

1. Välj ett tillgängligt nätverksgränssnitt för att öppna nätverksgränssnittsinställningarna.

2. Välj önskat undernät i listan över tillgängliga undernät.

3. Ange önskad IP-adress (efter behov).

    ![Inställningar för nätverksgränssnitt](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Välj **OK** om du vill slutföra redigeringen och återgå till fönstret **Beräkna och nätverk.**

5. Upprepa steg 1-4 för andra nätverksgränssnitt.

6. Välj **Spara** om du vill spara alla ändringar.

## <a name="next-steps"></a>Nästa steg
  [Läs mer](../virtual-network/virtual-network-network-interface-vm.md) om nätverksgränssnitt för virtuella Azure-datorer.
