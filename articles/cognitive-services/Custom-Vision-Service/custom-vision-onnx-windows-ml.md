---
title: 'Självstudier: Använda en ONNX-modell med Windows ML – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Windows UWP-app som använder en ONNX-modell som exporteras från Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: larryfr
ms.openlocfilehash: af1b96b4ab47053a6737893832b484372ed37e99
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351872"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Självstudier: Använda en ONNX-modell från Custom Vision med Windows ML (förhandsversion)

Lär dig hur du använder en ONNX-modell som exporteras från Custom Vision Service med Windows ML (förhandsversion).

Informationen i det här dokumentet visar hur du använder en ONNX-fil som exporterats från Custom Vision Service med Windows ML. Ett Windows UWP-appexempel tillhandahålls. En tränad modell för identifiering ingår i exemplet. Vi tillhandahåller även stegvisa anvisningar om hur du kan använda din egen modell med det här exemplet.

> [!div class="checklist"]
> * Om exempelappen
> * Hämta exempelkoden
> * Kör exemplet
> * Använd din egen modell

## <a name="prerequisites"></a>Förutsättningar

* Windows 10 version 17738 eller senare

* Windows SDK för version 17738 eller senare

* Visual Studio 2017 version 15.7 eller senare med arbetsbelastningen för __Universal Windows Platform-utveckling__ aktiverad.

* Developer-läget har aktiverats. Mer information finns i dokumentet [Aktivera din enhet för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Om exempelappen

Programmet är ett allmänt Windows UWP-program. Här kan du välja en bild från datorn, som skickas till modellen. Taggar och resultat som returneras av modellen visas bredvid bilden.

## <a name="get-the-example-code"></a>Hämta exempelkoden

Exempelprogrammet som finns på [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Kör exemplet

1. Starta programmet från Visual Studio genom att använda `F5`-tangenten. Du kan uppmanas att aktivera utvecklarläget. Mer information finns i dokumentet [Aktivera din enhet för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

1. När programmet startas använder du knappen för att välja en bild för bedömning.

## <a name="use-your-own-model"></a>Använd din egen modell

Om du vill använda din egen modell gör du så här:

1. [Skapa och träna](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) en klassificerare med Custom Vision Service. Om du vill exportera modellen väljer du en __kompakt__ domän som **General (compact)** Allmän (kompakt)). Om du vill exportera en befintlig klassificerare konverterar du domänen till kompakt genom att välja kugghjulsikonen högst upp till höger. I __Inställningar__ väljer du en kompakt modell, spara och träna projektet.  

1. [Exportera modellen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) genom att gå till fliken Prestanda. Välj en iteration med en kompakt domän så visas en ”Exportera”-knapp. Välj *Exportera*, *ONNX*, *ONNX1.2* och sedan *Exportera*. När filen är klar väljer du knappen *Ladda ned*.

1. Släpp ONNX-filen i projektets mapp __Tillgångar__. 

1. Högerklicka på mappen tillgångar i Solutions Explorer och välj __Lägg till befintligt objekt__. Välj ONNX-filen.

1. I Solutions Explorer väljer du ONNX-filen i mappen Tillgångar. Ändra följande egenskaper för filen:

    * __Byggåtgärd__ -> __Innehåll__
    * __Kopiera till utdatakatalog__ -> __Kopiera om nyare__

1. Ändra variabeln `_onnxFileNames` till namnet på ONNX-filen. Ändra även `ClassLabel` till antalet etiketter som modellen innehåller.

1. Skapa och kör.

1. Klicka på knappen för att välja bild att utvärdera.

## <a name="next-steps"></a>Nästa steg

Om du vill upptäcka andra sätt att exportera och använda Custom Vision-modellen på, så kan du läsa följande dokument:

* [Exportera din modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Använda en exporterad Tensorflow-modell i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Använda en exporterad CoreML-modell i ett Swift iOS-program](https://go.microsoft.com/fwlink/?linkid=857726)
* [Använda en exporterad CoreML-modell i ett iOS-program med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Mer information om hur du använder ONNX-modeller med Windows ML finns i dokumentet [Integrera en modell i din app med Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
