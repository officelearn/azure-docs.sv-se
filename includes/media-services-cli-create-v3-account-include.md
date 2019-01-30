---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4207031652db7ec4b2ae5468dc159320f6efdbc2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228822"
---
## <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Du måste först skapa ett Media Services-konto. Det här avsnittet visas vad du behöver för att skapa för kontot med hjälp av Azure CLI.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd följande kommando för att skapa en resursgrupp. En Azure-resursgrupp är en logisk container där resurser som Azure Media Services-konton och associerade Storage-konton distribueras och hanteras.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Azure Storage-konto](../articles/storage/common/storage-account-options.md). 

Mer information om hur lagringskonton används i Media Services finns i [Lagringskonton](../articles/media-services/latest/storage-account-concept.md).

Följande kommando skapar ett lagringskonto som ska associeras med Media Services-kontot. I skriptet nedan kan du ersätta `storageaccountforams` med ditt värde. Kontonamnet måste ha en längd som är mindre än 24.

```azurecli
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Följande Azure CLI-kommando skapar ett nytt Media Services-konto. Du kan ersätta följande värden: `amsaccount` `storageaccountforams` (måste matcha värdet som du gav för ditt lagringskonto) och `amsResourceGroup` (måste matcha värdet som du gav för resursgruppen).  

```azurecli
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
