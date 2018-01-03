---
title: "Kör en katastrof återställningsgranskning för lokala datorer till Azure med Azure Site Recovery | Microsoft Docs"
description: "Lär dig mer om med disaster recovery detaljgranska från lokala till Azure med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Köra ett programåterställningstest till Azure

Den här kursen visar hur du kör en katastrof återställningsgranskning för en lokal dator till Azure med hjälp av ett redundanstest. Visa verifierar din replikeringsstrategi för utan dataförlust. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ställ in ett isolerat nätverk för att testa redundans
> * Förbereda för att ansluta till den virtuella Azure-datorn efter redundans
> * Köra ett redundanstest för en enskild dator

Detta är den fjärde vägledningen i en serie. Den här kursen förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-vmware.md)
3. [Konfigurera haveriberedskap](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Kontrollera egenskaperna för VM

Innan du kör ett redundanstest Kontrollera egenskaper för Virtuella datorer och kontrollera att den virtuella datorn uppfyller [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. I **skyddade objekt**, klickar du på **replikerade objekt** > VM.
2. I den **replikerade objekt** , det finns en sammanfattning av VM-information, hälsostatus, och den senaste tillgängliga återställningspunkter. Klicka på **egenskaper** visa mer information.
3. I **beräknings- och nätverksinställningar**, kan du ändra Azure namn, resursgruppens namn, Målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md), och hanterade inställningar för disk.
   
      >[!NOTE]
      Återställning till det lokala Hyper-V-datorer från Azure virtuella datorer med hanterade diskar stöds inte för närvarande. Du bör endast använda alternativet hanterade diskar för växling vid fel om du planerar att migrera lokala virtuella datorer till Azure utan misslyckas dem igen.
   
4. Du kan visa och ändra inställningar för nätverk, inklusive de undernät eller det nätverk som virtuella Azure-datorn kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den.
5. I **diskar**, visas information om operativsystem och datadiskar på den virtuella datorn.

## <a name="run-a-test-failover-for-a-single-vm"></a>Köra ett redundanstest för en enskild virtuell dator

När du kör ett redundanstest händer följande:

1. En kravkontroll körs ska du kontrollera att alla de villkor som krävs för växling vid fel är på plats.
2. Redundans bearbetar data, så att du kan skapa en Azure VM. Om väljer skapas den senaste återställningspunkten, en återställningspunkt från data.
3. En Azure VM skapas med hjälp av de data som behandlas i föregående steg.

Kör redundanstestet på följande sätt:

1. I **inställningar** > **replikerade objekt**, klicka på den virtuella datorn > **+ testa redundans**.
2. Välj den **senaste bearbetas** återställningspunkt för den här kursen. Detta växlar den virtuella datorn till den senaste punkten i tid. Tidsstämpeln visas. Med det här alternativet läggs ingen tid bearbetning av data, så att den ger en låga RTO (mål).
3. I **Redundanstest**, Välj det Azure ska vara anslutet nätverk till vilka virtuella Azure-datorer efter redundansväxlingen.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på den **Redundanstest** jobb i valvnamnet > **inställningar** > **jobb** >
   **Site Recovery-jobb**.
5. När redundansväxlingen är klar repliken virtuella Azure-datorn visas i Azure-portalen > **virtuella datorer**. Kontrollera att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. Ta bort Azure virtuella datorer som skapades under redundanstestningen, klicka på **Rensa redundanstestet** på den virtuella datorn. I **anteckningar**, registrera och spara observationer från redundanstestningen.

I vissa scenarier kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter för att slutföra. Du kanske ser längre testa redundans tider för VMware Linux-datorer, virtuella VMware-datorer som inte har möjliggör DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör en redundans och återställning efter fel för lokala virtuella VMware-datorer](tutorial-vmware-to-azure-failover-failback.md).
