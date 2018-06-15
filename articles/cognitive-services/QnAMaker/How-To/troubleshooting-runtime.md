---
title: Felsökning av QnAMaker | Microsoft Docs
titleSuffix: Azure
description: Felsökning av QnAMaker-körning
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356038"
---
# <a name="qnamaker-troubleshooting"></a>Felsökning av QnAMaker
QnAMaker består av komponenter som finns i användarens Azure-konto. Felsökning kan kräva att användare att hantera sina QnAMaker Azure-resurser eller ange QnAMaker supportteamet med ytterligare information om deras inställningar.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hur du hämtar senaste QnAMaker runtime uppdateringar
QnAMaker runtime är en del av Azure App Service distribueras när du [skapar du en tjänst för QnAMaker](./set-up-qnamaker-service-azure.md) i Azure-portalen. Uppdateringar görs med jämna mellanrum i körningsmiljön. Om du vill använda de senaste uppdateringarna till gälla QnAMaker-installation, måste du starta om App Service.
1. Gå till din QnAMaker-tjänst (resursgrupp) i den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service och öppna översiktsavsnittet

     ![QnAMaker Apptjänst](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Starta om appen. Det bör slutföras inom några sekunder. Observera att din nedströms program/robotar bygger på den här QnAMaker-tjänsten inte tillgänglig för slutanvändarna under denna tid för omstart.

    ![QnAMaker apptjänst omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Hur du hämtar QnAMaker service värdnamn
QnAMaker service värdnamn är användbart för felsökning när du kontaktar supporten QnAMaker eller UserVoice. Värdnamnet är en URL för det här formuläret: https://*{hostname}*. azurewebsites.net.
    
1. Gå till din QnAMaker-tjänst (resursgrupp) i den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klicka på App Service

     ![QnAMaker Apptjänst](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Hostname-URL finns i avsnittet Översikt

    ![QnAMaker värdnamn](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd QnAMaker API](./upgrade-qnamaker-service.md)
