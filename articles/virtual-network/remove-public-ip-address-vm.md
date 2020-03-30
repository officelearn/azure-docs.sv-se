---
title: Ta bort en offentlig IP-adress från en virtuell Azure-dator
titlesuffix: Azure Virtual Network
description: Lär dig hur du tar bort en offentlig IP-adress från en virtuell dator
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900748"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Ta bort en offentlig IP-adress från en virtuell Azure-dator 

I den här artikeln får du lära dig hur du tar bort en offentlig IP-adress från en virtuell Azure-dator (VM).

Du kan använda [Azure-portalen,](#azure-portal)Azure [command-line interface](#azure-cli) (CLI) eller [PowerShell](#powershell) för att ta bort en offentlig IP-adress från en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller sök efter den virtuella datorn som du vill ta bort den offentliga IP-adressen från och välj den.
3. På sidan Virtuell dator väljer du **Översikt**, väljer du den offentliga IP-adressen som visas i följande bild:

   ![Välj offentlig IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. På sidan offentlig IP-adress väljer du **Översikt**och väljer sedan **Ta bort kopplingen**, som visas i följande bild:

    ![Koppla bort offentlig IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Välj **Ja**i **Koppla bort offentlig IP-adress**.

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Markera knappen **Prova** i de CLI-kommandon som följer. Om du väljer **Prova anropar** du ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i Bash `az login`loggar du in på Azure med .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) för att ta bort en offentlig IP-adress från en IP-konfiguration. I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* från IP-konfigurationen *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) för att visa dem. I följande kommando visas till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I föregående exempel är *myVMVMNic* namnet på nätverksgränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) för att hämta dem. Följande kommando visar till exempel namnen på de offentliga IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Om du inte känner till namnet på en offentlig IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) för att hämta dem. Följande kommando visar till exempel namnen på de offentliga IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat att använda med ditt konto. Markera knappen **Prova** i de PowerShell-kommandon som följer. Om du väljer **Prova anropar** du ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar `Connect-AzAccount`du in på Azure med .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta ett nätverksgränssnitt. Ange värdet för offentlig IP-adress till null och använd sedan kommandot [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverksgränssnittet.

   I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* från ett nätverksgränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *myVM*. Alla resurser finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. I följande kommando visas till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *myVMVMNic* namnet på nätverksgränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. I följande kommando visas till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Utdata innehåller en eller flera rader som liknar det exempel som följer. I exempelutdata är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [associerar en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md).
