---
title: Skapa en zoned Windows virtuell dator med Azure-portalen | Microsoft Docs
description: Skapa en virtuell Windows-dator i en zon för tillgänglighet med Azure-portalen
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321996"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Skapa en virtuell Windows-dator i en zon för tillgänglighet med Azure-portalen

Den här artikeln steg genom att använda Azure portal för att skapa en virtuell dator i en Azure tillgänglighet zonen. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en zon för tillgänglighet, skapa den virtuella datorn i en [stöd för Azure-region](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 

3. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm). Välj en plats, till exempel östra USA 2 som har stöd för tillgänglighet zoner. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Välj en storlek för den virtuella datorn. Välj en rekommenderad storlek eller filtrera baserat på funktioner. Bekräfta storleken är tillgängligt i zonen som du vill använda.

    ![Välj en VM-storlek](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Under **inställningar** > **hög tillgänglighet**, Välj någon av zonerna numrerade från den **tillgänglighet zonen** listrutan Behåll återstående standardvärdena och Klicka på **OK**.

    ![Välj en zon för tillgänglighet](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. På sidan Sammanfattning klickar du på **skapa** att starta distributionen av virtuella datorer.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bekräfta zon för hanterade diskar och IP-adress

När den virtuella datorn distribueras i en zon för tillgänglighet, skapas en hanterade diskar för den virtuella datorn i samma zon tillgänglighet. Som standard skapas också en offentlig IP-adress i zonen.

Du kan bekräfta Zoninställningar för dessa resurser i portalen.  

1. Klicka på **resursgrupper** och sedan namnet på resursen grupp för den virtuella datorn som *myResourceGroup*.

2. Klicka på namnet på diskresursen. Den **översikt** sidan innehåller information om zonen plats och tillgänglighet av resursen.

    ![Zonen för tillgänglighet för hanterade diskar](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klicka på namnet på den offentliga IP-adressresursen. Den **översikt** sidan innehåller information om zonen plats och tillgänglighet av resursen.

    ![Zonen för tillgänglighet för IP-adress](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en zon för tillgänglighet. Läs mer om [regioner och tillgänglighet](regions-and-availability.md) för virtuella Azure-datorer.
