---
title: "Konfigurera ett virtuellt nätverk i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du konfigurerar ett befintligt virtuellt nätverk och undernät och använda dem i en virtuell dator med Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurera ett virtuellt nätverk i Azure DevTest Labs
Enligt beskrivningen i artikeln [lägga till en virtuell dator med artefakter i ett labb](devtest-lab-add-vm-with-artifacts.md)när du skapar en virtuell dator i labbet, du kan ange ett konfigurerat virtuellt nätverk. Ett scenario för att göra detta är om du behöver åtkomst till corpnet resurser från dina virtuella datorer med hjälp av det virtuella nätverket som har konfigurerats med ExpressRoute eller plats-till-plats-VPN. Följande avsnitt visar hur du lägger till ett befintligt virtuellt nätverk i inställningarna för virtuella nätverk i ett labb så att den ska kunna välja när du skapar virtuella datorer.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurera ett virtuellt nätverk för ett labb i Azure Portal
Följande steg beskriver hur du lägger till ett befintligt virtuellt nätverk (och undernät) i ett labb så att den kan användas när du skapar en virtuell dator i samma labbet. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet. 
4. På den testmiljön bladet välj **konfiguration och principer**.
5. På testmiljön **konfiguration och principer** bladet väljer **virtuella nätverken**.
6. På den **virtuella nätverken** bladet visas en lista över virtuella nätverk som konfigurerats för det aktuella labbet som standard virtuella nätverket som skapas för ditt labb. 
7. Välj **+ Lägg till**.
   
    ![Lägg till ett befintligt virtuellt nätverk i ditt labb](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. På den **för virtuella nätverk** bladet väljer **[väljer virtuellt nätverk]**.
   
    ![Välj ett befintligt virtuellt nätverk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. På den **Välj virtuellt nätverk** bladet välj önskade virtuella nätverket. Bladet visar de virtuella nätverk som finns under samma region i prenumerationen som labbet.  
10. När du har valt ett virtuellt nätverk, är du tillbaka till den **för virtuella nätverk** på undernät i listan längst ned på bladet.

    ![Lista över undernät](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Lab undernät-bladet visas.

    ![Blad för labbet undernät](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Ange en **labbundernätsnamn**.
12. Om ett undernät som ska användas i testmiljön skapa Virtuella väljer **användning i virtuell dator skapas**.
13. Så här aktiverar du en [delad offentlig IP-adress](devtest-lab-shared-ip.md)väljer **aktivera delad offentlig IP**.
14. Om du vill tillåta offentliga IP-adresser i ett undernät, Välj **kan skapa en offentlig IP-**.
15. I den **maximalt virtuella datorer per användare** anger de maximala virtuella datorerna per användare för varje undernät. Lämna fältet tomt om du vill att ett obegränsat antal virtuella datorer.
16. Välj **OK** att stänga bladet Lab undernät.
17. Välj **spara** att stänga bladet virtuella nätverk.
18. Det virtuella nätverket är konfigurerat, kan du välja den när du skapar en virtuell dator. 
    Att se hur du skapar en virtuell dator och ange ett virtuellt nätverk finns i artikel [lägga till en virtuell dator med artefakter i ett labb](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har lagt till det önskade virtuella nätverket till ditt labb, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm-with-artifacts.md).

