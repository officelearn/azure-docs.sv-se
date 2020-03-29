---
title: Skapa en Cognitive Services-resurs med Hjälp av Azure CLI
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs med hjälp av Azure-kommandoradsgränssnittet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219614"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Skapa en Cognitive Services-resurs med hjälp av AZURE Command-Line Interface(CLI)

Använd den här snabbstarten för att komma igång med Azure Cognitive Services med hjälp av [Azure Command Line Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Cognitive Services representeras av [Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) som du skapar i din Azure-prenumeration. När du har skapat resursen använder du de nycklar och en slutpunkt som du har skapat för att autentisera dina program. 


I den här snabbstarten får du lära dig hur du registrerar dig för Azure Cognitive Services och skapar ett konto som har en prenumeration med en tjänst eller flera tjänster, med hjälp av [Azure Command Line Interface(CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Dessa tjänster representeras av [Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör att du kan ansluta till en eller flera av Azure Cognitive Services API:er.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Krav

* En giltig Azure-prenumeration - [Skapa en](https://azure.microsoft.com/free/) gratis.
* [Azure-kommandoradsgränssnittet(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Installera Azure CLI och logga in 

Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Om du vill logga in på din lokala installation av CLI kör du kommandot [az login:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Du kan också använda den gröna **Prova-knappen** för att köra dessa kommandon i din webbläsare.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Skapa en ny Azure Cognitive Services-resursgrupp

Innan du skapar en Cognitive Services-resurs måste du ha en Azure-resursgrupp för att innehålla resursen. När du skapar en ny resurs kan du välja att antingen skapa en ny resursgrupp eller använda en befintlig resurs. Den här artikeln visar hur du skapar en ny resursgrupp.

### <a name="choose-your-resource-group-location"></a>Välj plats för resursgrupp

Om du vill skapa en resurs behöver du en av de Azure-platser som är tillgängliga för din prenumeration. Du kan hämta en lista över tillgängliga platser med kommandot [az-kontolisteplatser.](/cli/azure/account#az-account-list-locations) De flesta kognitiva tjänster kan nås från flera platser. Välj den som är närmast dig eller se vilka platser som är tillgängliga för tjänsten.

> [!IMPORTANT]
> * Kom ihåg din Azure-plats, eftersom du behöver den när du anropar Azure Cognitive Services.
> * Tillgängligheten för vissa kognitiva tjänster kan variera mellan olika regioner. Mer information finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

När du har din azure-plats skapar du en ny resursgrupp i Azure CLI med kommandot [az group create.](/cli/azure/group#az-group-create)

I exemplet nedan ersätter `westus2` du azure-platsen med en av de Azure-platser som är tillgängliga för din prenumeration.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Välj en kognitiv tjänst och prisnivå

När du skapar en ny resurs måste du känna till vilken typ av tjänst du vill använda, tillsammans med den [prisnivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller sku) du vill ha. Du kommer att använda den här och annan information som parametrar när du skapar resursen.

### <a name="multi-service"></a>Flera tjänster

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Flera tjänster. Se [prissidan](https://azure.microsoft.com/pricing/details/cognitive-services/) för mer information.            | `CognitiveServices`     |


> [!NOTE]
> Många av Cognitive Services nedan har en kostnadsfri nivå som du kan använda för att prova tjänsten. Om du vill använda `F0` den kostnadsfria nivån använder du som sku för din resurs.

### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Anpassad vision - Förutsägelse | `CustomVision.Prediction` |
| Anpassad vision - Utbildning   | `CustomVision.Training`   |
| Ansikte                       | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

### <a name="search"></a>Search

| Tjänst            | Variant                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad Bing-sökning | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Tal

| Tjänst            | Variant                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

### <a name="language"></a>Språk

| Tjänst            | Variant                |
|--------------------|---------------------|
| Formulärsyssning | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

### <a name="decision"></a>Beslut

| Tjänst           | Variant               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |

Du kan hitta en lista över tillgängliga Cognitive Service "typer" med [az cognitiveservices konto lista-typer](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) kommando:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Lägga till en ny resurs i resursgruppen

Om du vill skapa och prenumerera på en ny Cognitive Services-resurs använder du kommandot [az cognitiveservices account create.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) Det här kommandot lägger till en ny fakturerbar resurs i resursgruppen som skapats tidigare. När du skapar din nya resurs måste du känna till vilken typ av tjänst du vill använda, tillsammans med dess prisnivå (eller sku) och en Azure-plats:

Du kan skapa en F0 -resurs (ledig) för avvikelsedetektor, med namnet `anomaly-detector-resource` med kommandot nedan.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklarna till din resurs

Om du vill logga in på din lokala installation av Command-Line Interface(CLI) använder du kommandot [az login.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Använd [listkommandot az cognitiveservices-kontonycklar](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) för att hämta nycklarna till din Cognitive Service-resurs.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prisnivåer och fakturering

Prisnivåer (och det belopp du faktureras) baseras på antalet transaktioner som du skickar med hjälp av din autentiseringsinformation. Varje prisnivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänstfunktioner som är aktiverade inom prisnivån.
* Kostnaden för ett fördefinierat transaktionsbelopp. Att gå över detta belopp kommer att orsaka en extra avgift som anges i [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

## <a name="get-current-quota-usage-for-your-resource"></a>Hämta aktuell kvotanvändning för din resurs

Använd kommandot [az cognitiveservices account list-usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) för att hämta användningen för din Cognitive Service-resurs.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-resurs kan du ta bort den eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som finns i gruppen bort.

Om du vill ta bort resursgruppen och dess associerade resurser använder du kommandot az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Se även

* [Autentisera begäranden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)
