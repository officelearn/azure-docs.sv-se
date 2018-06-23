---
title: Avvikelseidentifiering Python app – kognitiva Microsoft-tjänster | Microsoft Docs
description: 'Utforska en Python bärbar dator som använder API: et för Avvikelseidentifiering identifiering i kognitiva Microsoft-tjänster. Skicka ursprungliga datapunkter-API: et och få ett förväntat värde och avvikelseidentifiering punkter.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353937"
---
# <a name="anomaly-detection-python-application"></a>Avvikelseidentifiering identifiering Python program

Kursen visar hur du använder API: et för Avvikelseidentifiering identifiering i Python och hur du visualisera dina resultat populära bibliotek. Med Jupyter för att köra guiden och försök dina egna data med din prenumeration nyckel. Information om hur du kommer igång med interaktiva Jupyter-anteckningsböcker som avser [Jupyter dokumentationen](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på Avvikelseidentifiering och få en nyckel för prenumeration 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Hämta exempelkoden

1. Navigera till den [självstudiekursen anteckningsboken i Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Klicka på den gröna knappen att klona eller hämta kursen. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiekursen anteckningsboken i Jupyter

1. Öppna en kommandotolk och gå till mappen python-exemplet.
2. Kör kommandot Jupyter-anteckningsbok från Kommandotolken, vilket startar Jupyter.
3. I Jupyter-fönstret klickar du på <em>Avvikelseidentifiering identifiering API Example.ipynb</em> vill öppna självstudiekursen anteckningsboken.   

## <a name="running-the-tutorial"></a>Kör självstudien

Om du vill använda den här anteckningsboken, behöver du en prenumeration nyckel för Avvikelseidentifiering identifiering API. Gå till prenumerationssidan för att logga. På sidan ”inloggning” använda ditt Microsoft-konto för att logga in och du kommer att kunna prenumerera på och hämta dina nycklar. När du har slutfört registreringsprocessen klistrar du in din nyckel i avsnittet variabler i anteckningsboken (reproduceras nedan). Primärt eller den sekundära nyckeln fungerar. Se till att nyckeln omges av citattecken så att det blir en sträng.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
