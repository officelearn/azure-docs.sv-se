---
title: Skapa ett virtuellt Azure-nätverk med flera undernät - Portal | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk med flera undernät med Azure-portalen.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880558"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Skapa ett virtuellt nätverk med flera undernät med Azure-portalen

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. Skapa flera undernät i ett virtuellt nätverk kan du segmentera nätverket så att du kan filtrera eller kontrollera trafikflödet mellan undernät. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa ett undernät
> * Testa nätverkskommunikation mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. I följande bild visas ange *myVirtualNetwork* för **namn**, *10.0.0.0/16* för **adressutrymmet**,  **myResourceGroup** för **resursgruppen**, *offentliga* för undernätet **namn**, 10.0.0.0/24 för undernätet **adressintervall**väljer en **plats** och **prenumeration**återstående standardvärdena och välj sedan **skapa**:

    ![Skapa ett virtuellt nätverk](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    Den **adressutrymmet** och **adressintervall** anges i CIDR-notering. Den angivna **adressutrymmet** innehåller 10.0.0.0-10.0.255.254 för IP-adresser. Den **adressintervallet** anges för ett undernät måste finnas i den **adressutrymmet** har definierats för det virtuella nätverket. Azure DHCP tilldelas IP-adresser från ett adressintervall för undernätet resurser har distribuerats i ett undernät. Azure endast tilldelas adresser 10.0.0.4-10.0.0.254 resurser har distribuerats inom den **offentliga** undernät, eftersom Azure reserverar de första fyra adresserna (10.0.0.0-10.0.0.3 för undernät i det här exemplet) och sist adressen ( 10.0.0.255 för undernät i det här exemplet) i varje undernät.

## <a name="create-a-subnet"></a>Skapa ett undernät

1. I den **söka efter resurser, tjänster och dokumenten** överst i portalen och börja skriva *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultaten väljer den.
2. Välj **undernät** och välj sedan **+ undernät**som visas i följande bild:

     ![Lägg till ett undernät](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. I den **Lägg till undernät** som visas anger *privata* för **namn**, ange *10.0.1.0/24* för **adressintervall**, och välj sedan **OK**.  Ett undernät-adressintervall kan inte överlappa adressintervallen i andra undernät i ett virtuellt nätverk. 

Innan du distribuerar virtuella Azure-nätverk och undernät för produktion, rekommenderas att du noggrant bekanta dig med adressutrymme [överväganden](manage-virtual-network.md#create-a-virtual-network) och [virtuellt nätverk gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). När resurser har distribuerats till undernät kräver vissa virtuella nätverk och undernät ändringar, till exempel ändra adressintervall Omdistributionen av befintliga Azure-resurser som distribueras inom undernät.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan testa nätverkskommunikation mellan dem och Internet i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men stegen förutsätter att du har valt **Windows Server 2016 Datacenter**. 
3. Välj eller ange följande information för **grunderna**och välj **OK**:
    - **Name**: *myVmWeb*
    - **Resursgruppen**: Välj **använda befintliga** och välj sedan *myResourceGroup*.
    - **Plats**: Välj *östra USA*.

    Den **användarnamn** och **lösenord** du anger används i ett senare steg. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Den **plats** och **prenumeration** valt måste vara samma som plats och prenumerationen som det virtuella nätverket. Det är inte nödvändigt att du väljer du samma resursgrupp som det virtuella nätverket har skapats i, men samma resursgrupp som har valts för den här kursen.
4. Välj en VM-storlek under **välja en storlek**.
5. Välj eller ange följande information för **inställningar**och välj **OK**:
    - **Virtuellt nätverk**: se till att **myVirtualNetwork** är markerad. Om inte, Välj **för virtuella nätverk** och välj sedan **myVirtualNetwork** under **Välj virtuellt nätverk**.
    - **Undernät**: se till att **offentliga** är markerad. Om inte, Välj **undernät** och välj sedan **offentliga** under **Välj undernät**som visas i följande bild:
    
        ![Inställningar för virtuell dator](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. Under **skapa** i den **sammanfattning**väljer **skapa** att starta distributionen av virtuella datorer.
7. Slutför steg 1 – 6 igen, men ange *myVmMgmt* för den **namn** av den virtuella datorn och välj **privata** för den **undernät**.

De virtuella datorerna ta några minuter att skapa. Fortsätt inte med stegen tills både virtuella datorer har skapats.

De virtuella datorerna som skapats i den här artikeln har ett [nätverksgränssnittet](virtual-network-network-interface.md) med en IP-adress som tilldelas dynamiskt till nätverksgränssnittet. När du har distribuerat den virtuella datorn, kan du [lägga till flera offentliga och privata IP-adresser eller ändra tilldelningsmetod för IP-adress till statisk](virtual-network-network-interface-addresses.md#add-ip-addresses). Du kan [Lägg till nätverksgränssnitt](virtual-network-network-interface-vm.md#vm-add-nic), upp till den gräns som stöds av den [VM-storlek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att du väljer när du skapar en virtuell dator. Du kan också [aktivera single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) för en virtuell dator, men bara när du skapar en virtuell dator med en VM-storlek som stöder kapaciteten.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Kommunikation mellan virtuella datorer och internet

1. I den *Sök* överst i portalen och börja skriva *myVmMgmt*. När **myVmMgmt** visas i sökresultaten väljer den.
2. Skapa en fjärrskrivbordsanslutning till den *myVmMgmt* virtuell dator genom att välja **Anslut**som visas i följande bild:

    ![Ansluta till den virtuella datorn](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Öppna den hämta RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut**.
4. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn), välj sedan **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen.
6. I ett senare steg ping för att kommunicera med den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn. Ping använder ICMP som nekas via Windows-brandväggen som standard. Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från en kommandotolk:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Du rekommenderas att inte tillåta ICMP via Windows-brandväggen för Produktionsdistribution om ping används i den här artikeln.
7. Av säkerhetsskäl är det vanligt att begränsa antalet virtuella datorer som via fjärranslutning kan anslutas till i ett virtuellt nätverk. I den här självstudiekursen den *myVmMgmt* virtuella används för att hantera den *myVmWeb* virtuell dator i det virtuella nätverket. Till fjärrskrivbord till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella datorn, anger du följande kommando från en kommandotolk:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Att kommunicera med den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn, anger du följande kommando från en kommandotolk:

    ```
    ping myvmmgmt
    ```

    Utdata som liknar följande exempel utdata visas:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Du kan se som adressen till den *myVmMgmt* virtuella datorn är 10.0.1.4. 10.0.1.4 var den första tillgängliga IP-adressen i adressintervallet i *privata* undernät som du har distribuerat den *myVmMgmt* virtuella datorn till i föregående steg.  Du ser att det fullständigt kvalificerade domännamnet för den virtuella datorn är *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Även om den *dar5p44cif3ulfq00wxznl3i3f* del av domännamnet är olika för den virtuella datorn, återstående delar av namnet på en domän är samma. 

    Som standard använder alla virtuella Azure-datorer standard Azure DNS-tjänsten. Alla virtuella datorer i ett virtuellt nätverk kan matcha namnen på alla andra virtuella datorer i samma virtuella nätverk med Azures standard DNS-tjänsten. Du kan använda DNS-servern eller den privata förmågan för Azure DNS-tjänsten istället för att använda Azures standard DNS-tjänsten. Mer information finns i [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) eller [med hjälp av Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Installera Internet Information Services (IIS) för Windows Server på den *myVmWeb* virtuella datorn, anger du följande kommando från en PowerShell-session:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. När installationen av IIS är klar, kan du koppla från den *myVmWeb* fjärrskrivbords-session, vilket lämnar du i den *myVmMgmt* fjärrskrivbords-sessionen. Öppna en webbläsare och gå till http://myvmweb. Du kan se IIS välkomstsidan.
11. Koppla från den *myVmMgmt* fjärrskrivbords-sessionen.
12. Hitta den offentliga IP-adressen för den *myVmWeb* virtuella datorn. När Azure skapade den *myVmWeb* virtuell dator, en offentlig IP-adressresurs med namnet *myVmWeb* också skapades och tilldelade till den virtuella datorn. Du kan se att 52.170.5.92 tilldelades **offentliga IP-adressen** till den *myVmMgmt* virtuell dator i bilden i steg 2. Att hitta den offentliga IP-adress som tilldelats den *myVmWeb* virtuell dator, Sök efter *myVmWeb* i sökrutan på portalen, markerar den när den visas i sökresultaten.

    Även om en virtuell dator inte är nödvändigt att ha en offentlig IP-adress som tilldelats, tilldelar Azure en offentlig IP-adress till varje virtuell dator som du skapar som standard. För att kommunicera från Internet till en virtuell dator, måste en offentlig IP-adress tilldelas till den virtuella datorn. Alla virtuella datorer kan kommunicera utgående med Internet, oavsett om en offentlig IP-adress har tilldelats den virtuella datorn. Mer information om utgående Internet-anslutningar i Azure finns [utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. På din egen dator går du till den offentliga IP-adressen för den *myVmWeb* virtuella datorn. Det går inte att försöka Se IIS välkomstsidan från din dator. Försöket misslyckas eftersom när de virtuella datorerna har distribuerats Azure skapas en nätverkssäkerhetsgrupp för varje virtuell dator som standard. 

     En nätverkssäkerhetsgrupp innehåller säkerhetsregler som tillåter eller nekar inkommande och utgående nätverkstrafik genom porten och IP-adress. Standard-nätverkssäkerhetsgruppen Azure skapas tillåter kommunikation via alla portar mellan resurser i samma virtuella nätverk. Windows-datorer nekar nätverkssäkerhetsgruppen standard all inkommande trafik från Internet via alla portar, utom TCP-port 3389 (RDP). Därför kan som standard kan du också RDP direkt till den *myVmWeb* virtuell dator från Internet, även om du inte vill port 3389 öppen på en webbserver. Eftersom webbsurfning kommunicerar via port 80, misslyckas kommunikation från Internet eftersom det inte finns någon regel i standard nätverkssäkerhetsgruppen tillåter trafik via port 80.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller: 

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du distribuerar ett virtuellt nätverk med flera undernät. Du också lära dig att när du skapar en virtuell dator för Windows Azure skapar ett nätverksgränssnitt som den kopplas till den virtuella datorn och skapar en nätverkssäkerhetsgrupp som endast tillåter trafik via port 3389, från Internet. Gå vidare till nästa kurs att lära dig att filtrera trafik till undernät i stället för till enskilda virtuella datorer.

> [!div class="nextstepaction"]
> [Filtrera trafik till undernät](./virtual-networks-create-nsg-arm-pportal.md)
