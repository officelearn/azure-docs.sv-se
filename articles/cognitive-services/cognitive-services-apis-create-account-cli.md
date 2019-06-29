---
title: Skapa ett Cognitive Services-konto med hjälp av Azure CLI
titlesuffix: Azure Cognitive Services
description: 'Så här skapar du ett konto för Azure Cognitive Services API: er med hjälp av Azure CLI.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454899"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Skapa ett Cognitive Services-konto med Azure Command-Line Interface(CLI)

I den här snabbstarten du lär dig hur du registrerar dig för Azure Cognitive Services och skapa ett konto som har en prenumeration för en tjänst eller flera tjänster med hjälp av den [Azure Command Line Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). De här tjänsterna representeras av Azure [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör det möjligt att ansluta till en eller flera av Azure-API: er för Cognitive Services.

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration. [Skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.
* Den [Interface(CLI) för Azure-kommandorad](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Installera Azure CLI och logga in 

Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Om du vill logga in på den lokala installationen av CLI, kör den [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index#az-login) kommando:

```console
az login
```

Du kan också använda gröna **prova** för att köra kommandona i din webbläsare.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Skapa en ny resursgrupp för Azure Cognitive Services

Dina prenumerationer till Cognitive Services representeras av Azure-resurser. Varje Cognitive Services-konto (och dess associerade Azure-resurs) måste tillhöra en Azure-resursgrupp.

### <a name="choose-your-resource-group-location"></a>Välj din plats för resursgruppen

För att skapa en resurs, behöver du en av de Azure platserna som är tillgängliga för din prenumeration. Du kan hämta en lista över tillgängliga platser med den [az konto list-locations](/cli/azure/account#az_account_list) kommando. De flesta Cognitive Services kan nås från flera platser. Välj det som är närmast dig, eller se vilka platser som är tillgängliga för tjänsten.

> [!IMPORTANT]
> * Kom ihåg dina Azure-plats som du behöver den när du anropar Azure Cognitive Services.
> * Tillgängligheten för vissa Cognitive Services kan variera beroende på region. Mer information finns i [Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

När du har din azure-plats kan du skapa en ny resursgrupp i Azure CLI med den [az gruppen skapa](/cli/azure/group#az_group_create) kommando.

I exemplet nedan ersätter du den azure-platsen `westus2` med någon av de Azure platserna som är tillgänglig för din prenumeration.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Välj en cognitive service och prisnivå

När du skapar en ny resurs, du behöver veta vilken ”typ” av tjänsten som du vill använda, tillsammans med den [prisnivån](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller sku) du vill. Du använder detta och annan information som parametrar när du skapar resursen.

> [!NOTE]
> Många kognitiva tjänster har en kostnadsfri nivå som du kan använda för att testa tjänsten. Om du vill använda den kostnadsfria nivån, använda `F0` som SKU: n för din resurs.

### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Kind                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision - förutsägelse | `CustomVision.Prediction` |
| Custom Vision - utbildning   | `CustomVision.Training`   |
| Ansikts-API                   | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

### <a name="search"></a>Search

| Tjänst            | Kind                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad sökning i Bing | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Tal

| Tjänst            | Kind                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

### <a name="language"></a>Språk

| Tjänst            | Kind                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

### <a name="decision"></a>Beslut

| Tjänst           | Kind               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |

Du hittar en lista över tillgängliga Cognitive Service ”typ” med den [az cognitiveservices account list-typer](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) kommando:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Lägg till en ny resurs i resursgruppen

Om du vill skapa och prenumerera på en ny resurs för Cognitive Services kan använda den [az cognitiveservices-konto skapar](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) kommando. Det här kommandot lägger till en ny fakturerbar resurs resursgruppen som skapades tidigare. När du skapar nya resursen kan du behöver veta ”typ” av tjänsten som du vill använda tillsammans med dess prisnivå nivå (eller sku) och en Azure-plats:

Du kan skapa en F0 (kostnadsfri)-resurs för Avvikelseidentifiering detektor, med namnet `anomaly-detector-resource` med kommandot nedan.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Hämta nycklar för din prenumeration

Logga in på den lokala installationen av kommandoradsverktyget Interface(CLI), använda den [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) kommando.

```console
az login
```

Använd den [az cognitiveservices account nycklar lista](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) kommando för att hämta nycklar för din Cognitive Service-resurs.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Ta bort resursgruppen raderas även andra resurser som är associerade med resursgruppen.

Ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot med kommandot az group delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Se också

* [Autentisera begäranden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för docker-behållare](cognitive-services-container-support.md)
