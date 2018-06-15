---
title: Snabbstart – Skapa ett Azure Media Services-konto med CLI 2.0 | Microsoft Docs
description: Följ stegen i den här snabbstarten för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779726"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Snabbstart: Skapa ett Azure Media Services-konto

> [!NOTE]
> Den senaste versionen av Azure Media Services (2018-03-30) finns som förhandsversion. Den här versionen kallas även v3. 

Oavsett om du är utvecklare eller innehållsskapare, behöver du ett Media Services-konto för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure. När du skapar ett Media Services-konto, måste du ange ID:t för en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Lagringskontoresursen måste finnas i samma geografiska område som Media Services-kontot.  

Denna snabbstart beskriver stegen för att skapa ett nytt Azure Media Services-konto med hjälp av CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in i [Azure Portal](http://portal.azure.com) och starta **CloudShell** för att köra CLI-kommandon som visas i nästa steg.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resursgrupp

Följande kommando skapar en resursgrupp, som du lägger till Storage- och Media Services-kontot i. Ersätt platshållaren *myresourcegroup* med det namn du vill använda för resursgruppen.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

När du skapar ett Media Services-konto, måste du ange ID:t för en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Alternativ för Azure Storage-konton](../../storage/common/storage-account-options.md). 

Följande kommando skapar ett lagringskonto som associeras med Media Services-kontot (primär). Ersätt platshållaren *storageaccountforams* i skriptet nedan. Längden på 'account_name' måste vara mindre än 24.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Nedan hittar du Azure CLI-kommandona som skapar ett nytt Media Services-konto. Du behöver bara ersätta följande markerade värden:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services-kontot som du skapade i snabbstarten, kan du ta bort resursgruppen.

Kör följande kommando i **CloudShell**:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)
