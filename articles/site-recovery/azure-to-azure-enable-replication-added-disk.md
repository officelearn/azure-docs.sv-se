---
title: Aktivera replikering för en tillagd Azure VM-disk i Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs hur du aktiverar replikering för en disk som har lagts till i en Azure-dator som är aktiverad för haveribeã¤ndesã¤ckÃ¤r med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973799"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Aktivera replikering för en disk som lagts till i en virtuell Azure-dator


I den här artikeln beskrivs hur du aktiverar replikering för datadiskar som läggs till i en Azure-virtuell dator som redan är aktiverad för haveriberedskap till en annan Azure-region med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Det går att aktivera replikering för en disk som du lägger till i en virtuell dator för virtuella Azure-datorer med hanterade diskar.

När du lägger till en ny disk i en Virtuell Azure-dator som replikeras till en annan Azure-region inträffar följande:

-   Replikeringshälsa för den virtuella datorn visar en varning och en anteckning i portalen informerar dig om att en eller flera diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den första replikeringen av disken.
-   Om du väljer att inte aktivera replikering för disken kan du välja att stänga av varningen.

![Ny disk tillagd](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du redan har ställt in haveriberedskap för den virtuella datorn som du lägger till disken på. Om du inte har gjort det följer du [azure-till Azure-haveriberedskapsdialkursen](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Aktivera replikering för en tillagd disk

Så här aktiverar du replikering för en tillagd disk:

1. Klicka på den virtuella dator som du lade till disken i valvet > **replikerade objekt.**
2. Klicka på **Diskar**och välj sedan den datadisk som du vill aktivera replikering för (dessa diskar har **inte skyddad** status).
3.  Klicka på **Aktivera replikering** **i Diskinformation**.

    ![Aktivera replikering för tillagd disk](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

När aktivera replikeringsjobbet körs och den första replikeringen är klar tas hälsovarningen för replikering för diskproblemet bort.



## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra en testundanställning.
