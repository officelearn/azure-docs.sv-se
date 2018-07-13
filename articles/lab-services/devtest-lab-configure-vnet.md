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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 0141ea8a88c0322e6f56cbea56d3a43c923769af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687796"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurera ett virtuellt nätverk i Azure DevTest Labs
Enligt beskrivningen i artikeln [lägga till en virtuell dator i en labb](devtest-lab-add-vm.md), när du skapar en virtuell dator i ett labb, du kan ange ett virtuellt nätverk som är konfigurerade. Du kan behöva åtkomst till företagsnätets resurser från dina virtuella datorer med hjälp av det virtuella nätverk som har konfigurerats med ExpressRoute eller VPN för plats-till-plats.

Den här artikeln förklarar hur du lägger till det befintliga virtuella nätverket till ett labb inställningar för virtuella nätverk så att den ska kunna välja när du skapar virtuella datorer.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurera ett virtuellt nätverk för ett labb med hjälp av Azure portal
I följande steg vägleder dig genom att lägga till ett befintligt virtuellt nätverk och undernät som ett labb så att den kan användas när du skapar en virtuell dator i samma labbet. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Listan över labbar, Välj önskade labbet. 
1. På den testmiljön huvudfönstret väljer **konfiguration och principer**.

    ![Använda testmiljön konfigurations- och principer](./media/devtest-lab-configure-vnet/policies-menu.png)
1. I den **externa resurser** väljer **virtuella nätverk**. En lista över virtuella nätverk som konfigurerats för den aktuella testmiljön visas samt den virtuellt standardnätverk som skapats för ditt labb. 
1. Välj **+ Lägg till**.
   
    ![Lägga till ett befintligt virtuellt nätverk till ett labb](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. På den **virtuellt nätverk** väljer **[Välj ett virtuellt nätverk]**.
   
    ![Välj ett befintligt virtuellt nätverk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. På den **Välj ett virtuellt nätverk** fönstret, Välj det önskade virtuella nätverket. En lista visas med alla virtuella nätverk som är under prenumeration som labbet samma region.
1. När du har valt ett virtuellt nätverk som du kommer tillbaka till den **virtuellt nätverk** fönstret. Välj undernätet i listan längst ned på sidan.

    ![Lista över undernät](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Fönstret Lab undernät visas.

    ![Lab undernät fönstret](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Ange en **Lab undernätsnamn**.
   - Om du vill tillåta ett undernät som ska användas i labb skapa en virtuell dator, Välj **användning i Skapa en virtuell dator**.
   - Att aktivera en [delad offentlig IP-adress](devtest-lab-shared-ip.md)väljer **aktivera delad offentlig IP-adress**.
   - Om du vill tillåta offentliga IP-adresser i ett undernät väljer **får den offentliga IP-skapas**.
   - I den **maximalt virtuella datorer per användare** fältet, ange de maximala virtuella datorerna per användare för varje undernät. Lämna fältet tomt om du vill att ett obegränsat antal virtuella datorer.
1. Välj **OK** att Stäng fönstret Lab undernät.
1. Välj **spara** att Stäng fönstret virtuella nätverk.

Nu när det virtuella nätverket har konfigurerats, kan du välja den när du skapar en virtuell dator. Information om hur du skapar en virtuell dator och ange ett virtuellt nätverk finns i artikeln [lägga till en virtuell dator i en labb](devtest-lab-add-vm.md). 

Azures [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network) innehåller mer information om hur du använder virtuella nätverk, inklusive hur du konfigurerar och hanterar ett VNet och ansluter den till ditt lokala nätverk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har lagt till det önskade virtuella nätverket till ditt labb, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

