---
title: Distribuera LUIS-behållaren för Azure Container-instanser
titleSuffix: Azure Cognitive Services
description: Distribuera LUIS-behållaren till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: f1a0a08351a03e46d6c3a1e82b68ecea6e36c015
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757240"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuera LUIS-behållaren (Language Understanding) till Azure Container-instanser

Lär dig hur du [LUIS](luis-container-howto.md) distribuerar Cognitive Services LUIS-behållaren till Azure [Container-instanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar att en avvikelsedetektorresurs skapas. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

LUIS-behållaren `.gz` kräver en modellfil som hämtas vid körning. Behållaren måste kunna komma åt den här modellfilen via ett volymfäste från behållarinstansen. Information om hur du skapar en Azure-filresurs finns i [skapa en filresurs](../../storage/files/storage-how-to-create-file-share.md). Notera azure storage-kontonamnet, nyckeln och filresursnamnet när du behöver dem senare.

### <a name="export-and-upload-packaged-luis-app"></a>Exportera och ladda upp paketerad LUIS-app

För att kunna överföra LUIS-modellen (paketerad app) till Azure-filresursen måste du <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">exportera den från LUIS-portalen först <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Gå till sidan **Översikt** för lagringskontoresursen från Azure-portalen och välj **Filresurser**. Markera filresursnamnet som du nyligen skapade och välj sedan **knappen Ladda upp.**

> [!div class="mx-imgBorder"]
> ![Ladda upp till filresurs](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Ladda upp LUIS-modellfilen.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
