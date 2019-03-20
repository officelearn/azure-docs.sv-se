---
title: Associera en offentlig IP-adress till en virtuell dator
titlesuffix: Azure Virtual Network
description: Lär dig mer om att associera en offentlig IP-adress till en virtuell dator.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: ce573ff8fe61f2e1d4c88963e0f21fc9402776e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083223"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associera en offentlig IP-adress till en virtuell dator

I den här artikeln får du lära dig hur du associerar en offentlig IP-adress till en befintlig virtuell dator (VM). Om du vill ansluta till en virtuell dator från internet måste den virtuella datorn ha en offentlig IP-adress som är associerade med den. Om du vill skapa en ny virtuell dator med en offentlig IP-adress kan du göra det med hjälp av den [Azure-portalen](virtual-network-deploy-static-pip-arm-portal.md), [Azure-kommandoradsgränssnittet (CLI)](virtual-network-deploy-static-pip-arm-cli.md), eller [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Offentliga IP-adresser har en nominell avgift. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/ip-addresses/). Det finns en gräns för antalet offentliga IP-adresser som du kan använda per prenumeration. Mer information finns i [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits-1).

Du kan använda den [Azure-portalen](#azure-portal), Azure [kommandoradsgränssnittet](#azure-cli) (CLI), eller [PowerShell](#powershell) att associera en offentlig IP-adress till en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Bläddra till eller Sök efter den virtuella datorn som du vill lägga till den offentliga IP-adressen till och markera den.
3. Under **inställningar**väljer **nätverk**, och välj sedan de nätverksgränssnitt som du vill lägga till den offentliga IP-adressen till, enligt följande bild:

   ![Välj nätverksgränssnitt](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Offentliga IP-adresser är kopplade till nätverksgränssnitt som är kopplade till en virtuell dator. Den virtuella datorn har bara ett nätverksgränssnitt på föregående bild. Om den virtuella datorn har flera nätverksgränssnitt, de och du skulle välja nätverksgränssnittet som du vill associera den offentliga IP-adressen till.

4. Välj **IP-konfigurationer** och välj sedan en IP-konfiguration som visas i följande bild:

   ![Välj IP-konfiguration](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Offentliga IP-adresser är kopplade till IP-konfigurationer för ett nätverksgränssnitt. Nätverksgränssnittet har en IP-konfiguration på föregående bild. Om nätverksgränssnittet har haft flera IP-konfigurationer, de skulle visas i listan och väljer du den IP-konfiguration som du vill associera den offentliga IP-adressen till.

5. Välj **aktiverad**och välj sedan **IP-adress (*konfigurera nödvändiga inställningar*)**. Välj en befintlig offentlig IP-adress, som automatiskt stänger den **Välj offentlig IP-adress** box. Om du inte har någon tillgänglig offentliga IP-adresser i listan, måste du skapa en. Läs hur genom att läsa [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Välj **spara**, enligt bilden som följer och stäng sedan rutan för IP-konfiguration.

   ![Aktivera offentlig IP-adress](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Offentliga IP-adresser som visas är de som finns i samma region som den virtuella datorn. Om du har flera offentliga IP-adresser som skapats i regionen, visas alla här. Om någon är nedtonade beror det på att adressen är redan kopplad till en annan resurs.

6. Visa offentliga IP-adress som tilldelats IP-konfiguration som visas i bilden nedan. Det kan ta några sekunder innan en IP-adress visas.

   ![Visa tilldelade offentlig IP-adress](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över-adresspooler som används i varje region Se [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den adress som tilldelats kan vara en adress i pooler som använts för regionen. Om du behöver adressen som ska tilldelas från en specifik pool i regionen kan använda en [offentliga IP-adressprefix](public-ip-address-prefix.md).

7. [Tillåta nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en grupp.

## <a name="azure-cli"></a>Azure CLI

Installera den [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), eller använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj den **prova** knappen i CLI-kommandon som följer. Att välja **prova** anropar en Cloud Shell som du kan logga in på Azure-kontot med.

1. Om du använder CLI lokalt i Bash, logga in på Azure med `az login`.
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverksgränssnitt som är ansluten till en virtuell dator. Använd den [az network nic IP-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) kommando för att associera en offentlig IP-adress till en IP-konfiguration. I följande exempel kopplar en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för en befintlig nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Om du inte har en befintlig offentlig IP-adress kan du använda den [az nätverket offentliga ip-skapa](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) kommando för att skapa en. Till exempel följande kommando skapar en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kan anpassa. Läs mer om alla offentliga IP-adressinställningarna i [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Om du vill se en lista över-adresspooler som används i varje region Se [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte vet namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder den [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) kommando för att visa dem. Till exempel följande kommando visar namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I exemplet ovan *myVMVMNic* är namnet på nätverksgränssnittet.

   - Om du inte vet namnet på en IP-konfiguration för ett nätverksgränssnitt, använda den [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) kommando för att hämta dem. Till exempel följande kommando visar namnen på IP-konfigurationer för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visa offentliga IP-adress som tilldelats IP-konfiguration med den [az vm list-ip-adresser](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) kommando. I följande exempel visas de IP-adresserna som tilldelats till en befintlig virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över-adresspooler som används i varje region Se [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den adress som tilldelats kan vara en adress i pooler som använts för regionen. Om du behöver adressen som ska tilldelas från en specifik pool i regionen kan använda en [offentliga IP-adressprefix](public-ip-address-prefix.md).

4. [Tillåta nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en grupp.

## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps), eller använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och har konfigurerats för användning med ditt konto. Välj den **prova** knappen i PowerShell-kommandon som följer. Att välja **prova** anropar en Cloud Shell som du kan logga in på Azure-kontot med.

1. Om du använder PowerShell lokalt, logga in på Azure med `Connect-AzAccount`.
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverksgränssnitt som är ansluten till en virtuell dator. Använd den [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) och [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) kommandon för att hämta det virtuella nätverk och undernät som nätverksgränssnittet finns i. Använd sedan den [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) kommando för att hämta ett nätverksgränssnitt och [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) kommando för att hämta en befintlig offentlig IP-adress. Använd sedan den [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) kommando för att associera den offentliga IP-adressen till IP-konfigurationen och [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) kommando för att skriva den nya IP-konfigurationen till den nätverksgränssnitt.

   I följande exempel kopplar en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för en befintlig nätverksgränssnitt med namnet *myVMVMNic* som finns i ett undernät med namnet *myVMSubnet* i ett virtuellt nätverk med namnet *myVMVNet*. Alla resurser som finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Om du inte har en befintlig offentlig IP-adress kan du använda den [New AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) kommando för att skapa en. Till exempel följande kommando skapar en *dynamisk* offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup* i den  *eastus* region.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kan anpassa. Läs mer om alla offentliga IP-adressinställningarna i [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Om du vill se en lista över-adresspooler som används i varje region Se [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte vet namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder den [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) kommando för att visa dem. Till exempel följande kommando visar namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I Exempelutdata *myVMVMNic* är namnet på nätverksgränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte vet namnet på det virtuella nätverk eller undernät som nätverksgränssnittet finns i, Använd den `Get-AzNetworkInterface` kommando för att visa informationen. Till exempel följande kommando hämtar informationen virtuella nätverk och undernät för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I Exempelutdata *myVMVNET* är namnet på det virtuella nätverket och *myVMSubnet* är namnet på undernätet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Om du inte vet namnet på en IP-konfiguration för ett nätverksgränssnitt, använda den [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) kommando för att hämta dem. Till exempel följande kommando visar namnen på IP-konfigurationer för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I Exempelutdata *ipconfigmyVM* är namnet på en IP-konfiguration.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visa offentliga IP-adress som tilldelats IP-konfiguration med den [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) kommando. I följande exempel visas den adress som tilldelats en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Om du inte vet namnet på offentlig IP-adress som tilldelats en IP-konfiguration, kör du följande kommandon för att hämta den:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Utdata innehåller en eller flera rader som liknar följande exempel. I Exempelutdata *myVMPublicIP* är namnet på den offentliga IP-adress till IP-konfigurationen.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över-adresspooler som används i varje region Se [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den adress som tilldelats kan vara en adress i pooler som använts för regionen. Om du behöver adressen som ska tilldelas från en specifik pool i regionen kan använda en [offentliga IP-adressprefix](public-ip-address-prefix.md).

4. [Tillåta nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en grupp.

## <a name="allow-network-traffic-to-the-vm"></a>Tillåta nätverkstrafik till den virtuella datorn

Innan du kan ansluta till den offentliga IP-adressen från internet, kan du kontrollera att du har alla nödvändiga portar öppna i någon säkerhetsgrupp för nätverk som du har kopplat till ett nätverksgränssnitt, det undernät som nätverksgränssnittet finns i eller båda. Även om säkerhet grupper filtrera trafik till privata IP-adressen för nätverksgränssnittet, när inkommande internet-trafik anländer till den offentliga IP-adressen, Azure omvandlar den offentliga adressen till den privata IP adress, så om en nätverkssäkerhetsgrupp förhindrar den trafikflöde, kommunikationen med den offentliga IP-adressen misslyckas. Du kan visa de effektiva säkerhetsreglerna för ett nätverksgränssnitt och dess undernät med hjälp av den [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), eller [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Nästa steg

Tillåt inkommande Internettrafik till den virtuella datorn med en nätverkssäkerhetsgrupp. Läs hur du skapar en nätverkssäkerhetsgrupp i [arbeta med nätverkssäkerhetsgrupper](manage-network-security-group.md#work-with-network-security-groups). Mer information om nätverkssäkerhetsgrupper finns [säkerhetsgrupper](security-overview.md).