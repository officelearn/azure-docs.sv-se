---
title: "Skapa ett virtuellt nätverk i Azure - portalen | Microsoft Docs"
description: "Snabbt lära dig skapa ett virtuellt nätverk med Azure-portalen. Ett virtuellt nätverk gör det möjligt för flera olika typer av Azure-resurser till privat kommunicera med varandra."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 8b6a4abdb7677417462392feade0c7cfdf99246f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Skapa ett virtuellt nätverk med Azure Portal

I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk, distribuerar två virtuella datorer till det virtuella nätverket och kommunicera privat mellan dem och med internet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Anger, eller Välj följande information och välj sedan **skapa**:
    - **Name**: *myVirtualNetwork*
    - **Adressutrymmet**: acceptera standardvärdet. Adressutrymmet anges i CIDR-notering.
    - **Prenumerationen**: väljer din prenumeration.
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroup*.
    - **Plats**: Välj * östra USA **.
    - **Undernät, namnet**: acceptera standardvärdet.
    - **Undernät, adressintervall**: acceptera standardvärdet.
    - **Tjänstens slutpunkter**: acceptera standardvärdet.

    ![Ange grundläggande information om ditt virtuella nätverk](./media/quick-create-portal/virtual-network.png)

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser privat kommunicera med varandra och med internet. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator.

1. Välj **+ skapa en resurs för** hittades i det övre, vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.
3. Anger, eller Välj följande information och välj sedan **OK**:
    - **Namnet**: *myVm1*
    - **Användarnamnet**: Ange ett användarnamn som du väljer.
    - **Lösenordet**: Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
    - **Prenumerationen**: väljer din prenumeration.
    - **Resursgruppen**: Välj **använda befintliga** och välj **myResourceGroup**.
    - **Plats**: Välj *östra USA*.

    ![Ange grundläggande information om en virtuell dator](./media/quick-create-portal/virtual-machine-basics.png)
4. Välj en storlek för den virtuella datorn och välj sedan **Välj**.
5. Under **inställningar**, *myVirtualNetwork* bör redan vara markerad för **för virtuella nätverk**, men om det inte välja **för virtuella nätverk** , välj sedan *myVirtualNetwork*. Lämna *standard* markerade för **undernät**, och välj sedan **OK**.

    ![Välj ett virtuellt nätverk](./media/quick-create-portal/virtual-machine-network-settings.png)
6. På den **sammanfattning** väljer **skapa** att starta distributionen av virtuella datorer. Den virtuella datorn tar några minuter att distribuera. 
7. Slutför steg 1 – 6 igen, men i steg 3, namnge den virtuella datorn *myVm2*.

## <a name="connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

1. Efter *myVm1* har skapats från en annan dator att ansluta till den. Längst upp i Azure-portalen, ange *myVm1*. När **myVm1** visas i sökresultaten väljer den. Välj den **Anslut** knappen.

    ![Översikt över virtuella datorer](./media/quick-create-portal/virtual-machine-overview.png)

2. När du har valt den **Anslut** knapp och en Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn.  
3. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn), klicka på OK. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** att fortsätta med anslutningen.

## <a name="communicate-between-vms"></a>Kommunikation mellan virtuella datorer

1. Från en kommandotolk, ange `ping myvm2`. Ping misslyckas eftersom ping använder ICMP och ICMP inte är tillåtet via Windows-brandväggen, som standard. Att tillåta *myVm2* pinga *myVm1* i ett senare steg, anger du följande kommando från en kommandotolk:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

2. Stäng fjärrskrivbordsanslutning till *myVm1*. 

3. Utför stegen i [Anslut till en virtuell dator](#connect-to-a-virtual-machine), men ansluter till *myVm2*. Från en kommandotolk, ange `ping myvm1`.

    Du kan pinga den *myVm1* virtuell dator från den *myVm2* virtuella datorn eftersom:

    - ICMP tillåts via Windows-brandväggen på den *myVm1* virtuell dator i föregående steg.
    - som standard tillåter all nätverkstrafik mellan resurser i samma virtuella nätverk i Azure.

## <a name="communicate-to-the-internet"></a>Kommunicera med internet

1. När fortfarande är ansluten till den *myVm2* virtuell dator från en kommandotolk, ange `ping bing.com`.

    Du får fyra svar från bing.com. 

    Det går att pinga en internet-resurs från den *myVm2* virtuella datorn eftersom en virtuell dator kan kommunicera utgående till internet, som standard.

2. Avsluta fjärrskrivbords-sessionen.

## <a name="communicate-from-the-internet"></a>Kommunikation från internet

1. Hämta den offentliga IP-adressen för den *myVm1* virtuella datorn. I bilden visas under steg 1 i [Anslut till en virtuell dator](#connect-to-a-virtual-machine), visas en offentlig IP-adress. I bilden är adressen är *13.90.241.247*. Adressen för den virtuella datorn är olika. 

2. Pinga från datorn, den offentliga IP-adressen för din *myVm1* virtuella datorn. Ping misslyckas trots att ICMP är öppen via Windows-brandväggen.

    Det går inte att pinga eftersom all trafik till Windows-datorer, förutom fjärrskrivbordsanslutningar via port 3389, nekas av Azure, som standard. 

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en standard virtuellt nätverk med ett undernät i den här artikeln. Mer information om hur du skapar en anpassad virtuellt nätverk med flera undernät, även fortsättningsvis självstudierna för att skapa egna virtuella nätverk.

> [!div class="nextstepaction"]
> [Skapa en anpassad virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md)
