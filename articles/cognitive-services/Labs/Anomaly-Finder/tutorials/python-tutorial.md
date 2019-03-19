---
title: 'Självstudie: Avvikelseidentifiering, Python'
titlesuffix: Azure Cognitive Services
description: Utforska en Python-anteckningsbok som använder API:et för avvikelseidentifiering. Skicka ursprungliga datapunkter till API:et och få det förväntade värdet och avvikelsepunkter.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 5a2fb54658599e0500944aaae9225f314277f9da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539096"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Självstudie: Avvikelseidentifiering med Python-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Den här självstudien visar hur du använder API:et för avvikelseidentifiering i Python och hur du visualiserar dina resultat med populära bibliotek. Använd Jupyter för att köra självstudien testa dina egna data med din prenumerationsnyckel. Läs hur du kommer igång med interaktiva Jupyter-anteckningsböcker: [Jupyter-dokumentation](https://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på avvikelseidentifiering och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Ladda ned exempelkoden

1. Navigera till [självstudiekursens anteckningsbok i GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Klicka på den gröna knappen för att klona eller hämta självstudien. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiens anteckningsbok i Jupyter

1. Öppna en kommandotolk och gå till mappen python-sample.
2. Kör kommandot jupyter notebook från kommandotolken, vilket startar Jupyter.
3. I fönstret Jupyter klickar du på <em>Example.ipynb för API för avvikelseidentifiering</em> för att öppna anteckningsboken för självstudien.   

## <a name="running-the-tutorial"></a>Köra självstudien

Om du vill använda den här anteckningsboken behöver du en prenumerationsnyckel för API:et för avvikelseidentifiering. Besök prenumerationssidan för att registrera dig. På inloggningssidan använder du ditt Microsoft-konto för att logga in, så kan du prenumerera och få dina nycklar. När du har slutfört registreringsprocessen klistrar du in nyckeln i området variabler i anteckningsboken (återges nedan). Den primära eller sekundära nyckeln fungerar. Se till att ange nyckeln inom citattecken så att den blir en sträng.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
