---
title: Konfigurera replikering för lagringsdirigering (S2d) virtuella datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för virtuella datorer med S2D, från en Azure-region till en annan med Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112780"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replikera virtuella Azure-datorer med hjälp av funktionen lagringsdirigering lagring till en annan Azure-region

Den här artikeln beskriver hur du aktiverar haveriberedskap för virtuella Azure-datorer som körs lagringsdirigering.

>[!NOTE]
>Bara krascher som konsekventa återställningspunkter har stöd för storage spaces direct kluster.
>

## <a name="introduction"></a>Introduktion 
[Lagringsdirigering (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) är en programvarudefinierad lagring, vilket är ett sätt att skapa [gästkluster](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) på Azure.  Ett gästkluster i Microsoft Azure är ett Failover-kluster består av virtuella IaaS-datorer. Det gör värdbaserad VM-arbetsbelastningar att växla över mellan gästkluster uppnå högre tillgänglighet för program än vad som en virtuell Azure-dator. Det är användbart i scenarier där virtuell dator som är värd för kritiska program som SQL eller skala ut filserver osv.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Disaster Recovery av Azure-datorer med hjälp av lagringsdirigering
I ett typiskt scenario kanske gästkluster för virtuella datorer på Azure för högre återhämtning av ditt program som skala ut filserver. Detta kan användas för ditt program högre tillgänglighet men vill du skydda dessa program med hjälp av Site Recovery för underlåtenhet för regionen. Site Recovery replikerar data från en region till en annan Azure-region och öppnas på klustret i disaster recovery region i händelse av redundans.

Nedanstående diagram visar bildåtergivning av två virtuella datorer i Azure-redundanskluster med hjälp av storage blanksteg direkt.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Två virtuella Azure-datorer i ett redundanskluster för Windows och varje virtuell dator har två eller flera datadiskar.
- S2D synkroniserar data på datadisken och visar den synkroniserade storage som en lagringspool.
- Lagringspoolen presenteras som en klusterdelad volym (CSV) i failover-kluster.
- Klustret använder CSV-filen för dataenheterna.

**Överväganden kring haveriberedskap**

1. När du ställer in [cloud vittne](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) Behåll vittne för klustret, i regionen för Haveriberedskap.
2. Om du kommer att växla över virtuella datorer i undernätet i DR-regionen som skiljer sig från källregionen måste klustrets IP-adress sedan vara ändra efter en redundansväxling.  Ändra IP-Adressen för klustret måste du använda ASR [plan återställningsskriptet.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Exempel på skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) att köra kommandot i virtuell dator med tillägget för anpassat skript 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktiverar Site Recovery för S2D-kluster:

1. Inuti återställningen services-valv, klicka på ”+ replikera”
1. Välj alla noder i klustret och blir en del av en [konsekvensgrupp för Multi-VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Välj replikeringsprincip med programkonsekvens av * (endast krascher konsekvens support är tillgänglig)
1. Aktivera replikering

   ![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Gå till replikerade objekt och du kan se både virtuella datorns status. 
3. Båda de virtuella datorerna komma skyddas och visas också som en del av multi-VM konsekvensgrupp.

   ![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i ett flerskiktat program under en redundansväxling. Sekvensering hjälper till att upprätthålla programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer, fullständig stegen som beskrivs i [skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för växling vid fel

1.  Skapa en återställningsplan genom att lägga till de virtuella datorerna.
2.  Klicka på Anpassa om du vill gruppera de virtuella datorerna. Som standard har alla virtuella datorer ingår i ”grupp 1”.


### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i en återställningsplan
För dina program ska fungera korrekt, kan du behöva göra vissa åtgärder på Azure virtuella datorer efter redundans eller under ett redundanstest. Du kan automatisera vissa åtgärder efter en redundansväxling. Till exempel vi kopplar här loadbalancer och ändra kluster-IP.


### <a name="failover-of-the-virtual-machines"></a>Redundans för virtuella datorer 
Båda noderna i de virtuella datorerna måste vara växla över med hjälp av den [ASR återställningsplan](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Köra ett redundanstest
1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplan som du skapade.
3.  Välj **Testa redundans**.
4.  Välj återställningspunkten och Azure-nätverket om du vill börja testa redundansprocessen.
5.  När den sekundära miljön är igång, utföra verifieringar.
6.  När verifieringar har slutförts, för att rensa redundans-miljö, Välj **Rensa redundanstestning**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Välj återställningspunkten för att starta redundansprocessen.

Mer information finns i [redundans i Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nästa steg

[Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) om hur du kör återställning efter fel.
