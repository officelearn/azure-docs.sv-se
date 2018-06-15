---
title: Exportera din anpassade Vision tjänstmodell till - anpassad Vision Service - Azure kognitiva mobiltjänster | Microsoft Docs
description: Lär dig att exportera din modell som ska användas när mobila program.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: ce8f42d6239867dd217cddfc61a27d7835dc9c9b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355917"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportera din modell för användning med mobila enheter

Anpassade Vision tjänsten kan klassificerare exporteras för att köras offline. Du kan bädda in din exporterade klassificerare i ett program och köra den lokalt på en enhet för realtid klassificering. 

Anpassade Vision tjänsten stöder följande exporten:

* __Tensorflow__ för __Android__.
* __CoreML__ för __iOS11__.
* __ONNX__ för __Windows ML__.
* En Windows- eller Linux __behållare__. Behållaren innehåller en Tensorflow modell och kod för att använda anpassade Vision Service API-tjänsten. 

> [!IMPORTANT]
> Anpassade Vision Service exporterar endast __komprimera__ domäner. Modeller som genererats av compact domäner är optimerade för begränsningar i realtid klassificering på mobila enheter. Klassificerare som skapats med en compact domän kan vara något mindre exakt än en standard domän med samma grad av utbildningsdata.
>
> Information om att förbättra din klassificerare, finns det [förbättra din klassificerare](getting-started-improving-your-classifier.md) dokumentet.

## <a name="convert-to-a-compact-domain"></a>Konvertera till en compact domän

> [!NOTE]
> Stegen i det här avsnittet gäller endast om du har en befintlig klassificerare som inte är inställt på att komprimera domän.
 
Använd följande steg om du vill konvertera domänen för en befintlig klassificeraren:

1. Från den [anpassad vision sidan](https://customvision.ai), Välj den __Start__ ikon för att visa en lista över dina projekt. Du kan också använda den [ https://customvision.ai/projects ](https://customvision.ai/projects) att se dina projekt.

    ![Bild som hemma lista över ikonen och projekt](./media/export-your-model/projects-list.png)

2. Välj ett projekt och välj sedan den __Kugghjulet__ ikonen i det övre högra hörnet på sidan.

    ![Bild av ikonen växel](./media/export-your-model/gear-icon.png)

3. I den __domäner__ väljer en __komprimera__ domän. Välj __spara ändringar__ spara ändringarna.

    ![Bild av val av domäner](./media/export-your-model/domains.png)

4. Överst på sidan Välj __Train__ att träna om använder den nya domänen.

## <a name="export-your-model"></a>Exportera din modell

Använd följande steg om du vill exportera modellen efter omtränings:

1. Gå till den **prestanda** fliken och markera __exportera__. 

    ![Bild av ikonen Exportera](./media/export-your-model/export.png)

    > [!TIP]
    > Om den __exportera__ posten är inte tillgänglig och den valda upprepningen inte använda en compact domän. Använd den __iterationer__ på den här sidan för att välja en iteration som använder en compact domän och välj sedan __exportera__.

2. Välj exportformat och välj sedan __exportera__ att hämta modellen.

## <a name="next-steps"></a>Nästa steg

Integrera din exporterade modell i ett program. Det finns flera exempelprogram:

* Ett exempel för [med din exporterade CoreML modell i en iOS-App](https://go.microsoft.com/fwlink/?linkid=857726) för realtid avbildningen klassificering med Swift
* Exempelprogram för iOS [med hjälp av din exporterade CoreML modellen med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) för realtid avbildningen klassificering 
* Exempel för [med din exporterade Tensorflow modell i en Android-App](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) för realtid avbildningen klassificering 
* [Med Windows Tensorflow-modellen](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python)
* Exempel för [med hjälp av din exporterade ONNX modellen med Windows Machine Learning](https://azure.microsoft.com/en-us/resources/samples/cognitive-services-onnx-customvision-sample/)
