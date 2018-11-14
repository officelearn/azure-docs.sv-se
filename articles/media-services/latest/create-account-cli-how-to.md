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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: cb49b642137517c0ceef7d2fa01994a554db1f4e
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613167"
---
# <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Om du vill starta kryptering, kodning, analysera, hantera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. Vid tidpunkt du skapar ett Media Services-konto, du också skapa en tillhörande storage-konto (eller Använd en befintlig) i samma geografiska område som Media Services-kontot.

Den här artikeln beskriver steg för att skapa ett nytt Azure Media Services-konto med Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- Installera och använda CLI lokalt kan du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) kommandona fungerar i Azure Cloud Shell. Det rekommenderas att använda CLI lokalt.

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

