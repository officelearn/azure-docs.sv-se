---
title: Distribuera och konfigurera Azure-brandväggen med Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar och konfigurerar Azure-brandväggen med Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7f48012ca1f97c2e28380d95da37863c4bc17f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73831846"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Distribuera och konfigurera Azure-brandväggen med Azure PowerShell

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kanske till exempel vill begränsa åtkomsten till webbplatser. Du kanske vill begränsa de utgående IP-adresser och portar som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här artikeln skapar du ett förenklat enda virtuella nätverk med tre undernät för enkel distribution. För produktionsdistributioner rekommenderas en [hubb- och ekermodell,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) där brandväggen finns i sitt eget virtuella nätverk. Arbetsbelastningsservrarna finns i peer-virtuella nätverk i samma region med ett eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
* **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en programregel för att ge åtkomst till www.google.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * testa brandväggen.

Om du vill kan du slutföra den här proceduren med [Azure-portalen](tutorial-firewall-deploy-portal.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Den här proceduren kräver att du kör PowerShell lokalt. Du måste ha Azure PowerShell-modulen installerad. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för distributionen.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Det här virtuella nätverket har tre undernät:

> [!NOTE]
> Storleken på undernätet AzureFirewallSubnet är /26. Mer information om undernätsstorleken finns i [Vanliga frågor och svar om Azure-brandväggen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Nu, skapa det virtuella nätverket:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.
När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.

Skapa den virtuella datorn Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Skapa en virtuell dator för arbetsbelastning utan offentlig IP-adress.
När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera nu brandväggen till det virtuella nätverket.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

Skapa en tabell med BGP-flödesspridning inaktiverad

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Programregeln tillåter utgående åtkomst till www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Nätverksregeln tillåter utgående åtkomst till två IP-adresser vid port 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

Konfigurera serverns primära och sekundära DNS-adresser för teständamål i den här proceduren. Detta är inte ett allmänt Azure-brandväggskrav.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>testa brandväggen.

Testa nu brandväggen för att bekräfta att den fungerar som förväntat.

1. Observera den privata IP-adressen för den virtuella **datorn Srv-Work:**

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Anslut ett fjärrskrivbord till den virtuella datorn **Srv-Jump** och logga in. Därifrån öppnar du en fjärrskrivbordsanslutning till den privata **IP-adressen Srv-Work** och loggar in.

3. Öppna ett PowerShell-fönster på **SRV-Work**och kör följande kommandon:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Båda kommandona bör returnera svar som visar att dina DNS-frågor kommer igenom brandväggen.

1. Kör följande kommandon:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Begäranden `www.google.com` bör lyckas `www.microsoft.com` och begärandena bör misslyckas. Detta visar att brandväggsreglerna fungerar som förväntat.

Så nu har du verifierat att brandväggsreglerna fungerar:

* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.
* Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla brandväggsresurserna för nästa självstudiekurs, eller om det inte längre behövs, ta bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
