---
title: "Aktivera replikering för virtuella Hyper-V-datorer som replikerar till Azure (utan System Center VMM) med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du måste aktivera replikering till Azure för Hyper-V virtuella datorer med hjälp av Azure Site Recovery-tjänsten"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>Steg 10: Aktivera replikering för virtuella Hyper-V-datorer som replikerar till Azure


Den här artikeln beskriver hur du aktiverar replikering för lokala Hyper-V virtuella datorer (som inte hanteras av System Center VMM) till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du kontrollera att ditt Azure-konto har de nödvändiga [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en ny virtuell dator till Azure.

## <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Alla diskar på en dator replikeras som standard. Du kan exkludera diskar från replikeringen. Till exempel kanske du inte vill replikera diskar med tillfälliga data eller data som har uppdateras varje gång en dator eller programmet startas om (till exempel pagefile.sys eller SQL Server tempdb). [Läs mer](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>Replikera virtuella datorer

Aktivera replikering för virtuella datorer på följande sätt:          

1. Klicka på **replikera program** > **källa**. När du har konfigurerat replikering för första gången, kan du klicka på **+ replikera** att aktivera replikering för ytterligare datorer.

    ![Aktivera replikering](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. I **källa**, välj platsen för Hyper-V. Klicka sedan på **OK**.
3. I **mål**, Välj prenumerationen som valvet och växling vid fel modell du vill använda i Azure (klassiska eller resource management) efter växling vid fel.
4. Välj lagringskontot som du vill använda. Om du inte har ett konto som du vill använda, kan du [skapar du en](#set-up-an-azure-storage-account). Klicka sedan på **OK**.
5. Välj Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till när de skapas växling vid fel.

    - Markera för att tillämpa nätverksinställningarna på alla datorer som du aktiverar för replikering **konfigurera nu för valda datorer**.
    - Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
    - Om du inte har ett nätverk som du vill använda, kan du [skapar du en](#set-up-an-azure-network). Välj ett undernät om det behövs. Klicka sedan på **OK**.

   ![Aktivera replikering](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. I **Egenskaper** > **Konfigurera egenskaper** väljer du operativsystemet för de valda virtuella datorerna och operativsystemdisken.
8. Kontrollera att namnet på den virtuella datorn i Azure (målnamnet) uppfyller [kraven för virtuella datorer i Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Som standard markeras alla diskar på den virtuella datorn för replikering. Rensa diskar för att utesluta dem.
10. Spara ändringarna genom att klicka på **OK**. Du kan ange ytterligare egenskaper senare.

    ![Aktivera replikering](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** väljer du den replikeringsprincip som du vill använda för de skyddade virtuella datorerna. Klicka sedan på **OK**. Du kan ändra replikeringsprincipen i **replikeringsprinciper** > principnamn > **redigera inställningar för**. De ändringar du gör används både för datorer som redan replikeras och för nya datorer.


   ![Aktivera replikering](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

Du kan följa förloppet för jobbet **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


## <a name="next-steps"></a>Nästa steg


Gå till [steg 11: kör ett redundanstest](hyper-v-site-walkthrough-test-failover.md)
