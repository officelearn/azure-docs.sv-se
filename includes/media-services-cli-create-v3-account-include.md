---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 02/21/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 79af6512e9ce3d3f897be216ee3626c5d4fbcf1d
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56741366"
---
## <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Du måste först skapa ett Media Services-konto. Det här avsnittet visas vad du behöver för att skapa för kontot med hjälp av Azure CLI.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd följande kommando för att skapa en resursgrupp. En Azure-resursgrupp är en logisk container där resurser som Azure Media Services-konton och associerade Storage-konton distribueras och hanteras.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Mer information om hur lagringskonton används i Media Services finns i [Lagringskonton](../articles/media-services/latest/storage-account-concept.md).

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Alternativ för Azure Storage-konton](../articles/storage/common/storage-account-options.md). 

I det här exemplet skapar vi en generell användning v2, Standard LRS-konto. Om du vill experimentera med storage-konton kan använda `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du, `--sku Standard_RAGRS`, vilket ger geografiska replikering för affärskontinuitet. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Följande kommando skapar ett lagringskonto som ska associeras med Media Services-kontot. I skriptet nedan kan du ersätta `storageaccountforams` med ditt värde. Kontonamnet måste ha en längd som är mindre än 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Följande Azure CLI-kommando skapar ett nytt Media Services-konto. Du kan ersätta följande värden: `amsaccount` `storageaccountforams` (måste matcha värdet som du gav för ditt lagringskonto) och `amsResourceGroup` (måste matcha värdet som du gav för resursgruppen).  

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
