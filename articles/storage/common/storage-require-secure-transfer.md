---
title: Kräv säker överföring för att säkerställa säkra anslutningar
titleSuffix: Azure Storage
description: Lär dig hur du kräver säker överföring för begäranden till Azure Storage. När du behöver säker överföring för ett lagringskonto avvisas alla begäranden som kommer från en osäker anslutning.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870522"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Kräv säker överföring för att säkerställa säkra anslutningar

Du kan konfigurera ditt lagringskonto så att det accepterar begäranden från säkra anslutningar endast genom att ange egenskapen **Säker överföring krävs** för lagringskontot. När du behöver säker överföring avvisas alla begäranden som kommer från en osäker anslutning. Microsoft rekommenderar att du alltid behöver säker överföring för alla dina lagringskonton.

När säker överföring krävs måste ett anrop till en AZURE Storage REST API-åtgärd göras via HTTPS. Alla förfrågningar som görs via HTTP avvisas.

Att ansluta till en Azure File-resurs över SMB utan kryptering misslyckas när säker överföring krävs för lagringskontot. Exempel på osäkra anslutningar är de som gjorts över SMB 2.1, SMB 3.0 utan kryptering, eller vissa versioner av Linux SMB-klienten.

Som standard **aktiveras** egenskapen Säker överföring när du skapar ett lagringskonto.

> [!NOTE]
> Eftersom Azure Storage inte stöder HTTPS för anpassade domännamn tillämpas inte det här alternativet när du använder ett anpassat domännamn. Och klassiska lagringskonton stöds inte.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Kräv säker överföring i Azure-portalen

Du kan aktivera egenskapen **Säker överföring** när du skapar ett lagringskonto i [Azure-portalen](https://portal.azure.com). Du kan också aktivera den för befintliga lagringskonton.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Kräv säker överföring för ett nytt lagringskonto

1. Öppna fönstret **Skapa lagringskonto** i Azure-portalen.
1. Under **Säker överföring krävs**väljer du **Aktiverad**.

   ![Skapa lagringskontoblad](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Kräv säker överföring för ett befintligt lagringskonto

1. Välj ett befintligt lagringskonto i Azure-portalen.
1. Välj Konfiguration under **INSTÄLLNINGAR**i **menyfönstret**för lagringskonto.
1. Under **Säker överföring krävs**väljer du **Aktiverad**.

   ![Menyfönstret för lagringskonto](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Kräv säker överföring från kod

Om du vill kräva säker överföring programmässigt anger du _egenskapenHttpsTrafficOnly_ på lagringskontot. Du kan ange den här egenskapen med hjälp av REST-API:ET för lagringsresursprovidern, klientbibliotek eller verktyg:

* [REST-API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Kräv säker överföring med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell-modulen Az version 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

 Använd följande kommandorad för att kontrollera inställningen:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Använd följande kommandorad för att aktivera inställningen:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Kräv säker överföring med Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Använd följande kommando för att kontrollera inställningen:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Använd följande kommando för att aktivera inställningen:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Nästa steg

[Säkerhetsrekommendationer för Blob-lagring](../blobs/security-recommendations.md)
