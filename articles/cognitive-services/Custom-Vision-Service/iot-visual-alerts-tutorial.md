---
title: 'Självstudie: exempel på IoT Visual Alerts'
titleSuffix: Azure Cognitive Services
description: I den här självstudien använder du Custom Vision med en IoT-enhet för att identifiera och rapportera visuella tillstånd från en kameras video flöde.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020185"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Självstudie: använda Custom Vision med en IoT-enhet för att rapportera visuella tillstånd

Den här exempel appen illustrerar hur du använder Custom Vision för att träna en enhet med en kamera för att identifiera visuella tillstånd. Du kan köra det här identifierings scenariot på en IoT-enhet med hjälp av en exporterad ONNX-modell.

Ett visuellt tillstånd beskriver innehållet i en bild: ett tomt rum eller ett rum med människor, en tom driveway eller en driveway med en Last bil och så vidare. I bilden nedan kan du se appen identifiera när en banan eller ett Apple placeras framför kameran.

![Animering av en GRÄNSSNITTs etikett som är en frukt framför kameran](./media/iot-visual-alerts-tutorial/scoring.gif)

De här självstudierna visar hur du:
> [!div class="checklist"]
> * Konfigurera exempel appen så att den använder dina egna Custom Vision och IoT Hub resurser.
> * Använd appen för att träna ditt Custom Vision-projekt.
> * Använd appen för att räkna med nya bilder i real tid och skicka resultatet till Azure.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Projektet måste vara ett **komprimerings** projekt med kompakt bild, eftersom vi kommer att exportera modellen till ONNX senare.
* Du måste också [skapa en IoT Hub-resurs](https://ms.portal.azure.com/#create/Microsoft.IotHub) på Azure.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com/downloads/)
* Du kan också använda en IoT-enhet som kör Windows 10 IoT Core version 17763 eller senare. Du kan också köra appen direkt från din dator.
   * För Raspberry Pi 2 och 3 kan du konfigurera Windows 10 direkt från IoT Dashboard-appen. För andra enheter, till exempel DrangonBoard, måste du använda Flash med EMMC- [metoden](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Om du behöver hjälp med att konfigurera en ny enhet läser du konfigurera [din enhet](/windows/iot-core/tutorials/quickstarter/devicesetup) i Windows IoT-dokumentationen.

## <a name="about-the-visual-alerts-app"></a>Om appen visuella aviseringar

Appen IoT Visual Alerts körs i en kontinuerlig slinga och växlar mellan fyra olika tillstånd efter behov:

* **Ingen modell**: status No-op. Appen försätts i vilo läge för en sekund och kontrollerar kameran.
* **Fånga utbildnings avbildningar**: i det här läget fångar appen en bild och laddar upp den som en utbildnings avbildning till mål Custom vision projektet. Appen är sedan i vilo läge för 500 ms och upprepar åtgärden tills det angivna mål antalet bilder har fångats. Sedan utlöses utbildningen av Custom Visions modellen.
* **Väntar på tränad modell**: i det här läget anropar appen Custom vision-API varje sekund för att kontrol lera om mål projektet innehåller en utbildad iteration. När den hittar en hämtas motsvarande ONNX-modell till en lokal fil och växlar till **poängsättnings** tillstånd.
* **Poäng**: i det här läget använder appen Windows ml för att utvärdera en enskild RAM från kameran mot den lokala ONNX-modellen. Den resulterande bild klassificeringen visas på skärmen och skickas som ett meddelande till IoT Hub. Appen försätts sedan i vilo läge för en sekund innan en ny bild påvärderas.

## <a name="examine-the-code-structure"></a>Granska kod strukturen

Följande filer hanterar appens huvud funktioner.

| Fil | Description |
|-------------|-------------|
| [MainPage. XAML](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Den här filen definierar XAML-användargränssnittet. Den är värd för webb kamera kontrollen och innehåller etiketterna som används för status uppdateringar.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Den här koden styr beteendet för XAML-ANVÄNDARGRÄNSSNITTET. Den innehåller bearbetnings koden för tillstånds datorn.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Den här klassen är en omslutning som hanterar integrering med Custom Vision Service.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Den här klassen är en omslutning som hanterar integrering med Windows ML för inläsning av ONNX-modellen och Poäng bilder mot den.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Den här klassen är en omslutning som hanterar integrering med IoT Hub för att ladda upp resultat resultat till Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Konfigurera appen visuella aviseringar

Följ de här stegen för att få appen IoT Visual Alerts att köras på din dator eller IoT-enhet.

1. Klona eller hämta [IoTVisualAlerts-exemplet](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) på GitHub.
1. Öppna lösningen _IoTVisualAlerts. SLN_ i Visual Studio
1. Integrera ditt Custom Vision-projekt:
    1. Uppdatera _CustomVision\CustomVisionServiceWrapper.cs_ `ApiKey` variabeln med din utbildnings nyckel i CustomVision\CustomVisionServiceWrapper.cs-skriptet.
    1. Uppdatera sedan `Endpoint` variabeln med slut punkts-URL: en som är kopplad till din nyckel.
    1. Uppdatera `targetCVSProjectGuid` variabeln med motsvarande ID för det Custom vision projekt som du vill använda. 
1. Konfigurera IoT Hub resursen:
    1. I _IoTHub\IotHubWrapper.cs_ -skriptet uppdaterar du `s_connectionString` variabeln med rätt anslutnings sträng för enheten. 
    1. På Azure Portal läser du in IoT Hub-instansen, klickar på **IoT-enheter** under **Utforskaren**, väljer på din målenhet (eller skapar en vid behov) och letar rätt på anslutnings strängen under **primär anslutnings sträng**. Strängen kommer att innehålla ditt IoT Hub namn, enhets-ID och den delade åtkomst nyckeln. Det har följande format: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}` .

## <a name="run-the-app"></a>Kör appen

Om du kör appen på din dator väljer du **lokal dator** för mål enheten i Visual Studio och väljer **x64** eller **x86** för mål plattformen. Tryck sedan på F5 för att köra programmet. Appen bör starta och Visa Live-matningen från kameran och ett status meddelande.

Om du distribuerar till en IoT-enhet med en ARM-processor måste du välja **arm** som mål plattform och **fjärrdator** som mål enhet. Ange enhetens IP-adress när du uppmanas till det (den måste finnas i samma nätverk som datorn). Du kan hämta IP-adressen från Windows IoT standard-appen när du startar enheten och ansluter den till nätverket. Tryck på F5 för att köra programmet.

När du kör appen för första gången kommer den inte att ha någon kunskap om visuella tillstånd. Ett status meddelande visas om att ingen modell är tillgänglig. 

## <a name="capture-training-images"></a>Avbilda utbildnings avbildningar

Om du vill konfigurera en modell måste du lägga appen i läget för att ställa in **inlärnings avbildningar** . Utför något av följande steg:
* Om du kör appen på datorn använder du knappen i det övre högra hörnet i användar gränssnittet.
* Om du kör appen på en IoT-enhet anropar du `EnterLearningMode` metoden på enheten via IoT Hub. Du kan anropa det via enhets posten på IoT Hub-menyn på Azure Portal eller med ett verktyg som [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp).
 
När appen går in i läget för att **fånga in inlärnings avbildningar** , kommer den att fånga om två avbildningar varje sekund tills den har nått mål antalet bilder. Som standard är målet 30 bilder, men du kan ange den här parametern genom att skicka det önskade antalet som ett argument till `EnterLearningMode` metoden IoT Hub. 

När appen fångar in bilder måste du exponera kameran för de typer av visuella tillstånd som du vill identifiera (till exempel ett tomt rum, ett rum med personer, ett tomt skriv bord med en leksaks Last bil och så vidare).

## <a name="train-the-custom-vision-model"></a>Träna Custom Visions modellen

När appen har skapat avbildningarna laddar den upp dem och växlar sedan till **väntar på tränad modell** status. Nu måste du gå till [Custom vision webbplats](https://www.customvision.ai/) och bygga en modell som baseras på de nya utbildnings avbildningarna. Följande animering visar ett exempel på den här processen.

![Animering: tagga flera bilder av bananer](./media/iot-visual-alerts-tutorial/labeling.gif)

Upprepa den här processen med ditt eget scenario:

1. Logga in på [Custom vision webbplats](http://customvision.ai).
1. Hitta mål projektet, vilket nu bör ha alla utbildnings bilder som appen har laddat upp.
1. För varje visuellt läge som du vill identifiera väljer du lämpliga avbildningar och tillämpar sedan taggen manuellt.
    * Om målet till exempel är att skilja mellan ett tomt rum och ett rum med personer i det rekommenderar vi att du lägger till fem eller fler bilder med personer som en ny klass, **människor** och tagga fem eller flera bilder utan personer som den **negativa** taggen. Detta hjälper modellen att skilja mellan de två tillstånden.
    * Ett annat exempel är om målet är att uppskatta hur full hylla är, kan du använda taggar som **EmptyShelf**, **PartiallyFullShelf** och **FullShelf**.
1. När du är klar väljer du knappen **träna** .
1. När inlärningen är klar kommer appen att identifiera att en utbildad iteration är tillgänglig. Processen för att exportera den tränade modellen kommer att startas till ONNX och hämtas till enheten.

## <a name="use-the-trained-model"></a>Använd den tränade modellen

När appen har laddat ned den tränade modellen, kommer den att växla till **bedömnings** läget och börja poängs bilder från kameran i en kontinuerlig slinga.

Appen kommer att visa den översta taggen på skärmen för varje avbildnings avbildning. Om det inte går att identifiera det visuella läget visas **inga matchningar**. Appen skickar även dessa meddelanden till IoT Hub, och om det finns en klass som identifieras innehåller meddelandet etiketten, förtroende poängen och en egenskap `detectedClassAlert` som kallas, som kan användas av IoT Hub klienter som vill utföra snabb meddelanderoutning baserat på egenskaper.

Exemplet använder dessutom ett [Sense hat-bibliotek](https://github.com/emmellsoft/RPi.SenseHat) för att identifiera när den körs på en Raspberry Pi med en Sense hat-enhet, så att den kan använda den som en utmatnings visning genom att ställa in alla visnings lampor till röd när den identifierar en klass och är tom när den inte identifierar något.

## <a name="reuse-the-app"></a>Återanvänd appen

Om du vill återställa appen tillbaka till ursprungs läget kan du göra det genom att klicka på knappen i det övre högra hörnet i användar gränssnittet eller genom att anropa metoden `DeleteCurrentModel` via IoT Hub.

Du kan när som helst upprepa steget med att ladda upp inlärnings avbildningar genom att klicka på knappen längst upp till höger eller anropa `EnterLearningMode` metoden igen.

Om du kör appen på en enhet och behöver hämta IP-adressen igen (för att upprätta en fjärr anslutning via [fjärrklienten i Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)) kan du anropa `GetIpAddress` metoden via IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort Custom Vision projektet om du inte längre vill underhålla det. Navigera till **projekt** på [Custom vision webbplats](https://customvision.ai)och välj pappers korgen under det nya projektet.

![Skärm bild av en panel med etiketten mitt nya projekt med en pappers korgs ikon](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat och kört ett program som identifierar information om visuell information på en IoT-enhet och skickar resultatet till IoT Hub. Utforska sedan käll koden ytterligare eller gör något av de föreslagna ändringarna nedan.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-exempel (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Lägg till en IoT Hub-metod för att växla appen direkt till **väntar på intränat modell** tillstånd. På så sätt kan du träna modellen med bilder som inte har registrerats av själva enheten och sedan skicka den nya modellen till enheten i kommandot.
* Följ själv studie kursen [visualisera real tids sensor data](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) för att skapa en Power BI instrument panel för att visualisera de IoT Hub aviseringar som skickas av exemplet.
* Följ själv studie kursen för [IoT-fjärrövervakningen](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) för att skapa en logisk app som svarar på IoT Hub aviseringar när visuella tillstånd identifieras.