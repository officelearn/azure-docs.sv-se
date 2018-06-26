---
title: Anpassade Vision ONNX modellen med Windows ML - kognitiva tjänster | Microsoft Docs
description: Lär dig hur du skapar en Windows-UWP-app som använder en modell för ONNX som exporterats från kognitiva tjänster.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939491"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Självstudier: Använda en anpassad Vision ONNX modell med Windows ML (förhandsgranskning)

Lär dig hur du använder en modell för ONNX som exporterats från anpassade Vision tjänsten med Windows ML (förhandsversion).

Informationen i det här dokumentet visar hur du använder en ONNX-fil som har exporterats från anpassade Vision tjänsten med Windows ML. Ett exempel Windows UWP-appen har angetts. En tränad modell som kan identifiera hund och katt ingår i exemplet. Anvisningar finns också på hur du kan använda din egen modell med det här exemplet.

> [!div class="checklist"]
> * Om appen exempel
> * Hämta exempelkoden
> * Kör exemplet
> * Använda din egen modellen

## <a name="prerequisites"></a>Förutsättningar

* En enhet med Windows 10:

    * En kamera.

    * Visual Studio 2017 15.7 eller senare med den __Uwp development__ arbetsbelastning aktiverad.

    * Utvecklarläge har aktiverats. Mer information finns i [Aktivera enheten för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentet.

* (Valfritt) En ONNX-fil som exporterats från tjänsten anpassad Vision. Mer information finns i [exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentet.

    > [!NOTE]
    > Om du vill använda din egen modell, följer du stegen i den [använder egna modell](#use-your-own-model) avsnitt.

## <a name="about-the-example-app"></a>Om appen exempel

Programmet är ett allmänt UWP för Windows-program. Den använder kameran på din Windows 10-enhet för att leverera avbildningar till modellen. Taggar och resultat som returneras av modellen visas under Förhandsgranska video.

* Eftersom data kommer i men kameran, [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) används för att extrahera enskilda ramar. Ramar skickas till modellen för resultatfunktioner.

* Modellen returnerar de taggar som den var tränats på och ett flyttalsvärde som anger hur säker på att det är att bilden innehåller objektet.

### <a name="the-ui"></a>Användargränssnittet

Gränssnittet för exempelprogrammet skapas med hjälp av __CaptureElement__ och __tecknens__ kontroller. CaptureElement visar en förhandsgranskning av video från kameran och tecknens visar resultaten från modellen. 

### <a name="the-model"></a>Modellen

Modellen (`cat-or-dog.onnx`) som medföljer exemplet skapades och tränas med hjälp av tjänsten kognitiva Services anpassad Vision. Den tränade modellen exporteras sedan som en ONNX-modell. Mer information om hur du använder den här tjänsten finns på [hur du skapar en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) och [exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokument.

> [!IMPORTANT]
> Modellen med exemplet var tränas med en liten uppsättning hund och katt bilder. Så det inte kan vara världens bäst på att identifiera hund och katt.

### <a name="the-model-class-file"></a>Klassen modellfilen

När du lägger till en ONNX-fil till en Windows-UWP-appen skapas en .cs-fil. Den här filen har samma namn som den `.onnx` fil (`cat-or-dog` i det här exemplet) och innehåller de klasser som används för att arbeta med modellen från C#. Entiteter i den genererade klassen kan dock ha namn som `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Du kan på ett säkert sätt byta namn på de här posterna (Högerklicka på, Byt namn på) till ett eget namn.

> [!NOTE]
> Exempelkoden har omstrukturerade genererade klassen och metoden namnen på följande:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Kameraåtkomst

Den __funktioner__ fliken i den `Package.appxmanifest` filen är konfigurerad för att tillåta åtkomst till webbkamera och mikrofon.

> [!NOTE]
> Även om det här exemplet inte använder ljud, jag var tvungen att aktivera mikrofonens innan jag kunde åtkomst till kameran på enheten.

Programmet försöker hämta kameran på baksidan av enheten om det inte finns. Den använder den [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) klassen för att börja samla in video från kameran. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) används för att avbilda bildrutor och skicka dem till modellen.

## <a name="get-the-example-code"></a>Hämta exempelkoden

Exempelprogrammet är tillgängligt på [ https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP ](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Kör exemplet

1. Använd den `F5` för att starta programmet från Visual Studio. Du kan uppmanas att aktivera utvecklarläget. Mer information finns i [Aktivera enheten för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentet.

2. När du uppmanas att programmet ska kunna få åtkomst till kameran och mikrofon på enheten.

3. Peka kameran på en hund eller katt. Resultatet för om bilden innehåller en hund eller katt visas under förhandsgranskningen i programmet.

    > [!TIP]
    > Om du inte har en hund eller katt praktiska kan du använda ett foto av ett hund eller katt.

## <a name="use-your-own-model"></a>Använda din egen modellen

Om du vill använda din egen modellen gör du följande:

> [!IMPORTANT]
> Stegen i det här avsnittet Byt namn på den aktuella modellen (cat eller dog.cs) och refactor klass och metoden namnen på den nya modellen. Detta är att undvika namnkonflikter med exempelmodellen.

1. Träna en modell med hjälp av tjänsten anpassad Vision. Information om hur du tränar en modell finns i [hur du skapar en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportera den tränade modellen som en ONNX-modell. Information om hur du exporterar en modell finns i [exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentet.

3. I Solution Explorer högerklickar du på den __cat eller dog.cs__ och Byt namn på den till __cat eller dog.txt__. Byta namn på den förhindrar namnkonflikter med den nya modellen.

    > [!TIP]
    > Du kan också använda olika namn för klassnamn i den nya modellen, men det är enklare att återanvända befintliga namn.

4. I Solution Explorer högerklickar du på den __VisionApp__ posten och välj sedan __Lägg till__ > __befintliga objekt...__ .

5. Om du vill skapa en klass för modellen Välj ONNX fil att importera och välj sedan den __Lägg till__ knappen. En ny klass med samma namn som filen ONNX (men `.cs` tillägg) har lagts till i Solution Explorer.

6. Öppna filen genererade .cs och hitta namnen på följande:

    > [!IMPORTANT]
    > Använd exempel `cat-or-dog.txt` filen som en vägledning för att identifiera de klasser och funktioner.

    * Den klass som definierar den modell som indata. Det genererade namnet kan vara liknar `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Byt namn på den här klassen för __ModelInput__.
    * Den klass som definierar modellen utdata. Det genererade namnet kan vara liknar `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Byt namn på den här klassen för __ModelOutput__.
    * Den klass som definierar modellen. Det genererade namnet kan vara liknar `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Byt namn på den här klassen för __modellen__.
    * Den metod som skapar modellen. Det genererade namnet kan vara liknar `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Byt namn på den här metoden att __CreateModel__.

7. I Solution Explorer flyttar den `.onnx` filen till den __tillgångar__ mapp. 

8. Filen ONNX i programpaketet markerar du den `.onnx` fil och anger __Skapa åtgärd__ till __innehåll__ i egenskaperna.

9. Öppna den __MainPage.xaml.cs__ fil. Sök efter följande rad och ändra filnamnet till den nya `.onnx` fil:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Den här ändringen läser in den nya modellen vid körning.

10. Skapa och köra appen. Den nya modellen används nu för att poängsätta bilder.

## <a name="next-steps"></a>Nästa steg

För att identifiera andra sätt att exportera och använder en anpassad Vision modell, finns i följande dokument:

* [Exportera din modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Använd den exporterade Tensorflow modellen i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Använd exporterade CoreML modell i en Swift iOS-App](https://go.microsoft.com/fwlink/?linkid=857726)
* [Använd exporteras CoreML modell i ett iOS-program med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Mer information om hur du använder ONNX modeller med Windows ML finns i [integrera en modell i din app med Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentet.
