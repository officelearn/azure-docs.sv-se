---
title: Exportera din modell till mobil - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du exporterar din modell för användning i att skapa mobila program eller köras lokalt för realtidsklassificering.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718944"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportera din modell för användning med mobila enheter

Med Custom Vision Service kan klassificerare exporteras för att köras offline. Du kan bädda in den exporterade klassificeraren i ett program och köra den lokalt på en enhet för klassificering i realtid.

## <a name="export-options"></a>Exportera alternativ

Custom Vision Service stöder följande exporter:

* __Tensorflow__ för __Android__.
* __CoreML__ för __iOS11__.
* __ONNX__ för __Windows ML__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* En __Docker-behållare__ för Windows-, Linux- eller ARM-arkitektur. Behållaren innehåller en Tensorflow-modell och tjänstkod för att använda API:et för anpassad vision.

> [!IMPORTANT]
> Custom Vision Service exporterar endast __kompakta__ domäner. Modellerna som genereras av kompakta domäner är optimerade för begränsningar av realtidsklassificering på mobila enheter. Klassificerare som skapats med en kompakt domän kan vara något mindre exakta än en standarddomän med samma mängd träningsdata.
>
> Information om hur du förbättrar klassificerarna finns i dokumentet [Förbättra klassificeraren.](getting-started-improving-your-classifier.md)

## <a name="convert-to-a-compact-domain"></a>Konvertera till en kompakt domän

> [!NOTE]
> Stegen i det här avsnittet gäller bara om du har en befintlig modell som inte är inställd på kompakt domän.

Så här konverterar du domänen för en befintlig modell:

1. På [webbplatsen Anpassad syn](https://customvision.ai)väljer du __ikonen Start__ för att visa en lista över dina projekt.

    ![Bild på hemikonen och projektlistan](./media/export-your-model/projects-list.png)

1. Välj ett projekt och välj sedan __växelikonen__ längst upp till höger på sidan.

    ![Bild på växelikonen](./media/export-your-model/gear-icon.png)

1. Välj en av de __kompakta__ domänerna i avsnittet __Domäner.__ Välj __Spara ändringar__ om du vill spara ändringarna. 

    > [!NOTE]
    > För Vision AI Dev Kit måste projektet skapas med domänen __Allmänt (Kompakt)__ och du måste ange alternativet **Vision AI Dev Kit** under avsnittet **Exportfunktioner.**

    ![Bild av val av domäner](./media/export-your-model/domains.png)

1. Högst upp på sidan väljer du __Träna__ för att träna om med den nya domänen.

## <a name="export-your-model"></a>Exportera din modell

Så här exporterar du modellen efter omskolning:

1. Gå till fliken **Prestanda** och välj __Exportera__. 

    ![Bild på exportikonen](./media/export-your-model/export.png)

    > [!TIP]
    > Om __exportposten__ inte är tillgänglig använder den valda iterationen inte en kompakt domän. Använd avsnittet __Iterationer__ på den här sidan för att välja en iteration som använder en kompakt domän och välj sedan __Exportera__.

1. Välj önskat exportformat och välj sedan __Exportera__ för att hämta modellen.

## <a name="next-steps"></a>Nästa steg

Integrera din exporterade modell i ett program genom att utforska någon av följande artiklar eller exempel:

* [Använd din Tensorflow-modell med Python](export-model-python.md)
* [Använda DIN ONNX-modell med Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Se exemplet för [CoreML-modellen i ett iOS-program](https://go.microsoft.com/fwlink/?linkid=857726) för bildklassificering i realtid med Swift.
* Se exemplet för [Tensorflow-modellen i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) för bildklassificering i realtid på Android.
* Se exemplet för [CoreML-modellen med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) för bildklassificering i realtid i en Xamarin iOS-app.
