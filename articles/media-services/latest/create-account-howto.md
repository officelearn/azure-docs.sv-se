---
title: Skapa ett Azure Media Services-konto
description: Den här självstudien går igenom stegen för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478804"
---
# <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

För att börja kryptera, koda, analysera, hantera och strömma medieinnehåll i Azure behöver du skapa ett Media Services-konto. Media Services-kontot måste vara associerat med ett eller flera lagringskonton.

> [!NOTE]
> Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.

I den här artikeln beskrivs steg för att skapa ett nytt Azure Media Services-konto. Välj bland följande flikar.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure-portalen är ett sätt att snabbt skapa ett Azure Media Services-konto. Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure.

För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att:

* hantera Media Services v3 [Live Events](live-events-outputs-concept.md), 
* visa (inte hantera) [v3-tillgångar](assets-concept.md), 
* [få information om hur du använder API:er](access-api-portal.md). 

För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) and [Content protection)](content-protection-overview.md)använder du [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

I den här artikeln beskrivs hur man skapar ett Media Services-konto i Azure-portalen.

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Klicka på **+Skapa en resurs** > **Media** > **Media Services**.
1. I avsnittet **Skapa ett Media Services-konto** anger du obligatoriska värden.
    
    | Namn | Beskrivning |
    | ---|---|
    |**Kontonamn**|Ange namnet på det nya Media Services-kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.|
    |**Prenumeration**|Om du har mer än en prenumeration väljer du en i listan över Azure-prenumerationer som du har åtkomst till.|
    |**Resursgrupp**|Välj den nya eller befintliga resursen. En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Läs mer [här](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Location**|Välj det geografiska område som ska användas för att lagra medie- och metadataposterna för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. |
    |**Lagringskonto**|Välj ett lagringskonto för att tillhandahålla blob-lagring av medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.<br/><br/>Du måste ha ett **primärt** lagringskonto och du kan ha valfritt antal **sekundära lagringskonton** kopplade till ditt Media Services-konto. Du kan använda Azure-portalen för att lägga till sekundära lagringskonton. Mer information finns i [Azure Storage-konton med Azure Media Services-konton](storage-account-concept.md).<br/><br/>Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.|
    
1. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
1. Klicka på **Skapa** längst ned i formuläret.

    När ditt Media Services-konto skapas läggs en **standardslutpunkt** för direktuppspelning till ditt konto i tillståndet **Stoppad.** Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och [dynamisk kryptering](content-protection-overview.md)måste slutpunkten för direktuppspelning som du vill strömma innehåll från vara i **körläge.** 

## <a name="use-the-azure-cli"></a>Använda Azure CLI

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd följande kommando för att skapa en resursgrupp. En Azure-resursgrupp är en logisk container där resurser som Azure Media Services-konton och associerade Storage-konton distribueras och hanteras.

Du kan `amsResourceGroup` ersätta med ditt värde.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Mer information om hur lagringskonton används i Media Services finns i [Lagringskonton](storage-account-concept.md).

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Alternativ för Azure Storage-konton](../../storage/common/storage-account-options.md). 

I det här exemplet skapar vi ett General Purpose v2, Standard LRS-konto. Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som ger geografisk replikering för affärskontinuitet. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Följande kommando skapar ett lagringskonto som ska associeras med Media Services-kontot. I skriptet nedan kan du ersätta `storageaccountforams` med ditt värde. `amsResourceGroup`måste matcha värdet som du gav för resursgruppen i föregående steg. Lagringskontonamnet måste ha en längd som är mindre än 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Följande Azure CLI-kommando skapar ett nytt Media Services-konto. Du kan ersätta följande `amsaccount` `storageaccountforams` värden: (måste matcha värdet du `amsResourceGroup` gav för ditt lagringskonto) och (måste matcha värdet du gav för resursgruppen).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Se även

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Koppla en sekundär lagring till ett Media Services-konto](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
