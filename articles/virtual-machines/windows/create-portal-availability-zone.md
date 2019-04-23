---
title: Skapa en zonindelat Windows-VM med Azure-portalen | Microsoft Docs
description: Skapa en virtuell Windows-dator i en tillgänglighetszon med Azure portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e813b26a91d25fbaa1298acd455f27d2cac705f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997061"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Skapa en Windows-dator i en tillgänglighetszon med Azure portal

Den här artikeln beskriver hur med Azure portal för att skapa en virtuell dator i en Azure-tillgänglighetszon. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en tillgänglighetszon skapar du din virtuella dator i en [Azure-region som stöds](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 

3. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm). Välj en plats, till exempel USA, östra 2 som har stöd för tillgänglighetszoner. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Välj en storlek för den virtuella datorn. Välj en rekommenderad storlek eller filter baserat på funktioner. Bekräfta storleken är tillgängligt i zonen som du vill använda.

    ![Välj en VM-storlek](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Under **inställningar** > **hög tillgänglighet**, Välj någon av de numrerade zonerna från den **tillgänglighetszon** listrutan Behåll standardinställningarna för återstående inställningar, och Klicka på **OK**.

    ![Välj en tillgänglighetszon](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. På sidan Sammanfattning klickar du på **skapa** att starta distributionen av virtuella datorn.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bekräfta zon för hanterad disk och IP-adress

När den virtuella datorn har distribuerats i en tillgänglighetszon, skapas en hanterad disk för den virtuella datorn i samma tillgänglighetszon. Som standard skapas också en offentlig IP-adress i zonen.

Du kan bekräfta Zoninställningar för dessa resurser i portalen.  

1. Klicka på **resursgrupper** och sedan namnet på resursen för den virtuella datorn, till exempel *myResourceGroup*.

2. Klicka på namnet på diskresursen. Den **översikt** sidan innehåller information om plats och tillgänglighetszonen för resursen.

    ![Tillgänglighetszon för hanterad disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klicka på namnet på den offentliga IP-adressresursen. Den **översikt** sidan innehåller information om plats och tillgänglighetszonen för resursen.

    ![Tillgänglighetszon för IP-adress](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Läs mer om [regioner och tillgänglighet](regions-and-availability.md) för virtuella Azure-datorer.
