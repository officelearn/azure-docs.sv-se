---
title: Skapa en zonplanerad Windows-virtuell dator med Azure-portalen
description: Skapa en Virtuell Windows-dator i en tillgänglighetszon med Azure-portalen
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 418d02b1f271efc1112b67e5dd12b6ba67a3fcd9
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086189"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Skapa en virtuell Windows-dator i en tillgänglighetszon med Azure-portalen

Den här artikeln går igenom att använda Azure-portalen för att skapa en virtuell dator i en Azure-tillgänglighetszon. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en tillgänglighetszon skapar du din virtuella dator i en [Azure-region som stöds](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 

3. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm). Välj en plats som Östra USA 2 som stöder tillgänglighetszoner. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Välj en storlek för den virtuella datorn. Välj en rekommenderad storlek eller ett filter baserat på funktioner. Bekräfta att storleken är tillgänglig i den zon som du vill använda.

    ![Välj en vm-storlek](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Under **Inställningar** > **hög tillgänglighet**väljer du en av de numrerade zonerna i listrutan **Tillgänglighetszon,** behåller de återstående standardinställningarna och klickar på **OK**.

    ![Välj en tillgänglighetszon](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. På sammanfattningssidan klickar du på **Skapa** för att starta distributionen av den virtuella datorn.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bekräfta zon för hanterad disk- och IP-adress

När den virtuella datorn distribueras i en tillgänglighetszon skapas en hanterad disk för den virtuella datorn i samma tillgänglighetszon. Som standard skapas också en offentlig IP-adress i den zonen.

Du kan bekräfta zoninställningarna för dessa resurser i portalen.  

1. Klicka på **Resursgrupper** och sedan namnet på resursgruppen för den virtuella datorn, till exempel *myResourceGroup*.

2. Klicka på namnet på diskresursen. Sidan **Översikt** innehåller information om resursens plats- och tillgänglighetszon.

    ![Tillgänglighetszon för hanterad disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klicka på namnet på den offentliga IP-adressresursen. Sidan **Översikt** innehåller information om resursens plats- och tillgänglighetszon.

    ![Tillgänglighetszon för IP-adress](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Läs mer om [tillgänglighet](availability.md) för virtuella Azure-datorer.
