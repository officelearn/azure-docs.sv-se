---
title: Kör en granskning av haveri beredskap i Azure VM med Azure Site Recovery
description: Lär dig hur du kör en haveri beredskap på en sekundär region för virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091325"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Köra en haveri beredskaps granskning till en sekundär region för virtuella Azure-datorer 

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra de som är tillgängliga under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I den här självstudien visar vi hur du kör ett programåterställningstest för en virtuell Azure-dator från en Azure-region till en annan med ett redundanstest. Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningarna
> * Köra ett redundanstest för en enstaka virtuell dator

> [!NOTE]
> Den här självstudien hjälper dig att utföra en DR-granskning med minimala steg. Om du vill lära dig mer om de olika aspekter som är kopplade till att utföra en katastrof granskning, inklusive nätverks överväganden, automatisering eller fel sökning, kan du läsa dokumenten under "How to" för virtuella Azure-datorer.

## <a name="prerequisites"></a>Krav

- Innan du kör ett redundanstest rekommenderar vi att du kontrollerar VM-egenskaperna så att allt är som förväntat.  VM-egenskaper finns i **Replikerade objekt**. På bladet **Information** finns information om datorinställningar och status.
- **Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorer när du testar redundans**, i stället för det standardnätverk som skapades när du aktiverade replikeringen.
- Beroende på din käll nätverks konfiguration för varje nätverkskort kan du välja att ange **undernät, IP-adress, offentlig IP-adress, nätverks säkerhets grupp eller internt Load Balancer** att ansluta till varje nätverkskort under inställningarna för redundanstest i Compute & Network innan du utför en Dr-granskning.


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I **Inställningar** > **Replikerade objekt** klickar du på VM-ikonen **+Testa redundans**.

2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

    - **Senaste**: bearbetar alla data i Site Recovery och ger det lägsta RTO (återställnings tid).
    - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: redundansväxla till viss återställnings punkt. Anpassad är endast tillgängligt när du växlar över en enskild virtuell dator och inte för redundans med en återställnings plan.

3. Välj det virtuella Azure-nätverkets mål som virtuella Azure-datorer i den sekundära regionen ska anslutas till efter att redundansen utförts.

    > [!NOTE]
    > List rutan för att välja Azure Virtual Network visas inte om inställningarna för redundanstest är förkonfigurerade för det replikerade objektet.

4. Starta redundansen genom att klicka på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort de virtuella Azure-datorer som skapades under redundanstestet klickar du på **Rensa redundanstestning** i det replikerade objektet eller återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra en produktionsredundans](azure-to-azure-tutorial-failover-failback.md)
