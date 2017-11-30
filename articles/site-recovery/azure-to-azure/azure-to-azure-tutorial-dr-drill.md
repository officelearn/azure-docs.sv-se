---
title: "Kör en katastrof återställningsgranskning för virtuella Azure-datorer till en sekundär region som Azure med Azure Site Recovery (förhandsgranskning)"
description: "Lär dig hur du kör en katastrof återställningsgranskning för virtuella Azure-datorer till en sekundär Azure region med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d763865bb0b86b1bdf15258a3caf36561dd3385c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>Kör en katastrof återställningsgranskning för virtuella Azure-datorer till en sekundär Azure region (förhandsgranskning)

Den [Azure Site Recovery](../site-recovery-overview.md) tjänsten bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att hålla dina appar och köra tillgängliga under planerade och oplanerade avbrott. Site Recovery samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning hanterar.

Den här kursen visar hur du kör en katastrof återställningsgranskning för en Azure-VM från en Azure-region till en annan, med ett redundanstest. Visa verifierar din replikeringsstrategi för utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera krav
> * Köra ett redundanstest för en enskild virtuell dator

## <a name="prerequisites"></a>Krav

- Innan du kör ett redundanstest rekommenderar vi att du kontrollerar VM-egenskaper för att kontrollera att allt är som förväntat.  Komma åt VM-egenskaper i **replikerade objekt**. Den **Essentials** bladet visar information om inställningar för datorer och status.
- Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorn för att testa redundans och inte standardnätverk som skapades när du har aktiverat replikering.


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I **inställningar** > **replikerade objekt**, klicka på den virtuella datorn **+ testa redundans** ikon.

2. I **Redundanstest**, Välj en återställningspunkt som ska användas för redundans:

   - **Senaste bearbetas**: växlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid bearbetning av data, så att den ger en låga RTO (mål)
   - **Senaste programkonsekventa**: det här alternativet flyttas över alla virtuella datorer till den senaste programkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj någon annan återställningspunkt.

3. Välj målobjektet för Azure-nätverk till vilka virtuella Azure-datorer i den sekundära regionen ansluts efter att växlingen sker.

4. Starta växlingen vid fel, klicka på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på den **Redundanstest** jobb i valvnamnet > **inställningar** > **jobb** > **Site Recovery-jobb**.
5. När redundansväxlingen är klar repliken virtuella Azure-datorn visas i Azure-portalen > **virtuella datorer**. Kontrollera att den virtuella datorn körs, storlek på rätt sätt och ansluten till lämpligt nätverk.
6. Ta bort de virtuella datorerna som skapades under redundanstestningen, klicka på **Rensa redundanstestet** på det replikerade objektet eller återställningsplanen. I **anteckningar**, registrera och spara observationer från redundanstestningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör en produktions-redundans](azure-to-azure-tutorial-failover-failback.md)
