---
title: Datorn Vision API Python kursen | Microsoft Docs
description: 'Lär dig använda API: et för datorn Vision Python med hjälp av Jupyter-anteckningsböcker i kognitiva Microsoft-tjänster. Visualisera dina resultat populära bibliotek.'
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352377"
---
# <a name="computer-vision-api-python-tutorial"></a>Datorn Vision API Python självstudiekursen

Den här kursen visar hur du använder datorn Vision API i Python och visualisera dina resultat med hjälp av vissa populära bibliotek. Använd Jupyter för att köra guiden. Information om hur du kommer igång med interaktiva Jupyter-anteckningsböcker som avser: [Jupyter Documementation](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Öppna självstudiekursen anteckningsboken i Jupyter 

1. Navigera till den [självstudiekursen anteckningsboken i GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Klicka på den gröna knappen att klona eller hämta kursen. 
3. Öppna en kommandotolk och gå till mappen _kognitiva funktionsnedsättningar-Vision-Python-master\Jupyter anteckningsboken_. 
4. Kör kommandot **jupyter-anteckningsbok** från Kommandotolken. Detta startar Jupyter.
5. I Jupyter-fönstret klickar du på _datorn Vision API Example.ipynb_ vill öppna självstudiekursen anteckningsboken 

### <a name="running-the-tutorial"></a>Kör självstudien

Om du vill använda den här anteckningsboken, behöver du en prenumeration nyckel för datorn Vision API. Besök den [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) att logga. På sidan ”inloggning” använda ditt Microsoft-konto för att logga in och du kommer att kunna prenumerera på och hämta ledigt nycklar. När du har slutfört registreringsprocessen klistrar du in din nyckel i avsnittet variabler i anteckningsboken (reproduceras nedan). Primärt eller den sekundära nyckeln fungerar. Se till att nyckeln omges av citattecken så att det blir en sträng.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
