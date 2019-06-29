---
title: Felsökning – QnAMaker
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 5b65a121e895b4855c7c69d2b67e7055c88ddd08
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466047"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Felsökningstips för QnA Maker-tjänsten och runtime
QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker-supportteamet med ytterligare information om deras inställningar.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hur du hämtar senaste uppdateringarna för körning av QnAMaker
QnAMaker runtime är en del av Azure App Service distribueras när du [skapar du en tjänst för QnAMaker](./set-up-qnamaker-service-azure.md) i Azure-portalen. Uppdateringarna görs regelbundet för körningen. QnA Maker App Service är på automatisk uppdatering läge efter våra Apr 2019 plats tillägget versionen (version 5 +). Detta är redan utformad för att ta hand om driftstopp under uppgraderingar. Du hittar din nuvarande version i https://www.qnamaker.ai/UserSettings. Om din version är äldre än version 5.x, du måste starta om App Service för att tillämpa de senaste uppdateringarna ska tillämpas på din QnAMaker konfiguration.

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
> [Förbättra kunskapsbas frågor med aktiv inlärning](./improve-knowledge-base.md)
