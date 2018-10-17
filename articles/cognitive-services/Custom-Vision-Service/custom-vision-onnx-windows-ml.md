---
title: 'Självstudier: Använda en ONNX-modell med Windows ML – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Windows UWP-app som använder en ONNX-modell som exporteras från Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 3a9e9bc92ce38c4bb8d6d83c8017fa223342e7d2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365612"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Självstudier: Använd en ONNX-modell från Custom Vision med Windows ML (förhandsversion)

Lär dig hur du använder en ONNX-modell som exporteras från Custom Vision Service med Windows ML (förhandsversion).

Informationen i det här dokumentet visar hur du använder en ONNX-fil som exporterats från Custom Vision Service med Windows ML. Ett Windows UWP-appexempel tillhandahålls. En tränad modell som kan identifiera hundar och katter ingår i exemplet. Vi tillhandahåller även stegvisa anvisningar om hur du kan använda din egen modell med det här exemplet.

> [!div class="checklist"]
> * Om exempelappen
> * Hämta exempelkoden
> * Kör exemplet
> * Använd din egen modell

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Windows 10-enhet med:

    * En kamera.

    * Visual Studio 2017 version 15.7 eller senare med arbetsbelastningen för __Universal Windows Platform-utveckling__ aktiverad.

    * Developer-läget har aktiverats. Mer information finns i dokumentet [Aktivera din enhet för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

* (Valfritt) En ONNX-fil som exporterats från Custom Vision Service. Mer information finns i dokumentet [Exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

    > [!NOTE]
    > Om du vill använda din egen modell, följer du stegen i avsnittet [Använd din egen modell](#use-your-own-model).

## <a name="about-the-example-app"></a>Om exempelappen

Programmet är ett allmänt Windows UWP-program. Det använder kameran på din Windows 10-enhet för att förse modellen med bilder. Taggar och resultat som returneras av modellen visas under videoförhandsversionen.

* När data kommer in via kameran används [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) för att extrahera enskilda bildrutor. Bildrutorna skickas till modellen för bedömning.

* Modellen returnerar taggar som den har tränats på, och ett flyttalsvärde som anger med vilken säkerhet bilden innehåller objektet.

### <a name="the-ui"></a>Användargränssnittet

Exempelprogrammets användargränssnitt skapas med hjälp av kontrollerna __CaptureElement__ och __TextBlock__. CaptureElement visar en videoförhandsversion från kameran och TextBlock visar det resultat som returneras från modellen. 

### <a name="the-model"></a>Modellen

Den modell (`cat-or-dog.onnx`) som medföljer exemplet skapades och tränades med hjälp av Cognitive Services Custom Vision Service. Den tränade modellen exporterades sedan som en ONNX-modell. Mer information om hur du använder den här tjänsten finns i dokumenten [Skapa en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) och [Exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

> [!IMPORTANT]
> Modellen med det tillhandahållna exemplet tränades med en liten uppsättning bilder av hundar och katter. Så den är kanske inte världsbäst på att känna igen hundar och katter.

### <a name="the-model-class-file"></a>Modellklassfilen

När du lägger till en ONNX-fil till en Windows UWP-app skapas en .cs-fil. Den här filen har samma namn som `.onnx`-filen (`cat-or-dog` i det här exemplet) och innehåller de klasser som används för att arbeta med modellen från C#. Entiteter i den genererade klassen kan dock ha namn som `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Du kan enkelt byta namn på dessa poster (högerklicka, byt namn).

> [!NOTE]
> Exempelkoden har ändrat de genererade klass- och metodnamnen till följande:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Kameraåtkomst

Fliken __Funktioner__ i filen `Package.appxmanifest` har konfigurerats för att tillåta åtkomst till webbkamera och mikrofon.

> [!NOTE]
> Även om det här exemplet inte använder ljud var jag tvungen att aktivera mikrofonen innan jag kunde komma åt enhetens kamera.

Programmet försöker hämta kameran på baksidan av din enhet om den är tillgänglig. Den använder klassen [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) för att börja spela in video från kameran. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) används för att samla in bildrutor och skicka dem till modellen.

## <a name="get-the-example-code"></a>Hämta exempelkoden

Exempelprogrammet som finns på [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Kör exemplet

1. Starta programmet från Visual Studio genom att använda `F5`-tangenten. Du kan uppmanas att aktivera utvecklarläget. Mer information finns i dokumentet [Aktivera din enhet för utveckling](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. När du uppmanas till detta så tillåt programmet att få åtkomst till enhetens kamera och mikrofon.

3. Rikta kameran mot en hund eller katt. Poängen för huruvida bilden innehåller en hund eller katt visas under förhandsgranskningen i programmet.

    > [!TIP]
    > Om du inte har någon hund eller katt tillgänglig kan du använda ett foto av en hund eller en katt.

## <a name="use-your-own-model"></a>Använd din egen modell

Om du vill använda din egen modell gör du så här:

> [!IMPORTANT]
> I anvisningarna i det här avsnittet byter vi namn på den aktuella modellen (cat-or-dog.cs) och ändrar den nya modellens klass- och metodnamn. Detta för att undvika namnkonflikter med exempelmodellen.

1. Träna en modell med hjälp av Custom Vision Service. Information om hur du tränar en modell finns i [Skapa en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportera den tränade modellen som en ONNX-modell. Mer information om hur du exporterar en modell finns i dokumentet [Exportera din modell för användning med mobila enheter](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

3. Högerklicka på __cat-or-dog.cs__ i Solution Explorer och ändra namnet till __cat-or-dog.txt__. Genom att ändra namnet undviker du namnkonflikter med den nya modellen.

    > [!TIP]
    > Du kan också använda olika namn för klassnamnen i den nya modellen, men det är enklare att återanvända befintliga namn.

4. Högerklicka på posten __VisionApp__ i Solution Explorer och välj sedan __Lägg till__ > __Befintligt objekt...__.

5. Generera en klass för modellen genom att markera den ONNX-fil som ska importeras och välj sedan knappen __Lägg till__. En ny klass med samma namn som ONNX-filen (men med ett `.cs`-tillägg) har lagts till i Solution Explorer.

6. Öppna den genererade .cs-filen och sök efter namnen på följande objekt:

    > [!IMPORTANT]
    > Använd exempelfilen `cat-or-dog.txt` som en vägledning när du ska identifiera klasser och funktioner.

    * Den klass som definierar modellens indata. Det genererade namnet kan likna `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Ändra klassens namn till __ModelInput__.
    * Den klass som definierar modellens utdata. Det genererade namnet kan likna `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Ändra klassens namn till __ModelOutput__.
    * Den klass som definierar modellen. Det genererade namnet kan likna `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Ändra klassens namn till __Model__.
    * Den metod som skapar modellen. Det genererade namnet kan likna `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Ändra metodens namn till __CreateModel__.

7. Flytta `.onnx`-filen till mappen __Tillgångar__ i Solution Explorer. 

8. Om du vill inkludera ONNX-filen i programpaketet väljer du filen `.onnx` och ger __Byggåtgärd__ värdet __Innehåll__ i egenskaperna.

9. Öppna filen __MainPage.xaml.cs__. Sök efter följande rad och ändra filnamnet till den nya `.onnx`-filen:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Den här ändringen läser in den nya modellen vid körning.

10. Skapa och kör appen. Den använder nu den nya modellen vid bildbedömning.

## <a name="next-steps"></a>Nästa steg

Om du vill upptäcka andra sätt att exportera och använda Custom Vision-modellen på, så kan du läsa följande dokument:

* [Exportera din modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Använda en exporterad Tensorflow-modell i ett Android-program](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Använda en exporterad CoreML-modell i ett Swift iOS-program](https://go.microsoft.com/fwlink/?linkid=857726)
* [Använda en exporterad CoreML-modell i ett iOS-program med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Mer information om hur du använder ONNX-modeller med Windows ML finns i dokumentet [Integrera en modell i din app med Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
