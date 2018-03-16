---
title: "Ansluta virtuella nätverk med virtuella nätverk peering - Azure-portalen | Microsoft Docs"
description: "Lär dig mer om att ansluta virtuella nätverk med virtuella nätverk peering."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0962a917186277a34abbda17b8fea87bcf4ad1e9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Ansluta virtuella nätverk med virtuella nätverk peering med Azure-portalen

Du kan ansluta virtuella nätverk till varandra med virtuella nätverk peering. När virtuella nätverk är peerkopplat kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna som fanns i samma virtuella nätverk. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Ansluta två virtuella nätverk med ett virtuellt nätverk som peering
> * Distribuera en virtuell dator (VM) i varje virtuellt nätverk
> * Kommunikation mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på https://portal.azure.com.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Ange, eller Välj följande information, Godkänn standardinställningarna för de återstående inställningarna och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork1|
    |Adressutrymme|10.0.0.0/16|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa nytt** och ange *myResourceGroup*.|
    |Plats| Välj **östra USA**.|
    |Undernätsnamn|Subnet1|
    |Adressintervall för gatewayundernät|10.0.0.0/24|

      ![Skapa ett virtuellt nätverk](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Utför steg 1 – 3 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork2|
    |Adressutrymme|10.1.0.0/16|
    |Resursgrupp| Välj **använda befintliga** och välj sedan **myResourceGroup**.|
    |Adressintervall för gatewayundernät|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Peer-virtuella nätverk

1. I sökrutan överst i Azure-portalen har börjat skriva *MyVirtualNetwork1*. När **myVirtualNetwork1** visas i sökresultaten väljer den.
2. Välj **Peerkopplingar**under **inställningar**, och välj sedan **+ Lägg till**som visas i följande bild:

    ![Skapa peering](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Ange, eller Välj följande information, Godkänn standardinställningarna för de återstående inställningarna och välj sedan **OK**.

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork1-myVirtualNetwork2|
    |Prenumeration| Välj din prenumeration.|
    |Virtuellt nätverk|myVirtualNetwork2 - att välja den *myVirtualNetwork2* virtuellt nätverk, Välj **för virtuella nätverk**och välj **myVirtualNetwork2**.|

    ![Peering inställningar](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    Den **PEERING STATUS** är *initierade*som visas i följande bild:

    ![Peering status](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Om du inte ser status, uppdatera webbläsaren.

4. I den **Sök** överst i Azure-portalen och börja skriva *MyVirtualNetwork2*. När **myVirtualNetwork2** visas i sökresultaten väljer den.
5. Slutför steg 2 – 3 igen, med följande ändringar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuellt nätverk|myVirtualNetwork1|

    Den **PEERING STATUS** är *ansluten*. Azure har även ändrat peering status för den *myVirtualNetwork2 myVirtualNetwork1* peering från *initierade* till *ansluten.* Virtuellt nätverk peering fullständigt upprättas inte förrän peering status för båda virtuella nätverken är *ansluten.* 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men stegen förutsätter att du har valt **Windows Server 2016 Datacenter**. 
3. Ange eller Välj följande information för **grunderna**, Godkänn standardinställningarna för de återstående inställningarna och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm1|
    |Användarnamn| Ange ett användarnamn som du väljer.|
    |Lösenord| Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **använda befintliga** och välj sedan **myResourceGroup**.|
    |Plats| Välj **östra USA**.|
4. Välj en VM-storlek under **välja en storlek**.
5. Välj följande värden för **inställningar**och välj **OK**:
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| myVirtualNetwork1 - om det inte redan är vald väljer **för virtuella nätverk** och välj sedan **myVirtualNetwork1** under **Välj virtuellt nätverk**.|
    |Undernät| Undernät1 - om det inte redan är vald väljer **undernät** och välj sedan **Undernät1** under **Välj undernät**.|
    
    ![Inställningar för virtuell dator](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Under **skapa** i den **sammanfattning**väljer **skapa** att starta VM-distributionen.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Slutför steg 1 – 6 igen, med följande ändringar:

|Inställning|Värde|
|---|---|
|Namn | myVm2|
|Virtuellt nätverk | myVirtualNetwork2|

De virtuella datorerna ta några minuter att skapa. Fortsätt inte med stegen tills båda VM: ar har skapats.

## <a name="communicate-between-vms"></a>Kommunikation mellan virtuella datorer

1. I den *Sök* överst i portalen och börja skriva *myVm1*. När **myVm1** visas i sökresultaten väljer den.
2. Skapa en fjärrskrivbordsanslutning till den *myVm1* VM genom att välja **Anslut**som visas i följande bild:

    ![Ansluta till den virtuella datorn](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Öppna den hämta RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut**.
4. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn), Välj sedan **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen.
6. I ett senare steg ping för att kommunicera med den *myVm2* virtuell dator från den *myVm1* VM. Ping använder den kontrollen meddelandet ICMP (Internet Protocol), som nekas via Windows-brandväggen som standard. På den *myVm1* VM, aktivera den kontrollen meddelandet ICMP (Internet Protocol) via Windows-brandväggen så att du kan pinga den här virtuella datorn från *myVm2* i ett senare steg med hjälp av PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Du rekommenderas att inte tillåta ICMP via Windows-brandväggen för Produktionsdistribution om ping används för kommunikation mellan virtuella datorer i den här artikeln.

7. Att ansluta till den *myVm2* VM, anger du följande kommando från en kommandotolk den *myVm1* VM:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Eftersom du har aktiverat ping på *myVm1*, kan nu pinga IP-adressen:

    ```
    ping 10.0.0.4
    ```
    
9. Koppla från din RDP-sessioner till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller: 

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

**<a name="register"></a>Registrera sig för globalt virtuella nätverk peering förhandsversion**

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network) för tillgängliga regioner. To-peer-virtuella nätverk över regioner, måste du först registrera för förhandsgranskningen. Du kan inte registrera med hjälp av portalen, men du kan registrera med [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) eller [Azure CLI](tutorial-connect-virtual-networks-cli.md#register). Om du försöker att peer-virtuella nätverk i olika regioner innan du registrerar kapaciteten peering misslyckas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du ansluter två nätverk i samma Azure-plats, med virtuella nätverk peering. Du kan också peer-virtuella nätverk i [olika regioner](#register)i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#portal) och du kan skapa [NAV och ekrar nätverk Designer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peering. Innan du peering produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med de [peering översikt](virtual-network-peering-overview.md), [hantera peering](virtual-network-manage-peering.md), och [virtuellt nätverk gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

Fortsätta att ansluta datorn till ett virtuellt nätverk via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkoppla virtuella nätverk.

> [!div class="nextstepaction"]
> [Ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
