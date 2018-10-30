---
title: Konfigurera källmiljön för haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in din lokala miljö för haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 44b1cac461fa49b4bea19b7cf98f6038eb264492
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230734"
---
# <a name="set-up-the-source-environment-for-disaster-recovery-of-vmware-vms-to-azure"></a>Konfigurera källmiljön för haveriberedskap för virtuella VMware-datorer till Azure

Den här artikeln beskriver hur du ställer in din lokala miljö källa för att replikera virtuella VMware-datorer till Azure. Den inkluderar steg för att välja din replikeringsscenariot, hur du konfigurerar en lokal dator som konfigurationsservern Site Recovery och automatiskt identifiera lokala virtuella datorer. 

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:

- Planerat distributionen med hjälp av [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Detta hjälper dig att allokera tillräckligt mycket bandbredd, baserat på din dagliga dataändringshastighet, att uppfylla dina önskade mål för återställningspunkt (RPO).
- [Konfigurera resurser](tutorial-prepare-azure.md) i den [Azure-portalen](http://portal.azure.com).
- [Konfigurera en lokal VMware](vmware-azure-tutorial-prepare-on-premises.md), inklusive ett särskilt konto för automatisk identifiering.

## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

## <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

Du kan konfigurera konfigurationsservern som en lokal VMware VM via en mall för Open Virtualization program (OVA). [Läs mer](concepts-vmware-to-azure-architecture.md) om komponenter som ska installeras på VMware VM.

1. Lär dig mer om den [krav](vmware-azure-deploy-configuration-server.md#prerequisites) för distribution av konfigurationsserver.
2. [Kontrollera kapaciteten](vmware-azure-deploy-configuration-server.md#capacity-planning) för distribution.
3. [Ladda ned](vmware-azure-deploy-configuration-server.md#download-the-template) och [importera](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA-mallen för att konfigurera en lokal VMware VM som kör konfigurationsservern. Licensen som medföljer mallen är en licens för utvärdering och är giltig i 180 dagar. Efter måste denna period kunden aktivera windows med upphandlade licens.
4. Aktivera på VMware-VM och [registrera den](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) i Recovery Services-valv.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery mappar som ska undantas från antivirusprogram

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Om antivirusprogrammet är aktiv på källdatorn

Om källdatorn har en aktiv antivirusprogram, ska installationsmappen uteslutas. Därför exkludera mapp *C:\ProgramData\ASR\agent* för smooth replikering.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Om Antivirus Software är aktiv på konfigurationsservern

Undanta följande mappar från antivirusprogrammet för smooth replikering och för att undvika problem med nätverksanslutningen

 1. C:\Program Files\Microsoft Azure Recovery Services-agenten.
 2. C:\Program Files\Microsoft Azure Site Recovery-providern
 3. C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
 4. C:\Program Files\Microsoft Azure Site Recovery fel samling verktyg 
 5. C:\thirdparty
 6. C:\Temp
 7. C:\strawberry
 8. C:\ProgramData\MySQL
 9. C:\Program filer (x86) \MySQL
 10. C:\ProgramData\ASR
 11. C:\ProgramData\Microsoft Azure Site Recovery
 12. C:\ProgramData\ASRLogs
 13. C:\ProgramData\ASRSetupLogs
 14. C:\ProgramData\LogUploadServiceLogs
 15. C:\Inetpub
 16. ASR installationskatalog. Till exempel: E:\Program filer (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Om Antivirus Software är aktiv i skalbar bearbeta/Huvudmålservern mål

Undanta följande mappar från antivirusprogram

1. C:\Program Files\Microsoft Azure Recovery Services-agenten
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. ASR belastningsutjämnade installationskatalog processen, exempel: C:\Program Files (x86) \Microsoft Azure Site Recovery

## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nästa steg
[Konfigurera målmiljön](./vmware-azure-set-up-target.md) i Azure.
