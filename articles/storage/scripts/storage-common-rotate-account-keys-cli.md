---
title: "Exempel på Azure CLI-skript – Rotera åtkomstnycklar för lagringskonto | Microsoft Docs"
description: "Skapa ett Azure Storage-konto och sedan hämta och rotera kontoåtkomstnycklarna."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 160509a5a82b71b281d57d97e103bb4190605b7c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Skapa ett lagringskonto och rotera kontoåtkomstnycklarna

Det här skriptet skapar ett Azure Storage-konto, visar åtkomstnycklarna för det nya lagringskontot och förnyar (roterar) sedan nycklarna.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, lagringskontot och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa lagringskontot och hämta och rotera dess åtkomstnycklar. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Skapar ett Azure Storage-konto i den angivna resursgruppen. |
| [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Visar lagringskontots åtkomstnycklar för det angivna kontot. |
| [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) | Genererar om den primära eller sekundära åtkomstnyckeln för lagringskontot. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
