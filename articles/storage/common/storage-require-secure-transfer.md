---
title: Kräver säker överföring i Azure Storage | Microsoft Docs
description: Mer information om funktionen ”säker överföring krävs” för Azure Storage och hur du aktiverar den.
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 0ec36864377d724795197851341cbb837f47c4c6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599806"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Kräv säker överföring i Azure Storage

Alternativet ”säker överföring krävs” förbättrar säkerheten för ditt lagringskonto genom att bara tillåta begäranden till kontot från säkra anslutningar. Till exempel när du anropar REST API: er för att få åtkomst till ditt lagringskonto, måste du ansluta med hjälp av HTTPS. ”Säker överföring krävs” avvisar begäranden som använder HTTP.

När du använder tjänsten Azure-filer misslyckas en anslutning utan kryptering när ”säker överföring krävs” är aktiverat. Detta inkluderar scenarier som använder SMB 2.1 och SMB 3.0 utan kryptering vissa versioner av Linux SMB-klienten. 

”Säker överföring krävs”-alternativet är inaktiverat som standard.

> [!NOTE]
> Eftersom Azure Storage stöder HTTPS för anpassade domännamn, tillämpas inte det här alternativet när du använder ett anpassat domännamn. Och klassiska lagringskonton stöds inte.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Aktivera ”säker överföring krävs” i Azure-portalen

Du kan aktivera ”säker överföring krävs” inställningen när du skapar ett lagringskonto i den [Azure-portalen](https://portal.azure.com). Du kan också aktivera den för befintliga lagringskonton.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Kräv säker överföring för ett nytt lagringskonto

1. Öppna den **skapa lagringskonto** rutan i Azure-portalen.
1. Under **säker överföring krävs**väljer **aktiverad**.

  ![Skapa kontot lagring-bladet](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Kräv säker överföring för ett befintligt lagringskonto

1. Välj ett befintligt lagringskonto i Azure-portalen.
1. I lagringen konto menyn rutan under **inställningar**väljer **Configuration**.
1. Under **säker överföring krävs**väljer **aktiverad**.

  ![Storage-konto menyn rutan](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Aktivera ”säker överföring krävs” programmässigt

Använd inställningen för att kräva säker överföring via programmering _supportsHttpsTrafficOnly_ i lagringskontoegenskaperna med REST API, verktyg eller bibliotek:

* [REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (version: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (version: 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (version: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (version: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (version: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (version: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (version: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Aktivera ”säker överföring krävs” med PowerShell

Det här exemplet kräver Azure PowerShell Modulversion 4.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Kör `Connect-AzureRmAccount` för att skapa en anslutning med Azure.

 Använd följande kommandorad för att kontrollera inställningen:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Använd följande kommandorad för att aktivera inställningen:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Aktivera ”säker överföring krävs” med CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Använd följande kommandorad för att kontrollera inställningen:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Använd följande kommandorad för att aktivera inställningen:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Nästa steg
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner för, vilket tillsammans ger utvecklare möjligheten att skapa säkra program. Mer information finns i den [säkerhetsguiden för lagring](storage-security-guide.md).
