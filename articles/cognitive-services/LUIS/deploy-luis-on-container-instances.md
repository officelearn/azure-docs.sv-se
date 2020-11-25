---
title: Distribuera LUIS-behållare på Azure Container instances
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren LUIS till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: edc2ad0f895b8a1bb6448ce1cdf79b1b2ce83951
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95997211"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuera Language Understanding-behållaren (LUIS) till Azure Container instances

Lär dig hur du distribuerar Cognitive Services [Luis](luis-container-howto.md) -behållaren till Azure [container instances](../../container-instances/index.yml). Den här proceduren visar hur du skapar en avvikelse detektor resurs. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

LUIS-containern kräver en `.gz` modell fil som hämtas i vid körning. Behållaren måste kunna komma åt den här modell filen via en volym som monteras från behållar instansen. Information om hur du skapar en Azure-filresurs finns i [skapa en fil resurs](../../storage/files/storage-how-to-create-file-share.md). Anteckna Azure Storage konto namn, nyckel och fil resurs namn som du behöver dem senare.

### <a name="export-and-upload-packaged-luis-app"></a>Exportera och ladda upp paketerad LUIS-app

För att kunna ladda upp LUIS-modellen (paketerad app) till Azure-filresursen måste du <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">först <span class="docon docon-navigate-external x-hidden-focus"></span> Exportera den från Luis-portalen </a>. Från Azure Portal navigerar du till sidan **Översikt** i lagrings konto resursen och väljer **fil resurser**. Välj fil resurs namnet som du nyss skapade och välj sedan knappen **överför** .

> [!div class="mx-imgBorder"]
> ![Ladda upp till fil resurs](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Ladda upp LUIS-modell filen.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]