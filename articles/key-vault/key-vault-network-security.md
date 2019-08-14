---
title: Konfigurera Azure Key Vault brand väggar och virtuella nätverk – Azure Key Vault
description: Steg för steg-anvisningar för att konfigurera Key Vault brand väggar och virtuella nätverk
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 60378632a55fe4578bb376a3a00de5efffc5d275
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976958"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault brand väggar och virtuella nätverk

Den här artikeln innehåller stegvisa instruktioner för att konfigurera Azure Key Vault brand väggar och virtuella nätverk för att begränsa åtkomsten till ditt nyckel valv. Med [tjänst slut punkter för virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md) kan du begränsa åtkomsten till ett angivet virtuellt nätverk och en uppsättning adress intervall för IPv4 (Internet Protocol version 4).

> [!IMPORTANT]
> När brand Väggs reglerna är aktiva kan användarna bara utföra Key Vault [data Plans](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) åtgärder när deras begär Anden härstammar från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av Azure Portal:

1. Bläddra till det nyckel valv som du vill skydda.
2. Välj **brand väggar och virtuella nätverk**.
3. Under **Tillåt åtkomst från**väljer du **valda nätverk**.
4. Om du vill lägga till befintliga virtuella nätverk i brand väggar och regler för virtuella nätverk väljer du **+ Lägg till befintliga virtuella nätverk**.
5. På det nya bladet som öppnas väljer du den prenumeration, de virtuella nätverk och undernät som du vill ge åtkomst till det här nyckel valvet. Om de virtuella nätverk och undernät du väljer inte har aktiverat tjänst slut punkter, bekräftar du att du vill aktivera tjänstens slut punkter och väljer **Aktivera**. Det kan ta upp till 15 minuter att börja gälla.
6. Under **IP-nätverk**lägger du till IPv4-adress intervall genom att ange IPv4-adress intervall i [CIDR-notering (Classless Inter-Domain routing)](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
7. Välj **Spara**.

Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera tjänstens slut punkter för de nyligen skapade virtuella nätverken och under näten genom att välja **+ Lägg till nytt virtuellt nätverk**. Följ sedan anvisningarna.

## <a name="use-the-azure-cli"></a>Använda Azure CLI 

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av Azure CLI

1. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Logga](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)in.

2. Visa lista över tillgängliga virtuella nätverks regler. Om du inte har angett några regler för det här nyckel valvet kommer listan att vara tom.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivera en tjänst slut punkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Lägg till en regel för ett virtuellt nätverk och undernät.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Lägg till ett IP-adressintervall som trafiken ska tillåtas från.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Om det här nyckel valvet ska vara tillgängligt för alla betrodda tjänster `bypass` anger `AzureServices`du till.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivera nätverks reglerna genom att ställa in standard åtgärden till `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av PowerShell:

1. Installera den senaste [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)och [Logga](https://docs.microsoft.com/powershell/azure/authenticate-azureps)in.

2. Visa lista över tillgängliga virtuella nätverks regler. Om du inte har angett några regler för det här nyckel valvet kommer listan att vara tom.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivera tjänstens slut punkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Lägg till en regel för ett virtuellt nätverk och undernät.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Lägg till ett IP-adressintervall som trafiken ska tillåtas från.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Om det här nyckel valvet ska vara tillgängligt för alla betrodda tjänster `bypass` anger `AzureServices`du till.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivera nätverks reglerna genom att ställa in standard åtgärden till `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenser

* Azure CLI-kommandon: [AZ-nätverk-regel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlet: ar: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Tjänst slut punkter för virtuella nätverk för Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)
