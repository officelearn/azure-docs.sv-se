---
title: Använd en Azure Resource Manager-mall för att distribuera tjänsten
titleSuffix: Azure Cognitive Search
description: Du kan snabbt distribuera en Azure Kognitiv sökning service-instans med hjälp av Azure Resource Manager-mallen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80607626"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Snabb start: Distribuera Kognitiv sökning med en Resource Manager-mall

Den här artikeln vägleder dig genom processen för att använda en Resource Manager-mall för att distribuera en Azure Kognitiv sökning-resurs i Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-cognitive-search-service"></a>Skapa en Kognitiv sökning-tjänst

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure-mallar](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

Den Azure-resurs som definierats i den här mallen:

- [Microsoft. search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): skapa en Azure kognitiv sökning-tjänst

### <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en Azure Kognitiv sökning-resurs.

[![Distribuera till Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portalen visar ett formulär som gör att du enkelt kan ange parameter värden. Vissa parametrar fylls i automatiskt med standardvärdena från mallen. Du måste ange din prenumeration, resurs grupp, plats och tjänst namn. Om du vill använda Cognitive Services i en pipeline för [AI-anrikning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) , till exempel för att analysera binära bildfiler för text, väljer du en plats som erbjuder både Kognitiv sökning och Cognitive Services. Båda tjänsterna måste finnas i samma region för AI-arbetsbelastningar. När du har fyllt i formuläret måste du godkänna de allmänna villkoren och sedan välja knappen köp för att slutföra distributionen.

> [!div class="mx-imgBorder"]
> ![Azure Portal visning av mall](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar kan du komma åt den nya resurs gruppen och den nya Sök tjänsten i portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Andra Kognitiv sökning snabb starter och självstudier som bygger på den här snabb starten. Om du planerar att fortsätta att arbeta med efterföljande snabb starter och självstudier, kanske du vill lämna den här resursen på plats. När de inte längre behövs kan du ta bort resurs gruppen, som tar bort Kognitiv sökning tjänsten och relaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Kognitiv sökning-tjänst med en Azure Resource Manager-mall och validerade distributionen. Om du vill veta mer om Kognitiv sökning och Azure Resource Manager fortsätter du till artiklarna nedan.

 - Läs en [Översikt över Azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Skapa ett index](https://docs.microsoft.com/azure/search/search-get-started-portal) för Sök tjänsten
 - [Skapa en Sök-App](https://docs.microsoft.com/azure/search/search-create-app-portal) med hjälp av Portal guiden
 - [Skapa en färdigheter](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) för att extrahera information från dina data


