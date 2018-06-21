---
title: Växla över och misslyckas tillbaka Hyper-V VM replikerat till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du växla över Hyper-V virtuella datorer till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294300"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Redundans och återställning av Hyper-V VM replikerat till Azure

Den här självstudiekursen beskriver hur till en Hyper-V virtuell dator till Azure-redundans. När du inte har kunnat över återställning av du den lokala platsen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera egenskaperna Hyper-V-dator för att kontrollera överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Återställning från Azure till lokala
> * Omvänd replikera lokala virtuella datorer för att starta replikering till Azure igen

Den här kursen är den femte vägledningen i en serie. Det förutsätts att du redan har slutfört uppgifterna i de föregående självstudierna.    

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Ställ in katastrofåterställning för [Hyper-V VMs](tutorial-hyper-v-to-azure.md), eller för [Hyper-V-datorer som hanteras i System Center VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Kontrollera att det finns inga ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn är avstängd under återställning efter fel. Det hjälper att säkerställa datakonsekvens vid replikering. Stäng inte på den lokala virtuella datorn vid återställning. 

Redundans och återställning efter fel har tre steg:

1. **Redundans till Azure**: redundans Hyper-V virtuella datorer från den lokala platsen till Azure.
2. **Återställning till det lokala**: redundans virtuella Azure-datorer till din lokala plats när den är tillgänglig. Startar replikera VMs tillbaka till lokala Hyper-V virtuella datorer. Efter inledande datasynkronisering, redundans virtuella Azure-datorer till den lokala platsen.  
3. **Omvänd replikera lokala virtuella datorer**: efter att data har misslyckats tillbaka omvänd replikera lokala virtuella datorer starta replikera dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera egenskaper för Virtuella datorer före redundans och kontrollerar du att den virtuella datorn uppfyller med [krav för Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. I **skyddade objekt**, klickar du på **replikerade objekt** >< VM-name >.

2. I den **replikerade objekt** fönstret granska informationen om VM, hälsotillstånd och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
     - I **beräknings- och nätverksinställningar**, du kan ändra inställningarna för virtuell dator och inställningar för nätverk, inklusive de /-undernätet där den virtuella Azure-datorn kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den. Hanterade diskar stöds inte för återställning efter fel från Azure till Hyper-V.
      - I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="failover-to-azure"></a>Redundans till Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.
2. I **redundans** markerar du den **senaste** återställningspunkt. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker göra en avstängning av virtuella källdatorer innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. När du har kontrollerat redundans Klicka **genomför**. Alla tillgängliga återställningspunkter tas bort.

> [!WARNING]
> **Inte avbryta en växling pågår**: Om du avbryter pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Återställning av virtuella Azure-datorn lokalt och omvänd replikera lokala VM

Åtgärden för återställning efter fel är i princip en växling vid fel från Azure till den lokala platsen och i omvänd replikering den startar igen replikera virtuella datorer från den lokala platsen till Azure.

1. I **inställningar** > **replikerade objekt**, klicka på den virtuella datorn > **planerad redundans**.
2. I **bekräfta planerad redundans**kontrollerar redundansriktning (från Azure) och välj käll- och målplatserna.
3. Välj **synkronisera data innan redundans (synkronisera endast deltaändringar)**. Det här alternativet minimerar avbrottstid för virtuell dator eftersom det synkroniserar utan att stänga av den virtuella datorn.
4. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
5. När den inledande synkroniseringen är klar och du är redo att stänga av den virtuella Azure-datorer klickar du på **jobb** > planerad-redundans-jobb-name > **slutföra växling vid fel**. Stängs av Azure VM, överför den senaste ändringar lokalt och startar den virtuella datorn lokalt.
6. Logga in på den lokala virtuella datorn att kontrollera att den är tillgänglig som förväntat.
7. Den lokala virtuella datorn är nu i en **bekräfta väntande** tillstånd. Klicka på **genomför**. Det tar bort den virtuella Azure-datorer och dess diskar och förbereder den lokala virtuella datorn för omvänd replikering.
Starta replikering av den lokala virtuella datorn till Azure genom att aktivera **omvänd replikera**. Utlöser replikering av deltaändringar som har inträffat sedan den virtuella Azure-datorn är avstängd.  
