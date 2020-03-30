---
title: Kontrollera krypteringsstatus för en blob - Azure Storage
description: Lär dig hur du använder Azure Portal, PowerShell eller Azure CLI för att kontrollera om en viss blob är krypterad. Om en blob inte är krypterad kan du läsa om hur du använder AzCopy för att tvinga kryptering genom att hämta och ladda upp blobben igen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707585"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Kontrollera krypteringsstatus för en blob

Varje blockblob, tilläggsblob eller sidblob som skrevs till Azure Storage efter den 20 oktober 2017 krypteras med Azure Storage-kryptering. Blobbar som skapats före detta datum fortsätter att krypteras av en bakgrundsprocess.

Den här artikeln visar hur du avgör om en viss blob har krypterats.

## <a name="check-a-blobs-encryption-status"></a>Kontrollera krypteringsstatus för en blob

Använd Azure-portalen, PowerShell eller Azure CLI för att avgöra om en blob är krypterad utan kod.

### <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Så här använder du Azure-portalen för att kontrollera om en blob har krypterats:

1. Navigera till ditt lagringskonto i Azure Portal.
1. Välj **Behållare om** du vill navigera till en lista med behållare i kontot.
1. Leta reda på blobben och visa fliken **Översikt.**
1. Visa egenskapen **Server encrypted.** Om **Sant**, som visas i följande bild, krypteras bloben. Observera att blobens egenskaper även innehåller datum och tid då blobben skapades.

    ![Skärmbild som visar hur du kontrollerar serverkrypterad egenskap i Azure portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill använda PowerShell för att kontrollera om en blob har krypterats kontrollerar du blobens **isserverencrypted-egenskap.** Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Kontrollera värdet för egenskapen **Skapad** för att avgöra när blobben skapades:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill använda Azure CLI för att kontrollera om en blob har krypterats kontrollerar du blobens **isserverencrypted-egenskap.** Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Kontrollera värdet för den **skapade** egenskapen för att avgöra när blobben skapades.

---

## <a name="force-encryption-of-a-blob"></a>Tvinga kryptering av en blob

Om en blob som skapades före den 20 oktober 2017 ännu inte har krypterats av bakgrundsprocessen kan du tvinga krypteringen att ske omedelbart genom att ladda ned och ladda upp blobben igen. Ett enkelt sätt att göra detta är med AzCopy.

Om du vill hämta en blob till ditt lokala filsystem med AzCopy använder du följande syntax:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Om du vill ladda upp blobben till Azure Storage med AzCopy använder du följande syntax:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Mer information om hur du använder AzCopy för att kopiera blob-data finns i [Överföra data med AzCopy- och Blob-lagring](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Nästa steg

[Azure Storage-kryptering för data i vila](../common/storage-service-encryption.md)
