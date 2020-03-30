---
title: Lägga till eller ta bort en undernätsdelegering i ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du lägger till eller tar bort ett delegerat undernät för en tjänst i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201874"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Lägga till eller ta bort en undernätsdelegering

Undernätsdelegering ger uttryckliga behörigheter till tjänsten för att skapa tjänstspecifika resurser i undernätet med hjälp av en unik identifierare när tjänsten distribueras. I den här artikeln beskrivs hur du lägger till eller tar bort ett delegerat undernät för en Azure-tjänst.

## <a name="portal"></a>Portalen

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet skapar du ett virtuellt nätverk och undernätet som du senare delegerar till en Azure-tjänst.

1. Välj **Skapa ett** > **virtuellt** > **nätverk**längst upp till vänster på skärmen .
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.0.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **EastUS**.|
    | Undernät – Namn | Ange *mySubnet*. |
    | Undernät – adressintervall | Ange *10.0.0.0/24*. |
    |||
1. Lämna resten som standard och välj sedan **Skapa**.

### <a name="permissions"></a>Behörigheter

Om du inte skapade undernätet som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write`.

Rollen Inbyggd [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också nödvändiga behörigheter.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du undernätet som du skapade i föregående avsnitt till en Azure-tjänst.

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I sökresultaten väljer du *myVirtualNetwork*.
3. Välj **Undernät**under **INSTÄLLNINGAR**och välj sedan **mySubnet**.
4. På sidan *mySubnet* för **listan Undernätsdelegering** väljer du bland de tjänster som anges under **Undernät för ombud till en tjänst** (till exempel **Microsoft.DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I sökresultaten väljer du *myVirtualNetwork*.
3. Välj **Undernät**under **INSTÄLLNINGAR**och välj sedan **mySubnet**.
4. På *sidan Subnät* väljer du **Ingen** från tjänsterna som visas under Delegera undernät till en tjänst för undernätslistan på **undernätslistan**För **undernätsdelegering.** 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du bestämmer dig för att installera och använda Azure CLI lokalt i stället kräver den här artikeln att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet **myResourceGroup** på **eastus-platsen:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa det virtuella nätverket **myVnet** med undernätet **mySubnet** i **myResourceGroup** med [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Behörigheter

Om du inte skapade undernätet som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write`.

Rollen Inbyggd [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också nödvändiga behörigheter.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du undernätet som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) för att uppdatera undernätet med namnet **mySubnet** med en delegering till en Azure-tjänst.  I det här exemplet används **Microsoft.DBforPostgreSQL/serversv2** för exempeldelegeringen:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Om du vill kontrollera att delegeringen har tillämpats använder du [az network vnet-undernät.](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Kontrollera att tjänsten har delegerats till undernätet under **egenskapstjänstenName:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

Använd [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) för att ta bort delegeringen från undernätet med namnet **mySubnet:**

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Om du vill kontrollera att delegeringen har tagits bort använder du [az network vnet-undernät.](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Kontrollera att tjänsten tas bort från undernätet under **egenskapstjänstenName:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Utdata från kommandot är en null-hakparentes:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Anslut till Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **mySubnet** med [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) i **myResourceGroup** med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). IP-adressutrymmet för det virtuella nätverket är **10.0.0.0/16**. Undernätet i det virtuella nätverket är **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Behörigheter

Om du inte skapade undernätet som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write`.

Rollen Inbyggd [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också nödvändiga behörigheter.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du undernätet som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) för att uppdatera undernätet med namnet **mySubnet** med en delegering med namnet **myDelegation** till en Azure-tjänst.  I det här exemplet används **Microsoft.DBforPostgreSQL/serversv2** för exempeldelegeringen:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) för att verifiera delegeringen:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

Använd [Ta bort AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) för att ta bort delegeringen från undernätet med namnet **mySubnet:**

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) för att kontrollera att delegeringen har tagits bort:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [hanterar undernät i Azure](virtual-network-manage-subnet.md).
