---
title: 'Snabbstart: Skapa, distribuera och använda en anpassad modell – Custom Translator'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kan du gå igenom processen för att skapa ett översättningssystem med hjälp av Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: b0992c4d18fdb9cb5201ab3ef52fba8ee3feb7a2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964387"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Snabbstart: Skapa, distribuera och använda en anpassad modell för översättning

Den här artikeln innehåller stegvisa instruktioner för att skapa ett översättningssystem med Custom Translator.

## <a name="prerequisites"></a>Förutsättningar

1. För att använda [Custom Translator](https://portal.customtranslator.azure.ai)-portalen behöver du ett [Microsoft-konto](https://signup.live.com) eller [Azure AD-konto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (organisationskonto på Azure) för att logga in.

2. En prenumeration på Translator via Azure Portal. Du behöver en översättnings prenumerations nyckel för att koppla till din arbets yta i en anpassad översättare. Se [hur du registrerar dig för Translator](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. När du har båda ovanstående loggar du in på den [anpassade översättnings](https://portal.customtranslator.azure.ai) portalen för att skapa arbets ytor, projekt, ladda upp filer och skapa/distribuera modeller.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du första gången är användare uppmanas du att godkänna villkoren för tjänsten för att skapa en arbets yta och koppla din arbets yta till Translator-prenumerationen.

![Skapa arbets yta skapa arbets yta skapa arbets yta skapa arbets yta skapa arbets yta skapa arbets yta ](media/quickstart/terms-of-service.png)
 ![ ](media/quickstart/create-workspace-1.png)
 ![ ](media/quickstart/create-workspace-2.png)
 ![ ](media/quickstart/create-workspace-3.png)
 ![ ](media/quickstart/create-workspace-4.png)
 ![ ](media/quickstart/create-workspace-5.png)
 ![ skapa arbets yta](media/quickstart/create-workspace-6.png)

På efterföljande besök på den anpassade översättnings portalen navigerar du till sidan inställningar där du kan hantera din arbets yta, skapa fler arbets ytor, associera din Translator-prenumerations nyckel med dina arbets ytor, lägga till medägare och ändra en prenumerations nyckel.

## <a name="create-a-project"></a>Skapa ett projekt

Klicka på Nytt projekt Custom Translator-portalens landningssida. I dialogrutan kan du ange önskat projektnamn, språkpar och kategori samt övriga relevanta fält. Spara sedan ditt projekt. Mer information finns i [Skapa projekt](how-to-create-project.md).

![Skapa projekt](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Ladda upp dokument

Ladda sedan upp dokumentuppsättningarna för [träning](training-and-model.md#training-document-type-for-custom-translator), [justering](training-and-model.md#tuning-document-type-for-custom-translator) och [testning](training-and-model.md#testing-dataset-for-custom-translator). Du kan ladda upp både [parallella](what-are-parallel-documents.md) dokument och kombinationsdokument. Du kan också ladda upp en [ordlista](what-is-dictionary.md).

Du kan ladda upp dokument från dokumentfliken eller från en specifik projektsida.

![Ladda upp dokument](media/quickstart/ct-how-to-upload.png)

När du laddar upp dokument väljer du dokumenttyp (träning, justering eller testning) och språkpar. När du överför parallella dokument måste du också ange ett dokumentnamn. Mer information finns i [Ladda upp dokument](how-to-upload-document.md).

## <a name="create-a-model"></a>Skapa en modell

När alla nödvändiga dokument laddas upp är nästa steg att skapa din modell.

Välj det projekt som du har skapat. Alla dokument du har laddat upp som delar ett språkpar med det här projektet visas. Välj de dokument som du vill inkludera i din modell. Du kan välja [utbildning](training-and-model.md#training-document-type-for-custom-translator), [träning](training-and-model.md#tuning-document-type-for-custom-translator), och [testdata](training-and-model.md#testing-dataset-for-custom-translator) eller välja enbart justeringsdata och låta Custom Translator automatiskt skapa justerings- och testuppsättningar för din modell.

![Skapa en modell](media/quickstart/ct-how-to-train.png)

När du har valt önskade dokument klickar du på knappen Skapa modell för att skapa din modell och starta träningen. Du kan se statusen för din träning och information för alla modeller som du har tränat på fliken Modeller.

Mer information finns i [Skapa en modell](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analysera din modell

När träningen har slutförts kan du granska resultaten. BLEU-poängen är ett mått som anger kvaliteten på din översättning. Du kan också manuellt jämföra översättningar som gjorts med din anpassade modell med översättningarna som tillhandahålls i testuppsättningen genom att gå till fliken ”Test” och klicka på ”Systemresultat”. När du kontrollerar några av översättningarna manuellt får du en uppfattning om kvaliteten på översättningen som genereras av systemet. Läs mer i informationen om [testresultat för system](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Distribuera en tränad modell

När du är redo att distribuera din tränade modell klickar du på knappen ”Distribuera”. Du kan ha en distribuerad modell per projekt och du kan visa statusen för distributionen i kolumnen Status. Mer information finns i [Modelldistribution](how-to-view-system-test-results.md#deploy-a-model)

![Distribuera en tränad modell](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Använda en distribuerad modell

Distribuerade modeller kan nås via Translator genom att ange kategorin kategori] ( https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) . Mer information om Translator hittar du på webb sidan [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du navigerar i [Custom Translator-arbetsytan och hanterar dina projekt](workspace-and-project.md).
