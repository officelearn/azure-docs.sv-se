---
title: Köra ett programåterställningstest för virtuella Azure-datorer till en sekundär Azure-region med Azure Site Recovery-tjänsten
description: Lär dig att köra ett programåterställningstest för virtuella Azure-datorer till en sekundär Azure-region för virtuella Azure IaaS-datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: tutorial
origin.date: 04/08/2019
ms.date: 04/22/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 16c791287cc50b5ac8992a86f6de1e3eeb5e329e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552951"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Kör ett programåterställningstest för virtuella Azure-datorer till en sekundär Azure-region

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra de som är tillgängliga under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I den här självstudien visar vi hur du kör ett programåterställningstest för en virtuell Azure-dator från en Azure-region till en annan med ett redundanstest. Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningarna
> * Köra ett redundanstest för en enstaka virtuell dator

> [!NOTE]
> Den här självstudiekursen är avsedd att guida användaren genom stegen att utföra ett programåterställningstest med minimalt antal steg. Mer information om de olika aspekterna som förknippas med att utföra ett programåterställningstest, till exempel nätverksöverväganden, automatisering eller felsökning, finns i dokumenten under instruktionerna för virtuella Azure-datorer.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Innan du kör ett redundanstest rekommenderar vi att du kontrollerar VM-egenskaperna så att allt är som förväntat.  VM-egenskaper finns i **Replikerade objekt**. På bladet **Information** finns information om datorinställningar och status.
- **Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorer när du testar redundans**, i stället för det standardnätverk som skapades när du aktiverade replikeringen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I **Inställningar** > **Replikerade objekt** klickar du på VM-ikonen **+Testa redundans**.

2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj det virtuella Azure-nätverkets mål som virtuella Azure-datorer i den sekundära regionen ska anslutas till efter att redundansen utförts.

4. Starta redundansen genom att klicka på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort de virtuella Azure-datorer som skapades under redundanstestet klickar du på **Rensa redundanstestning** i det replikerade objektet eller återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra en produktionsredundans](azure-to-azure-tutorial-failover-failback.md)

<!-- Update_Description: update meta properties -->

