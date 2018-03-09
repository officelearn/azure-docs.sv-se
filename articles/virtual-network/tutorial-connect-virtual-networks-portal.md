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
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Ansluta virtuella nätverk med virtuella nätverk peering med Azure-portalen

Du kan ansluta virtuella nätverk till varandra med virtuella nätverk peering. När virtuella nätverk är peerkopplat kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna som fanns i samma virtuella nätverk. Den här artikeln beskriver skapandet och peering av två virtuella nätverk. Lär dig att:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Skapa en peering mellan virtuella nätverk
> * Testa peering

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. I följande bild visas ange *myVirtualNetwork1* för **namn**, *10.0.0.0/16* för **adressutrymmet**,  **myResourceGroup** för **resursgruppen**, *Undernät1* för undernätet **namn**, 10.0.0.0/24 för undernätet **adressintervall** , Välj en **plats** och **prenumeration**återstående standardvärdena och välj sedan **skapa**:

    ![Skapa ett virtuellt nätverk](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Utför steg 1 – 3 igen, med följande ändringar:
    - **Name**: *myVirtualNetwork2*
    - **Resursgruppen**: Välj **använda befintliga** och välj sedan **myResourceGroup**.
    - **Adressutrymmet**: *10.1.0.0/16*
    - **Adressintervall för gatewayundernät**: *10.1.0.0/24*

    Adressprefixet för den *myVirtualNetwork2* virtuellt nätverk inte överlappar adressutrymmet till den *myVirtualNetwork1* virtuellt nätverk. Du kan inte peer-virtuella nätverk med överlappande adressutrymmen.

## <a name="peer-virtual-networks"></a>Peer-virtuella nätverk

1. I sökrutan överst i Azure-portalen har börjat skriva *MyVirtualNetwork1*. När **myVirtualNetwork1** visas i sökresultaten väljer den.
2. Välj **Peerkopplingar**under **inställningar**, och välj sedan **+ Lägg till**som visas i följande bild:

    ![Skapa peering](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Ange eller välj informationen som visas i följande bild och sedan **OK**. Att välja den *myVirtualNetwork2* virtuellt nätverk, Välj **för virtuella nätverk**och välj *myVirtualNetwork2*.

    ![Peering inställningar](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    Den **PEERING STATUS** är *initierade*som visas i följande bild:

    ![Peering status](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Om du inte ser status, uppdatera webbläsaren.

4. Sök efter den *myVirtualNetwork2* virtuellt nätverk. När den returneras i sökresultaten väljer du den.
5. Utför steg 1 – 3 igen, med följande ändringar och välj sedan **OK**:
    - **Name**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Virtuellt nätverk**: *myVirtualNetwork1*

    Den **PEERING STATUS** är *ansluten*. Azure har även ändrat peering status för den *myVirtualNetwork2 myVirtualNetwork1* peering från *initierade* till *ansluten.* Virtuellt nätverk peering fullständigt upprättas inte förrän peering status för båda virtuella nätverken är *ansluten.* 

Peerkopplingar mellan två virtuella nätverk, men är inte transitiva. I så fall till exempel om du ville peer *myVirtualNetwork2* till *myVirtualNetwork3*, måste du skapa en ytterligare peering mellan virtuella nätverk *myVirtualNetwork2* och *myVirtualNetwork3*. Även om *myVirtualNetwork1* peerkopplat med *myVirtualNetwork2*, resurser inom *myVirtualNetwork1* kan bara komma åt resurser i  *myVirtualNetwork3* om *myVirtualNetwork1* också är peerkopplat med *myVirtualNetwork3*. 

Innan peering produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med de [peering översikt](virtual-network-peering-overview.md), [hantera peering](virtual-network-manage-peering.md), och [gränser för virtuellt nätverk ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Även om den här artikeln beskrivs en peering mellan två virtuella nätverk på samma prenumeration och plats, kan du också peer virtuella nätverk i [olika regioner](#register) och [olika Azure-prenumerationer](create-peering-different-subscriptions.md#portal). Du kan också skapa [NAV och ekrar nätverk Designer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peering.

## <a name="test-peering"></a>Testa peering

Distribuera en virtuell dator till varje undernät och sedan kommunicera mellan de virtuella datorerna för att testa nätverkskommunikation mellan virtuella datorer i olika virtuella nätverk via en peering. 

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kontrollera kommunikationen mellan dem i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men stegen förutsätter att du har valt **Windows Server 2016 Datacenter**. 
3. Välj eller ange följande information för **grunderna**och välj **OK**:
    - **Namnet**: *myVm1*
    - **Resursgruppen**: Välj **använda befintliga** och välj sedan *myResourceGroup*.
    - **Plats**: Välj *östra USA*.

    Den **användarnamn** och **lösenord** du anger används i ett senare steg. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Den **plats** och **prenumeration** valt måste vara samma som plats och prenumerationen som det virtuella nätverket. Det är inte nödvändigt att du väljer du samma resursgrupp som det virtuella nätverket har skapats i, men samma resursgrupp som har valts för den här artikeln.
4. Välj en VM-storlek under **välja en storlek**.
5. Välj eller ange följande information för **inställningar**och välj **OK**:
    - **Virtuellt nätverk**: se till att **myVirtualNetwork1** är markerad. Om inte, Välj **för virtuella nätverk** och välj sedan **myVirtualNetwork1** under **Välj virtuellt nätverk**.
    - **Undernät**: se till att **Undernät1** är markerad. Om inte, Välj **undernät** och välj sedan **Undernät1** under **Välj undernät**som visas i följande bild:
    
        ![Inställningar för virtuell dator](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Under **skapa** i den **sammanfattning**väljer **skapa** att starta distributionen av virtuella datorer.
7. Slutför steg 1 – 6 igen, men ange *myVm2* för den **namn** av den virtuella datorn och välj *myVirtualNetwork2* för **förvirtuellanätverk**.

Azure tilldelade *10.0.0.4* som privata IP-adressen för den *myVm1* virtuella datorn och 10.1.0.4 till den *myVm2* virtuella datorn, eftersom de var den första tillgängliga IP adresser i *Undernät1* av den *myVirtualNetwork1* och *myVirtualNetwork2* virtuella nätverk, respektive.

De virtuella datorerna ta några minuter att skapa. Fortsätt inte med stegen tills både virtuella datorer har skapats.

### <a name="test-virtual-machine-communication"></a>Testa virtuell dator-kommunikation

1. I den *Sök* överst i portalen och börja skriva *myVm1*. När **myVm1** visas i sökresultaten väljer den.
2. Skapa en fjärrskrivbordsanslutning till den *myVm1* virtuell dator genom att välja **Anslut**som visas i följande bild:

    ![Ansluta till den virtuella datorn](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Öppna den hämta RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut**.
4. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn), välj sedan **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen.
6. I ett senare steg ping för att kommunicera med den *myVm2* virtuell dator från den *myVmWeb* virtuella datorn. Ping använder ICMP som nekas via Windows-brandväggen som standard. Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från en kommandotolk:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Även om ping används i den här artikeln för att testa, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.

7. Att ansluta till den *myVm2* virtuella datorn, anger du följande kommando från en kommandotolk den *myVm1* virtuell dator:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Eftersom du har aktiverat ping på *myVm1*, kan nu pinga IP-adressen:

    ```
    ping 10.0.0.4
    ```
    
    Du får fyra svar. Om du pinga den virtuella datorns namn (*myVm1*), i stället för dess IP-adress pingningen misslyckas eftersom *myVm1* är en okänd värdnamnet. Azures standard namnmatchningen fungerar mellan virtuella datorer i samma virtuella nätverk, men inte mellan virtuella datorer i olika virtuella nätverk. Om du vill matcha namnen på virtuella nätverk, måste du [Distribuera DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md) eller använda [privata Azure DNS-domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Koppla från din RDP-sessioner till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller: 

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

**<a name="register"></a>Registrera sig för globalt virtuella nätverk peering förhandsversion**

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network) för tillgängliga regioner. To-peer-virtuella nätverk över regioner, måste du först registrera för förhandsgranskningen. Du kan inte registrera med hjälp av portalen, men du kan registrera med [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) eller [Azure CLI](tutorial-connect-virtual-networks-cli.md#register). Om du försöker att peer-virtuella nätverk i olika regioner innan du registrerar kapaciteten peering misslyckas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du ansluter två nätverk med virtuella nätverk peering.

Fortsätta att ansluta datorn till ett virtuellt nätverk via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkoppla virtuella nätverk.

> [!div class="nextstepaction"]
> [Ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
