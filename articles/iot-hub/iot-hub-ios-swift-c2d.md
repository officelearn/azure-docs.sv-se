---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (iOS) | Microsoft-dokument
description: Så här skickar du meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT SDK:er för iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732540"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Skicka meddelanden från molnet till enheten med IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. [Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en simulerad enhetsapp som skickar meddelanden från enhet till moln.

I den här självstudiekursen lär du dig att:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från din lösningsbaksida, begär leveransbekräftelse *(feedback)* för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i [meddelandeavsnittet i utvecklarguiden för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här artikeln kör du två Swift iOS-projekt:

* **exempelenhet**, samma app som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **exempeltjänst**, som skickar ett meddelande från molnet till enheten till den simulerade enhetsappen via IoT Hub, och sedan får sin leveransbekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och -språk (inklusive C, Java, Python och Javascript) via Azure IoT-enhetSDK:er. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiens kod och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Krav

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* En aktiv IoT-hubb i Azure.

* Kodexemplet från [Azure-exempel](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Den senaste versionen av [XCode](https://developer.apple.com/xcode/) med den senaste versionen av iOS SDK. Denna snabbstart har testats med XCode 9.3 och iOS 11.3.

* Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simulera en IoT-enhet

I det här avsnittet simulerar du en iOS-enhet som kör ett Swift-program för att ta emot meddelanden från IoT-hubben från IoT-hubben. 

Det här är exempelenheten som du skapar i artikeln [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md). Om du redan har den igång kan du hoppa över det här avsnittet.

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Öppna ett terminalfönster och navigera till mappen Azure-IoT-Samples-iOS som du laddade ned i förberedelserna. Navigera sedan till exempelprojektet:

```sh
cd quickstart/sample-device
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden.

### <a name="run-the-sample-device-application"></a>Köra exempelenhetsprogrammet

1. Hämta anslutningssträngen för enheten. Du kan kopiera strängen från [Azure-portalen](https://portal.azure.com) i enhetsinformationsbladet eller hämta den med följande CLI-kommando:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera **MQTT-klientexempelprojektet** och mapp med samma namn.  

3. Öppna **ViewController.swift** för redigering i XCode. 

4. Sök efter **connectionString-variabeln** och uppdatera värdet med enhetsanslutningssträngen som du kopierade i det första steget.

5. Spara ändringarna. 

6. Kör projektet i enhetsemulatorn med knappen **Build and run** (Skapa och kör) eller tangentkombinationen **command + r**.

   ![Kör projektet](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverd-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulera en tjänstenhet

I det här avsnittet simulerar du en andra iOS-enhet med en Swift-app som skickar meddelanden från molnet till enheten via IoT-hubben. Den här konfigurationen är användbar för IoT-scenarier där det finns en iPhone eller iPad som fungerar som en styrenhet för andra iOS-enheter som är anslutna till en IoT-hubb.

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Navigera till mappen Azure IoT iOS Samples som du hämtade i förutsättningarna. Navigera sedan till exempelserviceprojektet:

```sh
cd quickstart/sample-service
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden.

### <a name="run-the-sample-service-application"></a>Kör exempeltjänstprogrammet

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expandera **AzureIoTServiceSample-projektet** och expandera sedan mappen med samma namn.  

3. Öppna **ViewController.swift** för redigering i XCode. 

4. Sök efter **connectionString-variabeln** och uppdatera värdet med den tjänstanslutningssträng som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubb](#get-the-iot-hub-connection-string).

5. Spara ändringarna.

6. I Xcode ändrar du emulatorinställningarna till en annan iOS-enhet än du använde för att köra IoT-enheten. XCode kan inte köra flera emulatorer av samma typ.

   ![Ändra emulatorenheten](media/iot-hub-ios-swift-c2d/change-device.png)

7. Kör projektet i enhetsemulatorn med knappen **Bygg och kör** eller nyckelkombinationskommandot + **r**.

   ![Kör projektet](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

Du är nu redo att använda de två programmen för att skicka och ta emot meddelanden från molnet till enheten.

1. Klicka på **Start** **i iOS App Sample-appen** som körs på den simulerade IoT-enheten . Programmet börjar skicka meddelanden från enhet till moln, men börjar också lyssna efter meddelanden från molnet till enheten.

   ![Visa exempelapp för IoT-enhet](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. I **appen IoTHub Service Client Sample** som körs på den simulerade tjänstenheten anger du ID:t för IoT-enheten som du vill skicka ett meddelande till. 

3. Skriv ett klartextmeddelande och klicka sedan på **Skicka**.

    Flera åtgärder inträffar så fort du klickar på Skicka. Tjänstexemplet skickar meddelandet till din IoT-hubb, som appen har åtkomst till på grund av den tjänstanslutningssträng som du angav. IoT-hubben kontrollerar enhets-ID:t, skickar meddelandet till målenheten och skickar ett bekräftelsekvitto till källenheten. Appen som körs på din simulerade IoT-enhet söker efter meddelanden från IoT Hub och skriver ut texten från den senaste på skärmen.

    Utdata ska se ut som följande exempel:

   ![Visa meddelanden från molnet till enheten](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skicka och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i dokumentationen [för Azure IoT Solution Accelerators.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
