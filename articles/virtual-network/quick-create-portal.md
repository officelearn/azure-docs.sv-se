---
title: Skapa ett Azure Virtual Network - Portal | Microsoft Docs
description: "Snabbt lära dig skapa ett virtuellt nätverk med Azure-portalen. Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, privat kommunicera med varandra och med internet."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c8f2cbe6b7377772e019a4ff90f91355ba0815ae
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Skapa ett virtuellt nätverk med Azure Portal

Ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer (VM), privat kommunicera med varandra och med internet. I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du skapar ett virtuellt nätverk kan distribuera du två virtuella datorer i det virtuella nätverket. Sedan ansluter till en virtuell dator från internet och kommunicera privat mellan de två virtuella datorerna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Ange, eller Välj följande information, Godkänn standardinställningarna för de återstående inställningarna och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa nytt** och ange *myResourceGroup*.|
    |Plats| Välj **östra USA**.|

    ![Ange grundläggande information om ditt virtuella nätverk](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Välj **+ skapa en resurs för** hittades i det övre, vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.
3. Ange, eller Välj följande information, Godkänn standardinställningarna för de återstående inställningarna och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm1|
    |Användarnamn| Ange ett användarnamn som du väljer.|
    |Lösenord| Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **använda befintliga** och välj **myResourceGroup**.|
    |Plats| Välj **östra USA**|

    ![Grunderna i virtuell dator](./media/quick-create-portal/virtual-machine-basics.png)

4. Välj en storlek för den virtuella datorn och välj sedan **Välj**.
5. Under **inställningar**, acceptera alla standardvärden och välj sedan **OK**.

    ![Inställningar för virtuell dator](./media/quick-create-portal/virtual-machine-settings.png)

6. Under **skapa** av den **sammanfattning**väljer **skapa** att starta distribution av Virtuella datorer. Den virtuella datorn tar några minuter att distribuera. 

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Slutför steg 1 – 6 igen, men i steg 3, namnge den virtuella datorn *myVm2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Anslut till en virtuell dator från internet

1. Efter *myVm1* är skapat kan ansluta till den. Längst upp i Azure-portalen, ange *myVm1*. När **myVm1** visas i sökresultaten väljer den. Välj den **Anslut** knappen.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

2. När du har valt den **Anslut** knapp och en Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn.  
3. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn. Du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om varningen, väljer **Ja** eller **Fortsätt**, för att fortsätta med anslutningen.

## <a name="communicate-privately-between-vms"></a>Privat kommunikation mellan virtuella datorer

1. Från PowerShell, ange `ping myvm2`. Ping misslyckas, eftersom ping använder kontroll meddelandet ICMP (internet protocol) och ICMP är inte tillåtet via Windows-brandväggen som standard.
2. Att tillåta *myVm2* pinga *myVm1* i ett senare steg, anger du följande kommando från PowerShell, vilket gör att ICMP inkommande via Windows-brandväggen:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Stäng fjärrskrivbordsanslutning till *myVm1*. 

4. Utför stegen i [Anslut till en virtuell dator från internet](#connect-to-a-vm-from-the-internet) igen, men ansluter till *myVm2*. Från en kommandotolk, ange `ping myvm1`.

    Du får svar från *myVm1*, eftersom ICMP tillåts via Windows-brandväggen på den *myVm1* VM i föregående steg.

5. Stäng fjärrskrivbordsanslutning till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får skapat du ett virtuellt standardnätverk och två virtuella datorer. Du ansluten till en virtuell dator från Internet och kommunicerade privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om inställningar för virtuellt nätverk i [hantera ett virtuellt nätverk](manage-virtual-network.md).

Standard Azure tillåter obegränsad privat kommunikation mellan virtuella datorer, men tillåter bara inkommande anslutningar till fjärrskrivbord till virtuella Windows-datorer från Internet. Om du vill lära dig mer om att tillåta eller begränsa olika typer av kommunikation till och från virtuella datorer, går du vidare till nästa kurs.

> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](virtual-networks-create-nsg-arm-pportal.md)