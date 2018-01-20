---
title: "Förbereda datorer för att ställa in katastrofåterställning mellan Azure-regioner efter migrering till Azure med hjälp av Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du förbereda datorer för att ställa in katastrofåterställning mellan Azure-regioner efter migrering till Azure med hjälp av Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: ponatara
ms.openlocfilehash: fe7966d1817dc7f7896435f9202d2bcea981b5f3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Replikera virtuella Azure-datorer till en annan region efter migrering till Azure med hjälp av Azure Site Recovery

>[!NOTE]
> Azure Site Recovery replikering för virtuella Azure-datorer (VM) är för närvarande under förhandsgranskning.

## <a name="overview"></a>Översikt

Den här artikeln hjälper dig att förbereda virtuella Azure-datorer för replikering mellan två Azure-regioner när dessa datorer har migrerats från en lokal miljö till Azure med hjälp av Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Katastrofåterställning och efterlevnad
Idag Flyttar fler och fler företag sina arbetsbelastningar till Azure. Med företag flytta verksamhetskritiska lokalt produktionsarbetsbelastningar till Azure, ställa in katastrofåterställning för dessa arbetsbelastningar är obligatoriskt för efterlevnad och för att skydda mot eventuella avbrott i en Azure-region.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Steg för att förbereda migrerade datorer för replikering
Så här förbereder du har migrerat datorer för att konfigurera replikering till en annan Azure-region:

1. Slutföra migreringen.
2. Installera Azure-agenten om det behövs.
3. Ta bort mobilitetstjänsten.  
4. Starta om den virtuella datorn.

Dessa steg beskrivs i detalj i följande avsnitt.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Steg 1: Migrera arbetsbelastningar som körs på Hyper-V virtuella datorer, virtuella VMware-datorer och fysiska servrar körs på virtuella Azure-datorer

Konfigurera replikering och migrera dina lokala Hyper-V, VMware och fysiska arbetsbelastningar till Azure, följer du stegen i den [migrera Azure IaaS-virtuella datorer mellan Azure-regioner med Azure Site Recovery](site-recovery-migrate-azure-to-azure.md) artikel. 

Efter migreringen behöver du inte spara eller ta bort en växling vid fel. I stället väljer den **slutföra migreringen** alternativet för varje dator som du vill migrera:
1. I **Replikerade objekt** högerklickar du på den virtuella datorn och på **Slutför migrering**. Klicka på **OK** att slutföra steget. Du kan följa förloppet i Egenskaper för Virtuella datorer genom att övervaka jobbet slutföra migrering i **Site Recovery-jobb**.
2. Den **slutföra migreringen** åtgärden har slutförts migreringsprocessen, tar bort replikering för datorn och stoppar Site Recovery-faktureringen för datorn.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Steg 2: Installera Azure VM-agenten på den virtuella datorn
Azure [VM-agenten](../../virtual-machines/windows/classic/agents-and-extensions-classic.md#azure-vm-agents-for-windows-and-linux) måste vara installerad på den virtuella datorn för Site Recovery-tillägget ska fungera och för att skydda den virtuella datorn.

>[!IMPORTANT]
>Från och med version 9.7.0.0, på Windows-datorer installerar Mobilitetstjänstens installationsprogram också den senaste tillgängliga Virtuella Azure-agenten. Den virtuella datorn uppfyller installationen av nödvändiga för att använda alla VM-tillägg, inklusive tillägget Site Recovery på migreringen. Virtuella Azure-agenten måste installeras manuellt endast om mobilitetstjänsten installeras på den migrerade datorn är version 9,6 eller tidigare.

Följande tabell innehåller ytterligare information om att installera den Virtuella datoragenten och verifiera att den har installerats:

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation av VM-agenten |Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. |Installera senaste [Linux-agenten](../../virtual-machines/linux/agent-user-guide.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar agenten från databasen för din distribution. Vi *rekommenderar inte* Linux VM-agenten installeras direkt från GitHub.  |
| Verifiera installationen av VM |1. Bläddra till mappen C:\WindowsAzure\Packages i Azure-VM. Du bör se filen WaAppAgent.exe. <br>2. Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Den **produktversionen** fältet måste innehålla 2.6.1198.718 eller högre. |Gäller inte |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Steg 3: Ta bort mobilitetstjänsten från den migrerade virtuella datorn

Om du har migrerat dina lokala VMware-datorer eller fysiska servrar på Windows-/ Linux, måste du manuellt ta bort eller avinstallera mobilitetstjänsten från den migrerade virtuella datorn.

>[!IMPORTANT]
>Det här steget krävs inte för Hyper-V virtuella datorer migreras till Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Avinstallera mobilitetstjänsten på en Windows Server-VM
Använd någon av följande metoder för att avinstallera mobilitetstjänsten på Windows Server-dator.

##### <a name="uninstall-by-using-the-windows-ui"></a>Avinstallera med hjälp av Windows-Användargränssnittet
1. I Kontrollpanelen, Välj **program**.
2. Välj **Microsoft Azure Site Recovery Mobility tjänsten eller Huvudtjänsten målservern**, och välj sedan **avinstallera**.

##### <a name="uninstall-at-a-command-prompt"></a>Avinstallera i en kommandotolk
1. Öppna ett kommandotolksfönster som administratör.
2. Om du vill avinstallera mobilitetstjänsten, kör du följande kommando:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Avinstallera mobilitetstjänsten på en Linux-dator
1. Logga in på Linux-servern som en **rot** användare.
2. Gå till /user/local/ASR i en terminal.
3. Om du vill avinstallera mobilitetstjänsten, kör du följande kommando:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Steg 4: Starta om den virtuella datorn

När du avinstallerar mobilitetstjänsten startar du om den virtuella datorn innan du konfigurerar replikering till en annan Azure-region.


## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbetsbelastningar av [replikering av Azure virtuella datorer](../azure-to-azure-quickstart.md).
- Lär dig mer om [nätverk vägledning för att replikera virtuella datorer i Azure](../site-recovery-azure-to-azure-networking-guidance.md).
