---
title: Konfigurera källinställningar för VMware-haveriberedskap till Azure med Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar din lokala miljö för att replikera virtuella virtuella datorer med VMware till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257061"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurera källmiljön för VMware till Azure replication

I den här artikeln beskrivs hur du konfigurerar den lokala källmiljön för att replikera virtuella virtuella datorer med VMware till Azure. Artikeln innehåller steg för att välja replikeringsscenario, konfigurera en lokal dator som konfigurationsserver för platsåterställning och automatiskt identifiera lokala virtuella datorer.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter att du redan har:

- Planerade distributionen med hjälp av [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Detta hjälper dig att allokera tillräckligt med bandbredd, baserat på din dagliga dataförändringshastighet, för att uppfylla önskat återställningspunktsmål (RPO).
- [Ställ in resurser](tutorial-prepare-azure.md) i [Azure-portalen](https://portal.azure.com).
- [Ställ in lokalt VMware](vmware-azure-tutorial-prepare-on-premises.md), inklusive ett dedikerat konto för automatisk identifiering.

## <a name="choose-your-protection-goals"></a>Välj dina skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina maskiner**väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

## <a name="set-up-the-configuration-server"></a>Konfigurera konfigurationsservern

Du kan ställa in konfigurationsservern som en lokal virtuell virtuell dator med hjälp av en OVA-mall (Open Virtualization Application). [Läs mer](concepts-vmware-to-azure-architecture.md) om de komponenter som ska installeras på virtuella VMware-datorer.

1. Lär dig mer om förutsättningarna för distribution av [konfigurationsserver.](vmware-azure-deploy-configuration-server.md#prerequisites)
2. [Kontrollera kapacitetsnummer](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) för distribution.
3. [Hämta](vmware-azure-deploy-configuration-server.md#download-the-template) och [importera](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA-mallen för att konfigurera en lokal virtuell virtuell dator med VMware som kör konfigurationsservern. Licensen som medföljer mallen är en utvärderingslicens och är giltig i 180 dagar. Bokför denna period, måste kunden aktivera fönstren med en upphandlad licens.
4. Aktivera virtuella VMware-datorn och [registrera](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) den i valvet för Återställningstjänster.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Undantag för Azure Site Recovery-mapp från antivirusprogram

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Om antivirusprogram är aktivt på källmaskinen

Om källdatorn har ett antivirusprogram aktivt bör installationsmappen uteslutas. Så, uteslut mapp *C:\ProgramData\ASR\agent* för smidig replikering.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Om antivirusprogram är aktivt på konfigurationsservern

Exkludera följande mappar från antivirusprogram för smidig replikering och för att undvika anslutningsproblem

- C:\Program-filer\Microsoft Azure Recovery Services Agent.
- C:\Program\Microsoft Azure Site Recovery Provider
- C:\Program\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\tredje part
  - C:\Temp
  - C:\jordgubbe
  - C:\ProgramData\MySQL
  - C:\Programfiler (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Återställning av Microsoft Azure-webbplatser
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Installationskatalog för serverinstallation av platsåterställning. Till exempel: E:\Program-filer (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Om antivirusprogram är aktivt på utskalningsprocessserver/huvudmål

Utesluta följande mappar från antivirusprogram

1. C:\Program\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Återställning av Microsoft Azure-webbplatser
7. Azure Site Recovery belastningsbalanserad process serverinstallationskatalog, Exempel: C:\Program-filer (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Nästa steg
[Konfigurera din målmiljö](./vmware-azure-set-up-target.md) 
