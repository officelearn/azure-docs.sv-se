---
title: Kör en granskning av haveri beredskap i Azure VM med Azure Site Recovery
description: Lär dig hur du kör en haveri beredskap på en sekundär region för virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76166182"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Köra en haveri beredskaps granskning till en sekundär region för virtuella Azure-datorer

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra de som är tillgängliga under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I den här självstudien visar vi hur du kör ett programåterställningstest för en virtuell Azure-dator från en Azure-region till en annan med ett redundanstest. Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningarna
> * Köra ett redundanstest för en enstaka virtuell dator

> [!NOTE]
> Den här självstudien hjälper dig att utföra en haveri beredskaps granskning med minimala steg. Mer information om de olika funktionerna som rör haveri beredskap finns i dokumentationen för [replikering](azure-to-azure-how-to-enable-replication.md)av virtuella Azure-datorer, [nätverk](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md)eller [fel sökning](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Krav

Kontrol lera följande saker innan du gör den här självstudien:

- Innan du kör ett redundanstest rekommenderar vi att du kontrollerar egenskaperna för den virtuella datorn för att kontrol lera att de har kon figurer ATS för haveri beredskap. Gå till den virtuella datorns **funktions** > **haveri återställnings** > **Egenskaper** för att visa egenskaperna för replikering och redundans.
- **Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorer när du testar redundans**, i stället för det standardnätverk som skapades när du aktiverade replikeringen.
- Beroende på din käll nätverks konfiguration för varje nätverkskort kan du ange **undernät**, **privat IP-adress**, **offentlig IP**-adress, **nätverks säkerhets grupp**eller **belastningsutjämnare** för att ansluta till varje nätverkskort under inställningarna för redundanstest i **beräkning och nätverk** innan en haveri beredskap kan visas.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

I det här exemplet visas hur du använder ett Recovery Services valv för att utföra en redundanstest för virtuella datorer.

1. Välj ett valv och gå till **skyddade objekt** > **replikerade objekt** och välj en virtuell dator.
1. I **testa redundans**väljer du en återställnings punkt som ska användas för redundansväxlingen:
   - **Senaste**: bearbetar alla data i Site Recovery och ger det lägsta RTO (återställnings tid).
   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet används ingen tid för att bearbeta data, så det ger en låg RTO.
   - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: redundansväxla till viss återställnings punkt. Anpassad är endast tillgängligt när du växlar över en enskild virtuell dator och inte för redundans med en återställnings plan.
1. Välj det virtuella Azure-nätverk som virtuella Azure-datorer i den sekundära regionen ska ansluta till efter redundansväxlingen.

   > [!NOTE]
   > Om inställningarna för redundanstest är förkonfigurerade för det replikerade objektet, visas inte List rutan för att välja ett virtuellt Azure-nätverk.

1. Starta redundansväxlingen genom att välja **OK**. Om du vill spåra förloppet från valvet går du till **övervakning** > **Site Recovery jobb** och väljer jobbet **testa redundans** .
1. När redundansväxlingen har slutförts visas repliken av den virtuella Azure-datorn i Azure Portal **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
1. Om du vill ta bort de virtuella datorerna som skapades under redundanstest väljer du **rensning** av redundanstest på det replikerade objektet eller i återställnings planen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra en produktionsredundans](azure-to-azure-tutorial-failover-failback.md)
