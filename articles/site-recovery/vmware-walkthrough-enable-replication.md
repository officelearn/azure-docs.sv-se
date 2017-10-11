---
title: "Aktivera replikering för virtuella VMware-datorer som replikerar till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du måste aktivera replikering till Azure för VMwares virtuella datorer med hjälp av Azure Site Recovery-tjänsten"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 470b9ddd8df4a4e74ec7174f79020c252323e502
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-11-enable-replication-for-vmware-virtual-machines-to-azure"></a>Steg 11: Aktivera replikering för virtuella VMware-datorer till Azure


Den här artikeln beskriver hur du aktiverar replikering för den lokala virtuella VMware-datorer till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Innan du börjar

- Virtuella VMware-datorer måste ha den [mobilitetstjänsten installeras](vmware-walkthrough-install-mobility.md). – Om en virtuell dator är förberedd för push-installation installerar mobilitetstjänsten automatiskt processervern när du aktiverar replikering.
- Ditt Azure-konto måste specifika [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en virtuell dator till Azure
- När du lägger till eller ändra virtuella datorer, kan det ta upp till 15 minuter eller längre för att ändringarna ska börja gälla och att de visas på portalen.
- Du kan kontrollera senast identifierade för virtuella datorer i **Konfigurationsservrar** > **senaste kontakt på**.
- Lägg till virtuella datorer utan att vänta på den schemalagda identifieringen genom att markera konfigurationsservern (inte på den), och klicka på **uppdatera**.



## <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Alla diskar på en dator replikeras som standard. Du kan exkludera diskar från replikeringen. Till exempel kanske du inte vill replikera diskar med tillfälliga data eller data som har uppdateras varje gång en dator eller programmet startas om (till exempel pagefile.sys eller SQL Server tempdb). [Läs mer](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replikera virtuella datorer

Innan du börjar titta på en video Snabböversikt

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. Klicka på **Steg 2: Replikera program** > **Källa**.
2. I **källa**, Välj konfigurationsservern.
3. I **datorn typen**väljer **virtuella datorer**.
4. I **vCenter/vSphere-Hypervisor**väljer vCenter-servern som hanterar vSphere-värd, eller värden.
5. Välj processervern. Om du inte har skapat några ytterligare servrar är konfigurationsservern. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/vmware-walkthrough-enable-replication/enable-replication2.png)

6. I **mål**, Välj prenumerationen och resursgrupp som du vill skapa den misslyckade över virtuella datorer. Välj distributionsmodell som du vill använda i Azure (klassiska eller resource management), för den redundansväxlade virtuella datorer.


7. Välj Azure storage-konto som du vill använda för att replikera data. Om du inte vill använda ett konto som du redan har konfigurerat, kan du skapa en ny.

8. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du inte vill använda ett befintligt nätverk kan skapa du en.

    ![Aktivera replikering](./media/vmware-walkthrough-enable-replication/enable-rep3.png)
9. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/vmware-walkthrough-enable-replication/enable-replication5.png)
10. I **egenskaper** > **konfigurera egenskaper för**, Välj det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
11. Alla diskar replikeras som standard. Klicka på **alla diskar** och avmarkera alla diskar som du inte vill replikera. Klicka sedan på **OK**. Du kan ange ytterligare egenskaper för Virtuella datorer senare.

    ![Aktivera replikering](./media/vmware-walkthrough-enable-replication/enable-replication6.png)
11. I **replikeringsinställningarna** > **konfigurerar replikeringsinställningar**, kontrollera att rätt replikeringsprinciper har valts. Om du ändrar en princip tillämpas ändringarna för replikering av datorn och till nya datorer.
12. Aktivera **konsekvens för flera** om du vill samla in datorer i en replikeringsgrupp och ange ett namn för gruppen. Klicka sedan på **OK**. Tänk på följande:

    * Datorer i replikeringsgrupper replikeras tillsammans, och har delat kraschkonsekvent och programkonsekventa återställningspunkter när de växlar över.
    * Vi rekommenderar att du samla in virtuella datorer och fysiska servrar så att de speglar dina arbetsbelastningar. Aktivera konsekvens för flera kan påverka arbetsbelastningens prestanda och bör endast användas om datorer kör samma arbetsbelastning och du behöver enhetlighet.

    ![Aktivera replikering](./media/vmware-walkthrough-enable-replication/enable-replication7.png)
13. Klicka på **Aktivera replikering**. Du kan följa förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 12: kör ett redundanstest](vmware-walkthrough-test-failover.md)
