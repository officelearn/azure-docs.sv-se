---
title: Uppgradera QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Dela eller uppgradera dina QnA Maker-tjänster för att hantera resurserna bättre.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640489"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Dela eller uppgradera QnA Maker-tjänsten
Dela eller uppgradera dina QnA Maker-tjänster för att hantera resurserna bättre. 

Du kan välja att uppgradera enskilda komponenter i QnA Maker stacken efter den första skapandet. Se information om beroende komponenter och SKU-val [här](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Dela befintliga tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. För att minska hanteringen och dra nytta av kostnads delning kan du använda följande tabell för att förstå vad du kan och inte kan dela:

|Tjänsten|Dela|
|--|--|
|Cognitive Services|X|
|Apptjänstavtal|✔|
|Apptjänst|X|
|Application Insights|✔|
|Söktjänst|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Uppgradera QnA Maker Management SKU

När du behöver fler frågor och svar i din kunskaps bas, utöver din nuvarande nivå, uppgraderar du QnA Maker-tjänstens pris nivå. 

Så här uppgraderar du SKU: n för QnA Maker hantering:

1. Gå till din QnA Maker-resurs i Azure Portal och välj **pris nivå**.

    ![QnA Maker resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Välj lämplig SKU och tryck på **Välj**.

    ![QnA Maker priser](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uppgradera App Service

 När din kunskaps bas behöver hantera fler förfrågningar från din klient app uppgraderar du din app service-pris nivå.

Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned App Service.

1. Gå till App Service-resursen i Azure Portal och välj **skala upp** eller **ned ned** alternativ efter behov.

    ![QnA Maker App Service Scale](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uppgradera Azure Search-tjänsten

När du planerar att ha många kunskaps baser uppgraderar du Azure Search-tjänstens pris nivå. 

För närvarande går det inte att utföra en uppgradering på plats av Azure Search-SKU: n. Du kan dock skapa en ny Azure Search-resurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker stacken.

1. Skapa en ny Azure Search-resurs i Azure Portal och välj önskad SKU.

    ![QnA Maker Azure Search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Återställ indexen från din ursprungliga Azure Search-resurs till den nya. Se exempel koden för Backup Restore [här](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. När data har återställts går du till din nya Azure Search-resurs, väljer **nycklar**och noterar **namnet** och administratörs **nyckeln**.

    ![QnA Maker Azure Search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Om du vill länka den nya Azure Search-resursen till QnA Maker stack går du till QnA Maker app service.

    ![QnA Maker AppService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Välj **program inställningar** och ersätt fälten **AzureSearchName** och **AzureSearchAdminKey** från steg 3.

    ![QnA Maker AppService-inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Starta om App service.

    ![QnA Maker AppService-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd API för QnA Maker](../Quickstarts/csharp.md)
