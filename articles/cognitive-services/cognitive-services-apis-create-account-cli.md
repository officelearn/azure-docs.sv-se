---
title: Skapa en Cognitive Services resurs med hjälp av Azure CLI
titlesuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs med hjälp av Azures kommando rads gränssnitt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: aahi
ms.openlocfilehash: 05b679fd969dc766d697070979416312c3bad622
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334263"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Skapa en Cognitive Services resurs med hjälp av kommando rads gränssnittet för Azure (CLI)

Använd den här snabb starten för att komma igång med Azure Cognitive Services med hjälp av [kommando rads gränssnittet för Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Cognitive Services representeras av Azure- [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) som du skapar i din Azure-prenumeration. När du har skapat resursen använder du nycklarna och slut punkten som du skapade för att autentisera dina program. 


I den här snabb starten får du lära dig hur du registrerar dig för Azure Cognitive Services och skapar ett konto som har en tjänst prenumeration eller flera tjänster med hjälp av [kommando rads gränssnittet för Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Dessa tjänster representeras av Azure- [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör att du kan ansluta till en eller flera av azure-API:er för Cognitive Services.

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration – [skapa en](https://azure.microsoft.com/free/) kostnads fri.
* [Kommando rads gränssnittet för Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Installera Azure CLI och logga in 

Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Logga in på den lokala installationen av CLI genom att köra kommandot [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) :

```console
az login
```

Du kan också använda det gröna **try** -knappen för att köra kommandona i webbläsaren.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Skapa en ny resurs grupp för Azure Cognitive Services

Innan du skapar en Cognitive Services resurs måste du ha en Azure-resurs grupp som innehåller resursen. När du skapar en ny resurs har du möjlighet att antingen skapa en ny resurs grupp eller använda en befintlig. Den här artikeln visar hur du skapar en ny resurs grupp.

### <a name="choose-your-resource-group-location"></a>Välj plats för resurs grupp

Om du vill skapa en resurs behöver du en av de Azure-platser som är tillgängliga för din prenumeration. Du kan hämta en lista över tillgängliga platser med kommandot [AZ Account List-locations](/cli/azure/account#az-account-list-locations) . De flesta Cognitive Services kan nås från flera platser. Välj det som är närmast dig eller se vilka platser som är tillgängliga för tjänsten.

> [!IMPORTANT]
> * Kom ihåg Azure-platsen, eftersom du behöver den när du anropar Azure-Cognitive Services.
> * Tillgängligheten för vissa Cognitive Services kan variera beroende på region. Mer information finns i [Azure products by region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

När du har en Azure-plats skapar du en ny resurs grupp i Azure CLI med kommandot [AZ Group Create](/cli/azure/group#az-group-create) .

I exemplet nedan ersätter du Azure-platsen `westus2` med en av de Azure-platser som är tillgängliga för din prenumeration.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Välj en kognitiv tjänst och pris nivå

När du skapar en ny resurs behöver du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du kommer att använda den här och andra information som parametrar när du skapar resursen.

> [!NOTE]
> Många kognitiva tjänster har en kostnads fri nivå som du kan använda för att testa tjänsten. Använd den kostnads fria nivån `F0` som SKU för din resurs.

### <a name="vision"></a>Visuellt innehåll

| Tjänsten                    | Metod                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision förutsägelse | `CustomVision.Prediction` |
| Custom Vision-utbildning   | `CustomVision.Training`   |
| Ansikts-API                   | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

### <a name="search"></a>Search

| Tjänsten            | Metod                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad sökning i Bing | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Tal

| Tjänsten            | Metod                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

### <a name="language"></a>Språk

| Tjänsten            | Metod                |
|--------------------|---------------------|
| Formulär förståelse | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

### <a name="decision"></a>Beslut

| Tjänsten           | Metod               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |

Du hittar en lista över tillgängliga kognitiva tjänst typer med kommandot [AZ cognitiveservices Account List-typ](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Lägg till en ny resurs i resurs gruppen

Om du vill skapa och prenumerera på en ny Cognitive Services-resurs använder du kommandot [AZ cognitiveservices Account Create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) . Detta kommando lägger till en ny fakturerbar resurs till resurs gruppen som skapades tidigare. När du skapar din nya resurs behöver du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats:

Du kan skapa en F0 (kostnads fri) resurs för avvikelse detektor, med `anomaly-detector-resource` namnet med kommandot nedan.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklar för din resurs

Använd kommandot [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) för att logga in på den lokala installationen av kommando rads gränssnittet (CLI).

```console
az login
```

Använd kommandot [AZ cognitiveservices Account Keys List](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) för att hämta nycklarna för din kognitiva tjänst resurs.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services resurs kan du ta bort den eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

Om du vill ta bort resurs gruppen och dess associerade resurser använder du kommandot AZ Group Delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Se också

* [Autentisera begär anden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)
