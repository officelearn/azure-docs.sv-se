---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och Python'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med Python för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 606f01059620443c8c42a24f416128d0856eff50
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213794"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Snabbstart: Identifiera ansikten i en bild med ansikts-REST API och Python

I den här snabbstarten ska du använda Azure ansikts-REST API med Python för att identifiera ansikten i en bild. Skriptet ritar ramar kring ansikten och lägger på information om kön och ålder på bilderna.

![En man och en kvinna, med rektanglar ritade runt ansiktena och med information om ålder och kön på bilden](../images/labelled-faces-python.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 


## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.

## <a name="run-the-jupyter-notebook"></a>Kör Jupyter-anteckningsboken

Du kan köra den här snabbstarten som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org). Kör Binder genom att välja knappen nedan. Följ sedan anvisningarna i anteckningsboken.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>Nästa steg

Utforska sedan referensdokumentationen för Ansikts-API för att lära dig mer om de scenarier som stöds.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
