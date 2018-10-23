---
title: 'Självstudie: API för visuellt innehåll med Python'
titlesuffix: Azure Cognitive Services
description: Lär dig att använda API för visuellt innehåll med Python med hjälp av Jupyter-anteckningsböcker. Visualisera dina resultat med hjälp av populära bibliotek.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 046250d3d2142badaac35490eff27bcac220fea9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344905"
---
# <a name="tutorial-computer-vision-api-python"></a>Självstudie: API för visuellt innehåll med Python

Den här självstudien visar hur du använder API för visuellt innehåll i Python och visualiserar dina resultat med populära bibliotek. Använd Jupyter om du vill köra självstudien. Läs hur du kommer igång med interaktiva Jupyter-anteckningsböcker: [Jupyter-dokumentation](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiens anteckningsbok i Jupyter 

1. Navigera till [självstudiekursens anteckningsbok i GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Klicka på den gröna knappen att klona eller hämta självstudien. 
3. Öppna en kommandotolk och gå till mappen _Cognitive – Vision – Python – master\Jupyter Notebook_. 
4. Kör kommandot **jupyter notebook** från kommandotolken. Jupyter startas.
5. I fönstret Jupyter klickar du på _API för visuellt innehåll example.ipynb_ för att öppna anteckningsboken för självstudien 

## <a name="run-the-tutorial"></a>Köra självstudien

Om du vill använda den här anteckningsboken behöver du en prenumerationsnyckel för API:et för visuellt innehåll. Besök [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) för att registrera dig. På sidan ”inloggning” använder du ditt Microsoft-konto för inloggning och du kommer att kunna prenumerera och få kostnadsfria nycklar. När du har slutfört registreringsprocessen klistrar du in nyckeln i området variabler i anteckningsboken (återges nedan). Den primära eller sekundära nyckeln fungerar. Se till att ange nyckeln inom citattecken så att den blir en sträng.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
