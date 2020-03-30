---
title: Uppgradera Windows Server/System Center VMM 2012 R2 till Windows Server 2016-Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap till Azure för virtuella Azure Stack-datorer med Azure Site Recovery-tjänsten.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954411"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Uppgradera Windows Server Server/System Center 2012 R2 VMM till Windows Server/VMM 2016 

I den här artikeln beskrivs hur du uppgraderar Windows Server 2012 R2-värdar & SCVMM 2012 R2 som är konfigurerade med Azure Site Recovery till Windows Server 2016 & SCVMM 2016

Site Recovery bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR). Tjänsten säkerställer att dina VM-arbetsbelastningar förblir tillgängliga när förväntade och oväntade avbrott inträffar.

> [!IMPORTANT]
> När du uppgraderar Windows Server 2012 R2-värdar som redan är konfigurerade för replikering med Azure Site Recovery måste du följa stegen som nämns i det här dokumentet. En alternativ sökväg som valts för uppgradering kan resultera i tillstånd som inte stöds och kan resultera i en avbrott i replikering eller möjlighet att utföra redundans.


I den här artikeln får du lära dig hur du uppgraderar följande konfigurationer i din miljö:

> [!div class="checklist"]
> * **Windows Server 2012 R2-värdar som inte hanteras av SCVMM** 
> * **Windows Server 2012 R2-värdar som hanteras av en fristående SCVMM 2012 R2-server** 
> * **Windows Server 2012 R2-värdar som hanteras av scvmm 2012 R2-server som är tillgängliga för högtillgänglig SCVMM 2012 R2**


## <a name="prerequisites--factors-to-consider"></a>Förutsättningar & faktorer att beakta

Innan du uppgraderar bör du tänka på följande: -

- Om du har Windows Server 2012 R2-värdar som inte hanteras av SCVMM och det är en fristående miljökonfiguration, kommer det att finnas en paus i replikeringen om du försöker utföra uppgraderingen.
- Om du hade valt "*inte lagra mina nycklar i Active Directory under Distributed Key Management*" när du installerar SCVMM 2012 R2 i första hand, kommer uppgraderingarna inte att slutföras.

- Om du använder System Center 2012 R2 VMM 

    - Kontrollera databasinformationen om VMM: **VMM-konsolinställningar** -> **settings** -> **Allmän** -> **databasanslutning**
    - Kontrollera de tjänstkonton som används för tjänsten System Center Virtual Machine Manager Agent
    - Kontrollera att du har en säkerhetskopia av VMM-databasen.
    - Notera databasnamnet för de berörda SCVMM-servrarna. Detta kan göras genom att navigera till **VMM konsol** -> **Inställningar** -> **Allmän** -> **databasanslutning**
    - Notera ner VMM-ID för både 2012R2 primära och återvinning VMM-servrar. VMM-ID finns i registret "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Se till att du den nya SCVMMs som du lägger till i klustret har samma namn som var tidigare. 

- Om du replikerar mellan två av dina webbplatser som hanteras av SCVMMs på båda sidor, se till att du uppgraderar återställningssidan först innan du uppgraderar den primära sidan.
  > [!WARNING]
  > När du uppgraderar SCVMM 2012 R2 väljer du att **lagra krypteringsnycklar i Active Directory**under Hantering av distribuerad nyckel under Distribuerad nyckel. Välj inställningarna för tjänstkontot och hantering av distribuerade nyckel noggrant. Baserat på ditt val kanske krypterade data som lösenord i mallar inte är tillgängliga efter uppgraderingen och kan eventuellt påverka replikeringen med Azure Site Recovery

> [!IMPORTANT]
> Se den detaljerade SCVMM-dokumentationen av [förutsättningarna](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-värdar som inte hanteras av SCVMM 
Listan över steg som nämns nedan gäller användarkonfigurationen från [Hyper-V-värdar till Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) som körs genom att följa den här [självstudien](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Som nämnts i förutsättningarna gäller dessa steg endast för ett grupperat miljöscenario och inte i en fristående Hyper-V-värdkonfiguration.

1. Följ stegen för att utföra den [rullande klusteruppgraderingen.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) för att köra uppgraderingsprocessen för rullande kluster.
2. Med varje ny Windows Server 2016-värd som introduceras i klustret tar du bort referensen för en Windows Server 2012 R2-värd från Azure Site Recovery genom att följa följande steg som nämns [här]. Detta bör vara den värd du valde att tömma & bortkräpar från klustret.
3. När kommandot *Update-VMVersion* har körts för alla virtuella datorer har uppgraderingarna slutförts. 
4. Använd stegen [som](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) nämns här för att registrera den nya Windows Server 2016-värden på Azure Site Recovery. Observera att Hyper-V-webbplatsen redan är aktiv och att du bara behöver registrera den nya värden i klustret. 
5.  Gå till Azure-portalen och verifiera den replikerade hälsostatusen i Återställningstjänsterna

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar som hanteras av fristående SCVMM 2012 R2-server
Innan du uppgraderar dina Windows Sever 2012 R2-värdar måste du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följ stegen nedan: -

**Uppgradera fristående SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att navigera till Kontrollpanelen -> program -> program och funktioner – >Microsoft Azure Site Recovery och klicka på Avinstallera
2. [Behåll SCVMM-databasen och uppgradera operativsystemet](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Välj **VMM** > **Avinstallera**i Lägg **till program**. b. Välj **Ta bort funktioner**och välj sedan V**MM-hanteringsserver och VMM-konsol**. c. I **Databasalternativ**väljer du **Behåll databasen**. d. Granska sammanfattningen och klicka på **Avinstallera**.

4. [Installera VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Starta SCVMM och kontrollera status för varje **Refresh** värdar under **fliken Tyger.** Du bör se status som "Behöver uppmärksamhet". 
17. Installera den senaste [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) på SCVMM.
16. Installera den senaste [MICROSOFT Azure Recovery Service (MARS)-agenten](https://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att säkerställa att SCVMM kan fråga värdarna.

**Uppgradera Windows Server 2012 R2-värdar till Windows Server 2016**

1. Följ stegen som nämns [här](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) för att köra uppgraderingsprocessen för rullande kluster. 
2. När du har lagt till den nya värden i klustret uppdaterar du värden från SCVMM-konsolen för att installera VMM-agenten på den uppdaterade värden.
3. Kör *Update-VMVersion* för att uppdatera vm-versionerna av de virtuella datorerna. 
4.  Gå till Azure-portalen och verifiera den replikerade hälsostatusen för de virtuella datorerna i Recovery Services Vault. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar hanteras av SCVMM 2012 R2-server som är tillgänglig med högtillgänglig
Innan du uppgraderar dina Windows Sever 2012 R2-värdar måste du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följande uppgraderingslägen stöds vid uppgradering av SCVMM 2012 R2-servrar som konfigurerats med Azure Site Recovery - Blandat läge utan ytterligare VMM-servrar & blandat läge med ytterligare VMM-servrar.

**Uppgradera SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att navigera till Kontrollpanelen -> program -> program och funktioner – >Microsoft Azure Site Recovery och klicka på Avinstallera
2. Följ stegen som nämns [här](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) baserat på det uppgraderingsläge du vill köra.
3. Starta SCVMM-konsolen och kontrollera status för varje **Refresh** värdar under fliken **Tyger.** Du bör se status som "Behöver uppmärksamhet".
4. Installera den senaste [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) på SCVMM.
5. Uppdatera den senaste [MICROSOFT Azure Recovery Service (MARS)-agenten](https://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att säkerställa sc VMM kan framgångsrikt fråga värdarna.


**Uppgradera Windows Server 2012 R2-värdar till Windows Server 2016**

1. Följ stegen som nämns [här](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) för att köra uppgraderingsprocessen för rullande kluster.
2. När du har lagt till den nya värden i klustret uppdaterar du värden från SCVMM-konsolen för att installera VMM-agenten på den uppdaterade värden.
3. Kör *Update-VMVersion* för att uppdatera vm-versionerna av de virtuella datorerna. 
4.  Gå till Azure-portalen och verifiera den replikerade hälsostatusen för de virtuella datorerna i Recovery Services Vault. 

## <a name="next-steps"></a>Nästa steg
När uppgraderingen av värdarna har utförts kan du utföra en [testundans för](tutorial-dr-drill-azure.md) att testa hälsotillståndet för din replikerings- och haveriberedskapsstatus.

