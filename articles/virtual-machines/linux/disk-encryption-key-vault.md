---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0038d5fdb38fdcfd4130a710f51d764e0cf9d907
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459822"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter.  Mer information om nyckel valv finns i [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och [skydda nyckel valvet](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md) .

Att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption omfattar tre steg:

1. Skapa en resurs grupp, om det behövs.
2. Skapar ett nyckel valv. 
3. Ställer in avancerade åtkomst principer för nyckel valvet.

Dessa steg illustreras i följande snabb starter:

- [Skapa och kryptera en virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-cli-quickstart.md)

Du kan också, om du vill, skapa eller importera en nyckel krypterings nyckel (KEK).

> [!Note]
> Stegen i den här artikeln är automatiserade i [skripten för Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och [Azure Disk Encryption krav på PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Stegen i den här artikeln kan utföras med [Azure CLI](/cli/azure/), [Azure PowerShell AZ-modulen](/powershell/azure/overview)eller [Azure Portal](https://portal.azure.com). 

Även om portalen är tillgänglig via webbläsaren kan Azure CLI och Azure PowerShell kräva lokal installation. Se [Azure Disk Encryption for Linux: installera verktyg](disk-encryption-linux.md#install-tools-and-connect-to-azure) för mer information.

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör detta genom att [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)eller ange dina autentiseringsuppgifter till Azure Portal när du uppmanas till det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
