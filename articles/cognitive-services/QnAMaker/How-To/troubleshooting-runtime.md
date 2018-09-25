---
title: Felsökning – QnAMaker
titlesuffix: Azure Cognitive Services
description: QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker-supportteamet med ytterligare information om deras inställningar.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ad3c01ebf37c8b544830b281144090694eeadfcd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033743"
---
# <a name="qnamaker-troubleshooting"></a>Felsökning av QnAMaker
QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker-supportteamet med ytterligare information om deras inställningar.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hur du hämtar senaste uppdateringarna för körning av QnAMaker
QnAMaker runtime är en del av Azure App Service distribueras när du [skapar du en tjänst för QnAMaker](./set-up-qnamaker-service-azure.md) i Azure-portalen. Uppdateringarna görs regelbundet för körningen. Om du vill använda de senaste uppdateringarna ska tillämpas på din konfiguration för QnAMaker, måste du starta om App Service.
1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service och öppna översiktsavsnittet

     ![Apptjänst för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Starta om App service. Det bör vara klart inom några sekunder. Observera att din nedströms program/robotar som bygger på den här QnAMaker-tjänsten är inte tillgänglig för slutanvändarna under perioden omstart.

    ![QnAMaker appservice-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Så här hämtar du värdnamnet för QnAMaker-tjänsten
QnAMaker service värdnamn är användbart för felsökning när du kontaktar supporten för QnAMaker eller UserVoice. Värdnamnet är en URL för det här formatet: https://*{värdnamnet}*. azurewebsites.net.
    
1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service

     ![Apptjänst för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Hostname-URL: en finns i avsnittet Översikt

    ![Värdnamn för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd om API för QnAMaker](./upgrade-qnamaker-service.md)
