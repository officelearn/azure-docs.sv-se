---
title: Meddelanden från molnet till enheten med Azure IoT Hub (iOS) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från en Azure IoT hub med Azure IoT SDK för iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 882c2f7d9abfa40ddafbba4f98292c3f8f44c459
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548719"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Skicka meddelanden från moln till enhet med IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Den [skickar telemetri från en enhet till IoT hub](quickstart-send-telemetry-ios.md) artikeln visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en simulerad enhetsapp som skickar meddelanden från enheten till molnet.

Den här artikeln visar hur du:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i den [meddelanden i IoT Hub developer guiden](iot-hub-devguide-messaging.md).

I slutet av den här artikeln kör du två Swift iOS-projekt:

* **exemplet enhet**, samma app som skapats i [skickar telemetri från en enhet till IoT hub](quickstart-send-telemetry-ios.md), som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.

* **exempel-tjänst**, som skickar ett moln-till-enhet-meddelande till den simulerade enhetsappen via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [Azure IoT Developer Center](https://www.azure.com/develop/iot).

För att kunna genomföra den här kursen behöver du följande:

- Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

- En aktiv IoT-hubb i Azure. 

- Kodexemplet från [Azure-exempel](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .

- Den senaste versionen av [XCode](https://developer.apple.com/xcode/) med den senaste versionen av iOS SDK. Denna snabbstart har testats med XCode 9.3 och iOS 11.3.

- Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Simulera en IoT-enhet

I det här avsnittet ska simulera du en iOS-enhet som kör ett Swift program att ta emot meddelanden från moln till enhet från IoT hub. 

Det här är exempel-enhet som du skapar i artikeln [skickar telemetri från en enhet till IoT hub](quickstart-send-telemetry-ios.md). Om du redan har den körs, kan du hoppa över det här avsnittet.

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

### <a name="run-the-sample-device-application"></a>Kör programmet på exempelenheten 

1. Hämta anslutningssträngen för din enhet. Du kan kopiera den här strängen från den [Azure-portalen](https://portal.azure.com) i bladet med enhetsinformation, eller hämta den med följande CLI-kommando: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera den **MQTT Client Sample** projekt och sedan mappen med samma namn.  

3. Öppna **ViewController.swift** för redigering i XCode. 

4. Sök efter den **connectionString** variabeln och uppdatera värdet med strängen som du kopierade i det första steget.

5. Spara ändringarna. 

6. Kör projektet i enhetsemulatorn med knappen **Build and run** (Skapa och kör) eller tangentkombinationen **command + r**. 

   ![Kör projektet](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="simulate-a-service-device"></a>Simulera en enhet för tjänsten

I det här avsnittet ska simulera du en andra iOS-enhet med en Swift-app som skickar meddelanden från molnet till enheten via IoT hub. Den här konfigurationen är användbar för IoT-scenarier där det finns en iPhone eller iPad som fungerar som en domänkontrollant för andra iOS-enheter är anslutna till en IoT-hubb. 

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Navigera till mappen Azure IoT iOS-exempel som du hämtade i förutsättningarna. Gå sedan till exempelprojektet för tjänsten:

```sh
cd quickstart/sample-service
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden.

### <a name="run-the-sample-service-application"></a>Kör exempelprogrammet för tjänsten

1. Hämta tjänstanslutningssträngen för din IoT hub. Du kan kopiera den här strängen från den [Azure-portalen](https://portal.azure.com) från den **iothubowner** principen i den **principer för delad åtkomst** bladet eller hämta den med följande CLI-kommando:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Öppna exempelarbetsytan i XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Expandera den **AzureIoTServiceSample** projekt och expandera sedan mappen med samma namn.  

4. Öppna **ViewController.swift** för redigering i XCode. 

5. Sök efter den **connectionString** variabeln och uppdatera värdet med tjänstanslutningssträngen som du kopierade tidigare.

6. Spara ändringarna. 

7. Ändra inställningarna för emulatorn till en annan iOS-enhet än du används för att köra IoT-enheter i Xcode. XCode kan inte köra flera emulatorer av samma typ. 

   ![Ändra emulatorn-enheten](media/iot-hub-ios-swift-c2d/change-device.png)

8. Köra projektet i enhetsemulatorn med den **skapa och köra** knappen eller tangentkombinationen **Command + r**. 

   ![Kör projektet](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande

Du är nu redo att använda de två programmen för att skicka och ta emot meddelanden från molnet till enheten.

1. I den **iOS appexempel** app som körs på den simulerade IoT-enheten, klicka på **starta**. Programmet börjar skicka meddelanden från enheten till molnet, men även börjar lyssna efter meddelanden från molnet till enheten. 

   ![Visa exempelapp för IoT-enhet](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. I den **IoTHub Service Client Sample** app som körs på tjänstenheten simulerade Ange ID för IoT-enheter som du vill skicka ett meddelande till. 

3. Skriva ett meddelande som oformaterad text och klicka sedan på **skicka**. 

    Flera åtgärder inträffa när du klickar på Skicka. Tjänstexemplet skickar meddelandet till din IoT hub, som appen har åtkomst till strängen som du förutsatt för på grund av tjänst-anslutningen. IoT-hubben kontrollerar enhets-ID, skickar meddelandet till målenheten och skickar en bekräftelse inleverans till källan. När appen körs på den simulerade IoT-enheten söker efter meddelanden från IoT Hub och skriver ut texten från den senaste på skärmen.

    Dina utdata bör se ut som i följande exempel:

   ![Visa meddelanden från moln till enhet](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skicka och ta emot meddelanden från moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i den [Azure IoT-Lösningsacceleratorer](https://azure.microsoft.com/documentation/suites/iot-suite/) dokumentation.

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
