---
ms.assetid: ''
title: Konfigurera Azure Key Vault-brandväggar och virtuella nätverk
description: Stegvisa instruktioner för att konfigurera Key Vault-brandväggar och virtuella nätverk
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345203"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault-brandväggar och virtuella nätverk

Den här guiden beskriver steg för steg-anvisningar för att konfigurera Key Vault-brandväggar och virtuella nätverk för att begränsa åtkomsten till ditt nyckelvalv. Den [tjänstslutpunkter i virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md) kan du begränsa åtkomsten till nyckelvalvet angivna virtuella nätverket och/eller en uppsättning IPv4 (Internet Protocol version 4)-adressintervall.

> [!IMPORTANT]
> När brandväggen och virtual network-regler i praktiken är alla Nyckelvalv [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) åtgärder kan endast utföras när anroparen begäranden som kommer från tillåtna virtuella nätverk eller IPV4-adressintervall. Detta gäller även för åtkomst till nyckelvalv från Azure-portalen. När en användare kan webbläsaren till ett nyckelvalv från Azure-portalen, kanske de inte att lista nycklar/hemligheter/certificates om klientdatorn inte är i listan över tillåtna. Detta påverkar även 'Key Vault väljare' med andra Azure-tjänster. Användare kanske kan se lista över viktiga valv men inte att lista nycklar, om brandväggsregler förhindra klientdatorn.

## <a name="azure-portal"></a>Azure Portal

1. Gå till nyckelvalvet som du vill skydda.
2. Klicka på **brandväggar och virtuella nätverk**.
3. Klicka på **valda nätverk** under **tillåta åtkomst från**.
4. Om du vill lägga till befintliga virtuella nätverk i brandväggar och virtuella Nätverksregler, klickar du på **+ Lägg till befintliga virtuella nätverk**.
5. I det nya bladet som öppnas väljer du den prenumeration, virtuella nätverk och undernät som du vill tillåta åtkomst till det här nyckelvalvet. Om virtuella nätverk och undernät som du väljer inte har aktiverat Tjänsteslutpunkter ser du meddelandet, ”följande nätverk saknar tjänstens slutpunkter enavled...”. Klicka på **aktivera** när du har bekräftat att du vill aktivera Tjänsteslutpunkter för de angivna virtuella nätverk och undernät. Det kan ta upp till 15 minuter ska börja gälla.
6. Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera Tjänsteslutpunkter för den nyligen skapade virtuella nätverk och undernät, genom att klicka på **+ Lägg till nytt virtuellt nätverk** och följer anvisningarna.
7. Under **IP-nätverk**, du kan lägga till IPv4-adressintervall genom att skriva IPv4-adressintervall i [CIDR (Classless Inter-Domain Routing)-format](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
8. Klicka på **Spara**.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) och [inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista tillgängliga virtuella Nätverksregler, om du inte har några regler för den här key vault, listan ska vara tom.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Aktivera tjänstslutpunkt för Key Vault på ett befintligt virtuellt nätverk och undernät
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Lägg till en regel för ett virtuellt nätverk och undernät
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Lägg till IP-adressintervall för att tillåta trafik från
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Om det här nyckelvalvet måste vara tillgängliga för alla betrodda tjänster, inställd på 'kringgå ”AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Nu slutliga och viktiga steg, Stäng nätverket regler vidare genom att ange standardåtgärden för ”neka”
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Installera senast [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) och [inloggning](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista tillgängliga virtuella Nätverksregler, om du inte har några regler för den här key vault, listan ska vara tom.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Aktivera tjänstslutpunkt för Key Vault på ett befintligt virtuellt nätverk och undernät
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Lägg till en regel för ett virtuellt nätverk och undernät
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Lägg till IP-adressintervall för att tillåta trafik från
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Om det här nyckelvalvet måste vara tillgängliga för alla betrodda tjänster, inställd på 'kringgå ”AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Nu slutliga och viktiga steg, Stäng nätverket regler vidare genom att ange standardåtgärden för ”neka”
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Referenser

* Azure CLI 2.0-kommandon - [az keyvault-regel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets – [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Lägg till AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [ Uppdatera AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Tjänstslutpunkter i virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)