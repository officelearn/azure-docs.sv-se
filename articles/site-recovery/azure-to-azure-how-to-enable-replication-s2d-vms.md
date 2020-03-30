---
title: Replikera Virtuella Azure-datorer som kör Storage Spaces Direct med Azure Site Recovery
description: Lär dig hur du replikerar virtuella Azure-datorer som kör Storage Spaces Direct med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973655"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikera virtuella Azure-datorer som kör Lagringsutrymmen direkt till en annan region

I den här artikeln beskrivs hur du aktiverar haveriberedskap för virtuella Azure-datorer som kör lagringsutrymmen direkt.

>[!NOTE]
>Endast kraschkonsekventa återställningspunkter stöds för direktkluster för lagringsutrymmen.
>

[Direkt lagringsutrymmen (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) är programvarudefinierad lagring, vilket är ett sätt att skapa [gästkluster](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) på Azure.  Ett gästkluster i Microsoft Azure är ett redundanskluster som består av virtuella IaaS-datorer. Det gör att värdbaserade VM-arbetsbelastningar kan växla över mellan gästkluster, vilket ger större tillgänglighet SLA för program än en enda Azure VM-dator kan tillhandahålla. Det är användbart i scenarier där en virtuell dator är värd för ett kritiskt program som SQL eller utskalningsfilserver.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Katastrofåterställning med direkt lagringsutrymmen

I ett typiskt scenario kan du ha gästkluster för virtuella datorer på Azure för högre återhämtning av ditt program som Skala ut filservern. Även om detta kan ge ditt program högre tillgänglighet, vill du skydda dessa program med Site Recovery för alla regionnivåfel. Site Recovery replikerar data från en region till en annan Azure-region och tar upp klustret i katastrofåterställningsregionen i händelse av redundans.

Nedan visas ett redundanskluster för tvånoder av Azure VM med hjälp av direkt lagringsutrymmen.

![lagringsutrymmenriktad](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Två virtuella Azure-datorer i ett Windows Redundanskluster och varje virtuell dator har två eller flera datadiskar.
- S2D synkroniserar data på datadisken och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenterar som en klusterdelad volym (CSV) till redundansklustret.
- Redundansklustret använder CSV för dataenheterna.

**Överväganden om katastrofåterställning**

1. När du konfigurerar [molnvittne](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) för klustret ska du hålla vittne i regionen Haveriberedskap.
2. Om du ska växla över de virtuella datorerna till undernätet i DR-regionen som skiljer sig från källregionen måste klustrets IP-adress ändras efter redundans.  Om du vill ändra IP för klustret måste du använda skriptet för återställningsplan för [webbplatsåterställning.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Exempel på skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) för att köra kommando i vm med hjälp av anpassat skripttillägg 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktivera platsåterställning för S2D-kluster:

1. I valvet för återställningstjänster klickar du på "+replicate"
1. Markera alla noder i klustret och gör dem till en del av en [konsekvensgrupp för flera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Välj replikeringsprincip med programkonsekvens av* (endast stöd för kraschkonsekvens är tillgängligt)
1. Aktivera replikering

   ![lagringsutrymmendirekt skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Gå till replikerade objekt och du kan se både den virtuella datorn status.
3. Både de virtuella datorerna blir skyddade och visas också som en del av konsekvensgruppen för flera virtuella datorer.

   ![lagringsutrymmendirekt skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder sekvensering av olika nivåer i ett program på flera nivåer under en redundansväxling. Sekvensering hjälper till att upprätthålla programmets konsekvens. När du skapar en återställningsplan för ett webbprogram på flera nivåer slutför du stegen som beskrivs i [Skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i redundansgrupper

1.  Skapa en återställningsplan genom att lägga till de virtuella datorerna.
2.  Klicka på "Anpassa" för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i Grupp 1.


### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen
För att dina program ska fungera korrekt kan du behöva göra vissa åtgärder på virtuella Azure-datorer efter redundansen eller under en testväxling. Du kan automatisera vissa åtgärder efter redundans. Till exempel, här är vi bifoga belastningsutjämnare och ändra kluster IP.


### <a name="failover-of-the-virtual-machines"></a>Redundans för de virtuella datorerna 
Båda noderna för de virtuella datorerna måste växlas över med hjälp av [återställningsplanen](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) för återställning av webbplats 

![lagringsutrymmendirekt skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Köra ett redundanstest
1.  Välj ditt Recovery Services-valv i Azure-portalen.
2.  Välj den återställningsplan som du skapade.
3.  Välj **Testa redundans**.
4.  Om du vill starta testundanundansprocessen väljer du återställningspunkten och det virtuella Azure-nätverket.
5.  När den sekundära miljön är uppe utför du valideringar.
6.  När valideringarna är klara väljer du **Rensa test redundans**för att rensa redundansmiljön.

Mer information finns i [Testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  Välj ditt Recovery Services-valv i Azure-portalen.
2.  Välj den återställningsplan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Om du vill starta redundansprocessen väljer du återställningspunkten.

Mer information finns [i Redundans i Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nästa steg

[Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) om att köra återställning av återställning.
