---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure-portalen | Microsoft Docs
description: "Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Mall](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker distributionsmodell hanteraren för filserverresurser, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Skapa en virtuell dator med en statisk offentlig IP-adress

Om du vill skapa en virtuell dator med en statisk offentlig IP-adress i Azure-portalen, gör du följande:

1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på det övre vänstra hörnet av portalen **ny**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. I den **Välj en distributionsmodell** väljer **Resource Manager** och på **skapa**.
4. I den **grunderna** bladet ange VM-information som visas nedan och klickar sedan på **OK**.
   
    ![Azure portal – grunderna](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. I den **välja en storlek** bladet, klickar du på **A1 Standard** enligt nedan, och klicka sedan på **Välj**.
   
    ![Azure portal – Välj en storlek](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. I den **inställningar** bladet, klickar du på **offentliga IP-adressen**, i den **skapa offentlig IP-adress** bladet under **tilldelning**, klickar du på  **Statisk** enligt nedan. Och klicka sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. I den **inställningar** bladet, klickar du på **OK**.
8. Granska de **sammanfattning** bladet som visas nedan och klickar sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Lägg märke till den nya ikonen på instrumentpanelen.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. När den virtuella datorn har skapats kan den **inställningar** bladet visas enligt nedan
    
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

