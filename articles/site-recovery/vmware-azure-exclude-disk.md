---
title: Exkludera VMware VM-diskar från haveriberedskap till Azure med Azure Site Recovery
description: Så här utesluter du virtuella VMware-diskar från replikering till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495366"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Exkludera diskar från VMware VM-replikering till Azure

I den här artikeln beskrivs hur du utesluter diskar när du replikerar virtuella virtuella datorer med VMware till Azure för haveriberedskap. Du kanske vill utesluta diskar från replikering av flera skäl:

- Kontrollera att oviktiga data som spottas på den uteslutna disken inte replikeras.
- Optimera den förbrukade replikeringsbandbredden, eller målsidans resurser, genom att utesluta diskar som du inte behöver replikera.
- Spara lagrings- och nätverksresurser genom att inte replikera data som du inte behöver.

Innan du utesluter diskar från replikering:

- [Läs mer](exclude-disks-replication.md) om att utesluta diskar.
- Granska [typiska uteslutningsscenarier](exclude-disks-replication.md#typical-scenarios) och [exempel](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) som visar hur utesluta en disk påverkar replikering, redundans och återställning efter fel.

## <a name="before-you-start"></a>Innan du börjar

 Observera följande innan du börjar:

- **Replikering**: Som standard replikeras alla diskar på en dator.
- **Disktyp**: Endast enkla diskar kan uteslutas från replikering. Du kan inte undanta operativsystemdiskar eller dynamiska diskar.
- **Mobilitetstjänst**: Om du vill utesluta en disk från replikering måste du manuellt installera mobilitetstjänsten på datorn innan du aktiverar replikering. Du kan inte använda push-installationen, eftersom den här metoden installerar mobilitetstjänsten på en virtuell dator först efter att replikeringen har aktiverats.  
- **Lägg till/ta bort/exkludera diskar**: När du har aktiverat replikering kan du inte lägga till/ta bort/utesluta diskar för replikering. Om du vill lägga till/ta bort eller utesluta diskar måste du inaktivera skyddet för datorn och sedan aktivera den igen.
- **Redundans:** Efter redundans, om misslyckades över apps behöver uteslutna diskar för att fungera, måste du skapa dessa diskar manuellt. Alternativt kan du integrera Azure automation i en återställningsplan, för att skapa disken under redundans av datorn.
- **Failback-Windows**: När du växlar tillbaka till din lokala plats efter redundans, Windows-diskar som du skapar manuellt i Azure inte misslyckades tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar direkt på virtuella Azure-datorer, kommer endast de tre diskar som misslyckades över att återställas.
- **Failback-Linux**: För återställning av Linux-datorer misslyckas diskar som du skapar manuellt i Azure tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar direkt på virtuella Azure-datorer, kommer alla fem att återställas. Du kan inte utesluta diskar som har skapats manuellt i återställningen av fel, eller i återbeskyddande av virtuella datorer.



## <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

1. När du [aktiverar replikering](site-recovery-hyper-v-site-to-azure.md) för en virtuell virtuell vmware-dator, när du har valt de virtuella datorer som du vill replikera, i egenskapssidan **Aktivera replikeringsegenskaper** > **Properties** > **Konfigurera egenskaper,** granskar du kolumnen **Diskar att replikera.** Som standard är alla diskar markerade för replikering.
2. Om du inte vill replikera en viss disk rensar du markeringen för alla diskar som du vill utesluta i **Diskar.** 

    ![Undanta diskar från replikering](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.
