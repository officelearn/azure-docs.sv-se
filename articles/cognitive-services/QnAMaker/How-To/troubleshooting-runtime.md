---
title: Felsökning – QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker-supportteamet med ytterligare information om deras inställningar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876331"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Felsökningstips för QnA Maker-tjänsten och runtime

QnAMaker omfattar resurser som finns i användarens Azure-prenumeration. Fel sökning kan kräva att användare ändrar sina Azure QnAMaker-resurser eller ger support teamet för QnAMaker ytterligare information om deras konfiguration.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hur du hämtar senaste uppdateringarna för körning av QnAMaker

QnAMaker runtime är en del av de Azure App Service som distribueras när du [skapar en QnAMaker-tjänst](./set-up-qnamaker-service-azure.md) i Azure Portal. Uppdateringarna görs regelbundet för körningen. QnA Maker App Service är i läget för automatisk uppdatering efter version 2019 av webbplats tillägget (version 5 +). Detta är redan utformat för att ta hand om noll stillestånd vid uppgraderingar. 

Du kan kontrol lera din aktuella version https://www.qnamaker.ai/UserSettings på. Om din version är äldre än version 5. x måste du starta om App Service för att tillämpa de senaste uppdateringarna.

1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klicka på App Service och öppna översiktsavsnittet

     ![Apptjänst för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Starta om App service. Det bör vara klart inom några sekunder. Observera att alla beroende program eller robotar som använder den här QnAMaker-tjänsten inte kommer att vara tillgängliga för slutanvändare under den här omstarts perioden.

    ![QnAMaker appservice-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Så här hämtar du värdnamnet för QnAMaker-tjänsten
QnAMaker service värdnamn är användbart för felsökning när du kontaktar supporten för QnAMaker eller UserVoice. Värd namnet är en URL i det här formatet: https:// *{hostname}* . azurewebsites.net.
    
1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp i Azure-portalen](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj den App Service som är kopplad till QnA Maker resursen. Normalt är namnen desamma.

     ![Välj QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Hostname-URL: en finns i avsnittet Översikt

    ![Värdnamn för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbättra kunskaps bas frågorna med aktiv utbildning](./improve-knowledge-base.md)
