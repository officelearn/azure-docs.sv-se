---
title: "Konfigurera ett virtuellt nätverk i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du konfigurerar ett befintligt virtuellt nätverk och undernät och använda dem i en virtuell dator med Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: v-craic
ms.openlocfilehash: 21daa8ff756ee30c6d454d49af7db20afe488c47
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurera ett virtuellt nätverk i Azure DevTest Labs
Enligt beskrivningen i artikeln [lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)när du skapar en virtuell dator i labbet, du kan ange ett konfigurerat virtuellt nätverk. Du kan behöva åtkomst till corpnet resurser från dina virtuella datorer med hjälp av det virtuella nätverket som har konfigurerats med ExpressRoute eller plats-till-plats-VPN.

Den här artikeln förklarar hur du lägger till ett befintligt virtuellt nätverk i inställningarna för virtuella nätverk i ett labb så att den ska kunna välja när du skapar virtuella datorer.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurera ett virtuellt nätverk för ett labb i Azure Portal
Följande steg beskriver hur du lägger till ett befintligt virtuellt nätverk (och undernät) i ett labb så att den kan användas när du skapar en virtuell dator i samma labbet. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Lista över labs, Välj önskade labbet. 
1. På den testmiljön huvudfönstret väljer **konfiguration och principer**.

    ![Åtkomst till testmiljön konfiguration och principer](./media/devtest-lab-configure-vnet/policies-menu.png)
1. I den **externa resurser** väljer **virtuella nätverken**. En lista över virtuella nätverk som konfigurerats för det aktuella labbet visas samt standard virtuellt nätverk som skapats för ditt labb. 
1. Välj **+ Lägg till**.
   
    ![Lägg till ett befintligt virtuellt nätverk i ditt labb](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. På den **för virtuella nätverk** väljer **[väljer virtuellt nätverk]**.
   
    ![Välj ett befintligt virtuellt nätverk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. På den **Välj virtuellt nätverk** rutan, Välj önskade virtuella nätverket. En lista visas med alla virtuella nätverk som finns under samma region i prenumerationen som labbet.
1. När du har valt ett virtuellt nätverk, är du tillbaka till den **för virtuella nätverk** fönstret. Välj undernätet i listan längst ned.

    ![Lista över undernät](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Fönstret Lab undernät visas.

    ![Lab undernät fönstret](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Ange en **labbundernätsnamn**.
   - Om ett undernät som ska användas i testmiljön skapa Virtuella väljer **användning i virtuell dator skapas**.
   - Så här aktiverar du en [delad offentlig IP-adress](devtest-lab-shared-ip.md)väljer **aktivera delad offentlig IP**.
   - Om du vill tillåta offentliga IP-adresser i ett undernät, Välj **kan skapa en offentlig IP-**.
   - I den **maximalt virtuella datorer per användare** anger de maximala virtuella datorerna per användare för varje undernät. Lämna fältet tomt om du vill att ett obegränsat antal virtuella datorer.
1. Välj **OK** att stänga fönstret Lab undernät.
1. Välj **spara** att stänga fönstret virtuella nätverk.

Det virtuella nätverket är konfigurerat, kan du välja den när du skapar en virtuell dator. Att se hur du skapar en virtuell dator och ange ett virtuellt nätverk finns i artikel [lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md). 

Azures [virtuella nätverk dokumentationen](https://docs.microsoft.com/azure/virtual-network) innehåller mer information om hur du använder Vnet, inklusive hur du ställer in och hantera ett VNet och ansluta till ditt lokala nätverk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har lagt till det önskade virtuella nätverket till ditt labb, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

