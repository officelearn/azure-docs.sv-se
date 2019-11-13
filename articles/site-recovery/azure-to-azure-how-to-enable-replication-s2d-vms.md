---
title: Replikera virtuella Azure-datorer som kör Lagringsdirigering med Azure Site Recovery
description: Den här artikeln beskriver hur du replikerar virtuella Azure-datorer som kör Lagringsdirigering med Azure Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 25ac7fa577aa33eda036c0f8544cc5ab03b12cd7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954454"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikera virtuella Azure-datorer som kör Lagringsdirigering till en annan region

I den här artikeln beskrivs hur du aktiverar haveri beredskap för virtuella Azure-datorer som kör lagrings dirigering.

>[!NOTE]
>Endast kraschbaserade återställnings punkter stöds för lagrings Dirigerings kluster.
>

## <a name="introduction"></a>Introduktion 
[Lagrings dirigering (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) är en programdefinierad lagring som gör det möjligt att skapa [gäst kluster](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) i Azure.  Ett gäst kluster i Microsoft Azure är ett kluster för växling vid fel som består av virtuella IaaS-datorer. Den gör det möjligt för värdar för virtuella datorer att redundansväxla över gäst kluster som uppnår service avtal med högre tillgänglighet för program än en enskild virtuell Azure-dator kan tillhandahålla. Det är användbart i scenarier där VM är värd för ett kritiskt program som SQL eller skalbar fil Server osv.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Haveri beredskap för Azure Virtual Machines med hjälp av lagrings dirigering
I ett typiskt scenario kan du ha virtuella datorer i Azure för högre återhämtning av ditt program som skalbar fil server. Även om detta kan ge ditt program högre tillgänglighet, vill du skydda dessa program med hjälp av Site Recovery för ett region nivå haveri. Site Recovery replikerar data från en region till en annan Azure-region och tar upp klustret i haveri beredskaps region i händelse av redundans.

Diagrammet nedan visar bild representationen av två redundanskluster för virtuella Azure-datorer med lagrings dirigering.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Två virtuella Azure-datorer i ett Windows-redundanskluster och varje virtuell dator har två eller flera data diskar.
- S2D synkroniserar data på data disken och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenteras som en klusterdelad volym (CSV) till redundansklustret.
- Kluster för växling vid fel använder CSV för data enheterna.

**Överväganden vid haveri beredskap**

1. När du ställer in [moln vittne](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) för klustret ska du behålla vittnet i haveri beredskaps regionen.
2. Om du växlar över de virtuella datorerna till under nätet i den DR-region som skiljer sig från käll regionen måste kluster-IP-adressen ändras efter redundansväxlingen.  Om du vill ändra IP-adress för klustret måste du använda skript för ASR- [återställnings plan.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Exempel skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) för att köra kommando i virtuell dator med anpassat skript tillägg 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktivera Site Recovery för S2D-kluster:

1. I Recovery Services-valvet klickar du på "+ replikera"
1. Markera alla noder i klustret och gör dem till en del av en [konsekvens grupp för flera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Välj replikeringsprincip med Application konsekvens av * (det finns bara stöd för krasch konsekvens)
1. Aktivera replikering

   ![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Gå till replikerade objekt så kan du se både statusen för den virtuella datorn. 
3. Båda de virtuella datorerna skyddas och visas också som en del av konsekvens gruppen för flera virtuella datorer.

   ![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Skapa en återställnings plan
En återställnings plan stöder sekvenseringen av olika nivåer i ett program med flera nivåer under en redundansväxling. Sekvenseringen bidrar till att upprätthålla programmets konsekvens. När du skapar en återställnings plan för ett webb program med flera nivåer slutför du stegen som beskrivs i [skapa en återställnings plan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för redundans

1.  Skapa en återställnings plan genom att lägga till de virtuella datorerna.
2.  Klicka på Anpassa för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i "grupp 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen
För att dina program ska fungera korrekt kan du behöva utföra några åtgärder på de virtuella Azure-datorerna efter redundansväxlingen eller under ett redundanstest. Du kan automatisera vissa åtgärder efter redundans. Här lägger vi till exempel till Loadbalancer och ändrar kluster-IP.


### <a name="failover-of-the-virtual-machines"></a>Redundansväxling av de virtuella datorerna 
Båda noderna för de virtuella datorerna måste växlas över med [ASR-återställnings planen](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Köra ett redundanstest
1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du har skapat.
3.  Välj **Testa redundans**.
4.  Starta processen för redundanstest genom att välja återställnings punkten och det virtuella Azure-nätverket.
5.  Utföra verifieringar när den sekundära miljön är igång.
6.  När verifieringen är klar kan du rensa redundansväxlingen genom att välja **rensning**av redundanstest.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Starta redundansväxlingen genom att välja återställnings punkten.

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nästa steg

[Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) om att köra återställning efter fel.
