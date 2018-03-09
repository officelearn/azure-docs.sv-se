---
title: " Hantera en processerver som körs i Azure (Resource Manager) | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in en processerver (Resource Manager) i Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Hantera en processerver som körs i Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Klassisk ](./site-recovery-vmware-setup-azure-ps-classic.md)

Under återställning efter fel rekommenderas att distribuera processerver i Azure om det är fördröjningar mellan det virtuella Azure-nätverket och ditt lokala nätverk. Den här artikeln beskriver hur du kan skapa, konfigurera och hantera processen-servrar som körs i Azure.

> [!NOTE]
> Den här artikeln som ska användas om du har använt **Resource Manager** som distributionsmodell för de virtuella datorerna under växling vid fel. Om du använde **klassiska** som distributionsmodell, följer du stegen i [hur du ställer in och konfigurera en processerver (klassisk)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Distribuera en processerver i Azure
1. I valvet > **Site Recovery-infrastruktur** (under rubriken ”hantera”) > **Konfigurationsservrar** (under ”för VMware och fysiska datorer” rubrik), väljer du konfigurationsservern.
2. På sidan konfigurationsservern information som öppnas klickar du på ”+ bearbeta server”

  ![Lägg till Galleri för process-server](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  På den **Lägg till processerver** väljer du följande värden

  ![Lägg till galleriobjektet för processervern](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Fältnamn**|**Värde**|
|-|-|
|Välj var processervern ska distribueras|Välj värdet **distribuera en processerver i Azure** |
|Prenumeration|Välj den Azure-prenumeration där du redundansväxlade virtuella datorer|
|Resursgrupp|Du kan skapa en resursgrupp för att distribuera den här processen servern eller välja att distribuera processervern i en befintlig resursgrupp|
|Plats|Välj Azure-datacenter där de virtuella datorerna där redundansväxlats till|
|Azure-nätverk|Välj den virtuella Azure-Network(VNet) som de virtuella datorerna där redundansväxlats till. Om du redundansväxlade virtuella datorer i flera virtuella Azure-nätverk måste en processerver distribueras per virtuellt nätverk|

4. Fyll i resten av egenskaperna för processervern

  ![Lägg till processerver sammanfattning](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Fältnamn**|**Värde**|
|-|-|
|Servernamn|Visningsnamnet & värdnamnet för den virtuella datorn på processen|
| Användarnamn|Ett användarnamn som är administratör på den virtuella datorn|
|Lagringskonto|Namnet på det Lagringskonto där den virtuella datorns virtuella disken placeras|
|Undernät|Undernätet i Azure-VNet som den virtuella datorn är ansluten|
| IP-adress|IP-adress som du vill att processervern kan ta över en gång det startar|
5. Klicka på OK för att börja distribuera den virtuella datorn på processen.

> [!NOTE]
> För att kunna använda den här processerver för återställning efter fel, måste du registrera den med konfigurationsservern lokalt.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processervern (som körs i Azure) till en Server för konfiguration (som körs lokalt)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Uppgradera processervern till senaste versionen.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Avregistrera processervern (som körs i Azure) från en Server för konfiguration (som körs lokalt)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
