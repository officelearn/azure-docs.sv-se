---
title: "Konfigurera källmiljön (VMware till Azure) | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in din lokala miljö för att starta replikera virtuella VMware-datorer till Azure."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Konfigurera källmiljön (VMware till Azure)
> [!div class="op_single_selector"]
> * [VMware till Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fysiska till Azure](./site-recovery-set-up-physical-to-azure.md)

Den här artikeln beskriver hur du ställer in källfilerna lokala miljö att replikera virtuella datorer som körs på VMware till Azure. Den inkluderar steg för att välja replikering scenario kan ställa in en lokal dator som konfigurationsservern Site Recovery och automatiskt identifiera lokala virtuella datorer. 

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:
- [Ange resurser](tutorial-prepare-azure.md) i den [Azure-portalen](http://portal.azure.com).
- [Konfigurera lokal VMware](tutorial-prepare-on-premises-vmware.md), inklusive ett särskilt konto för automatisk identifiering.



## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valvet bladet och välj ditt valv.
2. På menyn resurs på valvet, gå till **komma igång** > **Site Recovery** > **steg 1: Förbered infrastrukturen**  >  **Skyddsmål**.

    ![Välja mål](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. I **skyddsmål**väljer **till Azure**, och välj **Ja, med VMware vSphere-Hypervisor**. Klicka sedan på **OK**.

    ![Välja mål](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

Du har skapat konfigurationsservern som en lokal VMware VM, använda en mall för Open Virtualization Format (OVF). [Lär dig mer](concepts-vmware-to-azure-architecture.md) om komponenter som ska installeras på VMware VM. 

1. Lär dig mer om den [krav](how-to-deploy-configuration-server.md#prerequisites) för distribution av konfigurationsserver. [Kontrollera kapacitet siffror](how-to-deploy-configuration-server.md#capacity-planning) för distribution.
2. [Hämta](how-to-deploy-configuration-server.md#download-the-template) och [importera](how-to-deploy-configuration-server.md#import-the-template-in-vmware) mallen OVF (how-till-distribuera-konfiguration – server.md) för att ställa in en lokal VMware virtuell dator som kör konfigurationsservern.
3. Aktivera VM VMware och [registrera den](how-to-deploy-configuration-server.md#register-the-configuration-server) i Recovery Services-valvet.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Lägg till VMware-konto för automatisk identifiering

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Ansluta till VMware-server

Du måste ansluta VMware vCenter-servern eller vSphere ESXi-värdar med Site Recovery för att tillåta Azure Site Recovery att identifiera virtuella datorer som körs i din lokala miljö.

Välj **+ vCenter** att starta ansluter en VMware vCenter-server eller en VMware vSphere ESXi-värd.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg
[Konfigurera målmiljön](./site-recovery-prepare-target-vmware-to-azure.md) i Azure.
