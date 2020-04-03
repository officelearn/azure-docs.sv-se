---
title: Använda en Azure Resource Manager-mall för att distribuera tjänsten
titleSuffix: Azure Cognitive Search
description: Du kan snabbt distribuera en Azure Cognitive Search-tjänstinstans med hjälp av Azure Resource Manager-mallen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607626"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Snabbstart: Distribuera kognitiv sökning med hjälp av en Resource Manager-mall

I den här artikeln får du hjälp med att använda en Resource Manager-mall för att distribuera en Azure Cognitive Search-resurs i Azure-portalen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-cognitive-search-service"></a>Skapa en kognitiv söktjänst

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-mallar](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

Azure-resursen som definieras i den här mallen:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): skapa en Azure Cognitive Search-tjänst

### <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en Azure Cognitive Search-resurs.

[![Distribuera till Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portalen visar ett formulär som gör att du enkelt kan ange parametervärden. Vissa parametrar är förfyllda med standardvärdena från mallen. Du måste ange prenumeration, resursgrupp, plats och tjänstnamn. Om du vill använda Cognitive Services i en [AI-anrikningspipeline,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) till exempel för att analysera binära bildfiler för text, väljer du en plats som erbjuder både kognitiv sökning och kognitiva tjänster. Båda tjänsterna måste vara i samma region för AI-anrikningsarbetsbelastningar. När du har fyllt i formuläret måste du godkänna villkoren och sedan välja köpknappen för att slutföra distributionen.

> [!div class="mx-imgBorder"]
> ![Visning av mall i Azure-portalen](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar kan du komma åt din nya resursgrupp och nya söktjänst i portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Andra kognitiva sökning snabbstarter och handledning bygga på denna snabbstart. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter och självstudier, kanske du vill lämna den här resursen på plats. När det inte längre behövs kan du ta bort resursgruppen, som tar bort tjänsten Kognitiv sökning och relaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Cognitive Search-tjänst med hjälp av en Azure Resource Manager-mall och validerade distributionen. Om du vill veta mer om Kognitiv sökning och Azure Resource Manager fortsätter du till artiklarna nedan.

 - Läs en [översikt över Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Skapa ett index](https://docs.microsoft.com/azure/search/search-get-started-portal) för söktjänsten
 - [Skapa en sökapp](https://docs.microsoft.com/azure/search/search-create-app-portal) med portalguiden
 - [Skapa en kompetens](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) för att extrahera information från dina data


