---
title: Konfigurera ett virtuellt nätverk i Azure DevTest Labs | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "70390039"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurera ett virtuellt nätverk i Azure DevTest Labs
Som förklaras i artikeln [lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md), kan du ange ett konfigurerat virtuellt nätverk när du skapar en virtuell dator i ett labb. Du kan till exempel behöva komma åt dina Corpnet-resurser från dina virtuella datorer med hjälp av det virtuella nätverket som har kon figurer ATS med ExpressRoute eller VPN för plats till plats.

I den här artikeln beskrivs hur du lägger till ditt befintliga virtuella nätverk i Labbets Virtual Network inställningar så att du kan välja när du skapar virtuella datorer.

> [!NOTE]
> Mer information om kostnader som är associerade med Azure Virtual Network-tjänsten finns i [prissättning för azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurera ett virtuellt nätverk för ett labb med hjälp av Azure Portal
Följande steg beskriver hur du lägger till ett befintligt virtuellt nätverk (och undernät) i ett labb så att det kan användas när du skapar en virtuell dator i samma labb. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du önskat labb. 
1. Välj **konfiguration och principer**i Labbets huvud fönster.

    ![Få till gång till Labbets konfiguration och principer](./media/devtest-lab-configure-vnet/policies-menu.png)
1. I avsnittet **externa resurser** väljer du **virtuella nätverk**. En lista över virtuella nätverk som har kon figurer ATS för det aktuella labbet visas samt det virtuella standard nätverk som har skapats för ditt labb. 
1. Välj **+ Lägg till**.
   
    ![Lägg till ett befintligt virtuellt nätverk i labbet](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. I fönstret **virtuellt nätverk** väljer du **[Välj virtuellt nätverk]**.
   
    ![Välj ett befintligt virtuellt nätverk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. I fönstret **Välj virtuellt nätverk** väljer du önskat virtuellt nätverk. En lista visas som visar alla virtuella nätverk som finns under samma region i prenumerationen som labbet.
1. När du har valt ett virtuellt nätverk kommer du tillbaka till fönstret **virtuellt nätverk** . Välj under nätet i listan längst ned.

    ![Under näts lista](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Rutan labb under nät visas.

    ![Rutan labb under nät](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Ange ett **namn på labb under nätet**.
   - Om du vill tillåta att ett undernät används i labb skapande av **virtuella datorer väljer du Använd i Skapa virtuell dator**.
   - Om du vill aktivera en [delad offentlig IP-adress](devtest-lab-shared-ip.md)väljer du **aktivera delad offentlig IP**.
   - Om du vill tillåta offentliga IP-adresser i ett undernät väljer du **Tillåt att offentlig IP skapas**.
   - I fältet **maximalt antal virtuella datorer per användare** anger du maximalt antal virtuella datorer per användare för varje undernät. Lämna fältet tomt om du vill ha ett obegränsat antal virtuella datorer.
1. Välj **OK** för att stänga fönstret labb under nät.
1. Stäng fönstret virtuellt nätverk genom att välja **Spara** .

Nu när det virtuella nätverket har kon figurer ATS kan du välja det när du skapar en virtuell dator. Information om hur du skapar en virtuell dator och anger ett virtuellt nätverk finns i artikeln [lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md). 

Azures [Virtual Network dokumentation](https://docs.microsoft.com/azure/virtual-network) innehåller mer information om hur du använder virtuella nätverk, inklusive hur du konfigurerar och hanterar ett VNet och ansluter det till ditt lokala nätverk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har lagt till önskat virtuellt nätverk i labbet är nästa steg att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

