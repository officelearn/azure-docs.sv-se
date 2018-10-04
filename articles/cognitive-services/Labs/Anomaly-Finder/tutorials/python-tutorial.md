---
title: Identifiering av avvikelser Python-app – Microsoft Cognitive Services | Microsoft Docs
description: 'Utforska en Python notebook som använder API: T för Avvikelseidentifiering identifiering i Microsoft Cognitive Services. Skicka ursprungliga datapunkter till API: et och få det förväntade värdet och avvikelseidentifiering punkter.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 87cd9e976d231291ad13acecf188cfd668d692b6
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248235"
---
# <a name="anomaly-detection-python-application"></a>Avvikelseidentifiering identifiering av Python-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Kursen visar hur du använder API: T för Avvikelseidentifiering identifiering i Python och hur du visualisera dina resultat med hjälp av populära bibliotek. För att köra självstudien med Jupyter och försök dina egna data med din prenumerationsnyckel. Läs hur du kommer igång med interaktiva Jupyter-anteckningsböcker [Jupyter dokumentation](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på identifiering av avvikelser och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Hämta exempelkoden

1. Navigera till den [självstudiekursen anteckningsboken i Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Klicka på den gröna knappen att klona eller hämta självstudien. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiekursen anteckningsboken i Jupyter

1. Öppna en kommandotolk och gå till mappen python-exemplet.
2. Kör kommandot Jupyter-anteckningsboken från Kommandotolken, vilket startar Jupyter.
3. I fönstret Jupyter klickar du på <em>Avvikelseidentifiering identifiering API Example.ipynb</em> att öppna självstudiekursen anteckningsboken.   

## <a name="running-the-tutorial"></a>Kör kursen

Om du vill använda den här anteckningsboken, måste en prenumerationsnyckel för API: T för Avvikelseidentifiering identifiering. Gå till prenumerationssidan för att registrera dig. På sidan ”inloggning” använda ditt Microsoft-konto för inloggning och du kommer att kunna prenumerera och få dina nycklar. När du har slutfört registreringsprocessen klistra du in din nyckel i avsnittet variabler i anteckningsboken (reproduceras nedan). Primärt eller sekundära nyckeln fungerar. Se till att ange nyckeln inom citattecken så att de blir en sträng.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
