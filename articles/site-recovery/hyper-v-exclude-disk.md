---
title: Exkludera Virtuella datorer med virtuella datorer från virtuella datorer från haveriberedskap till Azure med Azure Site Recovery
description: Så här utesluter du Virtuella hyper-v-diskar från replikering till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498135"
---
# <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

I den här artikeln beskrivs hur du utesluter diskar när du replikerar virtuella hyper-v-datorer till Azure. Du kanske vill utesluta diskar från replikering av flera skäl:

- Kontrollera att oviktiga data som spottas på den uteslutna disken inte replikeras.
- Optimera den förbrukade replikeringsbandbredden, eller målsidans resurser, genom att utesluta diskar som du inte behöver replikera.
- Spara lagrings- och nätverksresurser genom att inte replikera data som du inte behöver.

Innan du utesluter diskar från replikering:

- [Läs mer](exclude-disks-replication.md) om att utesluta diskar.
- Granska [typiska uteslutningsscenarier](exclude-disks-replication.md#typical-scenarios) och [exempel](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) som visar hur utesluta en disk påverkar replikering, redundans och återställning efter fel.

## <a name="before-you-start"></a>Innan du börjar

Observera följande innan du börjar:

- **Replikering:** Som standard replikeras alla diskar på en dator.
- **Disktyp:**
    - Du kan utesluta grundläggande diskar från replikering.
    - Du kan inte undanta operativsystemdiskar.
    - Vi rekommenderar att du inte undantar dynamiska diskar. Site Recovery kan inte identifiera vilken virtuell hårddisk som är grundläggande eller dynamisk i gästdatorn.  Om du inte utesluter alla beroende dynamiska volymdiskar blir den skyddade dynamiska disken en misslyckad disk på en misslyckad över virtuell dator och data på disken är inte tillgängliga.
- **Lägg till/ta bort/exkludera diskar**: När du har aktiverat replikering kan du inte lägga till/ta bort/utesluta diskar för replikering. Om du vill lägga till/ta bort eller utesluta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera den igen.
- **Redundans:** När redundans, om misslyckades över apps behöver utesluta diskar för att fungera, måste du skapa dessa diskar manuellt. Alternativt kan du integrera Azure automation i en återställningsplan, för att skapa disken under redundans av datorn.
- **Redundans:** När du växlar tillbaka till din lokala plats efter redundans, diskar som du skapade manuellt i Azure inte misslyckades tillbaka. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en Virtuell Azure, misslyckas sedan endast tre diskar som misslyckades över tillbaka. Du kan inte inkludera diskar som har skapats manuellt i återställning efter fel eller i omvänd replikering av virtuella datorer.

## <a name="exclude-disks"></a>Uteslut diskar

1. Om du vill utesluta diskar när du [aktiverar replikering](site-recovery-hyper-v-site-to-azure.md) för en virtuell dator med hyper-V, granska **diskarna till replikerar** i egenskapssidan Aktivera **replikeringsegenskaper.** > **Properties** > **Configure properties** Som standard är alla diskar markerade för replikering.
2. Om du inte vill replikera en viss disk rensar du markeringen för alla diskar som du vill utesluta i **Diskar.** 

    ![Undanta diskar från replikering](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.
