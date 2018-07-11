---
title: Redundansväxla och återställa virtuella Hyper-V-datorer som replikeras till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du växlar över Hyper-V-datorer till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: f758939964045ed373703a211d4cbef00f0e42e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919554"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Redundans och återställning efter fel Hyper-V-datorer som replikeras till Azure

Den här självstudien beskrivs hur du redundans en Hyper-V-dator till Azure. När du har redundansväxlat, du återställer till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera Hyper-V VM-egenskaperna för att kontrollera överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Återställning efter fel från Azure till lokalt
> * Omvänd replikera lokala virtuella datorer för att börja replikera till Azure igen

Den här självstudien är den femte självstudien i en serie. Det förutsätts att du redan har slutfört uppgifterna i de föregående självstudierna.    

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurera haveriberedskap för [Hyper-V-datorer](tutorial-hyper-v-to-azure.md), eller för [Hyper-V-datorer som hanteras i System Center VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Kontrollera att det finns inga ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn stängs av under återställning efter fel. Det hjälper till att säkerställa datakonsekvens vid replikeringen. Inte aktivera en lokal virtuell dator under återställningen. 

Redundans och återställning efter fel har tre steg:

1. **Redundans till Azure**: redundans Hyper-V-datorer från den lokala platsen till Azure.
2. **Växla tillbaka till den lokala**: redundans virtuella Azure-datorer till den lokala platsen när den lokala platsen är tillgänglig. Den börjar synkronisera data från Azure till lokalt och på slutförande, får alla virtuella datorer på lokalt.  
3. **Omvänd replikering lokala virtuella datorer**: efter misslyckade tillbaka till den lokala omvänd replikera lokala virtuella datorer att börja replikera dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera VM-egenskaperna före redundans och se till att den virtuella datorn uppfyller med [krav för Azure](hyper-v-azure-support-matrix.md#replicated-vms).

I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och hanterade diskinställningar.

4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="failover-to-azure"></a>Redundans till Azure

1. I **inställningar** > **replikerade objekt**, klickar du på den virtuella datorn > **redundans**.
2. I **redundans**väljer den **senaste** återställningspunkt. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker göra stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. När du har kontrollerat växling vid fel, klickar du på **genomför**. Alla tillgängliga återställningspunkter tas bort.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Om du avbryter pågående redundans så stoppas, men den virtuella datorn kommer inte att replikeras igen.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Återställning efter fel virtuella Azure-datorer till en lokal plats och omvänd replikera lokala virtuella datorn

Åtgärden för återställning efter fel är i princip en redundans från Azure till lokal plats och i omvänd replikering replikera datorer från den lokala platsen till Azure påbörjas igen.

1. I **inställningar** > **replikerade objekt**, klickar du på den virtuella datorn > **planerad redundans**.
2. I **bekräfta planerad redundans**kontrollerar redundansriktning (från Azure) och välj käll- och målplatserna.
3. Välj **synkronisera data före redundansväxling (synkronisera endast deltaändringar)**. Det här alternativet minskar stilleståndstid på virtuella datorer eftersom den synkroniserar utan att stänga av den virtuella datorn.
4. Påbörja redundans. Du kan följa redundansförloppet på den **jobb** fliken.
5. När den inledande synkroniseringen är klar och du är redo att Stäng av den virtuella Azure-datorer klickar du på **jobb** > planerat-redundans-jobb-name > **slutföra växling vid fel**. Den stängs av Azure VM, överför de senaste ändringarna på plats och startar den lokala virtuella datorn.
6. Logga in på den lokala virtuella datorn att kontrollera att den är tillgänglig som förväntat.
7. Den lokala virtuella datorn är nu i en **bekräfta väntande** tillstånd. Klicka på **genomför**. Det tar bort virtuella Azure-datorer och dess diskar och förbereder den lokala virtuella datorn för omvänd replikering.
Börja replikera lokala virtuella datorn till Azure genom att aktivera **omvänd replikera**. Utlöser replikering av deltaändringar som har inträffat sedan den virtuella Azure-datorn har stängts av.  
