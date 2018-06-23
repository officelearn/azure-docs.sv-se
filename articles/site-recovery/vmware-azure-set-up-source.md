---
title: Konfigurera källmiljön för VMware på Azure replikering med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in din lokala miljö att replikera virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 01b0717660265b28d4ea7d804a761e7e425c997c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319608"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurera källmiljön för VMware till Azure-replikering

Den här artikeln beskriver hur du ställer in din lokala miljö källa för att replikera virtuella VMware-datorer till Azure. Den inkluderar steg för att välja replikering scenario kan ställa in en lokal dator som konfigurationsservern Site Recovery och automatiskt identifiera lokala virtuella datorer. 

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:
- [Ange resurser](tutorial-prepare-azure.md) i den [Azure-portalen](http://portal.azure.com).
- [Konfigurera lokal VMware](vmware-azure-tutorial-prepare-on-premises.md), inklusive ett särskilt konto för automatisk identifiering.



## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valvet bladet och välj ditt valv.
2. På menyn resurs på valvet, gå till **komma igång** > **Site Recovery** > **steg 1: Förbered infrastrukturen**  >  **Skyddsmål**.

    ![Välja mål](./media/vmware-azure-set-up-source/choose-goals.png)
3. I **skyddsmål**väljer **till Azure**, och välj **Ja, med VMware vSphere-Hypervisor**. Klicka sedan på **OK**.

    ![Välja mål](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

Du kan ställa in konfigurationsservern som en lokal VMware VM via en mall för Open Virtualization program (ägg). [Lär dig mer](concepts-vmware-to-azure-architecture.md) om komponenter som ska installeras på VMware VM.

1. Lär dig mer om den [krav](vmware-azure-deploy-configuration-server.md#prerequisites) för distribution av konfigurationsserver.
2. [Kontrollera kapacitet siffror](vmware-azure-deploy-configuration-server.md#capacity-planning) för distribution.
3. [Hämta](vmware-azure-deploy-configuration-server.md#download-the-template) och [importera](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) mallen ägg för att konfigurera en lokal VMware virtuell dator som kör konfigurationsservern. Licensen som ingår i mallen är en licens för utvärdering och är giltig i 180 dagar. Efter måste denna tid kunden aktivera windows med upphandlade licens.
4. Aktivera VM VMware och [registrera den](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) i Recovery Services-valvet.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Lägg till VMware-konto för automatisk identifiering

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Ansluta till VMware-server

Du måste ansluta VMware vCenter-servern eller vSphere ESXi-värdar med Site Recovery för att tillåta Azure Site Recovery att identifiera virtuella datorer som körs i din lokala miljö.

Välj **+ vCenter** att starta ansluter en VMware vCenter-server eller en VMware vSphere ESXi-värd.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg
[Konfigurera målmiljön](./vmware-azure-set-up-target.md) i Azure.
