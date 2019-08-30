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
ms.date: 08/26/2019
ms.author: diberry
ms.openlocfilehash: ba9c2cd5a85e02a7dd4b1091a050d76e94861964
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147090"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Dela eller uppgradera QnA Maker-tjänsten
Dela eller uppgradera dina QnA Maker-tjänster för att hantera resurserna bättre. 

Du kan välja att uppgradera enskilda komponenter i QnA Maker stacken efter den första skapandet. Se information om beroende komponenter och SKU-val [här](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Dela befintliga tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. För att minska hanteringen och dra nytta av kostnads delning kan du använda följande tabell för att förstå vad du kan och inte kan dela:

|Tjänsten|Dela|Reason|
|--|--|--|
|Cognitive Services|X|Inte möjlig enligt design|
|Apptjänstavtal|✔|Fast disk utrymme som allokerats för en app service-plan. Om andra appar, delar samma App Service plan, använder upp till ett stort disk utrymme, kommer QnAMaker App Service att stöta på problem.|
|Apptjänst|X|Inte möjlig enligt design|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten och den kan inte användas av andra.<br>2. Synonym mappning med namnet `synonym-map` är reserverat för QnAMaker-tjänsten.<br>3. Antalet publicerade KB begränsas av Sök tjänst nivån. Om det finns lediga index är andra tjänster som kan använda den.|


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
