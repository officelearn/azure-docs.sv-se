---
title: Uppgradera Windows Server 2012 R2-värdar och SCVMM som konfigurerats med Azure Site Recovery till Windows Server 2016
description: Lär dig hur du konfigurerar haveriberedskap till Azure för virtuella datorer i Azure Stack med Azure Site Recovery-tjänsten.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 351213749dcec2b4c16728c04230c75a12179118
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411025"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Uppgradera Windows Server 2012 R2-värdar, SCVMM 2012 R2 som konfigurerats med Azure Site Recovery till Windows Server 2016 & SCVMM 2016

Den här artikeln visar hur du uppgraderar Windows Server 2012 R2-värdar och SCVMM 2012 R2 som har konfigurerats med Azure Site Recovery till Windows Server 2016 & SCVMM 2016

Site Recovery bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi. Tjänsten säkerställer att dina VM-arbetsbelastningar är tillgängliga när förväntade och oväntade avbrott inträffar.

> [!IMPORTANT]
> När du uppgraderar Windows Server 2012 R2-värdar som redan har konfigurerats för replikering med Azure Site Recovery måste du följa stegen i det här dokumentet. Alla alternativ sökväg som valts för uppgraderingen kan resultera i ett tillstånd som inte stöds och kan resultera i ett avbrott i replikering eller möjligheten att utföra redundans.


I den här artikeln får du lära dig uppgradera de följande konfigurationerna i din miljö:

> [!div class="checklist"]
> * **Windows Server 2012 R2-värdar som inte hanteras av SCVMM** 
> * **Windows Server 2012 R2-värdar som hanteras av en fristående SCVMM 2012 R2-server** 
> * **Windows Server 2012 R2-värdar som hanteras av SCVMM 2012 R2-server med hög tillgänglighet**


## <a name="prerequisites--factors-to-consider"></a>Krav och faktorer att tänka på

Observera följande innan du uppgraderar:-

- Om du har Windows Server 2012 R2-värdar som inte hanteras av SCVMM och det är en fristående miljö, debiteras en radbrytning i replikering om du försöker utföra uppgraderingen.
- Om du har valt ”*inte lagra Mina nycklar i Active Directory under hantering av distribuerad nyckel*” när du installerar SCVMM 2012 R2 i första hand uppgraderingarna kommer inte att slutföra.

- Om du använder VMM för System Center 2012 R2, 

    - Kontrollera databasinformation på VMM: **VMM-konsolen** -> **inställningar** -> **Allmänt** -> **databasanslutningen**
    - Kontrollera service-konton som används för System Center Virtual Machine Manager Agent-tjänsten
    - Se till att du har en säkerhetskopia av VMM-databasen.
    - Anteckna namnet på databasen för de berörda servrarna i SCVMM. Detta kan göras genom att gå till **VMM-konsolen** -> **inställningar** -> **Allmänt** -> **databasanslutningen**
    - Anteckna VMM-ID för 2012R2 primära såväl recovery VMM-servrar. ID för VMM finns i registret ”HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup”.
    - Kontrollera att du de nya SCVMMs som du lägger till i klustret har samma namn som hade tidigare. 

- Om du replikerar mellan två av du är dina webbplatser som hanteras av SCVMMs på båda sidorna, se till att du uppgraderar din återställningssidan först innan du uppgraderar den primära sidan.
> [!WARNING]
> När du uppgraderar SCVMM 2012 R2 under hantering av distribuerad nyckel, väljer du att **lagra krypteringsnycklar i Active Directory**. Välj noga inställningarna för tjänstkonto och hantering av distribuerad nyckel. Baserat på ditt val av påverka krypterade data som lösenord i mallar inte kan vara tillgängliga efter uppgraderingen och kan potentiellt replikeringen med Azure Site Recovery

> [!IMPORTANT]
> Läs den detaljerade SCVMM-dokumentationen för [krav](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-värdar som inte hanteras av SCVMM 
Listan över åtgärderna som nämns nedan gäller för Användarkonfiguration från [Hyper-V-värdar till Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) körs genom att följa det här [självstudien](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Enligt kraven, gäller de här stegen endast för en klustrad miljö scenario och inte i konfigurationen för en fristående Hyper-V-värd.

1. Följ stegen för att utföra den [löpande uppgradering av klustret.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) att köra den löpande uppgraderingen för kluster.
2. Med varje ny Windows Server 2016-värd som introduceras i klustret, att ta bort referensen till en Windows Server 2012 R2-värd från Azure Site Recovery genom att följa anvisningarna [här]. Det bör vara den värd som du har valt att tömma & Ta bort från klustret.
3. När den *uppdatering VMVersion* kommando har körts för alla virtuella datorer, uppgraderingen har slutförts. 
4. Använd stegen [här](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) att registrera den nya värden för Windows Server 2016 till Azure Site Recovery. Observera att Hyper-V-platsen är redan aktiv och du behöver bara registrera den nya värden i klustret. 
5.  Gå till Azure-portalen och kontrollera replikerade hälsostatus i Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar som hanteras av fristående SCVMM 2012 R2-server
Innan du uppgraderar dina Windows Server 2012 R2-värdar kan behöva du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följ de stegen nedan:-

**Uppgradera fristående SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att gå till Kontrollpanelen -> program -> program och funktioner -> Microsoft Azure Site Recovery och klicka på Avinstallera
2. [Behålla SCVMM-databasen och uppgradera operativsystemet](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. I **Lägg till ta bort program**väljer **VMM** > **avinstallera**. b. Välj **ta bort funktioner**, och välj sedan V**MM hanteringsservern och VMM-konsolen**. c. I **databasalternativ**väljer **Spara databas**. d. Granska sammanfattningen och klicka på **avinstallera**.

4. [Installera VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Starta SCVMM och kontrollera statusen för varje värdar under **Infrastrukturresurser** fliken. Klicka på **uppdatera** att hämta senaste status. Du bör se status som ”Värdstatus”. 
17. Installera senast [Microsoft Azure Site Recovery-providern](http://aka.ms/downloaddra) på SCVMM.
16. Installera senast [Microsoft Azure Recovery Service MARS-agenten](http://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att säkerställa SCVMM kan ställa frågor värdarna.

**Uppgradera Windows Server 2012 R2-värdar i Windows Server 2016**

1. Följ stegen [här](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) att köra den löpande uppgraderingen för kluster. 
2. Uppdatera värden från SCVMM-konsolen om du vill installera VMM-agenten på den här uppdaterade värden när du lägger till den nya värden i klustret.
3. Köra *uppdatering VMVersion* att uppdatera VM-versioner av de virtuella datorerna. 
4.  Gå till Azure-portalen och kontrollera replikerade hälsotillståndet för de virtuella datorerna i Recovery Services-valvet. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar som hanteras av SCVMM 2012 R2-server med hög tillgänglighet
Innan du uppgraderar dina Windows Server 2012 R2-värdar kan behöva du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följande lägen av uppgraderingen stöds när du uppgraderar SCVMM 2012 R2-servrar som konfigurerats med Azure Site Recovery - blandat läge utan några ytterligare VMM-servrar och blandat läge med ytterligare VMM-servrar.

**Uppgradera SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att gå till Kontrollpanelen -> program -> program och funktioner -> Microsoft Azure Site Recovery och klicka på Avinstallera
2. Följ stegen [här](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) baserat på läget för uppgradering som du vill köra.
3. Starta SCVMM-konsolen och kontrollera statusen för varje värdar under **Infrastrukturresurser** fliken. Klicka på **uppdatera** att hämta senaste status. Du bör se status som ”Värdstatus”.
4. Installera senast [Microsoft Azure Site Recovery-providern](http://aka.ms/downloaddra) på SCVMM.
5. Uppdatera senast [Microsoft Azure Recovery Service MARS-agenten](http://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att säkerställa SC VMM kan ställa frågor värdarna.


**Uppgradera Windows Server 2012 R2-värdar i Windows Server 2016**

1. Följ stegen [här](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) att köra den löpande uppgraderingen för kluster.
2. Uppdatera värden från SCVMM-konsolen om du vill installera VMM-agenten på den här uppdaterade värden när du lägger till den nya värden i klustret.
3. Köra *uppdatering VMVersion* att uppdatera VM-versioner av de virtuella datorerna. 
4.  Gå till Azure-portalen och kontrollera replikerade hälsotillståndet för de virtuella datorerna i Recovery Services-valvet. 

## <a name="next-steps"></a>Nästa steg
När uppgraderingen av värdarna utförs, kan du utföra en [redundanstest](tutorial-dr-drill-azure.md) att testa hälsotillståndet för din datareplikering och återställning vid recovey status.

