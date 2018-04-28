---
title: Moln till enhet meddelanden med Azure IoT Hub (iOS) | Microsoft Docs
description: Hur du skickar meddelanden moln till enhet till en enhet från en Azure IoT-hubb med Azure IoT-SDK för iOS.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 032412c329e79ec671f59a049da7d8ddc0b9dd08
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Skicka meddelanden moln till enhet med IoT-hubb (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas. Den [skicka telemetri från en enhet till en IoT-hubb] artikeln visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och code en simulerad enhetsapp som skickar meddelanden från enhet till moln.

Den här artikeln beskrivs hur du vill:

* Skicka meddelanden moln till enhet på en enhet till IoT-hubb från din lösningens serverdel.
* Ta emot meddelanden moln till enhet på en enhet.
* Begära leverans bekräftelse från din lösningens serverdel (*feedback*) för meddelanden som skickas till en enhet från IoT-hubb.

Du hittar mer information om moln till enhet meddelanden i den [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I slutet av den här artikeln kan köra du två Swift iOS-projekt:

* **exempel enhet**, med samma app som skapats i [skicka telemetri från en enhet till en IoT-hubb], som ansluter till din IoT-hubb och tar emot meddelanden moln till enhet.
* **exempel-service**, som skickar ett moln till enhet-meddelande till appen simulerade enheten via IoT-hubb och tar emot dess leverans bekräftelse.

> [!NOTE]
> IoT-hubben har SDK stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK för Azure IoT-enhet. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiekursen kod och vanligtvis Azure IoT Hub finns i [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

- Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)
- En aktiv IoT-hubb i Azure. 
- Kodexempel från [Azure-exempel](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .
- Den senaste versionen av [XCode](https://developer.apple.com/xcode/), kör den senaste versionen av iOS SDK. Denna Snabbstart har testats med XCode 9.3 och iOS 11,3.
- Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>Simulera en IoT-enhet
I det här avsnittet kan du simulera en iOS-enhet som kör en snabb program ta emot meddelanden moln till enhet från IoT-hubben. 

Det här är exempel exempel enhet som du skapar i artikeln [skicka telemetri från en enhet till en IoT-hubb]. Om du redan har den körs, kan du hoppa över det här avsnittet.

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hantera beroenden för iOS-projekt med tredjeparts-bibliotek.

Navigera till den Azure-IoT-exempel-iOS-mappen som du hämtade i förutsättningarna i ett terminalfönster. Gå till exempelprojektet:

```sh
cd quickstart/sample-device
```

Kontrollera att XCode är stängt, och kör sedan följande kommando för att installera CocoaPods som deklareras i den **podfile** fil:

```sh
pod install
```

Tillsammans med installerar skida som krävs för ditt projekt, skapas också en arbetsyta XCode-fil som redan är konfigurerad för att använda skida för beroenden i installationskommandot. 

### <a name="run-the-sample-device-application"></a>Kör exempelprogrammet enhet 

1. Hämta anslutningssträngen för din enhet. Du kan kopiera den här strängen från Azure-portalen i bladet enheten information eller hämta med följande CLI-kommando: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Öppna arbetsytan exemplet i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera den **MQTT klienten exempel** projektet och sedan mappen med samma namn.  
3. Öppna **ViewController.swift** för redigering i XCode. 
4. Sök efter den **connectionString** variabeln och uppdatera värdet med enhetsanslutningen sträng som du kopierade i det första steget.
5. Spara ändringarna. 
6. Köra projektet i emulatorn med den **skapa och köra** knappen eller viktiga kombinationsrutan **kommando + r**. 

   ![Kör projektet](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>Simulera en enhet för tjänsten

I det här avsnittet kan du simulera en annan iOS-enhet med en snabb app som skickar meddelanden moln till enhet via IoT-hubben. Den här konfigurationen är användbar för IoT-scenarier där det finns en iPhone eller iPad som fungerar som en domänkontrollant för andra iOS-enheter anslutna till en IoT-hubb. 

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hantera beroenden för iOS-projekt med tredjeparts-bibliotek.

Navigera till mappen Azure IoT iOS-exempel som du hämtade i kraven. Gå till tjänsten exempelprojektet:

```sh
cd quickstart/sample-service
```

Kontrollera att XCode är stängt, och kör sedan följande kommando för att installera CocoaPods som deklareras i den **podfile** fil:

```sh
pod install
```

Tillsammans med installerar skida som krävs för ditt projekt, skapas också en arbetsyta XCode-fil som redan är konfigurerad för att använda skida för beroenden i installationskommandot.

### <a name="run-the-sample-service-application"></a>Kör exempelprogrammet service

1. Hämta anslutningssträngen service för din IoT-hubb. Du kan kopiera den här strängen från Azure-portalen från den **iothubowner** princip i den **principer för delad åtkomst** bladet eller hämta med följande CLI-kommando:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Öppna arbetsytan exemplet i XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Expandera den **AzureIoTServiceSample** projektet och sedan expandera mappen med samma namn.  
4. Öppna **ViewController.swift** för redigering i XCode. 
5. Sök efter den **connectionString** variabeln och uppdatera värdet med den anslutningssträng som du kopierade tidigare.
6. Spara ändringarna. 
7. Ändra inställningarna för emulatorn till en annan iOS-enhet än du används för att köra IoT-enhet i Xcode. XCode kan inte köra flera emulatorerna av samma typ. 

   ![Ändra emulatorenhet](media/iot-hub-ios-swift-c2d/change-device.png)

8. Köra projektet i emulatorn med den **skapa och köra** knappen eller viktiga kombinationsrutan **kommando + r**. 

   ![Kör projektet](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande moln till enhet
Du är nu redo att använda de två programmen att skicka och ta emot meddelanden moln till enhet.

1. I den **iOS App Sample** app som körs på den simulerade IoT-enheten, klickar du på **starta**. Programmet börjar skicka meddelanden från enhet till moln, men även börjar lyssna efter meddelanden moln till enhet. 

   ![Visa exempelapp för IoT-enhet](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. I den **IoTHub Service klienten exempel** app som körs på enheten simulerade service Ange ID för IoT-enhet som du vill använda en att skicka ett meddelande till. 
3. Skriva ett meddelande som oformaterad text och klicka sedan på **skicka**. 

Flera åtgärder inträffa när du klickar på Skicka. Tjänsten exemplet skickar meddelandet till din IoT hub som appen har åtkomst till strängen som du angivna för på grund av tjänst-anslutningen. IoT-hubb kontrollerar enhets-ID, skickar meddelandet till målenheten och skickar en bekräftelse inleverans till källan. Appen körs på den simulerade enheten IoT söker efter meddelanden från IoT-hubb och skriver ut texten från det senaste på skärmen.

Dina utdata bör se ut som i följande exempel:

   ![Visa meddelanden moln till enhet](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du skickar och tar emot meddelanden moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[skicka telemetri från en enhet till en IoT-hubb]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
