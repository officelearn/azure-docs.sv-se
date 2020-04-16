---
title: 'Självstudiekurs: Exempel på IoT-visuella varningar'
titleSuffix: Azure Cognitive Services
description: I den här självstudien använder du Anpassad vision med en IoT-enhet för att känna igen och rapportera visuella tillstånd från en kameras videoflöde.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f66347727ad3c1b8eaf1f0e023abe1f2eeefcacb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403727"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Självstudiekurs: Använd anpassad syn med en IoT-enhet för att rapportera visuella tillstånd

Den här exempelappen illustrerar hur du använder Anpassad syn för att träna en enhet med en kamera för att identifiera visuella tillstånd. Du kan köra det här identifieringsscenariot på en IoT-enhet med hjälp av en exporterad ONNX-modell.

Ett visuellt tillstånd beskriver innehållet i en bild: ett tomt rum eller ett rum med människor, en tom uppfart med en lastbil och så vidare. I bilden nedan kan du se app upptäcka när en banan eller ett äpple placeras framför kameran.

![Animering av ett användargränssnitt som etiketterar frukt framför kameran](./media/iot-visual-alerts-tutorial/scoring.gif)

De här självstudierna visar hur du:
> [!div class="checklist"]
> * Konfigurera exempelappen så att den använder dina egna anpassade visions- och IoT Hub-resurser.
> * Använd appen för att träna ditt Custom Vision-projekt.
> * Använd appen för att få nya avbildningar i realtid och skicka resultaten till Azure.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 

## <a name="prerequisites"></a>Krav

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Detta projekt måste vara en **Kompakt** bild klassificering projekt, eftersom vi kommer att exportera modellen till ONNX senare.
* Du måste också [skapa en IoT Hub-resurs](https://ms.portal.azure.com/#create/Microsoft.IotHub) på Azure.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com/downloads/)
* Alternativt kan en IoT-enhet som kör Windows 10 IoT Core version 17763 eller senare. Du kan också köra appen direkt från datorn.
   * För Raspberry Pi 2 och 3 kan du konfigurera Windows 10 direkt från IoT Dashboard-appen. För andra enheter, till exempel DrangonBoard, måste du blinka den med [eMMC-metoden](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Om du behöver hjälp med att konfigurera en ny enhet läser [du Konfigurera enheten](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) i Windows IoT-dokumentationen.

## <a name="about-the-visual-alerts-app"></a>Om appen Visuella aviseringar

IoT Visual Alerts-appen körs i en kontinuerlig loop och växlar mellan fyra olika lägen efter behov:

* **Ingen modell:** Ett tillstånd utan op. Appen kommer att sova kontinuerligt i en sekund och kontrollera kameran.
* **Fånga träningsbilder:** I det här tillståndet tar appen en bild och laddar upp den som en träningsbild till projektet Custom Vision. Appen försätts sedan i 500 ms och upprepar åtgärden tills det angivna målantalet bilder tas. Sedan utlöser den utbildning av Custom Vision-modellen.
* **Väntar på tränad modell:** I det här tillståndet anropar appen Custom Vision API varje sekund för att kontrollera om målprojektet innehåller en tränad iteration. När den hittar en hämtas motsvarande ONNX-modell till en lokal fil och växlar till **bedömningstillståndet.**
* **Poängsättning:** I det här läget använder appen Windows ML för att utvärdera en enda bildruta från kameran mot den lokala ONNX-modellen. Den resulterande bildklassificeringen visas på skärmen och skickas som ett meddelande till IoT Hub. Appen förser sedan en sekund innan du gör en ny bild.

## <a name="understand-the-code-structure"></a>Förstå kodstrukturen

Följande filer hanterar appens huvudfunktioner.

| Fil | Beskrivning |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Den här filen definierar XAML-användargränssnittet. Den är värd för webbkamerakontrollen och innehåller de etiketter som används för statusuppdateringar.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Den här koden styr xaml-användargränssnittets beteende. Den innehåller tillståndsmaskinens bearbetningskod.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Den här klassen är ett omslag som hanterar integrering med Custom Vision-tjänsten.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Den här klassen är ett omslag som hanterar integrering med Windows ML för att läsa in ONNX-modellen och göra bedömning av bilder mot den.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Den här klassen är ett omslag som hanterar integrering med IoT Hub för att överföra poängresultat till Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Konfigurera appen Visuella aviseringar

Följ dessa steg för att få IoT Visual Alerts-appen att köras på din dator eller IoT-enhet.

1. Klona eller hämta [ioTVisualAlerts-exemplet](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) på GitHub.
1. Öppna lösningen _IoTVisualAlerts.sln_ i Visual Studio
1. Integrera ditt Custom Vision-projekt:
    1. Uppdatera variabeln med träningsnyckeln i `ApiKey` skriptet _CustomVision\CustomVisionServiceWrapper.cs._
    1. Uppdatera sedan `Endpoint` variabeln med slutpunkts-URL:en som är associerad med nyckeln.
    1. Uppdatera `targetCVSProjectGuid` variabeln med motsvarande ID för det Custom Vision-projekt som du vill använda. 
1. Konfigurera IoT Hub-resursen:
    1. Uppdatera variabeln med rätt anslutningssträng för enheten i `s_connectionString` skriptet _IoTHub\IotHubWrapper.cs._ 
    1. På Azure-portalen läser du in din IoT Hub-instans, klickar på **IoT-enheter** under Utforskare , väljer på din målenhet (eller skapar en om det behövs) och hittar anslutningssträngen under **Primär anslutningssträng**. **Explorers** Strängen innehåller ditt IoT Hub-namn, enhets-ID och nyckeln för delad åtkomst. Den har följande `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`format: .

## <a name="run-the-app"></a>Kör appen

Om du kör appen på datorn väljer du **Lokal dator** för målenheten i Visual Studio och väljer **x64** eller **x86** för målplattformen. Tryck sedan på F5 för att köra programmet. Appen ska starta och visa liveflödet från kameran och ett statusmeddelande.

Om du distribuerar till en IoT-enhet med en ARM-processor måste du välja **ARM** som målplattform och **Fjärrdator** som målenhet. Ange enhetens IP-adress när du uppmanas (den måste finnas i samma nätverk som datorn). Du kan hämta IP-adressen från Windows IoT-standardappen när du har startat enheten och anslutit den till nätverket. Tryck på F5 för att köra programmet.

När du kör appen för första gången har den ingen kunskap om visuella tillstånd. Det kommer att visa ett statusmeddelande om att ingen modell är tillgänglig. 

## <a name="capture-training-images"></a>Ta in träningsbilder

Om du vill konfigurera en modell måste du placera appen i tillståndet **Fånga utbildningsbilder.** Gör något av följande:
* Om du kör appen på datorn använder du knappen längst upp till höger i användargränssnittet.
* Om du kör appen på en IoT-enhet ringer du `EnterLearningMode` metoden på enheten via IoT Hub. Du kan anropa den via enhetsposten på IoT Hub-menyn på Azure-portalen eller med ett verktyg som [Utforskaren för IoT Hub Device](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
När appen går in i läget **Fånga träningsbilder,** kommer den att fånga ungefär två bilder varje sekund tills den har nått målantalet bilder. Som standard är målet 30 bilder, men du kan ange den här `EnterLearningMode` parametern genom att skicka önskat nummer som ett argument till IoT Hub-metoden. 

Medan appen tar bilder måste du exponera kameran för de typer av visuella tillstånd som du vill upptäcka (till exempel ett tomt rum, ett rum med människor, ett tomt skrivbord, ett skrivbord med en leksaksbil och så vidare).

## <a name="train-the-custom-vision-model"></a>Träna Custom Vision-modellen

När appen har hämtat bilder laddas den upp och växlar sedan till tillståndet **Väntar på tränad modell.** Nu måste du gå till [Custom Vision-portalen](https://www.customvision.ai/) och bygga en modell baserad på de nya träningsbilderna. Följande animering visar ett exempel på den här processen.

![Animation: taggning av flera bilder av bananer](./media/iot-visual-alerts-tutorial/labeling.gif)

Så här upprepar du den här processen med ditt eget scenario:

1. Logga in på [portalen Anpassad vision](http://customvision.ai).
1. Hitta ditt målprojekt, som nu ska ha alla träningsbilder som appen har laddat upp.
1. För varje visuellt tillstånd som du vill identifiera markerar du lämpliga bilder och använder taggen manuellt.
    * Om ditt mål till exempel är att skilja mellan ett tomt rum och ett rum med personer i det, rekommenderar vi att du taggar fem eller fler bilder med personer som en ny klass, **Kontakter**och taggar fem eller fler bilder utan att personer är negativa som **den negativa** taggen. Detta kommer att hjälpa modellen skilja mellan de två staterna.
    * Om ditt mål är att approximera hur fullständig en hylla är kan du använda taggar som **EmptyShelf,** **PartiallyFullShelf**och **FullShelf**.
1. När du är klar **Train** väljer du tågknappen.
1. När utbildningen är klar kommer appen att upptäcka att en tränad iteration är tillgänglig. Det kommer att starta processen med att exportera den tränade modellen till ONNX och ladda ner den till enheten.

## <a name="use-the-trained-model"></a>Använd den tränade modellen

När appen hämtar den tränade modellen växlar den till **bedömningsläget** och börjar göra mål på bilder från kameran i en kontinuerlig loop.

För varje tagen bild visar appen den översta taggen på skärmen. Om det inte känner igen det visuella tillståndet visas **inga matchningar**. Appen skickar också dessa meddelanden till IoT Hub, och om det finns en klass som upptäcks, kommer `detectedClassAlert`meddelandet att innehålla etiketten, förtroendepoängen och en egenskap som heter , som kan användas av IoT Hub-klienter som är intresserade av att göra snabb meddelanderoutning baserat på egenskaper.

Dessutom använder exemplet ett [Sense HAT-bibliotek](https://github.com/emmellsoft/RPi.SenseHat) för att identifiera när det körs på en Raspberry Pi med en Sense HAT-enhet, så att den kan använda den som en utdataskärm genom att ställa in alla bildskärmslampor på rött när det upptäcker en klass och tom när den inte upptäcker någonting.

## <a name="reuse-the-app"></a>Återanvända appen

Om du vill återställa appen till sitt ursprungliga tillstånd kan du göra det genom att klicka på knappen längst upp till `DeleteCurrentModel` höger i användargränssnittet eller genom att anropa metoden via IoT Hub.

När som helst kan du upprepa steget för att ladda upp träningsbilder genom `EnterLearningMode` att klicka på den övre högra UI-knappen eller anropa metoden igen.

Om du kör appen på en enhet och behöver hämta IP-adressen igen (för att upprätta en fjärranslutning via [Windows IoT Remote Client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), till exempel), kan du anropa `GetIpAddress` metoden via IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort projektet Custom Vision om du inte längre vill behålla det. På [webbplatsen Custom Vision](https://customvision.ai)navigerar du till **Projekt** och väljer papperskorgen under det nya projektet.

![Skärmbild av en panel med etiketten Mitt nytt projekt med en papperskorgsikon](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar och körde du ett program som identifierar visuell tillståndsinformation på en IoT-enhet och skickar resultaten till IoT Hub. Därefter utforska källkoden ytterligare eller gör en av de föreslagna ändringarna nedan.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-exempel (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Lägg till en IoT Hub-metod för att växla appen direkt till tillståndet **Väntar på tränad modell.** På så sätt kan du träna modellen med bilder som inte tas av själva enheten och sedan skicka den nya modellen till enheten på kommando.
* Följ självstudiekursen [Visualisera sensordata](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) i realtid för att skapa en Power BI Dashboard för att visualisera IoT Hub-aviseringarna som skickas av exemplet.
* Följ [ioT-guiden för fjärrövervakning](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) för att skapa en Logic App som svarar på IoT Hub-aviseringarna när visuella tillstånd identifieras.
