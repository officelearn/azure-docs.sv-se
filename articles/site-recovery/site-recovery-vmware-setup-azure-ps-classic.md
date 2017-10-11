---
title: " Hantera en Server för Process som körs i Azure(Classic) | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in en återställning efter fel processen Server(Classic) i Azure."
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
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-a-process-server-running-in-azure-classic"></a>Hantera en Server för Process som körs i Azure (klassisk)
> [!div class="op_single_selector"]
> * [Klassiska Azure-portalen](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

Under återställning efter fel rekommenderas att distribuera Processerver i Azure om det är fördröjningar mellan det virtuella Azure-nätverket och ditt lokala nätverk. Den här artikeln beskriver hur du kan skapa, konfigurera och hantera processen-servrar som körs i Azure.

> [!NOTE]
> Den här artikeln som ska användas om du använde klassisk som distributionsmodell för de virtuella datorerna under växling vid fel. Om du använde Resource Manager som distributionsmodell följer du stegen i [hur du ställer in och konfigurera en Processerver (Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

## <a name="prerequisites"></a>Krav

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Distribuera en Processerver i Azure

1. I Azure Marketplace, skapar du en virtuell dator med hjälp av den **Microsoft Azure Site Recovery Process Server V2** </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. Se till att du väljer distributionsmodellen som **klassiska** </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. I guiden Skapa virtuell dator > grundläggande inställningar, se till att du väljer prenumeration och plats där du misslyckades för virtuella datorer.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. Kontrollera att den virtuella datorn är ansluten till det virtuella Azure-nätverket som den redundansväxlade virtuella datorn är ansluten.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. När den virtuella datorn på Processervern har etablerats måste du logga in och registrera den med konfigurationsservern.

> [!NOTE]
> För att kunna använda den här Processerver för återställning efter fel, måste du registrera den med konfigurationsservern lokalt.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera Processervern (som körs i Azure) till en Server för konfiguration (som körs lokalt)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Uppgradera Processervern till senaste versionen.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Avregistrera Processervern (som körs i Azure) från en Server för konfiguration (som körs lokalt)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
