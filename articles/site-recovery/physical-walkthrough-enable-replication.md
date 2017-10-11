---
title: "Aktivera replikering för fysiska servrar som replikerar till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du måste aktivera replikering till Azure för fysiska servrar med hjälp av Azure Site Recovery-tjänsten"
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
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>Steg 10: Aktivera replikering för fysiska servrar till Azure


Den här artikeln beskriver hur du aktiverar replikering för lokala Windows-/ Linux fysiska servrar till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Innan du börjar

- Servrarna måste ha den [mobilitetstjänsten installeras](physical-walkthrough-install-mobility.md).
- Om en virtuell dator är förberedd för push-installation installerar mobilitetstjänsten automatiskt processervern när du aktiverar replikering.
- När du aktiverar en dator för replikering ditt Azure-konto måste specifika [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) så det går att använda Azure storage, och skapa virtuella Azure-datorer.
- När du lägger till eller ändra IP-adresser för servrar, kan det ta upp till 15 minuter eller längre för att ändringarna ska börja gälla och att de visas på portalen.


## <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Alla diskar på en dator replikeras som standard. Du kan exkludera diskar från replikeringen. Till exempel kanske du inte vill replikera diskar med tillfälliga data eller data som har uppdateras varje gång en dator eller programmet startas om (till exempel pagefile.sys eller SQL Server tempdb). [Läs mer](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>Replikera servrar

1. Klicka på **Steg 2: Replikera program** > **Källa**.
2. I **källa**, Välj konfigurationsservern.
3. I **datorn typen**väljer **fysiska datorer**.
4. Välj processervern. Om du inte har skapat några ytterligare servrar är konfigurationsservern. Klicka sedan på **OK**.
5. I **mål**, Välj prenumerationen och resursgrupp som du vill skapa den misslyckade över virtuella datorer. Välj distributionsmodell som du vill använda i Azure (klassiska eller resource management), för den redundansväxlade virtuella datorer.
6. Välj Azure storage-konto som du vill använda för att replikera data. Om du inte vill använda ett konto som du redan har konfigurerat, kan du skapa en ny.
7. Välj den **Azure-nätverk** och **undernät** som virtuella Azure-datorer ansluta efter växling vid fel. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du inte vill använda ett befintligt nätverk kan skapa du en.

    ![Aktivera replikering](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. I **fysiska datorer**, klickar du på **+ fysisk dator** och ange den **namn** och **IP-adress**. Välj operativsystem på den dator som du vill replikera. Det tar några minuter tills datorer identifieras och visas i listan.
9. I **egenskaper** > **konfigurera egenskaper för**, Välj det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
10. Alla diskar replikeras som standard. Klicka på **alla diskar** och avmarkera alla diskar som du inte vill replikera. Klicka sedan på **OK**. Du kan ange ytterligare egenskaper för Virtuella datorer senare.

    ![Aktivera replikering](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. I **replikeringsinställningarna** > **konfigurerar replikeringsinställningar**, kontrollera att rätt replikeringsprinciper har valts. Om du ändrar en princip tillämpas ändringarna för replikering av datorn och till nya datorer.
12. Aktivera **konsekvens för flera** om du vill samla in datorer i en replikeringsgrupp och ange ett namn för gruppen. Klicka sedan på **OK**. Tänk på följande:

    * Datorer i replikeringsgrupper replikeras tillsammans, och har delat kraschkonsekvent och programkonsekventa återställningspunkter när de växlar över.
    * Vi rekommenderar att du samla fysiska servrar så att de speglar dina arbetsbelastningar. Aktivera konsekvens för flera kan påverka arbetsbelastningens prestanda och bör endast användas om datorer kör samma arbetsbelastning och du behöver enhetlighet.

13. Klicka på **Aktivera replikering**. Du kan följa förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 11: kör ett redundanstest](physical-walkthrough-test-failover.md)
