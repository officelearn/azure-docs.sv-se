---
title: Aktivera replikering för en disk som har lagts till i en Azure-dator som replikeras av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar replikering för en disk som har lagts till i en Azure-dator som är aktiverad för haveriberedskap med Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928069"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Aktivera replikering för en disk som har lagts till i en Azure-dator


Den här artikeln beskriver hur du aktiverar replikering för datadiskar som läggs till en Azure-dator som redan är aktiverad för haveriberedskap till en annan Azure-region, med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Aktiverar replikering för en disk som du lägger till i en virtuell dator har stöd för virtuella Azure-datorer med hanterade diskar.

När du lägger till en ny disk till en Azure virtuell dator som replikeras till en annan Azure-region, inträffar följande:

-   Tillståndet för replikeringen för den virtuella datorn visas en varning och en anteckning i portalen meddelar att en eller fler diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen när den inledande replikeringen på disken.
-   Om du väljer att inte aktivera replikering av disken, kan du välja för att ignorera varningen.

![Ny disk som har lagts till](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du har ställt in katastrofåterställning för den virtuella datorn som du vill lägga till disken. Om du inte gjort det, följer du de [Azure till Azure disaster recovery självstudien](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Aktivera replikering för en disk som har lagts till 

Om du vill aktivera replikering för en disk som har lagts till, gör du följande:

1. I valvet > **replikerade objekt**, klickar du på den virtuella datorn som du har lagt till disken.
2. Klicka på **diskar**, och välj sedan datadisken som du vill aktivera replikering (dessa diskar har en **oskyddade** status).
3.  I **diskinformation**, klickar du på **Aktivera replikering**.

    ![Aktivera replikering för tillagda disken](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Efter att aktivera replikering jobbet har körts och den inledande replikeringen är klar, tas health Replikeringsvarning för disk-problemet bort.



# <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
