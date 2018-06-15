---
title: Skapa ett virtuellt Azure-nätverk med flera undernät - PowerShell | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk med flera undernät med hjälp av PowerShell.
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
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880565"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Skapa ett virtuellt nätverk med flera undernät med hjälp av PowerShell

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. Skapa flera undernät i ett virtuellt nätverk kan du segmentera nätverket så att du kan filtrera eller kontrollera trafikflödet mellan undernät. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa ett undernät
> * Testa nätverkskommunikation mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudiekursen Azure PowerShell Modulversion 3,6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *EastUS* plats.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Den **AddressPrefix** anges i CIDR-notering. Det angivna adressprefixet innehåller 10.0.0.0-10.0.255.254 för IP-adresser.

## <a name="create-a-subnet"></a>Skapa ett undernät

Ett undernät har skapats genom att först skapa en konfiguration av undernät och sedan uppdaterar det virtuella nätverket med undernätskonfiguration. Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas två undernät konfigurationer för *offentliga* och *privata* undernät:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

Den **AddressPrefix** angetts för ett undernät måste vara inom adressprefixet som definierats för det virtuella nätverket. Azure DHCP tilldelas IP-adresser från ett undernät adressprefix resurser har distribuerats i ett undernät. Azure endast tilldelas adresser 10.0.0.4-10.0.0.254 resurser har distribuerats inom den **offentliga** undernät, eftersom Azure reserverar de första fyra adresserna (10.0.0.0-10.0.0.3 för undernät i det här exemplet) och sist adressen ( 10.0.0.255 för undernät i det här exemplet) i varje undernät.

Skriva undernät konfigurationer till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernäten i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Innan du distribuerar virtuella Azure-nätverk och undernät för produktion, rekommenderas att du noggrant bekanta dig med adressutrymme [överväganden](manage-virtual-network.md#create-a-virtual-network) och [virtuellt nätverk gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). När resurser har distribuerats till undernät kräver vissa virtuella nätverk och undernät ändringar, till exempel ändra adressintervall Omdistributionen av befintliga Azure-resurser som distribueras inom undernät.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan testa nätverkskommunikation mellan dem och Internet i ett senare steg. 

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVmWeb* i den *offentliga* undernätet för den *myVirtualNetwork* virtuellt nätverk. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg. När du uppmanas, anger du användarnamn och lösenord som du vill logga in på den virtuella datorn med.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

Azure tilldelas automatiskt 10.0.0.4 som privata IP-adressen för den virtuella datorn, eftersom 10.0.0.4 är den första tillgängliga IP-adressen i den *offentliga* undernät. 

Skapa en virtuell dator i den *privata* undernät.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

Det tar några minuter att skapa den virtuella datorn. Även om inte returnerade utdata och Azure utsetts 10.0.1.4 privata IP-adressen för den virtuella datorn, eftersom 10.0.1.4 är den första tillgängliga IP-adressen i den *privata* undernätet för *myVirtualNetwork*. 

Fortsätt inte med stegen tills den virtuella datorn skapas och PowerShell returnerar utdata.

De virtuella datorerna som skapats i den här artikeln har ett [nätverksgränssnittet](virtual-network-network-interface.md) med en IP-adress som tilldelas dynamiskt till nätverksgränssnittet. När du har distribuerat den virtuella datorn, kan du [lägga till flera offentliga och privata IP-adresser eller ändra tilldelningsmetod för IP-adress till statisk](virtual-network-network-interface-addresses.md#add-ip-addresses). Du kan [Lägg till nätverksgränssnitt](virtual-network-network-interface-vm.md#vm-add-nic), upp till den gräns som stöds av den [VM-storlek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att du väljer när du skapar en virtuell dator. Du kan också [aktivera single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) för en virtuell dator, men bara när du skapar en virtuell dator med en VM-storlek som stöder kapaciteten.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Kommunikation mellan virtuella datorer och internet

Du kan ansluta till en virtuell dators offentlig IP-adress från Internet. När Azure skapade den *myVmMgmt* virtuell dator, en offentlig IP-adress med namnet *myVmMgmt* också skapades och tilldelade till den virtuella datorn. Även om en virtuell dator inte är nödvändigt att ha en offentlig IP-adress som tilldelats, tilldelar Azure en offentlig IP-adress till varje virtuell dator som du skapar som standard. För att kommunicera från Internet till en virtuell dator, måste en offentlig IP-adress tilldelas till den virtuella datorn. Alla virtuella datorer kan kommunicera utgående med Internet, oavsett om en offentlig IP-adress har tilldelats den virtuella datorn. Mer information om utgående Internet-anslutningar i Azure finns [utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVmMgmt* virtuell dator:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVmMgmt* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returneras från det föregående kommandot.

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas, hämtas till datorn och öppnas. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn) och klicka sedan på **OK**. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen. 

I ett senare steg ping för att kommunicera med den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn. Ping använder ICMP som nekas via Windows-brandväggen som standard. Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från en kommandotolk:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Du rekommenderas att inte tillåta ICMP via Windows-brandväggen för Produktionsdistribution om ping används i den här artikeln.

Av säkerhetsskäl är det vanligt att begränsa antalet virtuella datorer som via fjärranslutning kan anslutas till i ett virtuellt nätverk. I den här självstudiekursen den *myVmMgmt* virtuella används för att hantera den *myVmWeb* virtuell dator i det virtuella nätverket. Använd följande kommando för att fjärrskrivbord till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella:

``` 
mstsc /v:myVmWeb
```

Att kommunicera med den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn, anger du följande kommando från en kommandotolk:

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
      
Du kan se som adressen till den *myVmMgmt* virtuella datorn är 10.0.1.4. 10.0.1.4 var den första tillgängliga IP-adressen i adressintervallet i *privata* undernät som du har distribuerat den *myVmMgmt* virtuella datorn till i föregående steg.  Du ser att det fullständigt kvalificerade domännamnet för den virtuella datorn är *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Även om den *dar5p44cif3ulfq00wxznl3i3f* del av domännamnet är olika för den virtuella datorn, återstående delar av namnet på en domän är samma. Som standard använder alla virtuella Azure-datorer standard Azure DNS-tjänsten. Alla virtuella datorer i ett virtuellt nätverk kan matcha namnen på alla andra virtuella datorer i samma virtuella nätverk med Azures standard DNS-tjänsten. Du kan använda DNS-servern eller den privata förmågan för Azure DNS-tjänsten istället för att använda Azures standard DNS-tjänsten. Mer information finns i [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) eller [med hjälp av Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Installera Internet Information Services (IIS) för Windows Server på den *myVmWeb* virtuella datorn, anger du följande kommando från en PowerShell-session:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När installationen av IIS är klar, kan du koppla från den *myVmWeb* fjärrskrivbords-session, vilket lämnar du i den *myVmMgmt* fjärrskrivbords-sessionen. Öppna en webbläsare och gå till http://myvmweb. Du kan se IIS välkomstsidan.

Koppla från den *myVmMgmt* fjärrskrivbords-sessionen.

Hämta den offentliga adressen Azure som tilldelats den *myVmWeb* virtuell dator:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

På din egen dator går du till den offentliga IP-adressen för den *myVmWeb* virtuella datorn. Det går inte att försöka Se IIS välkomstsidan från din dator. Försöket misslyckas eftersom när de virtuella datorerna har distribuerats Azure skapas en nätverkssäkerhetsgrupp för varje virtuell dator som standard. 

En nätverkssäkerhetsgrupp innehåller säkerhetsregler som tillåter eller nekar inkommande och utgående nätverkstrafik genom porten och IP-adress. Standard-nätverkssäkerhetsgruppen Azure skapas tillåter kommunikation via alla portar mellan resurser i samma virtuella nätverk. Nätverkssäkerhetsgruppen standard nekar all inkommande trafik från Internet via alla portar för Windows-datorer måste acceptera TCP-port 3389 (RDP). Därför kan som standard kan du också RDP direkt till den *myVmWeb* virtuell dator från Internet, även om du inte vill port 3389 öppen på en webbserver. Eftersom webbsurfning kommunicerar via port 80, misslyckas kommunikation från Internet eftersom det inte finns någon regel i standard nätverkssäkerhetsgruppen tillåter trafik via port 80.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du distribuerar ett virtuellt nätverk med flera undernät. Du också lära dig att när du skapar en virtuell dator för Windows Azure skapar ett nätverksgränssnitt som den kopplas till den virtuella datorn och skapar en nätverkssäkerhetsgrupp som endast tillåter trafik via port 3389, från Internet. Gå vidare till nästa kurs att lära dig att filtrera trafik till undernät i stället för till enskilda virtuella datorer.

> [!div class="nextstepaction"]
> [Filtrera trafik till undernät](./virtual-networks-create-nsg-arm-ps.md)
