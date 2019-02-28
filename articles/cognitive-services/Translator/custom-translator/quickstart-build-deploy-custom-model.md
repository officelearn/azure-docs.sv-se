---
title: 'Snabbstart: Skapa, distribuera och använda en anpassad modell – Custom Translator'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kan du gå igenom processen för att skapa ett översättningssystem med hjälp av Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: 25ff375b3b29729270b23372d82d1978c4ae37be
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735007"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Snabbstart: Skapa, distribuera och använda en anpassad modell för översättning

Den här artikeln innehåller stegvisa instruktioner för att skapa ett översättningssystem med Custom Translator.

## <a name="prerequisites"></a>Nödvändiga komponenter

1. För att använda [Custom Translator](https://portal.customtranslator.azure.ai)-portalen behöver du ett [Microsoft-konto](https://signup.live.com) eller [Azure AD-konto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (organisationskonto på Azure) för att logga in.

2. En prenumeration på Translator Text API via Azure-portalen. Du behöver prenumerationsnyckeln för Translator Text API för att associera med din arbetsyta i Custom Translator. Se [hur du registrerar dig för Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. När du har båda av ovanstående kan du logga in på [Custom Translator](https://portal.customtranslator.azure.ai)-portalen. När du är i Custom Translator-portalen går du till sidan Inställningar där du kan koppla din Microsoft Translator Text API-prenumerationsnyckel till din arbetsyta.

## <a name="create-a-project"></a>Skapa ett projekt

Klicka på Nytt projekt Custom Translator-portalens landningssida. I dialogrutan kan du ange önskat projektnamn, språkpar och kategori samt övriga relevanta fält. Spara sedan ditt projekt. Mer information finns i [Skapa projekt](how-to-create-project.md).

![Skapa projekt](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Ladda upp dokument

Ladda sedan upp dokumentuppsättningarna för [träning](training-and-model.md#training-dataset-for-custom-translator), [justering](training-and-model.md#tuning-dataset-for-custom-translator) och [testning](training-and-model.md#testing-dataset-for-custom-translator). Du kan ladda upp både [parallella](what-are-parallel-documents.md) dokument och kombinationsdokument. Du kan också ladda upp en [ordlista](what-is-dictionary.md).

Du kan ladda upp dokument från dokumentfliken eller från en specifik projektsida.

![Ladda upp dokument](media/quickstart/ct-how-to-upload.png)

När du laddar upp dokument väljer du dokumenttyp (träning, justering eller testning) och språkpar. När du överför parallella dokument måste du också ange ett dokumentnamn. Mer information finns i [Ladda upp dokument](how-to-upload-document.md).

## <a name="create-a-model"></a>Skapa en modell

När alla nödvändiga dokument laddas upp är nästa steg att skapa din modell.

Välj det projekt som du har skapat. Alla dokument du har laddat upp som delar ett språkpar med det här projektet visas. Välj de dokument som du vill inkludera i din modell. Du kan välja [utbildning](training-and-model.md#training-dataset-for-custom-translator), [träning](training-and-model.md#tuning-dataset-for-custom-translator), och [testdata](training-and-model.md#testing-dataset-for-custom-translator) eller välja enbart justeringsdata och låta Custom Translator automatiskt skapa justerings- och testuppsättningar för din modell.

![Skapa en modell](media/quickstart/ct-how-to-train.png)

När du har valt önskade dokument klickar du på knappen Skapa modell för att skapa din modell och starta träningen. Du kan se statusen för din träning och information för alla modeller som du har tränat på fliken Modeller.

Mer information finns i [Skapa en modell](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analysera din modell

När träningen har slutförts kan du granska resultaten. BLEU-poängen är ett mått som anger kvaliteten på din översättning. Du kan också manuellt jämföra översättningar som gjorts med din anpassade modell med översättningarna som tillhandahålls i testuppsättningen genom att gå till fliken ”Test” och klicka på ”Systemresultat”. När du kontrollerar några av översättningarna manuellt får du en uppfattning om kvaliteten på översättningen som genereras av systemet. Läs mer i informationen om [testresultat för system](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Distribuera en tränad modell

När du är redo att distribuera din tränade modell klickar du på knappen ”Distribuera”. Du kan ha en distribuerad modell per projekt och du kan visa statusen för distributionen i kolumnen Status. Mer information finns i [Modelldistribution](how-to-view-system-test-results.md#deploy-a-model)

![Distribuera en tränad modell](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Använda en distribuerad modell

Du kan nå distribuerade modeller via Microsoft Translator [Text API V3 genom att ange CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Mer information om Translator Text API kan hittas på webbsidan [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du navigerar i [Custom Translator-arbetsytan och hanterar dina projekt](workspace-and-project.md).
