---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 6350e85552a6c92592dbe2b1a9cf48a35f86a7be
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198453"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter.  Mer information om nyckel valv finns i [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md) och [skydda nyckel valvet](../key-vault/general/secure-your-key-vault.md).

Att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption omfattar tre steg:

1. Skapa en resurs grupp, om det behövs.
2. Skapar ett nyckel valv. 
3. Ställer in avancerade åtkomst principer för nyckel valvet.

Dessa steg illustreras i följande snabb starter:

Du kan också, om du vill, skapa eller importera en nyckel krypterings nyckel (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Stegen i den här artikeln kan utföras med [Azure CLI](/cli/azure/), [Azure PowerShell AZ-modulen](/powershell/azure/overview)eller [Azure Portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör detta genom att [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)eller ange dina autentiseringsuppgifter till Azure Portal när du uppmanas till det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure CLI](disk-encryption-cli.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure PowerShell](disk-encryption-powershell.md)
