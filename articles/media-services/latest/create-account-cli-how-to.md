---
title: Skapa ett Azure Media Services-konto med Azure CLI | Microsoft Docs
description: Följ stegen i den här snabbstarten för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: ca01f32709ce7c9fc49629415cd8697a9d9ba43a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091957"
---
# <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Om du vill starta kryptering, kodning, analysera, hantera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto skapar du också ett associerat lagringskonto (eller använder ett befintligt) i samma geografiska område som Media Services-kontot.

Det här avsnittet beskriver stegen för att skapa ett nytt Azure Media Services-konto med Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in i [Azure Portal](http://portal.azure.com) och starta **CloudShell** för att köra CLI-kommandon som visas i nästa steg.

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
