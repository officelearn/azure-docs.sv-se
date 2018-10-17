---
title: 'Självstudie: Känsloigenkänning i ett ansikte på en bild – Känslo-API, Python'
titlesuffix: Azure Cognitive Services
description: Använ en Jupyter Notebook för att lära dig hur du använder Känslo-API med Python. Visualisera dina resultat med hjälp av populära bibliotek.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237865"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Självstudie: Använda Känslo-API med Jupyter Notebook och Python.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

För att göra det lätt att komma igång med Känslo-API visar Jupyter Notebook som är länkad nedan hur du använder API:et i Python och hur du visualiserar dina resultat med hjälp av några populära bibliotek.

[Länka till anteckningsbok i GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Använda Jupyter Notebook

Om du vill använda anteckningsboken interaktivt måste du klona den och köra den i Jupyter. Om du vill lära dig hur du kommer igång med Jupyter Notebook följer du instruktionerna på http://jupyter.readthedocs.org/en/latest/install.html.

Om du vill använda den här anteckningsboken behöver du en prenumerationsnyckel för känslo-API:et. Besök [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) för att registrera dig. På sidan ”inloggning” använder du ditt Microsoft-konto för inloggning och du kommer att kunna prenumerera och få kostnadsfria nycklar. När du har slutfört registreringen klistrar du in din nyckeln i avsnittet för variabler som visas nedan. Den primära eller sekundära nyckeln fungerar.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
