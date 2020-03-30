---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297888"
---
## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv

Om du vill skapa ett nytt nyckelvalv med PowerShell anropar du [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Nyckelvalvet som du använder för att lagra kundhanterade nycklar för Azure Data Explorer-kryptering måste ha två nyckelskyddsinställningar aktiverade, **Mjuk borttagning** och **Rensa inte**. Ersätt platshållarvärdena inom parentes med dina egna värden i exemplet nedan.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera principen för nyckelvalvsåtkomst

Konfigurera sedan åtkomstprincipen för nyckelvalvet så att klustret har behörighet att komma åt den. I det här steget ska du använda den systemtilldelade hanterade identitet som du tidigare har tilldelat klustret. Om du vill ange åtkomstprincipen för nyckelvalvet anropar du [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ersätt platshållarvärdena inom parentes med dina egna värden och använd de variabler som definierats i föregående exempel.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckelvalvet. Om du vill skapa en ny nyckel anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ersätt platshållarvärdena inom parentes med dina egna värden och använd de variabler som definierats i föregående exempel.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
