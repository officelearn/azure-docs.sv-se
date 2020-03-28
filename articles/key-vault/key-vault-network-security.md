---
title: Konfigurera Azure Key Vault-brandväggar och virtuella nätverk – Azure Key Vault
description: Steg-för-steg-instruktioner för att konfigurera Key Vault-brandväggar och virtuella nätverk
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 8f483f8d383da1f9ba05eb172db185bec9406c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78195166"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault-brandväggar och virtuella nätverk

Den här artikeln innehåller steg-för-steg-instruktioner för att konfigurera Azure Key Vault-brandväggar och virtuella nätverk för att begränsa åtkomsten till nyckelvalvet. [Slutpunkterna för den virtuella nätverkstjänsten för Key Vault](key-vault-overview-vnet-service-endpoints.md) gör att du kan begränsa åtkomsten till ett angivet virtuellt nätverk och en uppsättning IPv4-adressintervall (internet protocol version 4).

> [!IMPORTANT]
> När brandväggsregler har gällande kan användare bara utföra Key [Vault-dataplansåtgärder](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) när deras begäranden kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Key Vault från Azure-portalen. Även om användare kan bläddra till ett nyckelvalv från Azure-portalen kanske de inte kan lista nycklar, hemligheter eller certifikat om klientdatorn inte finns i listan över tillåtna. Detta påverkar också Nyckelvalvväljaren av andra Azure-tjänster. Användare kanske kan se en lista över nyckelvalv, men inte listnycklar, om brandväggsregler förhindrar klientdatorn.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här konfigurerar du Key Vault-brandväggar och virtuella nätverk med hjälp av Azure-portalen:

1. Bläddra till det nyckelvalv som du vill skydda.
2. Välj **Nätverk**och välj sedan fliken **Brandväggar och virtuella nätverk.**
3. Under **Tillåt åtkomst från**väljer du Markerade **nätverk**.
4. Om du vill lägga till befintliga virtuella nätverk i brandväggar och virtuella nätverksregler väljer du **+ Lägg till befintliga virtuella nätverk**.
5. I det nya bladet som öppnas väljer du den prenumeration, de virtuella nätverk och undernät som du vill tillåta åtkomst till det här nyckelvalvet. Om de virtuella nätverk och undernät som du väljer inte har aktiverat tjänstslutpunkter bekräftar du att du vill aktivera tjänstslutpunkter och väljer **Aktivera**. Det kan ta upp till 15 minuter att träda i kraft.
6. Under **IP-nätverk**lägger du till IPv4-adressintervall genom att skriva IPv4-adressintervall i [CIDR-notation (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
7. Välj **Spara**.

Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera tjänstslutpunkter för de nyskapade virtuella nätverken och undernäten genom att välja **+ Lägg till nytt virtuellt nätverk**. Följ sedan anvisningarna.

## <a name="use-the-azure-cli"></a>Använda Azure CLI 

Så här konfigurerar du Key Vault-brandväggar och virtuella nätverk med hjälp av Azure CLI

1. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [logga in](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista tillgängliga virtuella nätverksregler. Om du inte har angett några regler för det här nyckelvalvet är listan tom.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivera en tjänstslutpunkt för Key Vault i ett befintligt virtuellt nätverk och undernät.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Lägg till ett IP-adressintervall som du kan tillåta trafik från.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara `bypass` `AzureServices`tillgängligt för alla betrodda tjänster anger du till .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivera nätverksreglerna genom att ange `Deny`standardåtgärden till .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här konfigurerar du Key Vault-brandväggar och virtuella nätverk med PowerShell:

1. Installera den senaste [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)och [logga in](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista tillgängliga virtuella nätverksregler. Om du inte har angett några regler för det här nyckelvalvet är listan tom.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivera tjänstslutpunkt för Key Vault i ett befintligt virtuellt nätverk och undernät.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Lägg till ett IP-adressintervall som du kan tillåta trafik från.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara `bypass` `AzureServices`tillgängligt för alla betrodda tjänster anger du till .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivera nätverksreglerna genom att ange `Deny`standardåtgärden till .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenser

* Azure CLI-kommandon: [az keyvault-nätverksregel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Slutpunkter för virtuella nätverkstjänster för Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)
