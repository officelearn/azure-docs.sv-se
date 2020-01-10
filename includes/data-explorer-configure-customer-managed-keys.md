---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5c51a32c9dd82f2efe469d7a8844ed518b8f4d59
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725720"
---
Azure Datautforskaren krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för data kryptering. Kundhanterade nycklar måste lagras i en [Azure Key Vault](/azure/key-vault/key-vault-overview). Du kan skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda ett Azure Key Vault API för att generera nycklar. Azure Datautforskaren-klustret och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. En detaljerad förklaring om Kundhanterade nycklar finns i [Kundhanterade nycklar med Azure Key Vault](/azure/storage/common/storage-service-encryption). Den här artikeln visar hur du konfigurerar Kundhanterade nycklar.

> [!Note]
> Om du vill konfigurera Kundhanterade nycklar med Azure Datautforskaren måste du [Ange två egenskaper för nyckel valvet](/azure/key-vault/key-vault-ovw-soft-delete): **mjuk borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard. Om du vill aktivera dessa egenskaper använder du [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) eller [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli). Endast RSA-nycklar och nyckel storlek 2048 stöds.

## <a name="assign-an-identity-to-the-cluster"></a>Tilldela en identitet till klustret

Om du vill aktivera Kundhanterade nycklar för klustret, tilldelar du först en systemtilldelad hanterad identitet till klustret. Du använder den här hanterade identiteten för att ge kluster behörighet att komma åt nyckel valvet. Information om hur du konfigurerar systemtilldelade hanterade identiteter finns i [hanterade identiteter](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckel valv

Anropa [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault.md)om du vill skapa ett nytt nyckel valv med PowerShell. Nyckel valvet som du använder för att lagra Kundhanterade nycklar för Azure Datautforskaren-kryptering måste ha två nyckel skydds inställningar aktiverade, **mjuk borttagning** och **Rensa inte**. Ersätt plats hållarnas värden inom hakparenteser med dina egna värden i exemplet nedan.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera åtkomst principen för nyckel valvet

Konfigurera sedan åtkomst principen för nyckel valvet så att klustret har behörighet att komma åt det. I det här steget ska du använda den systemtilldelade hanterade identitet som du tidigare tilldelade till klustret. Ange åtkomst principen för nyckel valvet genom att anropa [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy.md). Ersätt plats hållarnas värden inom hakparenteser med dina egna värden och Använd de variabler som definierats i föregående exempel.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckel valvet. Om du vill skapa en ny nyckel anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey.md). Ersätt plats hållarnas värden inom hakparenteser med dina egna värden och Använd de variabler som definierats i föregående exempel.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
