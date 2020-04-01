---
title: Exempel på Azure CLI-skript – Rotera åtkomstnycklar för lagringskonto | Microsoft Docs
description: Skapa ett Azure Storage-konto och sedan hämta och rotera kontoåtkomstnycklarna.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: aa7cba00d865bb7b2c1e0fd1f0059fb35ffff690
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060833"
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
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account) | Skapar ett Azure Storage-konto i den angivna resursgruppen. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Visar lagringskontots åtkomstnycklar för det angivna kontot. |
| [az storage account keys renew](/cli/azure/storage/account/keys) | Genererar om den primära eller sekundära åtkomstnyckeln för lagringskontot. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
