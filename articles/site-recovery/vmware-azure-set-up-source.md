---
title: Konfigurera källmiljön för VMware till Azure-replikering med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in din lokala miljö att replikera virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952662"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurera källmiljön för VMware till Azure replikering

Den här artikeln beskriver hur du ställer in din lokala miljö källa för att replikera virtuella VMware-datorer till Azure. Den inkluderar steg för att välja din replikeringsscenariot, hur du konfigurerar en lokal dator som konfigurationsservern Site Recovery och automatiskt identifiera lokala virtuella datorer. 

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:
- [Konfigurera resurser](tutorial-prepare-azure.md) i den [Azure-portalen](http://portal.azure.com).
- [Konfigurera en lokal VMware](vmware-azure-tutorial-prepare-on-premises.md), inklusive ett särskilt konto för automatisk identifiering.



## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valv bladet och välj ditt valv.
2. På resurs-menyn för valvet går du till **komma igång** > **Site Recovery** > **steg 1: förbereda infrastrukturen**  >  **Skyddsmål**.

    ![Välja mål](./media/vmware-azure-set-up-source/choose-goals.png)
3. I **skyddsmål**väljer **till Azure**, och välj **Ja, med VMware vSphere Hypervisor**. Klicka sedan på **OK**.

    ![Välja mål](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

Du kan konfigurera konfigurationsservern som en lokal VMware VM via en mall för Open Virtualization program (OVA). [Läs mer](concepts-vmware-to-azure-architecture.md) om komponenter som ska installeras på VMware VM.

1. Lär dig mer om den [krav](vmware-azure-deploy-configuration-server.md#prerequisites) för distribution av konfigurationsserver.
2. [Kontrollera kapaciteten](vmware-azure-deploy-configuration-server.md#capacity-planning) för distribution.
3. [Ladda ned](vmware-azure-deploy-configuration-server.md#download-the-template) och [importera](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA-mallen för att konfigurera en lokal VMware VM som kör konfigurationsservern. Licensen som medföljer mallen är en licens för utvärdering och är giltig i 180 dagar. Efter måste denna period kunden aktivera windows med upphandlade licens.
4. Aktivera på VMware-VM och [registrera den](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) i Recovery Services-valv.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Lägg till VMware-konto för automatisk identifiering

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Anslut till VMware-servern

Om du vill tillåta Azure Site Recovery att identifiera virtuella datorer som körs i din lokala miljö, måste du ansluta din VMware vCenter-servern eller vSphere ESXi-värdar med Site Recovery.

Välj **+ vCenter** att starta ansluter en VMware vCenter-server eller VMware vSphere ESXi-värden.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg
[Konfigurera målmiljön](./vmware-azure-set-up-target.md) i Azure.
