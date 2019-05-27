---
title: Skapa ett Media Services-konto med Azure CLI – Azure | Microsoft Docs
description: Följ stegen i den här snabbstarten för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/19/2019
ms.author: juliako
ms.openlocfilehash: f2cb2e2ee6393a59125ee879f2058516eb50d6b1
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956733"
---
# <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

Om du vill starta kryptering, kodning, analysera, hantera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. Media Services-kontot måste vara associerat med ett eller flera lagringskonton.

> [!NOTE]
> Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.

Den här artikeln beskriver steg för att skapa ett nytt Azure Media Services-konto med Azure CLI.  

## <a name="prerequisites"></a>Nödvändiga komponenter

En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Nästa steg

* [Åtkomst v3-API: er](access-api-cli-how-to.md)
* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Koppla en sekundär lagring till ett Media Services-konto](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

