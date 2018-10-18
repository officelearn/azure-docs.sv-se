---
title: Skapa ett Azure Media Services-konto med Azure CLI | Microsoft Docs
description: Följ stegen i den här snabbstarten för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 988b56569c2537f9f0c74b1bcc2bf5e2a9400a80
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378813"
---
# <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Om du vill starta kryptering, kodning, analysera, hantera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. Vid tidpunkt du skapar ett Media Services-konto, du också skapa en tillhörande storage-konto (eller Använd en befintlig) i samma geografiska område som Media Services-kontot.

Den här artikeln beskriver steg för att skapa ett nytt Azure Media Services-konto med Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på den [Azure-portalen](http://portal.azure.com) och starta **CloudShell** att köra CLI-kommandon som visas i nästa steg.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)
