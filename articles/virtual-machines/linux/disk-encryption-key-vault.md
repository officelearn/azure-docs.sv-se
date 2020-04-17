---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0038d5fdb38fdcfd4130a710f51d764e0cf9d907
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459822"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i [Komma igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och Säkra ditt [nyckelvalv](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Mer information finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version).](disk-encryption-key-vault-aad.md)

Att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption innebär tre steg:

1. Skapa en resursgrupp om det behövs.
2. Skapar ett nyckelvalv. 
3. Ställa in avancerade åtkomstprinciper för nyckelvalv.

De här stegen illustreras i följande snabbstarter:

- [Skapa och kryptera en virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-cli-quickstart.md)

Du kan också, om du vill, generera eller importera en nyckelkrypteringsnyckel (KEK).

> [!Note]
> Stegen i den här artikeln är automatiserade i [Azure Disk Encryption förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och Azure Disk Kryptering förutsättningar [PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Stegen i den här artikeln kan slutföras med [Azure CLI](/cli/azure/), [Azure PowerShell Az-modulen](/powershell/azure/overview)eller [Azure-portalen](https://portal.azure.com). 

Medan portalen är tillgänglig via din webbläsare kräver Azure CLI och Azure PowerShell lokal installation. Se [Azure Disk Encryption för Linux: Installera verktyg](disk-encryption-linux.md#install-tools-and-connect-to-azure) för mer information.

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör det genom [att logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), Logga in med Azure [Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)eller ange dina autentiseringsuppgifter till Azure-portalen när du uppmanas.

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
