---
title: Exportera din modell till Mobile-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du exporterar din modell för användning i skapa mobila program.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561082"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportera modellen för användning med mobila enheter

Custom Vision Service tillåter att klassificerare exporteras för att köras offline. Du kan bädda in din exporterade klassificerare i ett program och köra den lokalt på en enhet för klassificering i real tid.

Custom Vision Service stöder följande exporter:

* __Tensorflow__ för __Android__.
* __CoreML__ för __iOS11__.
* __ONNX__ för __Windows ml__.
* En Windows-eller Linux- __behållare__. Behållaren innehåller en Tensorflow-modell och tjänst kod som använder Custom Vision Service-API: et. 

> [!IMPORTANT]
> Custom Vision Service bara exporterar __komprimerade__ domäner. Modeller som genereras av komprimerade domäner är optimerade för begränsningar i real tids klassificering på mobila enheter. Klassificerare som skapats med en kompakt domän kan vara något mindre exakt än en standard domän med samma mängd tränings data.
>
> Information om hur du kan förbättra dina klassificerare finns i [förbättra ditt klassificerare](getting-started-improving-your-classifier.md) -dokument.

## <a name="convert-to-a-compact-domain"></a>Konvertera till en komprimerad domän

> [!NOTE]
> Stegen i det här avsnittet gäller endast om du har en befintlig klassificerare som inte är inställd på komprimerad domän.

Använd följande steg för att konvertera en befintlig klassificerares domän:

1. På [sidan anpassad vision](https://customvision.ai)väljer du ikonen __Start__ för att visa en lista över dina projekt. Du kan också använda [https://customvision.ai/projects](https://customvision.ai/projects) för att se dina projekt.

    ![Bild av listan start ikon och projekt](./media/export-your-model/projects-list.png)

2. Välj ett projekt och välj sedan __kugg hjuls__ ikonen i det övre högra hörnet på sidan.

    ![Bild av kugg hjuls ikonen](./media/export-your-model/gear-icon.png)

3. I avsnittet __domäner__ väljer du en __komprimerad__ domän. Välj __Spara ändringar__ för att spara ändringarna.

    ![Bild av val av domäner](./media/export-your-model/domains.png)

4. Längst upp på sidan väljer du __träna__ för att träna om med den nya domänen.

## <a name="export-your-model"></a>Exportera din modell

Använd följande steg för att exportera modellen efter omträning:

1. Gå till fliken **prestanda** och välj __Exportera__. 

    ![Bild av ikonen Exportera](./media/export-your-model/export.png)

    > [!TIP]
    > Om __export__ posten inte är tillgänglig använder inte den valda iterationen en komprimerad domän. Använd __upprepnings__ avsnittet på den här sidan för att välja en iteration som använder en komprimerad domän och välj sedan __Exportera__.

2. Välj export format och välj sedan __Exportera__ för att ladda ned modellen.

## <a name="next-steps"></a>Nästa steg

Integrera din exporterade modell i ett program genom att utforska någon av följande artiklar eller exempel:

* [Använd din Tensorflow-modell med python](export-model-python.md)
* [Använd din ONNX-modell med Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Se exemplet för [CoreML-modellen i ett iOS-program](https://go.microsoft.com/fwlink/?linkid=857726) för bild klassificering i real tid med Swift.
* Se exemplet för [Tensorflow-modellen i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) för bild klassificering i real tid på Android.
* Se exemplet för [CoreML-modellen med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) för bild klassificering i real tid i en Xamarin iOS-app.
