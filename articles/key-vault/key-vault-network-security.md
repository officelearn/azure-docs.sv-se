---
ms.assetid: ''
title: Konfigurera Azure Key Vault-brandväggar och virtuella nätverk – Azure Key Vault
description: Stegvisa instruktioner för att konfigurera Key Vault-brandväggar och virtuella nätverk
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 09a19b92a496650f94be208d4f463f1fb3fa4256
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001863"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault-brandväggar och virtuella nätverk

Den här artikeln innehåller stegvisa instruktioner för att konfigurera Azure Key Vault-brandväggar och virtuella nätverk för att begränsa åtkomsten till ditt nyckelvalv. Den [tjänstslutpunkter i virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md) kan du begränsa åtkomsten till ett angivet virtuellt nätverk och en uppsättning IPv4 (internet protocol version 4)-adressintervall.

> [!IMPORTANT]
> När brandväggsregler är aktiverat kan användarna kan bara utföra Key Vault [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) åtgärder när deras begäranden som kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Nyckelvalv från Azure-portalen. Även om användarna kan bläddra till ett nyckelvalv i Azure Portal, kanske de inte att lista nycklar, hemligheter eller certifikat om klientdatorn inte är i listan över tillåtna. Detta påverkar även väljaren för Key Vault med andra Azure-tjänster. Användare kanske kan se listan över nyckelvalv, men inte att lista nycklar, om brandväggsregler förhindra klientdatorn.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Här är hur du konfigurerar Key Vault-brandväggar och virtuella nätverk med hjälp av Azure portal:

1. Bläddra till det nyckelvalv som du vill skydda.
2. Välj **brandväggar och virtuella nätverk**.
3. Under **tillåta åtkomst från**väljer **valda nätverk**.
4. Om du vill lägga till befintliga virtuella nätverk i brandväggar och virtuella Nätverksregler, Välj **+ Lägg till befintliga virtuella nätverk**.
5. I det nya bladet som öppnas väljer du den prenumeration, virtuella nätverk och undernät som du vill tillåta åtkomst till det här nyckelvalvet. Om virtuella nätverk och undernät som du väljer inte har aktiverat Tjänsteslutpunkter kan bekräfta att du vill aktivera Tjänsteslutpunkter och välj **aktivera**. Det kan ta upp till 15 minuter ska börja gälla.
6. Under **IP-nätverk**, Lägg till IPv4-adressintervall genom att skriva IPv4-adressintervall i [CIDR (Classless Inter-Domain Routing)-format](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
7. Välj **Spara**.

Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera Tjänsteslutpunkter för den nyligen skapade virtuella nätverk och undernät, genom att välja **+ Lägg till nytt virtuellt nätverk**. Följ sedan anvisningarna.

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0

Här är hur du konfigurerar Key Vault-brandväggar och virtuella nätverk med hjälp av Azure CLI 2.0:

1. [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) och [logga in](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista över tillgängliga virtuella Nätverksregler. Om du inte angett några regler för den här key vault, kommer listan vara tom.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivera en tjänstslutpunkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Lägg till en regel för ett virtuellt nätverk och undernät.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Lägg till ett IP-adressintervall som du vill tillåta trafik från.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara tillgängliga för alla betrodda tjänster, ställa in `bypass` till `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivera network-regler genom att ställa in standardåtgärden `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Här är hur du konfigurerar Key Vault-brandväggar och virtuella nätverk med hjälp av PowerShell:

1. Installera senast [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), och [logga in](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista över tillgängliga virtuella Nätverksregler. Om du inte har några regler för den här key vault, kommer listan vara tom.
   ```PowerShell
   (Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivera tjänstslutpunkten för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```PowerShell
   Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
   ```

4. Lägg till en regel för ett virtuellt nätverk och undernät.
   ```PowerShell
   $subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Lägg till ett IP-adressintervall som du vill tillåta trafik från.
   ```PowerShell
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara tillgängliga för alla betrodda tjänster, ställa in `bypass` till `AzureServices`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivera network-regler genom att ställa in standardåtgärden `Deny`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenser

* Azure CLI 2.0-kommandon: [az keyvault-regel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets: [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [lägga till AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Tjänstslutpunkter i virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)