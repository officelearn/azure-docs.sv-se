---
title: Känslo-API Python kursen | Microsoft Docs
description: Använd en Jupyter-anteckningsbok att lära dig hur du använder kognitiva Services Känslo-API med Python. Visualisera dina resultat genom att använda populära bibliotek.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352398"
---
# <a name="emotion-api-using-python-tutorial"></a>Känslo-API: et med Python självstudiekursen

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Om du vill göra det enklare att komma igång med Känslo-API, visar Jupyter-anteckningsbok länkade nedan hur du använder API i Python och visualisera dina resultat med hjälp av vissa populära bibliotek. 

[Länka till bärbara datorer i GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Med Jupyter-anteckningsbok

Om du vill använda den bärbara datorn interaktivt, behöver du klona den och kör det i Jupyter. Mer information om hur du kommer igång med interaktiva Jupyter-anteckningsböcker, följer du anvisningarna http://jupyter.readthedocs.org/en/latest/install.html. 

Om du vill använda den här anteckningsboken, behöver du en prenumeration nyckel för Känslo-API. Besök den [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) att logga. På sidan ”inloggning” använda ditt Microsoft-konto för att logga in och du kommer att kunna prenumerera på och hämta ledigt nycklar. När du har slutfört registreringsprocessen klistrar du in din nyckel i avsnittet variables visas nedan. Primärt eller den sekundära nyckeln fungerar.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
