---
title: Använda en ONNX-modell med Windows ML – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Windows UWP-app som använder en ONNX-modell som exporteras från Azure Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594303"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Använda en ONNX-modell från Custom Vision med Windows ML (förhandsversion)

Lär dig hur du använder en ONNX-modell som exporteras från Custom Vision Service med Windows ML (förhandsversion).

I den här guiden får du lära dig hur du använder en ONNX-fil som exporter ATS från Custom Vision Service med Windows ML. Du ska använda exemplet UWP-program med din egen utbildade bild klassificerare.

## <a name="prerequisites"></a>Krav

* Windows 10 version 1809 eller senare
* Windows SDK för build 17763 eller högre
* Visual Studio 2017 version 15.7 eller senare med arbetsbelastningen för __Universal Windows Platform-utveckling__ aktiverad.
* Utvecklarläge aktiverat på din dator. Mer information finns i [aktivera din enhet för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Om exempelappen

Det inkluderade programmet är en generisk Windows UWP-app. Du kan välja en avbildning från datorn som sedan bearbetas av en lokalt lagrad klassificerings modell. Taggar och resultat som returneras av modellen visas bredvid bilden.

## <a name="get-the-example-code"></a>Hämta exempelkoden

Exempel programmet finns på [COGNITIVE Services ONNX Custom vision Sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) lagrings platsen på GitHub. Klona den till din lokala dator och öppna *SampleOnnxEvaluationApp. SLN* i Visual Studio.

## <a name="test-the-application"></a>Testa programmet

1. Starta programmet från Visual Studio genom att använda `F5`-tangenten. Du kan uppmanas att aktivera utvecklarläget.
1. När programmet startas använder du knappen för att välja en bild för bedömning. Standard modellen för ONNX är utbildad för att klassificera olika typer av plankton.

## <a name="use-your-own-model"></a>Använd din egen modell

Följ dessa steg om du vill använda en egen bild klassificerings modell:

1. Skapa och träna en klassificerare med Custom Vision Service. Instruktioner för hur du gör detta finns i [skapa och träna en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier). Använd en av de **komprimerade** domänerna, till exempel **Allmänt (komprimera)**. 
   * Om du har en befintlig klassificerare som använder en annan domän kan du konvertera den till **Komprimera** i projekt inställningarna. Träna sedan projektet igen innan du fortsätter.
1. Exportera din modell. Växla till fliken prestanda och välj en iteration som har tränats med en **komprimerad** domän. Välj knappen **Exportera** som visas. Välj sedan **ONNX**och sedan **Exportera**. När filen är klar väljer du knappen **Ladda ned**. Mer information om export alternativ finns i [exportera din modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).
1. Öppna den hämtade *zip* -filen och extrahera filen *Model. Onnx* från den. Den här filen innehåller din klassificerings modell.
1. I Solution Explorer i Visual Studio högerklickar du på mappen **resurser** och väljer __Lägg till befintligt objekt__. Välj din ONNX-fil.
1. I Solution Explorer högerklickar du på filen ONNX och väljer **Egenskaper**. Ändra följande egenskaper för filen:
   * __Bygg åtgärds__ -> __innehåll__
   * __Kopiera till utdata katalog__ -> __kopia om nyare__
1. Öppna sedan _mainpage.XAML.cs_ och ändra värdet för `_ourOnnxFileName` till namnet på din ONNX-fil.
1. Använd `F5` för att skapa och köra projektet.
1. Klicka på knappen för att välja bild att utvärdera.

## <a name="next-steps"></a>Nästa steg

Om du vill upptäcka andra sätt att exportera och använda Custom Vision-modellen på, så kan du läsa följande dokument:

* [Exportera din modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Använda en exporterad Tensorflow-modell i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Använda en exporterad CoreML-modell i ett Swift iOS-program](https://go.microsoft.com/fwlink/?linkid=857726)
* [Använda en exporterad CoreML-modell i ett iOS-program med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Mer information om hur du använder ONNX-modeller med Windows ML finns i [integrera en modell i din app med Windows ml](/windows/ai/windows-ml/integrate-model).
