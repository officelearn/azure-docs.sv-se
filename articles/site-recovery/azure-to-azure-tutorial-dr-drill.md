---
title: Kör en azure VM-haveriberedskapsövning med Azure Site Recovery
description: Lär dig hur du kör en snabbrmaskin för haveriberedskap till en sekundär region för virtuella Azure-datorer med azure site recovery-tjänsten.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166182"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Kör en borrmaskin för haveriberedskap till en sekundär region för virtuella Azure-datorer

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra de som är tillgängliga under planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I den här självstudien visar vi hur du kör ett programåterställningstest för en virtuell Azure-dator från en Azure-region till en annan med ett redundanstest. Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningarna
> * Köra ett redundanstest för en enstaka virtuell dator

> [!NOTE]
> Den här självstudien hjälper dig att utföra en borr för haveriberedskap med minimala steg. Mer information om de olika funktionerna i samband med att utföra en snabbrmaskin för haveriberedskap finns i dokumentationen för [Azure VMs-replikering,](azure-to-azure-how-to-enable-replication.md) [nätverks-,](azure-to-azure-about-networking.md) [automatiserings-](azure-to-azure-powershell.md)eller [felsökning](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Krav

Kontrollera följande objekt innan du gör den här självstudien:

- Innan du kör en testväxling rekommenderar vi att du kontrollerar den virtuella datorns egenskaper för att se till att den är konfigurerad för haveriberedskap. Gå till den virtuella datorns > **egenskaper** för **haveriberedskap** > **Disaster Recovery**för operationer för att visa egenskaperna för replikering och redundans.
- **Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorer när du testar redundans**, i stället för det standardnätverk som skapades när du aktiverade replikeringen.
- Beroende på dina källnätverkskonfigurationer för varje nätverkskort kan du ange **Undernät,** **Privat IP-adress,** **Offentlig IP,** **Nätverkssäkerhetsgrupp**eller **Belastningsutjämning** för att koppla till varje nätverkskort under testväxlingsinställningar i **Beräkning och nätverk** innan du utför en snabbrutningsövning för haveriberedskap.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Det här exemplet visar hur du använder ett Recovery Services-valv för att göra en vm-testväxling.

1. Välj ett valv och gå till **Skyddade objekt** > **Replikerade objekt** och välj en virtuell dator.
1. I **Test Redundans**väljer du en återställningspunkt som ska användas för redundansen:
   - **Senaste**: Bearbetar alla data i Site Recovery och ger den lägsta RTO (Recovery Time Mål).
   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet går ingen tid åt att bearbeta data, så det ger en låg RTO.
   - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad:** Växla över till en viss återställningspunkt. Anpassad är bara tillgänglig när du växlar över en enda virtuell dator och inte för redundans med en återställningsplan.
1. Välj det virtuella nätverket för Mål Azure som Azure virtuella datorer i den sekundära regionen ansluter till efter redundansen.

   > [!NOTE]
   > Om testundanpassningsinställningarna är förkonfigurerade för det replikerade objektet visas inte den nedrullningsbara menyn för att välja ett virtuellt Azure-nätverk.

1. Om du vill starta redundansen väljer du **OK**. Om du vill spåra förloppet från valvet går du till **Jobb för övervakning av** > **platsåterställning** och väljer jobbet **Testa redundans.**
1. När redundansen är klar visas den virtuella repliken azure-virtuella datorer i Azure-portalens **virtuella datorer**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
1. Om du vill ta bort de virtuella datorerna som skapades under testundansen väljer du **Redundanstest på** det replikerade objektet eller återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra en produktionsredundans](azure-to-azure-tutorial-failover-failback.md)
