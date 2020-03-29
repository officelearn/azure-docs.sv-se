---
title: Konfigurera ett virtuellt nätverk i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du konfigurerar ett befintligt virtuellt nätverk och undernät och använder dem i en virtuell dator med Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390039"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurera ett virtuellt nätverk i Azure DevTest Labs
Som förklaras i artikeln [Lägg till en virtuell dator i ett labb](devtest-lab-add-vm.md), när du skapar en virtuell dator i ett labb, kan du ange ett konfigurerat virtuellt nätverk. Du kan till exempel behöva komma åt corpnet-resurser från dina virtuella datorer med hjälp av det virtuella nätverket som konfigurerats med ExpressRoute eller plats-till-plats-VPN.

I den här artikeln beskrivs hur du lägger till det befintliga virtuella nätverket i labbets inställningar för virtuella nätverk så att det är tillgängligt att välja när du skapar virtuella datorer.

> [!NOTE]
> Mer information om kostnader som är associerade med Azure Virtual Network-tjänsten finns i [Priser för Azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurera ett virtuellt nätverk för ett labb med Azure-portalen
Följande steg går igenom att lägga till ett befintligt virtuellt nätverk (och undernät) i ett labb så att det kan användas när du skapar en virtuell dator i samma labb. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. Välj önskat labb i listan över labb. 
1. I labbets huvudfönster väljer du **Konfiguration och principer**.

    ![Få tillgång till labbets konfiguration och principer](./media/devtest-lab-configure-vnet/policies-menu.png)
1. I avsnittet **EXTERNA RESURSER** väljer du **Virtuella nätverk**. En lista över virtuella nätverk som konfigurerats för det aktuella labbet visas samt det virtuella standardnätverk som skapats för labbet. 
1. Välj **+ Lägg till**.
   
    ![Lägga till ett befintligt virtuellt nätverk i labbet](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Välj **[Välj virtuellt nätverk]** i fönstret **Virtuellt nätverk** .
   
    ![Välj ett befintligt virtuellt nätverk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Välj önskat virtuellt nätverk i fönstret **Välj virtuellt nätverk.** En lista visas som visar alla virtuella nätverk som finns under samma region i prenumerationen som labbet.
1. När du har valt ett virtuellt nätverk returneras du till fönstret **Virtuellt nätverk.** Markera undernätet i listan längst ned.

    ![Lista över undernät](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Fönstret Lab-undernät visas.

    ![Fönstret Labbundernät](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Ange ett **labbundernätnamn**.
   - Om du vill tillåta att ett undernät används i skapandet av virtuella datorer väljer du **Använd i skapandet av virtuella datorer**.
   - Om du vill aktivera en [delad offentlig IP-adress](devtest-lab-shared-ip.md)väljer du **Aktivera delad offentlig IP**.
   - Om du vill tillåta offentliga IP-adresser i ett undernät väljer du **Tillåt offentlig IP-generering**.
   - I fältet **Maximalt antal virtuella datorer per användare** anger du de maximala virtuella datorerna per användare för varje undernät. Om du vill ha ett obegränsat antal virtuella datorer lämnar du det här fältet tomt.
1. Stäng fönstret Lab-undernät genom att välja **OK.**
1. Välj **Spara** om du vill stänga fönstret Virtuellt nätverk.

Nu när det virtuella nätverket är konfigurerat kan det väljas när du skapar en virtuell dator. Information om hur du skapar en virtuell dator och anger ett virtuellt nätverk finns i artikeln Lägga till [en virtuell dator i ett labb](devtest-lab-add-vm.md). 

Azures [dokumentation för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network) innehåller mer information om hur du använder virtuella nätverk, inklusive hur du konfigurerar och hanterar ett virtuellt nätverk och ansluter det till ditt lokala nätverk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har lagt till det önskade virtuella nätverket i labbet är nästa steg att lägga till [en virtuell dator i labbet](devtest-lab-add-vm.md).

