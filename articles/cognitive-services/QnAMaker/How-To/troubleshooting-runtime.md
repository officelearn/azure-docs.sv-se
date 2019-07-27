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
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559918"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Felsökningstips för QnA Maker-tjänsten och runtime
QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker-supportteamet med ytterligare information om deras inställningar.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hur du hämtar senaste uppdateringarna för körning av QnAMaker
QnAMaker runtime är en del av Azure App Service distribueras när du [skapar du en tjänst för QnAMaker](./set-up-qnamaker-service-azure.md) i Azure-portalen. Uppdateringarna görs regelbundet för körningen. QnA Maker App Service är i läget för automatisk uppdatering publicera vår apr 2019 site Extension-version (version 5 +). Detta är redan utformat för att ta hand om noll stillestånd vid uppgraderingar. Du kan kontrol lera din aktuella version https://www.qnamaker.ai/UserSettings på. Om din version är äldre än version 5. x måste du starta om App Service för att tillämpa de senaste uppdateringarna för din QnAMaker-installation.

1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service och öppna översiktsavsnittet

     ![Apptjänst för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Starta om App service. Det bör vara klart inom några sekunder. Observera att din nedströms program/robotar som bygger på den här QnAMaker-tjänsten är inte tillgänglig för slutanvändarna under perioden omstart.

    ![QnAMaker appservice-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Så här hämtar du värdnamnet för QnAMaker-tjänsten
QnAMaker service värdnamn är användbart för felsökning när du kontaktar supporten för QnAMaker eller UserVoice. Värdnamnet är en URL för det här formatet: https:// *{värdnamnet}* . azurewebsites.net.
    
1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp i Azure-portalen](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service

     ![Välj QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Hostname-URL: en finns i avsnittet Översikt

    ![Värdnamn för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbättra kunskaps bas frågorna med aktiv utbildning](./improve-knowledge-base.md)
