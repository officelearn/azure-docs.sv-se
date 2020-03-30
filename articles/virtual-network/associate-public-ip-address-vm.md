---
title: Associera en offentlig IP-adress till en virtuell dator
titlesuffix: Azure Virtual Network
description: Lär dig hur du associerar en offentlig IP-adress till en virtuell dator.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647466"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associera en offentlig IP-adress till en virtuell dator

I den här artikeln får du lära dig hur du associerar en offentlig IP-adress till en befintlig virtuell dator (VM). Om du vill ansluta till en virtuell dator från internet måste den virtuella datorn ha en offentlig IP-adress kopplad till den. Om du vill skapa en ny virtuell dator med en offentlig IP-adress kan du göra det med hjälp av [Azure-portalen,](virtual-network-deploy-static-pip-arm-portal.md) [Azure command-line interface (CLI)](virtual-network-deploy-static-pip-arm-cli.md)eller [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Offentliga IP-adresser har en nominell avgift. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/ip-addresses/). Det finns en gräns för antalet offentliga IP-adresser som du kan använda per prenumeration. Mer information finns i [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Du kan använda [Azure-portalen,](#azure-portal)Azure [command-line interface](#azure-cli) (CLI) eller [PowerShell](#powershell) för att associera en offentlig IP-adress till en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller sök efter den virtuella dator som du vill lägga till den offentliga IP-adressen i och välj den sedan.
3. Under **Inställningar**väljer du **Nätverk**och väljer sedan det nätverksgränssnitt som du vill lägga till den offentliga IP-adressen i, som visas i följande bild:

   ![Välj nätverksgränssnitt](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Offentliga IP-adresser är associerade till nätverksgränssnitt som är kopplade till en virtuell dator. I föregående bild har den virtuella datorn bara ett nätverksgränssnitt. Om den virtuella datorn hade flera nätverksgränssnitt visas alla och du väljer det nätverksgränssnitt som du vill koppla den offentliga IP-adressen till.

4. Välj **IP-konfigurationer** och välj sedan en IP-konfiguration, som visas i följande bild:

   ![Välj IP-konfiguration](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Offentliga IP-adresser är associerade till IP-konfigurationer för ett nätverksgränssnitt. I föregående bild har nätverksgränssnittet en IP-konfiguration. Om nätverksgränssnittet hade flera IP-konfigurationer visas alla i listan och du väljer den IP-konfiguration som du vill associera den offentliga IP-adressen till.

5. Välj **Aktiverad**och välj sedan **IP-adress (*Konfigurera obligatoriska inställningar*)**. Välj en befintlig offentlig IP-adress som automatiskt stänger rutan **Välj offentlig IP-adress.** Om du inte har några tillgängliga offentliga IP-adresser i listan måste du skapa en. Mer information finns i [Skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Välj **Spara**, som visas i bilden som följer, och stäng sedan rutan för IP-konfigurationen.

   ![Aktivera offentlig IP-adress](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De offentliga IP-adresser som visas är de som finns i samma region som den virtuella datorn. Om du har flera offentliga IP-adresser som skapats i regionen visas alla här. Om någon är nedtonad beror det på att adressen redan är kopplad till en annan resurs.

6. Visa den offentliga IP-adressen som tilldelats IP-konfigurationen, som visas i bilden som följer. Det kan ta några sekunder innan en IP-adress visas.

   ![Visa tilldelad offentlig IP-adress](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Information om hur du ser en lista över adresspooler som används i varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Adressen som tilldelats kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en viss pool i regionen använder du ett [prefix för offentlig IP-adress](public-ip-address-prefix.md).

7. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Markera knappen **Prova** i de CLI-kommandon som följer. Om du väljer **Prova anropar** du ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i Bash `az login`loggar du in på Azure med .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) för att associera en offentlig IP-adress till en IP-konfiguration. I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Om du inte har en befintlig offentlig [IP-adress](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) använder du kommandot az network public-ip create för att skapa en. Följande kommando skapar till exempel en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om alla inställningar för offentliga IP-adresser finns i [Skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Information om hur du ser en lista över adresspooler som används i varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) för att visa dem. I följande kommando visas till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I föregående exempel är *myVMVMNic* namnet på nätverksgränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) för att hämta dem. I följande kommando visas till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visa den offentliga IP-adressen som tilldelats IP-konfigurationen med kommandot [az vm list-ip-addresses.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) I följande exempel visas ip-adresserna som tilldelats en befintlig virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Information om hur du ser en lista över adresspooler som används i varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Adressen som tilldelats kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en viss pool i regionen använder du ett [prefix för offentlig IP-adress](public-ip-address-prefix.md).

4. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat att använda med ditt konto. Markera knappen **Prova** i de PowerShell-kommandon som följer. Om du väljer **Prova anropar** du ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar `Connect-AzAccount`du in på Azure med .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd [kommandona Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) och [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) för att hämta det virtuella nätverk och det undernät som nätverksgränssnittet finns i. Använd sedan kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att få ett nätverksgränssnitt och kommandot [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att få en befintlig offentlig IP-adress. Använd sedan kommandot [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) för att associera den offentliga IP-adressen till IP-konfigurationen och kommandot [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverksgränssnittet.

   I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som finns i ett undernät med namnet *myVMSubnet* i ett virtuellt nätverk med namnet *myVMVNet*. Alla resurser finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Om du inte har en befintlig offentlig IP-adress använder du kommandot [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) för att skapa en. Följande kommando skapar till exempel en *dynamisk* offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup* i *regionen eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om alla inställningar för offentliga IP-adresser finns i [Skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Information om hur du ser en lista över adresspooler som används i varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. I följande kommando visas till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *myVMVMNic* namnet på nätverksgränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte känner till namnet på det virtuella nätverket eller undernätet som nätverksgränssnittet finns i använder du `Get-AzNetworkInterface` kommandot för att visa informationen. Följande kommando hämtar till exempel den virtuella nätverks- och undernätsinformationen för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *myVMVNET* namnet på det virtuella nätverket och *myVMSubnet* är namnet på undernätet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. I följande kommando visas till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visa den offentliga IP-adressen som tilldelats IP-konfigurationen med kommandot [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) I följande exempel visas adressen som tilldelats en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Om du inte känner till namnet på den offentliga IP-adress som tilldelats en IP-konfiguration kör du följande kommandon för att hämta den:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *myVMPublicIP* namnet på den offentliga IP-adress som tilldelats IP-konfigurationen.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Information om hur du ser en lista över adresspooler som används i varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Adressen som tilldelats kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en viss pool i regionen använder du ett [prefix för offentlig IP-adress](public-ip-address-prefix.md).

4. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="allow-network-traffic-to-the-vm"></a>Tillåt nätverkstrafik till den virtuella datorn

Innan du kan ansluta till den offentliga IP-adressen från Internet, se till att du har nödvändiga portar öppna i alla nätverkssäkerhetsgrupper som du kan ha associerat till nätverksgränssnittet, undernätet nätverksgränssnittet är i eller båda. Även om säkerhetsgrupper filtrerar trafik till nätverksgränssnittets privata IP-adress översätts den offentliga adressen till den offentliga IP-adressen när inkommande internettrafik anländer till den offentliga IP-adressen. trafikflödet misslyckas kommunikationen med den offentliga IP-adressen. Du kan visa de gällande säkerhetsreglerna för ett nätverksgränssnitt och dess undernät med hjälp av [Portal,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)eller [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Nästa steg

Tillåt inkommande internettrafik till den virtuella datorn med en nätverkssäkerhetsgrupp. Mer information om hur du skapar en nätverkssäkerhetsgrupp finns i [Arbeta med nätverkssäkerhetsgrupper](manage-network-security-group.md#work-with-network-security-groups). Mer information om nätverkssäkerhetsgrupper finns i [Säkerhetsgrupper](security-overview.md).
