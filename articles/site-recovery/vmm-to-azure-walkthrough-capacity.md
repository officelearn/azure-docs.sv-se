---
title: "Planera kapacitet och skalning för Virtuella Hyper-V-replikering (med VMM) till Azure med Azure Site Recovery | Microsoft Docs"
description: "Använd den här artikeln för att planera kapaciteten och skala vid replikering av Hyper-V virtuella datorer i VMM-moln till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>Steg 3: Planera kapacitet och skalning för Hyper-V (med VMM) till Azure-replikering

När du har granskat den [kraven för distribution av](vmm-to-azure-walkthrough-prerequisites.md), använda den här artikeln för att ta reda på planera för kapacitet och skala vid replikering av lokala Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM)-moln till Azure med [Azure Site Recovery](site-recovery-overview.md).

När du har läst den här artikeln efter eventuella kommentarer längst ned eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="how-do-i-start-capacity-planning"></a>Hur börjar jag kapacitetsplanering?


Du samla in information om replikeringsmiljön och planera kapacitet med hjälp av data, tillsammans med överväganden markerat i den här artikeln.


## <a name="gather-information"></a>Samla in information

1. Samla in information om replikeringsmiljön, inklusive virtuella datorer, diskar per virtuell dator och lagringsutrymme per disk.
2. Identifiera dina dagliga (omsättningen) förändringstakten för replikerade data. Hämta den [Hyper-V kapacitetsplanering verktyget](https://www.microsoft.com/download/details.aspx?id=39057) få förändringstakten. Vi rekommenderar att du kör det här verktyget över en vecka att avbilda medelvärden.
 

## <a name="figure-out-capacity"></a>Ta reda på kapaciteten

Utifrån den information som du har samla kan du köra den [Site Recovery Kapacitetsplaneringsverktyget för](http://aka.ms/asr-capacity-planner-excel) beräkna bandbreddsbehov och serverresurser för källplatsen och resurser (virtuella datorer och lagring osv), som du behöver på målplatsen för att analysera dina källmiljön och arbetsbelastningar. Du kan köra verktyget på ett par olika lägen:

- Snabb planering: kör verktyget i det här läget för att hämta nätverks- och projektioner baserat på ett genomsnittligt antal virtuella datorer, diskar, lagring och förändringstakten.
- Detaljerad planering: köra verktyget i det här läget och ger information för varje arbetsbelastning på VM-nivå. Analysera VM-kompatibilitet och får nätverks- och projektioner.

Nu köra verktyget:

1. Hämta den [verktyget](http://aka.ms/asr-capacity-planner-excel)
2. Så här kör du snabbt planner [instruktionerna](site-recovery-capacity-planner.md#run-the-quick-planner), och välj scenariot **Hyper-V till Azure**.
3. Så här kör du detaljerad planner [instruktionerna](site-recovery-capacity-planner.md#run-the-detailed-planner), och välj scenariot **Hyper-V till Azure**.

## <a name="control-network-bandwidth"></a>Kontrollera nätverksbandbredd

När du har beräknat bandbredd som du behöver ha ett antal alternativ för att styra hur mycket bandbredd som används för replikering:

* **Begränsa bandbredden**: Hyper-V-trafik som replikeras till Azure går igenom en specifik Hyper-V-värd. Du kan begränsa bandbredden på värdservern.
* **Påverka bandbredd**: du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden
1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värdservern. Som standard finns det en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. På fliken **Begränsning** väljer du **Aktivera användningsbegränsning för Internetbandbredd för säkerhetskopieringsåtgärder** och ange begränsningarna för arbetstid och övrig tid. Giltiga intervall är från 512 kbit/s till 102 Mbit/s.

    ![Begränsa bandbredden](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="influence-network-bandwidth"></a>Påverka nätverkets bandbredd
1. Gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** i registret.
   * Om du vill påverka bandbredd trafiken på en replikering disk ändrar du värdet i **UploadThreadsPerVM**, eller skapa nyckeln om den inte finns.
   * Ändra värdet för att påverka bandbredden för redundanstrafik från Azure **DownloadThreadsPerVM**.
2. Standardvärdet är 4. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det högsta antalet är 32. Övervaka trafiken för att optimera värdet.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 4: Planera nätverk](vmm-to-azure-walkthrough-network.md).
