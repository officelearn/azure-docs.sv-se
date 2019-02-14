---
title: 'Självstudier: Utföra bildåtgärder – Python'
titlesuffix: Azure Cognitive Services
description: Lär dig att använda API för visuellt innehåll med Python med hjälp av Jupyter-anteckningsböcker. Visualisera dina resultat med hjälp av populära bibliotek.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: afb937f1be780c35ef44023acfedeb38a6b6a877
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875078"
---
# <a name="tutorial-computer-vision-api-python"></a>Självstudier: API för visuellt innehåll – Python

Den här självstudien visar hur du använder API för visuellt innehåll i Python och visualiserar dina resultat med populära bibliotek. Du använder Jupyter om du vill köra självstudien. Läs hur du kommer igång med interaktiva Jupyter-anteckningsböcker: [Jupyter-dokumentation](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget
- [Jupyter Notebook](https://jupyter.org/install) installerat

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiens anteckningsbok i Jupyter 

1. Gå till GitHub-lagringsplatsen [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Klicka på den gröna knappen för att klona eller hämta lagringsplatsen. 
3. Öppna en kommandotolk och gå till mappen **Cognitive-Vision-Python\Jupyter Notebook**.
1. Kontrollera att du har alla bibliotek som krävs genom att köra kommandot `pip install requests opencv-python numpy matplotlib` från Kommandotolken.
1. Starta Jupyter genom att köra kommandot `jupyter notebook` från Kommandotolken.
1. I fönstret Jupyter klickar du på _API för visuellt innehåll example.ipynb_ för att öppna anteckningsboken för självstudien.

## <a name="run-the-tutorial"></a>Köra självstudien

Om du vill använda den här anteckningsboken behöver du en prenumerationsnyckel för API:et för visuellt innehåll. Besök [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) för att registrera dig. På **inloggningssidan** använder du ditt Microsoft-konto för inloggning och du kommer att kunna prenumerera och få kostnadsfria nycklar. När du har slutfört registreringsprocessen klistrar du in nyckeln i avsnittet `Variables` i anteckningsboken (återges nedan). Den primära eller sekundära nyckeln fungerar. Se till att ange nyckeln inom citattecken så att den blir en sträng.

Du måste också kontrollera att fältet `_region` matchar den region som motsvarar din prenumeration.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

När du kör självstudien kommer du att kunna lägga till bilder att analysera, både från en URL och lokal lagring. Skriptet visar bilderna och analysinformationen i anteckningsboken.
